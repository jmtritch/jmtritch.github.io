<div id="menu">
    <a href="#webapp">Web/App Development</a> |
    <a href="#eda">Exploratory Data Analysis</a> |
    <a href="#model">Modeling</a> |
    <a href="#functions">Custom Functions</a>
</div>

## Web/App Development <a name="web"></a>

- [FileMaker XML Sync](https://github.com/jmtritch/FileMaker_XML_Sync){:target="blank"} - Sync between offline FileMaker databases using XML.  I developed this to collect data in the [FileMaker Go](https://www.filemaker.com/products/filemaker-go/) app on iPads in remote locations and sync the updates with a master system.
- [FileMaker Edit Text Files](https://github.com/jmtritch/FileMaker_Edit_Text_Files){:target="blank"} - Allows for native editing of text files stored in containers in FileMaker.  I developed this as part of the XML Sync above in order to natively export an XML file from within [FileMaker Go](https://www.filemaker.com/products/filemaker-go/), which does not allow plugins.
- [FieldVIEW](https://data.forestspemba.org) - FieldVIEW (Field Visit Information for Evaluating Work) is currently being piloted and actively developed.  It is a scalable app for organizations to use for mobile monitoring and continuous evaluation.  The backend uses Laravel with an API interface and the front end uses Vue.js for the user interface.  Mobile data collection is done via a custom FileMaker Go app that can collect or update data offline to be synced with the backend when internet becomes available.  The system includes:
    - Registering users and assigning permissions to the users
    - Registering beneficiaries
    - Tracking project inputs (equipment and field visits to beneficiaries)
    - Tracking beneficiary results (outputs, outcomes, and impact) via an online dashboard
    - Enabling users to create unique surveys and conduct them with the beneficiaries
    - Associating inputs and results with projects via tags
    - Syncing offline data collection and updates when internet becomes available

## Exploratory Data Analysis (EDA) <a name="eda"></a>

- [Is there a relationship between U.S. state spending, enrollment, and examination results?](/eda/us_state_education)
    - Interaction terms
    - Cook's Distance and outliers
- [Working with missing data to estimate the total number of U.S. teachers by state](eda/impute_us_teachers)
    - Multiple data sources
    - Imputation of missing data via regression with perturbations
- [Is there a relationship between Tanzanian school performance on the Form IV National Exams and student access to textbooks and teachers?](eda/tz_stdnt_tchr_bks)
    - Multiple data sources
    - Interaction terms
    - Shapiro-Wilk test for normality
    - Multicollinearity

## Modeling <a name="model"></a>

- [Estimating U.S. examination results from spending per student](/modeling/us_state_education)
    - Simple Linear Regression
    - Evaluation of Goodness of Fit
        - Linearity / mean zero assumption
        - Constant variance assumption
        - Normality assumption
        - Independence assumption
    - Box-Cox Transformation
- [Estimating U.S. examination results from spending per student and average class size](/modeling/us_state_education_multi)
    - Multiple Linear Regression
- [Predicting credit card application approvals](modeling/credit_card_apps)
    - Support Vector Machine
    - K Nearest Neighbor
    - Training, validation, and testing sets
- [Clustering for flower petal dimensions](modeling/iris)
    - K-means Clustering
    - Elbow detection of a curve

### Custom Functions <a name="functions"></a>

#### R

- [`split_indices()`](functions/split_indices) - Helps quickly split a dataset into training, validation, and testing sets.
