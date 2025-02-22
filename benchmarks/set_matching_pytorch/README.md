# Set-to-set matching

## Set up

### Requirements
- Python 3.7.0+
- [set-matching-pytorch](https://github.com/tn1031/set-matching-pytorch)
- and its dependencies

### Install set-matching-pytorch

The packages required to run this benchmark are managed as extra packages.


```
$ pip install shift15m[pytorch]
```

Or, clone this repository and run poetry install with extras option.

```
$ git clone https://github.com/st-tech/zozo-shift15m.git
$ cd zozo-shift15m
$ poetry install -E pytorch
```

## Training

### Vanilla set matching model

You can train a set matching model indicating the label directory.
For example, to train a model from the training data collected in the year 2013 split as 'label1', you can use the code as follows:

```
$ python train.py -m set_matching_sim -b 32 -e 32 -i ../../data -l ../../data/set_matching/set_matching_labels/2013-2014-label1 -o /tmp/ml/set_matching/
```

Note that all the training data in the same label split are identical, so you can select any label directories regardless of the validation year.

Also, you can reduce the required memory size by setting minibatch-size -b to a small number.

### Weighted set matching model

To obtain a set matching model trained with the weighted loss, you need to train a weight estimator first and then the extended set matching model.

Also, setting training and testing years here are required for covariate adaptation. The following examples are adapting the data of the year 2013 to the year 2014.

#### Weight estimator

```
$ python train_we.py -b 128 -e 16 -i ../../data -l ../../data/set_matching/set_matching_labels/2013-2014-label1 -o /tmp/ml/weight_estimation
```

#### Weighted training on set matching model

You can select the weighting strategy `-m cov_max` or `-m cov_mean,` which represent max-IW or mean-IW, respectively.

To compare the results, we recommend using the same number of minibatch-size and training epochs as the vanilla set matching model.

```
$ python train_sm.py -m cov_max -b 32 -e 32 -i ../../data -l ../../data/set_matching/set_matching_labels/2013-2014-label1 -o /tmp/ml/set_matching/ -w /tmp/ml/weight_estimation/model.pt
```

## Testing

Using our test data, you can evaluate the trained model.
For example, the command for testing the *cov-max* weighted model on the set matching task, a.k.a. Fill-in-the-N-blank with four candidates in the covariate assumption for the years from 2013 to 2014, is:

```
$ python test.py -i ../../data/set_matching_test_data/test_ncand4/2013-2014-label1 -d /tmp/ml/set_matching/
```

# Remarks

The set matching modules on this repository use the [OSS](https://github.com/soskek/attention_is_all_you_need), which is distributed under the [BSD 3-Clause License](networks/LICENSE).

