**Tai Dinh \- IDX Exchange, Week 10**

**Dataset Source and Test Split:**  
The main dataset is sourced from the CRMLS(California Regional Multiple Listing Service), that details the attributes of many properties that have been sold in California. ClosePrice is the target variable, where we are trying to predict the close price of specifically single family residential properties.   
I also used a dataset from the California Open Data Portal that details all school district area boundaries in California.

For the model, I used the last 12 months to make the training and validation sets. I used data from June 2025 to April 2026 as the training set. I wanted to account for a whole year’s worth of data with my model to account for seasonal patterns that may have happened in certain months. July 2026 was the most current month and served as the training set while May 2026 served as the validation set since it was the next most current month.

**Preprocessing:**  
I chose to drop properties with 0 or negative LivingArea, as this incorrect data may hurt the validity of the model.   
For numerical features with high variability like Living Area, Year Built, and LotSizeArea, I imputed missing features with the median as the dataset was very heavily right skewed.

For the numerical features including ParkingTotal, BathroomsTotalInteger, GarageSpaces, AssociationFee,  and FireplacesTotal, these were often integers and I chose to impute missing ones as 0, going off the assumption they weren’t filled out because they had none.

For boolean columns including AttachedGarageYN, PoolPrivateYN, ViewYN, WaterfrontYN, BasementYN, FireplaceYN, and NewConstructionYN, I imputed missing values with false for similar reasons.

I used a similar imputation with AssociationFeeFrequency but instead imputed “None” for missing values for similar logic. Missing data most likely did not have an association fee.

For the numerical features Stories, I imputed missing values with 1, going off the assumption that buildings by default had 1 story.

**Feature Engineering:**  
I then used the longitude and latitude of each property to assign each property its respective school district in California. Due to the high cardinality of this feature, I only took the most common 48 school districts and assigned other properties to either “None” or “Other” if they were in a lesser school district.

For categorical variables that I encoded, I used AssociationFeeFrequency, CountyOrParish, and DistrictName as well as adding a column called has\_elementary\_school that checked if a property had an elementary school. 

I also added several material columns for the flooring of the property, such as Wood, Laminate, Carpet etc. as these are key features that influence property value.

For additional features I added myself:  
I created a Bed/Bath Ratio column by dividing BedroomsTotal by BathroomsTotalInteger.   
I also created LivingAreaPerBedroom that was LivingArea divided by BedroomsTotal  
I also created LivingArea/LotSizeArea with the respective columns divided.  
I also added age which was how long ago the property was built from 2026\.

For all of these, I replaced missing values with 0\. These were all features that are commonly used in ML for real estate.

**Models and Best Results:**  
I used these features through models of linear regression, decision trees, random forests, and XGBoost. Before fitting models, I removed invalid properties with negative close prices and extremely high outliers over $600,000,000. It is important to account for high priced properties but over $600,000,000 was too extreme to be used. 

Since the data was right-skewed, I used a log transformation on ClosePrice to centralize the data for fitting. I initially used the models without the log transformation but found the log transformation reduced the MAPE and MDAPE as well as increasing r2. 

Random Forests were the best model metrics wise. MDAPE is the most important metric as it is not as affected by the high outliers in the set, and random forests had the lowest MDAPE at 0.149 and an r2 of 0.789. However, this comes at the cost of computational power and time. 

The next best model and preferred one was XGBoost at an MDAPE of 0.162 and r2 of 0.789. This accounted for the most log variance in the data at reasonable computational power and time cost.