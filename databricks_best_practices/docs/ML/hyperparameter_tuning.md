# Hyperparameter Tuning

1. Use Hyperopt Tree of Parzen Estimators (TPE) algorithm
    1. Using domain knowledge to restrict the search domain can optimize tuning and produce better results.
    2. Bayesian approaches can be much more efficient than grid search and random search. 
2. When you use hp.choice(), Hyperopt returns the index of the choice list. Therefore the parameter logged in MLflow is also the index. 
3. Use hyperopt.space_eval() to retrieve the parameter values.
4. Start experimenting with small datasets and many hyperparameters

    Use MLflow to identify the best performing models and determine which hyperparameters can be fixed.

5. [Take advantage of Hyperopt support for conditional dimensions and hyperparameters](http://hyperopt.github.io/hyperopt/getting-started/search_spaces/)
6. When using SparkTrials, configure parallelism appropriately for CPU-only versus GPU-enabled clusters
    1. CPU clusters use multiple executor threads per node. 
    2. GPU clusters use only one executor thread per node to avoid conflicts among multiple Spark tasks trying to use the same GPU.
7. Do not use SparkTrials on autoscaling clusters.
8. [Guidelines for using the Hyperopt class SparkTrials when working with datasets of different sizes](https://docs.databricks.com/_static/notebooks/hyperopt-spark-data.html)
9. Troubleshooting
    1. A reported loss of NaN (not a number) usually means the objective function passed to fmin() returned NaN. This does not affect other runs and you can safely ignore it. 
    2. Because Hyperopt uses stochastic search algorithms, the loss usually does not decrease monotonically with each run.
    3. Both Hyperopt and Spark incur overhead that can dominate the trial duration for short trial runs (low tens of seconds). The speedup you observe may be small or even zero.
