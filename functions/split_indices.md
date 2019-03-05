
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
    trn_idc = sort(sample(1:nrows, round(nrows*trn_pct)))
    # Get the non-training indices
    non_trn_idc = setdiff(seq_len(nrows), trn_idc)
    # Get the validation indices
    val_idc = sort(sample(non_trn_idc, round(nrows*val_pct)))
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
<div class="output">
<pre>
Training indices:
1 2 3 6 7 8 9 10 11 13 14 15 17 18 19 20 22 23 24 25 26 29 30 31 32 37 38 39 41 43 44 46 47 48 49`
Testing indices:
4 5 12 16 21 27 28 33 34 35 36 40 42 45 50`
</pre>
</div>

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
<div class="output">
<pre>
Training indices:
2 6 7 10 12 13 14 15 17 19 21 22 23 25 26 27 29 30 32 34 36 37 38 39 40 43 44 46 48 49
Validation indices:
1 5 11 16 18 24 28 31 42 47
Testing indices:
3 4 8 9 20 33 35 41 45 50
</pre>
</div>

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
<div class="output">
<pre>
Combined Training and Validation indices:
1 2 5 6 7 10 11 12 13 14 15 16 17 18 19 21 22 23 24 25 26 27 28 29 30 31 32 34 36 37 38 39 40 42 43 44 46 47 48 49
</pre>
</div>
