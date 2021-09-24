# Simple Tracker remodel

## Work Flow
1. Step 1 Create Multiple Hypotheses when birth the track (in the trackBase constructor)
2. Step 2 predict those different Hypotheses (In the prediction step)
3. Step 3 Select the heading from the pseudo heading [0. 90 -90, 180] for each hypotheses by using the Mahalanobis distance. 
4. Step 4 Update the Multiple Hypotheses
5. Step 5 Update Hypotheses weight
6. Step 6 re normalized the Hypotheses weight
7. Step 7 Prune the Hypotheses with small weight


## TrackBase

### Data structure

added data in the trackBase

```cpp
private: 
    std::vector<Eigen::VectorXd> m_mu_multiHypothesis; 
    std::vector<Eigen::MatrixXd> m_P_multiHypothesis; 
    std::vector<double> log_weight_multiHypothesis; 
```

### Constructor
Original code
```cpp
explicit TrackBase(Eigen::VectorXd mu, 
                           Eigne::MatrixXd P, 
                           Eigen::VectorXd embedding, 
                           std::shared_ptr<Trajectories> trajectories, 
                           std::shared_ptr<PredictionAgent> agent, 
                           ObjectType objType, 
                           int64_t id, 
                           TimePoint timestamp)
            : m_mu(std::move(mu))
            , m_P(std::move(P))
            , m_embedding(std::move(embedding))
            , m_type(objType)
            , m_id(id)
            , m_timestamp(timestamp)
            , m_trajectories(std::move(trajectories))
            , m_predictionAgent(std::move(agent))
        {
            // initial mean and covariance matrix. 
            // Currently directly using the meas noise because space are matching. 
            // In the long, we need to either adjust or use a different model. 
            m_measRecorder.recordHit(m_timestamp); 
            // PERCPRED-1289:  all vehicle measurement types are tracked as a 
            //                 single vehicle within tracker. 
            if (objType == ObjectType::heavyVehicle) 
            {
                m_type = ObjectType::passengetCar; 
            } else
            {
                m_type = objType; 
            }
        }
```

in the Constructor add those code
```cpp
// initial the multiHypothesis. 
for auto item : m_mu_multiHypothesis
{
    // create the m_mu_multiHypothesis. 
    item = m_mu + [0, 90, -90, 180]. 
}
m_P_multiHypothesis = [m_P, m_P, m_P,m_P]
log_weight_multiHypothesis = log([0.65, 0.10, 0.10, 0.15])
```

### prediction
in the TrackBase::predict
Original Code

```cpp
void TrackBase::predict(const Duration dt, 
                            const MotionBase& motionModel)
    {
        motionModel.predict(m_mu, m_P, dt); 
        m_timestamp += dt; 
        // reset at new T, indicating the need to re-compute. 
        m_preductedCovOpt = std::experimental::nullopt; 
    }
```


```cpp
void TrackBase::predict(const Duration dt, 
                            const MotionBase& motionModel)
{
    
    for index in len(multiHypothesis): 
        hypo_mu = m_mu_multiHypothesis[index]; 
        hypo_p  = m_P_multiHypothesis[index];
        motionModel.predict(hypo_mu, hypo_P, dt); 
    
    outPutHypothesisIndex = argmax(log_weight_multiHypothesis); 
    m_mu = m_mu_multiHypothesis[outPutHypothesisIndex]; 
    m_P  = m_P_multiHypothesis[outPutHypothesisIndex];
}
```


### Association
Use the multiple Hypothesis for data association. 
Could be implement later. Just use the highest weight hypothesis for data association. 

Later we could use the Mahalanobis distance for data association, by not using the heading. or rotating the heading. 

### Update Step

previous code
```cpp
void TrackBase::update(const Measurement& meas, 
                           const MeasurementModelBase& measModel)
    {
        m_measRecorder.recordHit(meas.getTimestamp()); 
        measModel.update(m_mu, m_P, meas); 
        // TODO: (PERCPRED-689) decide if we want to track the embedding (id dime is low)? 
        m_embedding = meas.getEmbedding();
        // Trajectories are currently just pass-through data. 
        m_trajectories = meas.getTrajectoriesPtr(); 
        // PredictionAgent are currently just pass-through data
        m_predictionAgent = meas.getPredictionAgent();
    }
```

Change to
```cpp
void TrackBase::update(const Measurement& meas, 
                       const MeasurementModelBase& measModel)
{
    m_measRecorder.recordHit(meas.getTimestamp()); 

    /***************************************************************************/
    pseudo_meas = [0, 90, -90, 180]; 
    for index in len(multiHypothesis): 
        hypo_mu = m_mu_multiHypothesis[index]; 
        hypo_p  = m_P_multiHypothesis[index];
        MahalanobisDistForDifferentHypo = [0, 0, 0, 0]
        for angelIndex, angle in enumerate(pseudo_meas):
            meas_tmp = meas + angle
            MahalanobisDistForDifferentHypo[angelIndex] = 
                measModel.getMahalanobisDistance(hypo_mu, hypo_p.value(), meas); 
        // get the heading win in this competition. 
        winnerIndex = argmax(MahalanobisDistForDifferentHypo)

        meas[heading] += pseudo_meas[winnIndex]; 
        // just update the pseudo measurement for that
        measModel.update(hypo_mu, hypo_p, meas); 
        // weight updating
        updateLog_weight_multiHypothesis(
            index,  // index for the Hypothesis, 
            MahalanobisDistForDifferentHypo[winerIndex], // measurement Likelihood
            log_weight_multiHypothesis // weight vector)
    // re-normalized the log weight, because the sum of the weight need == 1. 
    weightRenormalization(log_weight_multiHypothesis);
    
    
    winnerHypothesisIndex = argmax(log_weight_multiHypothesis); 
    mu = m_mu_multiHypothesis[winnerHypothesisIndex]
    m_P = m_P_multiHypothesis[winnerHypothesisIndex]

    /************************************************************************/
    


    // TODO: (PERCPRED-689) decide if we want to track the embedding (id dime is low)? 
    m_embedding = meas.getEmbedding();
    // Trajectories are currently just pass-through data. 
    m_trajectories = meas.getTrajectoriesPtr(); 
    // PredictionAgent are currently just pass-through data
    m_predictionAgent = meas.getPredictionAgent();
}
```

### New function needed
1. Calculate the Mahalanobis Distance for each pseudo measurements
```cpp
measModel.getMahalanobisDistance(hypo_mu, hypo_p.value(), meas); 
```

2. Update the Hypotheses weight
The equation to update the current hypotheses weight
```cpp
updateLog_weight_multiHypothesis(
            index,  // index for the Hypothesis, 
            MahalanobisDistForDifferentHypo[winerIndex], // measurement Likelihood
            log_weight_multiHypothesis // weight vector)
```

3. Re-normalized the hypotheses weight 
Because the weight need to be summed to 1. 

```cpp
weightRenormalization(log_weight_multiHypothesis);
```


