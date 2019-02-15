<p align="center">[Web/App Development](#webapp) \| [Data Analysis](#data) \| [Exploratory](#eda) \| [Modeling](#model) \| [Custom Functions](#functions)</p>

## Web/App Development <a name="web"></a> <sup>[Top](#top)</sup>

- [FileMaker XML Sync](https://github.com/jmtritch/FileMaker_XML_Sync){:target="blank"} - Sync between offline FileMaker databases using XML.  I developed this to collect data in the [FileMaker Go](https://www.filemaker.com/products/filemaker-go/) app on iPads in remote locations and sync the updates with a master system.
- [FileMaker Edit Text Files](https://github.com/jmtritch/FileMaker_Edit_Text_Files){:target="blank"} - Allows for native editing of text files stored in containers in FileMaker.  I developed this as part of the XML Sync above in order to natively export an XML file from within [FileMaker Go](https://www.filemaker.com/products/filemaker-go/), which does not allow plugins.
- [Laravel/Vue.js Survey App](#) - Currently developing a scalable app for organizations to use for mobile monitoring and continuous evaluation.  The backend uses Laravel with an API inte)rface and the front end uses Vue.js for the user interface.  This includes:
    - Registering users and assigning permissions to the users
    - Registering beneficiaries
    - Tracking beneficiary inputs (equipment and field visits to beneficiaries)
    - Tracking beneficiary results (outputs, outcomes, and impact) via surveys
    - Enabling users to create unique surveys and conduct them with the beneficiaries
    - Associating inputs and results with projects via tags

## Data Analysis <a name="data"></a> <sup>[Top](#top)</sup>

### Exploratory Data Analysis (EDA) <a name="eda"></a> <sup>[Top](#top)</sup>

- [Is there a relationship between Tanzanian average school performance on the Form IV National Exams and student access to textbooks and teachers?](eda/tz_stdnt_tchr_bks)

### Modeling <a name="model"></a> <sup>[Top](#top)</sup>

#### Regression

- Simple Linear Regression
- Multiple Linear Regression
- Variable Selection (Stepwise Regression, Lasso, Elastic Net)
- Logistic Regression

#### Classification

- Support Vector Machine
- K-Nearest Neighbors

#### Clustering

- K-means

#### Change Detection

- CUSUM

#### Time Series

- Exponential Smoothing

#### Trees

- Regression Tree
- Random Forest

#### Simulation

- Airport

#### Optimization

- Budgeted Nutrition

### Custom Functions <a name="functions"></a> <sup>[Top](#top)</sup>

#### R

- [`split_indices()`](functions/split_indices) - Helps quickly split a dataset into training, validation, and testing sets.
