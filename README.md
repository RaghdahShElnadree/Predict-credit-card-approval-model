# Predict-credit-card-approval-model

{
 "cells": [
  {
   "cell_type": "markdown",
   "source": "## 1. Credit card applications\n<p>Commercial banks receive <em>a lot</em> of applications for credit cards. Many of them get rejected for many reasons, like high loan balances, low income levels, or too many inquiries on an individual's credit report, for example. Manually analyzing these applications is mundane, error-prone, and time-consuming (and time is money!). Luckily, this task can be automated with the power of machine learning and pretty much every commercial bank does so nowadays. In this notebook, we will build an automatic credit card approval predictor using machine learning techniques, just like the real banks do.</p>\n<p><img src=\"https://assets.datacamp.com/production/project_558/img/credit_card.jpg\" alt=\"Credit card being held in hand\"></p>\n<p>We'll use the <a href=\"http://archive.ics.uci.edu/ml/datasets/credit+approval\">Credit Card Approval dataset</a> from the UCI Machine Learning Repository. The structure of this notebook is as follows:</p>\n<ul>\n<li>First, we will start off by loading and viewing the dataset.</li>\n<li>We will see that the dataset has a mixture of both numerical and non-numerical features, that it contains values from different ranges, plus that it contains a number of missing entries.</li>\n<li>We will have to preprocess the dataset to ensure the machine learning model we choose can make good predictions.</li>\n<li>After our data is in good shape, we will do some exploratory data analysis to build our intuitions.</li>\n<li>Finally, we will build a machine learning model that can predict if an individual's application for a credit card will be accepted.</li>\n</ul>\n<p>First, loading and viewing the dataset. We find that since this data is confidential, the contributor of the dataset has anonymized the feature names.</p>",
   "metadata": {
    "dc": {
     "key": "3"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import  pandas\nimport pandas as pd\n\n# ... YOUR CODE FOR TASK 1 ...\n\n# Load dataset\ncc_apps = pd.read_csv('datasets/cc_approvals.data', header= None )\n\n# Inspect data\ncc_apps.head()\n# ... YOUR CODE FOR TASK 1 ...",
   "execution_count": 2,
   "outputs": [
    {
     "output_type": "execute_result",
     "execution_count": 2,
     "data": {
      "text/plain": "  0      1      2  3  4  5  6     7  8  9   10 11 12     13   14 15\n0  b  30.83  0.000  u  g  w  v  1.25  t  t   1  f  g  00202    0  +\n1  a  58.67  4.460  u  g  q  h  3.04  t  t   6  f  g  00043  560  +\n2  a  24.50  0.500  u  g  q  h  1.50  t  f   0  f  g  00280  824  +\n3  b  27.83  1.540  u  g  w  v  3.75  t  t   5  t  g  00100    3  +\n4  b  20.17  5.625  u  g  w  v  1.71  t  f   0  f  s  00120    0  +",
      "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>0</th>\n      <th>1</th>\n      <th>2</th>\n      <th>3</th>\n      <th>4</th>\n      <th>5</th>\n      <th>6</th>\n      <th>7</th>\n      <th>8</th>\n      <th>9</th>\n      <th>10</th>\n      <th>11</th>\n      <th>12</th>\n      <th>13</th>\n      <th>14</th>\n      <th>15</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>b</td>\n      <td>30.83</td>\n      <td>0.000</td>\n      <td>u</td>\n      <td>g</td>\n      <td>w</td>\n      <td>v</td>\n      <td>1.25</td>\n      <td>t</td>\n      <td>t</td>\n      <td>1</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00202</td>\n      <td>0</td>\n      <td>+</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>a</td>\n      <td>58.67</td>\n      <td>4.460</td>\n      <td>u</td>\n      <td>g</td>\n      <td>q</td>\n      <td>h</td>\n      <td>3.04</td>\n      <td>t</td>\n      <td>t</td>\n      <td>6</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00043</td>\n      <td>560</td>\n      <td>+</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>a</td>\n      <td>24.50</td>\n      <td>0.500</td>\n      <td>u</td>\n      <td>g</td>\n      <td>q</td>\n      <td>h</td>\n      <td>1.50</td>\n      <td>t</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00280</td>\n      <td>824</td>\n      <td>+</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>b</td>\n      <td>27.83</td>\n      <td>1.540</td>\n      <td>u</td>\n      <td>g</td>\n      <td>w</td>\n      <td>v</td>\n      <td>3.75</td>\n      <td>t</td>\n      <td>t</td>\n      <td>5</td>\n      <td>t</td>\n      <td>g</td>\n      <td>00100</td>\n      <td>3</td>\n      <td>+</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>b</td>\n      <td>20.17</td>\n      <td>5.625</td>\n      <td>u</td>\n      <td>g</td>\n      <td>w</td>\n      <td>v</td>\n      <td>1.71</td>\n      <td>t</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>s</td>\n      <td>00120</td>\n      <td>0</td>\n      <td>+</td>\n    </tr>\n  </tbody>\n</table>\n</div>"
     },
     "metadata": {}
    }
   ],
   "metadata": {
    "dc": {
     "key": "3"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 2. Inspecting the applications\n<p>The output may appear a bit confusing at its first sight, but let's try to figure out the most important features of a credit card application. The features of this dataset have been anonymized to protect the privacy, but <a href=\"http://rstudio-pubs-static.s3.amazonaws.com/73039_9946de135c0a49daa7a0a9eda4a67a72.html\">this blog</a> gives us a pretty good overview of the probable features. The probable features in a typical credit card application are <code>Gender</code>, <code>Age</code>, <code>Debt</code>, <code>Married</code>, <code>BankCustomer</code>, <code>EducationLevel</code>, <code>Ethnicity</code>, <code>YearsEmployed</code>, <code>PriorDefault</code>, <code>Employed</code>, <code>CreditScore</code>, <code>DriversLicense</code>, <code>Citizen</code>, <code>ZipCode</code>, <code>Income</code> and finally the <code>ApprovalStatus</code>. This gives us a pretty good starting point, and we can map these features with respect to the columns in the output.   </p>\n<p>As we can see from our first glance at the data, the dataset has a mixture of numerical and non-numerical features. This can be fixed with some preprocessing, but before we do that, let's learn about the dataset a bit more to see if there are other dataset issues that need to be fixed.</p>",
   "metadata": {
    "dc": {
     "key": "10"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Print summary statistics\ncc_apps_description = cc_apps.describe()\nprint(cc_apps_description)\n\nprint('\\n')\n\n# Print DataFrame information\ncc_apps_info =  cc_apps.info()\nprint(cc_apps_info)\n\nprint('\\n')\n\n# Inspect missing values in the dataset\ncc_apps.tail(17)\n# ... YOUR CODE FOR TASK 2 ...",
   "execution_count": 4,
   "outputs": [
    {
     "output_type": "stream",
     "text": "               2           7          10             14\ncount  690.000000  690.000000  690.00000     690.000000\nmean     4.758725    2.223406    2.40000    1017.385507\nstd      4.978163    3.346513    4.86294    5210.102598\nmin      0.000000    0.000000    0.00000       0.000000\n25%      1.000000    0.165000    0.00000       0.000000\n50%      2.750000    1.000000    0.00000       5.000000\n75%      7.207500    2.625000    3.00000     395.500000\nmax     28.000000   28.500000   67.00000  100000.000000\n\n\n<class 'pandas.core.frame.DataFrame'>\nRangeIndex: 690 entries, 0 to 689\nData columns (total 16 columns):\n0     690 non-null object\n1     690 non-null object\n2     690 non-null float64\n3     690 non-null object\n4     690 non-null object\n5     690 non-null object\n6     690 non-null object\n7     690 non-null float64\n8     690 non-null object\n9     690 non-null object\n10    690 non-null int64\n11    690 non-null object\n12    690 non-null object\n13    690 non-null object\n14    690 non-null int64\n15    690 non-null object\ndtypes: float64(2), int64(2), object(12)\nmemory usage: 86.3+ KB\nNone\n\n\n",
     "name": "stdout"
    },
    {
     "output_type": "execute_result",
     "execution_count": 4,
     "data": {
      "text/plain": "    0      1       2  3  4   5   6      7  8  9   10 11 12     13   14 15\n673  ?  29.50   2.000  y  p   e   h  2.000  f  f   0  f  g  00256   17  -\n674  a  37.33   2.500  u  g   i   h  0.210  f  f   0  f  g  00260  246  -\n675  a  41.58   1.040  u  g  aa   v  0.665  f  f   0  f  g  00240  237  -\n676  a  30.58  10.665  u  g   q   h  0.085  f  t  12  t  g  00129    3  -\n677  b  19.42   7.250  u  g   m   v  0.040  f  t   1  f  g  00100    1  -\n678  a  17.92  10.210  u  g  ff  ff  0.000  f  f   0  f  g  00000   50  -\n679  a  20.08   1.250  u  g   c   v  0.000  f  f   0  f  g  00000    0  -\n680  b  19.50   0.290  u  g   k   v  0.290  f  f   0  f  g  00280  364  -\n681  b  27.83   1.000  y  p   d   h  3.000  f  f   0  f  g  00176  537  -\n682  b  17.08   3.290  u  g   i   v  0.335  f  f   0  t  g  00140    2  -\n683  b  36.42   0.750  y  p   d   v  0.585  f  f   0  f  g  00240    3  -\n684  b  40.58   3.290  u  g   m   v  3.500  f  f   0  t  s  00400    0  -\n685  b  21.08  10.085  y  p   e   h  1.250  f  f   0  f  g  00260    0  -\n686  a  22.67   0.750  u  g   c   v  2.000  f  t   2  t  g  00200  394  -\n687  a  25.25  13.500  y  p  ff  ff  2.000  f  t   1  t  g  00200    1  -\n688  b  17.92   0.205  u  g  aa   v  0.040  f  f   0  f  g  00280  750  -\n689  b  35.00   3.375  u  g   c   h  8.290  f  f   0  t  g  00000    0  -",
      "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>0</th>\n      <th>1</th>\n      <th>2</th>\n      <th>3</th>\n      <th>4</th>\n      <th>5</th>\n      <th>6</th>\n      <th>7</th>\n      <th>8</th>\n      <th>9</th>\n      <th>10</th>\n      <th>11</th>\n      <th>12</th>\n      <th>13</th>\n      <th>14</th>\n      <th>15</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>673</th>\n      <td>?</td>\n      <td>29.50</td>\n      <td>2.000</td>\n      <td>y</td>\n      <td>p</td>\n      <td>e</td>\n      <td>h</td>\n      <td>2.000</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00256</td>\n      <td>17</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>674</th>\n      <td>a</td>\n      <td>37.33</td>\n      <td>2.500</td>\n      <td>u</td>\n      <td>g</td>\n      <td>i</td>\n      <td>h</td>\n      <td>0.210</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00260</td>\n      <td>246</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>675</th>\n      <td>a</td>\n      <td>41.58</td>\n      <td>1.040</td>\n      <td>u</td>\n      <td>g</td>\n      <td>aa</td>\n      <td>v</td>\n      <td>0.665</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00240</td>\n      <td>237</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>676</th>\n      <td>a</td>\n      <td>30.58</td>\n      <td>10.665</td>\n      <td>u</td>\n      <td>g</td>\n      <td>q</td>\n      <td>h</td>\n      <td>0.085</td>\n      <td>f</td>\n      <td>t</td>\n      <td>12</td>\n      <td>t</td>\n      <td>g</td>\n      <td>00129</td>\n      <td>3</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>677</th>\n      <td>b</td>\n      <td>19.42</td>\n      <td>7.250</td>\n      <td>u</td>\n      <td>g</td>\n      <td>m</td>\n      <td>v</td>\n      <td>0.040</td>\n      <td>f</td>\n      <td>t</td>\n      <td>1</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00100</td>\n      <td>1</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>678</th>\n      <td>a</td>\n      <td>17.92</td>\n      <td>10.210</td>\n      <td>u</td>\n      <td>g</td>\n      <td>ff</td>\n      <td>ff</td>\n      <td>0.000</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00000</td>\n      <td>50</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>679</th>\n      <td>a</td>\n      <td>20.08</td>\n      <td>1.250</td>\n      <td>u</td>\n      <td>g</td>\n      <td>c</td>\n      <td>v</td>\n      <td>0.000</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00000</td>\n      <td>0</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>680</th>\n      <td>b</td>\n      <td>19.50</td>\n      <td>0.290</td>\n      <td>u</td>\n      <td>g</td>\n      <td>k</td>\n      <td>v</td>\n      <td>0.290</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00280</td>\n      <td>364</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>681</th>\n      <td>b</td>\n      <td>27.83</td>\n      <td>1.000</td>\n      <td>y</td>\n      <td>p</td>\n      <td>d</td>\n      <td>h</td>\n      <td>3.000</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00176</td>\n      <td>537</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>682</th>\n      <td>b</td>\n      <td>17.08</td>\n      <td>3.290</td>\n      <td>u</td>\n      <td>g</td>\n      <td>i</td>\n      <td>v</td>\n      <td>0.335</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>t</td>\n      <td>g</td>\n      <td>00140</td>\n      <td>2</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>683</th>\n      <td>b</td>\n      <td>36.42</td>\n      <td>0.750</td>\n      <td>y</td>\n      <td>p</td>\n      <td>d</td>\n      <td>v</td>\n      <td>0.585</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00240</td>\n      <td>3</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>684</th>\n      <td>b</td>\n      <td>40.58</td>\n      <td>3.290</td>\n      <td>u</td>\n      <td>g</td>\n      <td>m</td>\n      <td>v</td>\n      <td>3.500</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>t</td>\n      <td>s</td>\n      <td>00400</td>\n      <td>0</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>685</th>\n      <td>b</td>\n      <td>21.08</td>\n      <td>10.085</td>\n      <td>y</td>\n      <td>p</td>\n      <td>e</td>\n      <td>h</td>\n      <td>1.250</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00260</td>\n      <td>0</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>686</th>\n      <td>a</td>\n      <td>22.67</td>\n      <td>0.750</td>\n      <td>u</td>\n      <td>g</td>\n      <td>c</td>\n      <td>v</td>\n      <td>2.000</td>\n      <td>f</td>\n      <td>t</td>\n      <td>2</td>\n      <td>t</td>\n      <td>g</td>\n      <td>00200</td>\n      <td>394</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>687</th>\n      <td>a</td>\n      <td>25.25</td>\n      <td>13.500</td>\n      <td>y</td>\n      <td>p</td>\n      <td>ff</td>\n      <td>ff</td>\n      <td>2.000</td>\n      <td>f</td>\n      <td>t</td>\n      <td>1</td>\n      <td>t</td>\n      <td>g</td>\n      <td>00200</td>\n      <td>1</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>688</th>\n      <td>b</td>\n      <td>17.92</td>\n      <td>0.205</td>\n      <td>u</td>\n      <td>g</td>\n      <td>aa</td>\n      <td>v</td>\n      <td>0.040</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>f</td>\n      <td>g</td>\n      <td>00280</td>\n      <td>750</td>\n      <td>-</td>\n    </tr>\n    <tr>\n      <th>689</th>\n      <td>b</td>\n      <td>35.00</td>\n      <td>3.375</td>\n      <td>u</td>\n      <td>g</td>\n      <td>c</td>\n      <td>h</td>\n      <td>8.290</td>\n      <td>f</td>\n      <td>f</td>\n      <td>0</td>\n      <td>t</td>\n      <td>g</td>\n      <td>00000</td>\n      <td>0</td>\n      <td>-</td>\n    </tr>\n  </tbody>\n</table>\n</div>"
     },
     "metadata": {}
    }
   ],
   "metadata": {
    "dc": {
     "key": "10"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 3. Splitting the dataset into train and test sets\n<p>Now, we will split our data into train set and test set to prepare our data for two different phases of machine learning modeling: training and testing. Ideally, no information from the test data should be used to preprocess the training data or should be used to direct the training process of a machine learning model. Hence, we first split the data and then preprocess it.</p>\n<p>Also, features like <code>DriversLicense</code> and <code>ZipCode</code> are not as important as the other features in the dataset for predicting credit card approvals. To get a better sense, we can measure their <a href=\"https://realpython.com/numpy-scipy-pandas-correlation-python/\">statistical correlation</a> to the labels of the dataset. But this is out of scope for this project. We should drop them to design our machine learning model with the best set of features. In Data Science literature, this is often referred to as <em>feature selection</em>. </p>",
   "metadata": {
    "dc": {
     "key": "17"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import train_test_split\nfrom sklearn.model_selection import train_test_split\n# ... YOUR CODE FOR TASK 3 ...\n\n# Drop the features 11 and 13\ncc_apps = cc_apps.drop([11,13], axis=1)\n\n# Split into train and test sets\ncc_apps_train, cc_apps_test = train_test_split(cc_apps, test_size=0.33, random_state=42)",
   "execution_count": 6,
   "outputs": [],
   "metadata": {
    "dc": {
     "key": "17"
    },
    "tags": [
     "sample_code"
    ],
    "collapsed": true,
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 4. Handling the missing values (part i)\n<p>Now we've split our data, we can handle some of the issues we identified when inspecting the DataFrame, including:</p>\n<ul>\n<li>Our dataset contains both numeric and non-numeric data (specifically data that are of <code>float64</code>, <code>int64</code> and <code>object</code> types). Specifically, the features 2, 7, 10 and 14 contain numeric values (of types float64, float64, int64 and int64 respectively) and all the other features contain non-numeric values.</li>\n<li>The dataset also contains values from several ranges. Some features have a value range of 0 - 28, some have a range of 2 - 67, and some have a range of 1017 - 100000. Apart from these, we can get useful statistical information (like <code>mean</code>, <code>max</code>, and <code>min</code>) about the features that have numerical values. </li>\n<li>Finally, the dataset has missing values, which we'll take care of in this task. The missing values in the dataset are labeled with '?', which can be seen in the last cell's output of the second task.</li>\n</ul>\n<p>Now, let's temporarily replace these missing value question marks with NaN.</p>",
   "metadata": {
    "dc": {
     "key": "24"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import numpy\nimport numpy as np\n\n# ... YOUR CODE FOR TASK 4 ...\n\n# Replace the '?'s with NaN in the train and test sets\ncc_apps_train = cc_apps_train.replace('?', np.NaN)\ncc_apps_test = cc_apps_test.replace('?', np.NaN)",
   "execution_count": 8,
   "outputs": [],
   "metadata": {
    "dc": {
     "key": "24"
    },
    "tags": [
     "sample_code"
    ],
    "collapsed": true,
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 5. Handling the missing values (part ii)\n<p>We replaced all the question marks with NaNs. This is going to help us in the next missing value treatment that we are going to perform.</p>\n<p>An important question that gets raised here is <em>why are we giving so much importance to missing values</em>? Can't they be just ignored? Ignoring missing values can affect the performance of a machine learning model heavily. While ignoring the missing values our machine learning model may miss out on information about the dataset that may be useful for its training. Then, there are many models which cannot handle missing values implicitly such as Linear Discriminant Analysis (LDA). </p>\n<p>So, to avoid this problem, we are going to impute the missing values with a strategy called mean imputation.</p>",
   "metadata": {
    "dc": {
     "key": "31"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Impute the missing values with mean imputation\ncc_apps_train.fillna(cc_apps_train.mean(), inplace=True)\ncc_apps_test.fillna(cc_apps_train.mean(), inplace=True)\n\n# Count the number of NaNs in the datasets and print the counts to verify\n\nprint(cc_apps_train.isnull().sum())\nprint(cc_apps_test.isnull().sum())\n# ... YOUR CODE FOR TASK 5 ...",
   "execution_count": 10,
   "outputs": [
    {
     "output_type": "stream",
     "text": "0     8\n1     5\n2     0\n3     6\n4     6\n5     7\n6     7\n7     0\n8     0\n9     0\n10    0\n12    0\n14    0\n15    0\ndtype: int64\n0     4\n1     7\n2     0\n3     0\n4     0\n5     2\n6     2\n7     0\n8     0\n9     0\n10    0\n12    0\n14    0\n15    0\ndtype: int64\n",
     "name": "stdout"
    }
   ],
   "metadata": {
    "dc": {
     "key": "31"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 6. Handling the missing values (part iii)\n<p>We have successfully taken care of the missing values present in the numeric columns. There are still some missing values to be imputed for columns 0, 1, 3, 4, 5, 6 and 13. All of these columns contain non-numeric data and this is why the mean imputation strategy would not work here. This needs a different treatment. </p>\n<p>We are going to impute these missing values with the most frequent values as present in the respective columns. This is <a href=\"https://www.datacamp.com/community/tutorials/categorical-data\">good practice</a> when it comes to imputing missing values for categorical data in general.</p>",
   "metadata": {
    "dc": {
     "key": "38"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Iterate over each column of cc_apps_train\nfor col in cc_apps_train.columns:\n    # Check if the column is of object type\n    if cc_apps_train[col].dtypes == 'object':\n        # Impute with the most frequent value\n        cc_apps_train = cc_apps_train.fillna(cc_apps_train[col].value_counts().index[0])\n        cc_apps_test = cc_apps_test.fillna(cc_apps_train[col].value_counts().index[0])\n\n# Count the number of NaNs in the dataset and print the counts to verify\n\nprint(cc_apps_train.isnull().sum())\nprint(cc_apps_test.isnull().sum)\n# ... YOUR CODE FOR TASK 6 ...",
   "execution_count": 12,
   "outputs": [
    {
     "output_type": "stream",
     "text": "0     0\n1     0\n2     0\n3     0\n4     0\n5     0\n6     0\n7     0\n8     0\n9     0\n10    0\n12    0\n14    0\n15    0\ndtype: int64\n<bound method DataFrame.sum of         0      1      2      3      4      5      6      7      8      9   \\\n286  False  False  False  False  False  False  False  False  False  False   \n511  False  False  False  False  False  False  False  False  False  False   \n257  False  False  False  False  False  False  False  False  False  False   \n336  False  False  False  False  False  False  False  False  False  False   \n318  False  False  False  False  False  False  False  False  False  False   \n211  False  False  False  False  False  False  False  False  False  False   \n624  False  False  False  False  False  False  False  False  False  False   \n176  False  False  False  False  False  False  False  False  False  False   \n462  False  False  False  False  False  False  False  False  False  False   \n256  False  False  False  False  False  False  False  False  False  False   \n662  False  False  False  False  False  False  False  False  False  False   \n165  False  False  False  False  False  False  False  False  False  False   \n621  False  False  False  False  False  False  False  False  False  False   \n78   False  False  False  False  False  False  False  False  False  False   \n676  False  False  False  False  False  False  False  False  False  False   \n497  False  False  False  False  False  False  False  False  False  False   \n681  False  False  False  False  False  False  False  False  False  False   \n294  False  False  False  False  False  False  False  False  False  False   \n645  False  False  False  False  False  False  False  False  False  False   \n396  False  False  False  False  False  False  False  False  False  False   \n355  False  False  False  False  False  False  False  False  False  False   \n334  False  False  False  False  False  False  False  False  False  False   \n289  False  False  False  False  False  False  False  False  False  False   \n572  False  False  False  False  False  False  False  False  False  False   \n281  False  False  False  False  False  False  False  False  False  False   \n327  False  False  False  False  False  False  False  False  False  False   \n250  False  False  False  False  False  False  False  False  False  False   \n54   False  False  False  False  False  False  False  False  False  False   \n652  False  False  False  False  False  False  False  False  False  False   \n335  False  False  False  False  False  False  False  False  False  False   \n..     ...    ...    ...    ...    ...    ...    ...    ...    ...    ...   \n42   False  False  False  False  False  False  False  False  False  False   \n73   False  False  False  False  False  False  False  False  False  False   \n167  False  False  False  False  False  False  False  False  False  False   \n209  False  False  False  False  False  False  False  False  False  False   \n601  False  False  False  False  False  False  False  False  False  False   \n66   False  False  False  False  False  False  False  False  False  False   \n11   False  False  False  False  False  False  False  False  False  False   \n674  False  False  False  False  False  False  False  False  False  False   \n559  False  False  False  False  False  False  False  False  False  False   \n178  False  False  False  False  False  False  False  False  False  False   \n265  False  False  False  False  False  False  False  False  False  False   \n352  False  False  False  False  False  False  False  False  False  False   \n529  False  False  False  False  False  False  False  False  False  False   \n409  False  False  False  False  False  False  False  False  False  False   \n588  False  False  False  False  False  False  False  False  False  False   \n177  False  False  False  False  False  False  False  False  False  False   \n449  False  False  False  False  False  False  False  False  False  False   \n656  False  False  False  False  False  False  False  False  False  False   \n83   False  False  False  False  False  False  False  False  False  False   \n447  False  False  False  False  False  False  False  False  False  False   \n349  False  False  False  False  False  False  False  False  False  False   \n453  False  False  False  False  False  False  False  False  False  False   \n79   False  False  False  False  False  False  False  False  False  False   \n23   False  False  False  False  False  False  False  False  False  False   \n15   False  False  False  False  False  False  False  False  False  False   \n375  False  False  False  False  False  False  False  False  False  False   \n234  False  False  False  False  False  False  False  False  False  False   \n644  False  False  False  False  False  False  False  False  False  False   \n271  False  False  False  False  False  False  False  False  False  False   \n311  False  False  False  False  False  False  False  False  False  False   \n\n        10     12     14     15  \n286  False  False  False  False  \n511  False  False  False  False  \n257  False  False  False  False  \n336  False  False  False  False  \n318  False  False  False  False  \n211  False  False  False  False  \n624  False  False  False  False  \n176  False  False  False  False  \n462  False  False  False  False  \n256  False  False  False  False  \n662  False  False  False  False  \n165  False  False  False  False  \n621  False  False  False  False  \n78   False  False  False  False  \n676  False  False  False  False  \n497  False  False  False  False  \n681  False  False  False  False  \n294  False  False  False  False  \n645  False  False  False  False  \n396  False  False  False  False  \n355  False  False  False  False  \n334  False  False  False  False  \n289  False  False  False  False  \n572  False  False  False  False  \n281  False  False  False  False  \n327  False  False  False  False  \n250  False  False  False  False  \n54   False  False  False  False  \n652  False  False  False  False  \n335  False  False  False  False  \n..     ...    ...    ...    ...  \n42   False  False  False  False  \n73   False  False  False  False  \n167  False  False  False  False  \n209  False  False  False  False  \n601  False  False  False  False  \n66   False  False  False  False  \n11   False  False  False  False  \n674  False  False  False  False  \n559  False  False  False  False  \n178  False  False  False  False  \n265  False  False  False  False  \n352  False  False  False  False  \n529  False  False  False  False  \n409  False  False  False  False  \n588  False  False  False  False  \n177  False  False  False  False  \n449  False  False  False  False  \n656  False  False  False  False  \n83   False  False  False  False  \n447  False  False  False  False  \n349  False  False  False  False  \n453  False  False  False  False  \n79   False  False  False  False  \n23   False  False  False  False  \n15   False  False  False  False  \n375  False  False  False  False  \n234  False  False  False  False  \n644  False  False  False  False  \n271  False  False  False  False  \n311  False  False  False  False  \n\n[228 rows x 14 columns]>\n",
     "name": "stdout"
    }
   ],
   "metadata": {
    "dc": {
     "key": "38"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 7. Preprocessing the data (part i)\n<p>The missing values are now successfully handled.</p>\n<p>There is still some minor but essential data preprocessing needed before we proceed towards building our machine learning model. We are going to divide these remaining preprocessing steps into two main tasks:</p>\n<ol>\n<li>Convert the non-numeric data into numeric.</li>\n<li>Scale the feature values to a uniform range.</li>\n</ol>\n<p>First, we will be converting all the non-numeric values into numeric ones. We do this because not only it results in a faster computation but also many machine learning models (like XGBoost) (and especially the ones developed using scikit-learn) require the data to be in a strictly numeric format. We will do this by using the <code>get_dummies()</code> method from pandas.</p>",
   "metadata": {
    "dc": {
     "key": "45"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Convert the categorical features in the train and test sets independently\ncc_apps_train = pd.get_dummies(cc_apps_train)\ncc_apps_test = pd.get_dummies(cc_apps_test)\n\n# Reindex the columns of the test set aligning with the train set\ncc_apps_test = cc_apps_test.reindex(columns=cc_apps_train.columns, fill_value= 0)",
   "execution_count": 14,
   "outputs": [],
   "metadata": {
    "dc": {
     "key": "45"
    },
    "tags": [
     "sample_code"
    ],
    "collapsed": true,
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 8. Preprocessing the data (part ii)\n<p>Now, we are only left with one final preprocessing step of scaling before we can fit a machine learning model to the data. </p>\n<p>Now, let's try to understand what these scaled values mean in the real world. Let's use <code>CreditScore</code> as an example. The credit score of a person is their creditworthiness based on their credit history. The higher this number, the more financially trustworthy a person is considered to be. So, a <code>CreditScore</code> of 1 is the highest since we're rescaling all the values to the range of 0-1.</p>",
   "metadata": {
    "dc": {
     "key": "52"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import MinMaxScaler\nfrom sklearn.preprocessing import MinMaxScaler\n# ... YOUR CODE FOR TASK 8 ...\n\n# Segregate features and labels into separate variables\nX_train, y_train = cc_apps_train.iloc[:, :-1].values, cc_apps_train.iloc[:, [-1]].values\nX_test, y_test = cc_apps_test.iloc[:, :-1].values, cc_apps_test.iloc[:,[-1]].values\n\n# Instantiate MinMaxScaler and use it to rescale X_train and X_test\nscaler = MinMaxScaler(feature_range=(0,1))\nrescaledX_train = scaler.fit_transform(X_train)\nrescaledX_test = scaler.transform(X_test)",
   "execution_count": 17,
   "outputs": [],
   "metadata": {
    "dc": {
     "key": "52"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 9. Fitting a logistic regression model to the train set\n<p>Essentially, predicting if a credit card application will be approved or not is a <a href=\"https://en.wikipedia.org/wiki/Statistical_classification\">classification</a> task. According to UCI, our dataset contains more instances that correspond to \"Denied\" status than instances corresponding to \"Approved\" status. Specifically, out of 690 instances, there are 383 (55.5%) applications that got denied and 307 (44.5%) applications that got approved. </p>\n<p>This gives us a benchmark. A good machine learning model should be able to accurately predict the status of the applications with respect to these statistics.</p>\n<p>Which model should we pick? A question to ask is: <em>are the features that affect the credit card approval decision process correlated with each other?</em> Although we can measure correlation, that is outside the scope of this notebook, so we'll rely on our intuition that they indeed are correlated for now. Because of this correlation, we'll take advantage of the fact that generalized linear models perform well in these cases. Let's start our machine learning modeling with a Logistic Regression model (a generalized linear model).</p>",
   "metadata": {
    "dc": {
     "key": "59"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import LogisticRegression\nfrom sklearn.linear_model import LogisticRegression\n# ... YOUR CODE FOR TASK 9 ...\n\n# Instantiate a LogisticRegression classifier with default parameter values\nlogreg = LogisticRegression()\n\n# Fit logreg to the train set\\\nlogreg.fit(rescaledX_train, y_train)\n# ... YOUR CODE FOR TASK 9 ...",
   "execution_count": 18,
   "outputs": [
    {
     "output_type": "execute_result",
     "execution_count": 18,
     "data": {
      "text/plain": "LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,\n          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,\n          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,\n          verbose=0, warm_start=False)"
     },
     "metadata": {}
    }
   ],
   "metadata": {
    "dc": {
     "key": "59"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 10. Making predictions and evaluating performance\n<p>But how well does our model perform? </p>\n<p>We will now evaluate our model on the test set with respect to <a href=\"https://developers.google.com/machine-learning/crash-course/classification/accuracy\">classification accuracy</a>. But we will also take a look the model's <a href=\"http://www.dataschool.io/simple-guide-to-confusion-matrix-terminology/\">confusion matrix</a>. In the case of predicting credit card applications, it is important to see if our machine learning model is equally capable of predicting approved and denied status, in line with the frequency of these labels in our original dataset. If our model is not performing well in this aspect, then it might end up approving the application that should have been approved. The confusion matrix helps us to view our model's performance from these aspects.  </p>",
   "metadata": {
    "dc": {
     "key": "66"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import confusion_matrix\nfrom sklearn.metrics import confusion_matrix\n# ... YOUR CODE FOR TASK 10 ...\n\n# Use logreg to predict instances from the test set and store it\ny_pred = logreg.predict(rescaledX_test)\n\n# Get the accuracy score of logreg model and print it\nprint(\"Accuracy of logistic regression classifier: \", logreg.score(rescaledX_test, y_test))\n\n# Print the confusion matrix of the logreg model\nconfusion_matrix(y_test, y_pred)\n# ... YOUR CODE FOR TASK 10 ...",
   "execution_count": 19,
   "outputs": [
    {
     "output_type": "stream",
     "text": "Accuracy of logistic regression classifier:  1.0\n",
     "name": "stdout"
    },
    {
     "output_type": "execute_result",
     "execution_count": 19,
     "data": {
      "text/plain": "array([[103,   0],\n       [  0, 125]])"
     },
     "metadata": {}
    }
   ],
   "metadata": {
    "dc": {
     "key": "66"
    },
    "tags": [
     "sample_code"
    ],
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 11. Grid searching and making the model perform better\n<p>Our model was pretty good! In fact it was able to yield an accuracy score of 100%.</p>\n<p>For the confusion matrix, the first element of the of the first row of the confusion matrix denotes the true negatives meaning the number of negative instances (denied applications) predicted by the model correctly. And the last element of the second row of the confusion matrix denotes the true positives meaning the number of positive instances (approved applications) predicted by the model correctly.</p>\n<p>But if we hadn't got a perfect score what's to be done?. We can perform a <a href=\"https://machinelearningmastery.com/how-to-tune-algorithm-parameters-with-scikit-learn/\">grid search</a> of the model parameters to improve the model's ability to predict credit card approvals.</p>\n<p><a href=\"http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html\">scikit-learn's implementation of logistic regression</a> consists of different hyperparameters but we will grid search over the following two:</p>\n<ul>\n<li>tol</li>\n<li>max_iter</li>\n</ul>",
   "metadata": {
    "dc": {
     "key": "73"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Import GridSearchCV\nfrom sklearn.model_selection import GridSearchCV\n# ... YOUR CODE FOR TASK 11 ...\n\n# Define the grid of values for tol and max_iter\ntol = [0.01, 0.001, 0.0001]\nmax_iter = [100,150,200]\n\n# Create a dictionary where tol and max_iter are keys and the lists of their values are corresponding values\nparam_grid = dict(tol = tol, max_iter = max_iter)",
   "execution_count": 20,
   "outputs": [],
   "metadata": {
    "dc": {
     "key": "73"
    },
    "tags": [
     "sample_code"
    ],
    "collapsed": true,
    "trusted": true
   }
  },
  {
   "cell_type": "markdown",
   "source": "## 12. Finding the best performing model\n<p>We have defined the grid of hyperparameter values and converted them into a single dictionary format which <code>GridSearchCV()</code> expects as one of its parameters. Now, we will begin the grid search to see which values perform best.</p>\n<p>We will instantiate <code>GridSearchCV()</code> with our earlier <code>logreg</code> model with all the data we have. We will also instruct <code>GridSearchCV()</code> to perform a <a href=\"https://www.dataschool.io/machine-learning-with-scikit-learn/\">cross-validation</a> of five folds.</p>\n<p>We'll end the notebook by storing the best-achieved score and the respective best parameters.</p>\n<p>While building this credit card predictor, we tackled some of the most widely-known preprocessing steps such as <strong>scaling</strong>, <strong>label encoding</strong>, and <strong>missing value imputation</strong>. We finished with some <strong>machine learning</strong> to predict if a person's application for a credit card would get approved or not given some information about that person.</p>",
   "metadata": {
    "dc": {
     "key": "80"
    },
    "run_control": {
     "frozen": true
    },
    "tags": [
     "context"
    ]
   }
  },
  {
   "cell_type": "code",
   "source": "# Instantiate GridSearchCV with the required parameters\ngrid_model = GridSearchCV(estimator=logreg, param_grid = paran_grid, cv= 5 )\n\n# Fit grid_model to the data\ngrid_model_result = grid_model.fit(rescaledX_train, y_train)\n\n# Summarize results\nbest_score, best_params = grid_model_result.best_score_, grid_model_result.best_params_\nprint(\"Best: %f using %s\" % (best_score, best_params))\n\n# Extract the best model and evaluate it on the test set\nbest_model = \nprint(\"Accuracy of logistic regression classifier: \", ...)",
   "execution_count": null,
   "outputs": [],
   "metadata": {
    "dc": {
     "key": "80"
    },
    "tags": [
     "sample_code"
    ],
    "collapsed": true,
    "trusted": true
   }
  }
 ],
 "metadata": {
  "kernelspec": {
   "name": "python3",
   "display_name": "Python 3",
   "language": "python"
  },
  "language_info": {
   "name": "python",
   "version": "3.6.7",
   "mimetype": "text/x-python",
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "pygments_lexer": "ipython3",
   "nbconvert_exporter": "python",
   "file_extension": ".py"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}