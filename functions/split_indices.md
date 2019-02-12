
# split_indices

This function helps to easily split your dataset into training, validation and testing sets.

```R
# Purpose: Splits the indices of a dataset into training, validation, and
#          testing sets
# Parameters:
#     nrows - Number of rows in the full dataset
#     trn_pct - Percent of the dataset to use for training
#     val_pct - Percent of the/' dataset to use for validation
#     seed - Value to use as a seed for the random sampling
# Returns: A list containing
#     train - Training dataset indices
#     valid - Validation dataset indices
#     test  - Testing dataset indices
split_indices = function(nrows, trn_pct, val_pct=0.0, seed=NULL) {
    # Set the seed
    if(!is.null(seed)) {
        set.seed(seed)
    }
    # Get the training indices
    trn_idc = sort(sample(1:nrows, floor(nrows*trn_pct)))
    # Get the non-training indices
    non_trn_idc = setdiff(seq_len(nrows), trn_idc)
    # Get the validation indices
    val_idc = sort(sample(non_trn_idc, floor(nrows*val_pct)))
    # Get the remaining testing indices
    tst_idc = setdiff(non_trn_idc, val_idc)
    # Return the list of sets
    return( list('train'=trn_idc,
                 'valid'=val_idc,
                 'trval'=sort(c(trn_idc, val_idc)),
                 'test'= tst_idc) )
}
```

## Usage

```R
split_indices(nrows, trn_pct, val_pct=0.0, seed=NULL)
```

## Examples

Let's create some dummy data first.


```R
# Set the total number of rows for the dummy data frame
nrows = 50
# Create the dummy data frame
dummy_df = data.frame(a=sample(1:600,nrows,replace=TRUE),
                      b=sample(1:600,nrows,replace=TRUE),
                      c=sample(1:600,nrows,replace=TRUE),
                      d=sample(1:600,nrows,replace=TRUE))
# Show the first three records of the dummy data frame
head(dummy_df, 3)
```


<table>
<thead><tr><th>a</th><th>b</th><th>c</th><th>d</th></tr></thead>
<tbody>
	<tr><td>424</td><td>467</td><td>589</td><td>503</td></tr>
	<tr><td>466</td><td>  1</td><td>188</td><td> 79</td></tr>
	<tr><td> 95</td><td>509</td><td>390</td><td>114</td></tr>
</tbody>
</table>



### Split into Training and Testing Sets

To split a dataset into training and testing sets, you only specify the total number of rows in the data frame and the training percent.

The testing percent is calculated as the remaining percent.


```R
# Split 70% training 30% testing
idc = split_indices(nrows=nrows, trn_pct=0.7)

# Split the dataset into training and testing
train = dummy_df[idc$train,]
test = dummy_df[idc$test,]

# Show the indices split
cat('Training indices:')
idc$train
cat('Testing indices:')
idc$test
```

    Training indices:


<ol>
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>6</li>
	<li>7</li>
	<li>8</li>
	<li>9</li>
	<li>10</li>
	<li>11</li>
	<li>13</li>
	<li>14</li>
	<li>15</li>
	<li>17</li>
	<li>18</li>
	<li>19</li>
	<li>20</li>
	<li>22</li>
	<li>23</li>
	<li>24</li>
	<li>25</li>
	<li>26</li>
	<li>29</li>
	<li>30</li>
	<li>31</li>
	<li>32</li>
	<li>37</li>
	<li>38</li>
	<li>39</li>
	<li>41</li>
	<li>43</li>
	<li>44</li>
	<li>46</li>
	<li>47</li>
	<li>48</li>
	<li>49</li>
</ol>



    Testing indices:


<ol class=list-inline>
	<li>4</li>
	<li>5</li>
	<li>12</li>
	<li>16</li>
	<li>21</li>
	<li>27</li>
	<li>28</li>
	<li>33</li>
	<li>34</li>
	<li>35</li>
	<li>36</li>
	<li>40</li>
	<li>42</li>
	<li>45</li>
	<li>50</li>
</ol>



### Split into Training, Validation, and Testing Sets

To split a dataset into training, validation, and testing sets, you specify the total number of rows in the data frame, the training percent, and the validation percent.

The testing percent is calculated as the remaining percent.


```R
# Split 60% training 40% testing
idc = split_indices(nrows=nrows, trn_pct=0.6, val_pct=0.2)

# Split the dataset into training, validation, and testing
train = dummy_df[idc$train,]
valid = dummy_df[idc$valid,]
test = dummy_df[idc$test,]

# Show the indices split
cat('Training indices:')
idc$train
cat('Validation indices:')
idc$valid
cat('Testing indices:')
idc$test
```

    Training indices:


<ol class=list-inline>
	<li>2</li>
	<li>6</li>
	<li>7</li>
	<li>10</li>
	<li>12</li>
	<li>13</li>
	<li>14</li>
	<li>15</li>
	<li>17</li>
	<li>19</li>
	<li>21</li>
	<li>22</li>
	<li>23</li>
	<li>25</li>
	<li>26</li>
	<li>27</li>
	<li>29</li>
	<li>30</li>
	<li>32</li>
	<li>34</li>
	<li>36</li>
	<li>37</li>
	<li>38</li>
	<li>39</li>
	<li>40</li>
	<li>43</li>
	<li>44</li>
	<li>46</li>
	<li>48</li>
	<li>49</li>
</ol>



    Validation indices:


<ol class=list-inline>
	<li>1</li>
	<li>5</li>
	<li>11</li>
	<li>16</li>
	<li>18</li>
	<li>24</li>
	<li>28</li>
	<li>31</li>
	<li>42</li>
	<li>47</li>
</ol>



    Testing indices:


<ol class=list-inline>
	<li>3</li>
	<li>4</li>
	<li>8</li>
	<li>9</li>
	<li>20</li>
	<li>33</li>
	<li>35</li>
	<li>41</li>
	<li>45</li>
	<li>50</li>
</ol>



### Combine Training and Validation

Often times, after selecting the optimum model/parameters, you want to retrain the optimum model using the entire training and validation datasets.  Then you test it against the untouched test set to see how well it generalizes to completely new data.

It is easy to recombine the training and validation datasets as one.


```R
# Combine the training and validation indices
idc_trval = sort(c(idc$train, idc$valid))

# Combine the training and validation datasets
trval = dummy_df[idc_trval,]

# Show the indices split
cat('Combined Training and Validation indices:')
idc_trval
```

    Combined Training and Validation indices:


<ol class=list-inline>
	<li>1</li>
	<li>2</li>
	<li>5</li>
	<li>6</li>
	<li>7</li>
	<li>10</li>
	<li>11</li>
	<li>12</li>
	<li>13</li>
	<li>14</li>
	<li>15</li>
	<li>16</li>
	<li>17</li>
	<li>18</li>
	<li>19</li>
	<li>21</li>
	<li>22</li>
	<li>23</li>
	<li>24</li>
	<li>25</li>
	<li>26</li>
	<li>27</li>
	<li>28</li>
	<li>29</li>
	<li>30</li>
	<li>31</li>
	<li>32</li>
	<li>34</li>
	<li>36</li>
	<li>37</li>
	<li>38</li>
	<li>39</li>
	<li>40</li>
	<li>42</li>
	<li>43</li>
	<li>44</li>
	<li>46</li>
	<li>47</li>
	<li>48</li>
	<li>49</li>
</ol>
