Isma
 21I-1744
 Assignment: 01
 Deep Learning
 National University of Emerging and Computer Science
1. Column Selection and Dropping
 The dataset contains several columns, many of which provide detailed flight-related information.
 However, not all columns are necessary for building an ensemble model. Several columns were dropped
 based on redundancy, lack of relevance, or because they could introduce noise into the model.
 Columns Dropped:
 ● CodesharedAirline Information:
 Columns related to codesharing (e.g., codeshared_airline_name,
 codeshared_flight_number)weredroppedbecause codesharing typically involves
 another airline operating the flight under a different code, which might not contribute directly to
 predicting delays or flight durations. Including these could introduce unnecessary noise.
 ● Arrival and Departure Gate Information: Columns like arrival_gate, departure_gate,
 arrival_terminal,anddeparture_terminalweredroppedbecausethegateand
 terminal assignments are more operational logistics rather than factors affecting delays or
 overall flight behavior. Moreover, not all flights may have this data consistently, leading to
 sparsity.
 ● StatusandTimeInformation: Columns like arrival_actualTime,
 departure_actualTime,estimated_runway_times,andstatusweredropped
 because they represent actual and estimated timings. These provide information after the fact,
 whereas our focus is to predict delays before flight completion.
 Rationale for Dropping Columns:
 ● Redundancy:Manyofthecolumnsprovided information that would be irrelevant to the final
 objective, such as gate assignments or codeshare data.
 ● Post-event Data: Data like actual arrival or departure times represent after-the-fact information,
 which we wouldn't have in real-world prediction scenarios.
 ● Sparsity: Some columns had many missing values, making it impractical to retain them in the
 dataset.
 2. Handling Missing Values
 Missing data is a common challenge when working with real-world datasets. In this case, missing values
 were handled as follows:
 ● DepartureTerminal: The mode of the column ('departure_terminal') was used to fill in
 missing values. Since terminal assignment is generally consistent across an airline and airport,
 this was deemed a reasonable approach.
 ● DepartureDelay: Missing values in the 'departure_delay' column were filled with the
 median. The median is a robust statistic, particularly for skewed distributions like delays.
 ● Airline IATA and Flight Numbers: The mode of the 'airline_iataCode' and
 'flight_iataNumber'columnswereusedtoimpute missing values, as airline codes and
 flight numbers tend to repeat across days, and this was a logical choice.
3. Feature Engineering
 Feature engineering helps create additional features that may be more predictive than the raw data
 alone.
 ● DepartureTimeofDay: Anewcolumn'departure_time_of_day'wascreatedby
 categorizing the departure times into different periods (Morning, Afternoon, Evening, and
 Night). This allows the model to capture any patterns related to when flights are more likely to
 be delayed.
 ● ScheduledFlight Duration (in Hours): By calculating the duration of the scheduled flight
 ('scheduled_flight_duration_hours'),themodelgains insight into how long the
 flight is expected to take, which may correlate with delays.
 4. Binning Departure Delays
 The 'departure_delay'columnwasbinnedinto categories representing ranges of delay times.
 These bins represent different levels of delay severity (e.g., 0-15 minutes, 16-30 minutes), making the
 delay prediction task more interpretable. The bins used were:
 ● 0-15minutes(minimal delay)
 ● 16-30minutes
 ● 31-60minutes
 ● 61-120minutes
 ● 121-240minutes
 ● 241-360minutes
 ● 361-600minutes
 ● 600+minutes
 Binning simplifies the regression problem by reducing it to a classification problem where we predict
 which range of delays a flight falls into.
 5. Final Column Selection
 Selected Essential Columns: The following columns were selected as essential for model training:
 1. Type: Indicates the type of flight (e.g., domestic, international).
 2. Departure IATA Code: Represents the departure airport.
 3. Departure Terminal: Where the flight is departing from within the airport.
 4. Arrival IATA Code: Represents the destination airport.
 5. Airline Name, IATA, and ICAO Codes: The airline operating the flight.
 6. Flight Number: The unique flight identifier.
 7. Departure Time of Day: Time category based on the departure hour.
 8. Scheduled Flight Duration (Hours): The planned duration of the flight.
 9. DelayBin: The categorical delay outcome, which is the target variable.
 6. Label Encoding for Categorical Variables
Manyof the selected columns are categorical (e.g., type, departure_iataCode,
 airline_name). To makethese columns usable by machine learning algorithms, they were
 label-encoded. Label encoding assigns a unique integer to each category. This transformation ensures
 that categorical data can be interpreted numerically by machine learning models without introducing
 unnecessary bias.
 Categorical Columns Transformed:
 ● type
 ● departure_iataCode
 ● departure_terminal
 ● arrival_iataCode
 ● airline_name
 ● airline_iataCode
 ● airline_icaoCode
 ● departure_time_of_day
 ● delay_bin
 2. Modeling Approach
 Weutilized ensemble learning as a method to improve predictive accuracy by combining multiple
 models. The ensemble method is based on stacking, where predictions from different models are
 combined to create a final prediction through majority voting.
 The following models were implemented:
 1. Support Vector Machines (SVM):
 ○ Weusedamulti-kernel SVMmodelwith linear, polynomial, and RBF kernels.
 ○ Itworkedinaone-vs-one classification setup for multiple classes, voting between pairs
 of delay bins.
 2. Decision Tree:
 ○ Acustom-built decision tree classifier that split the data based on Gini impurity.
 ○ Itattempted to find the best feature-value split at each node of the tree and recursively
 split the data until a stopping criterion (e.g., max depth) was reached.
 3. Logistic Regression:
 ○ Amulti-class logistic regression model was trained using the one-vs-rest (OvR) method.
 Each classifier was trained for one class versus the rest.
 3. Evaluation Metrics and Model Performance
 Evaluation Metrics: We used two key metrics to evaluate the ensemble model:
 ● Accuracy: The proportion of correctly predicted samples.
● F1Score(weighted): A balance between precision and recall is especially useful for unbalanced
 datasets like flight delays.
 Model Performance
 Wefirst trained the models on the dataset, used them to make predictions, and then combined these
 predictions using majority voting.
 ● Initial Model Performance:
 ○ Accuracy: 52.13%
 ○ F1Score:41.43%
 ● Cross-Validation Results (K-Folds, n=5):
 ○ AverageAccuracy: 40.98%
 ○ AverageF1Score: 34.00%
 Although the ensemble method improved the prediction slightly, the models struggled with the
 imbalanced nature of the delay data. The overall accuracy and F1 scores indicate that while the model
 can make decent predictions, it struggles with more challenging delay categories. Further
 hyperparameter tuning, additional features, or alternative ensemble strategies could improve
 performance.