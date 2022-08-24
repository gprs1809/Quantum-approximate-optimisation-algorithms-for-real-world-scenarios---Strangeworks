# QuantumVal


## Quantum approximate optimisation algorithms for real-world scenarios --> by Strangeworks

## Name of the team : QuantumVal
 
 
### Name of all the members : Grishma Prasad,Abha Naik & Shivani  Rajput


Grishma Prasad :  Discord Id: Grishma#4171
                     
                  Github Id: https://github.com/gprs1809
                   
                  Email Id: 


Abha Naik :
                  Discord Id: ABHANAIKIM(IST)#5826
                  
                  Github Id: https://github.com/AbhaSNK
                    
                  Email Id: abhanaikimq@gmail.com


Shivani Rajput :
                  Discord Id: Shivani Rajput#6848
                  
                  Github Id: https://github.com/ShivaniRajput11
                    
                  Email Id: shivanirajput1880@gmail.com






## Introduction:

Problem : We explore QAOA with CVAR approach for portfolio optimization application using different number of assets, backends and classical optimizers and analyze the results.


You can learn more about QAOA here: https://qiskit.org/textbook/ch-applications/qaoa.html

#### Data Extraction: 

We used Yahoo! Finance to get the historical data over 3 months of 10 Indian Stocks: 


MARUTI.NS , ONGC.NS, TATASTEEL.NS, HINDALCO.NS, ICICIBANK.NS, BRITANNIA.NS, ULTRACEMCO.NS, WIPRO.NS, APOLLOHOSP.NS, JUBLFOOD.NS


We used variance method to calculate individual stock risk and covariance to calculate the inter-related risk of all the stocks.
You can have an indepth look at the data in the attached excel sheet (FINANCEDATA 3 MON.xlsx)

#### Backends:


We have initially solved the problem classically using NumPyMinimumEigenSolver(). Linear equality to penalty converter is used on the quadratic problem so that the constraints are taken into account in the objective, making the problem simpler. Converting to an ising model post that.We are using cobyla optimizer and qasm simulator in this case and CVaR expectation and hence, creating a list of alpha values.

### Grishma 


#### Optimizers :
<u> Why use different Optimizers? </u>

Different Optimizers work differently and affect:

 alpha cost, optimizer cost, circuit cost and time.

1) IBM Quantum Cloud : Simulated the result for 8 and 10 assets case on IBM Quantum Cloud by increasing the no of alphas , changing different classical optimisers i.e  COBYLA ,CG POWELL,GradientDescent, and we tried to run it on Qasm simulator and fake backends i.e. Fake Mumbai, Fake Washington , Fake Toronto


Conclusion we have drawn from this is that :

1. The Fake Backend Washington was a poor choice as it has large no of Qubits i.e.127 which is not require for our usecases and was taking a long time to show the result.
2. For a particular case of Optimisers it is showing different alpha's cost when trying on simulator and on fake backends. 
3. No iteration for Alpha increases then cost of alpha will decrease ,and that particular Alpha will converges fast. This particular case was a better optimal choice 


2) Local System : Simulated the result for 8 and 10 assets on the local by increasing the no of alphas , changing different classical optimisers namely COBYLA ,CG POWELL and trying to run it on qasm simulator, fake backends i.e. Fake Mumbai, Fake Toronto.



#### Note: We have noticed that running the same problem on IBM Quantum Cloud on the Fake Backends i.e. Fake Mumbai, Fake Torronto was taking a greater time to show the result as compared to running it on our system locally.




#### CVAR and Alphas

The standard approach to calculating the expectation value of a Hamiltonian w.r.t. a state is to take the sample mean of the measurement outcomes. This corresponds to an estimator of the energy. However in several problem settings with a diagonal Hamiltonian, e.g. in combinatorial optimization where the Hamiltonian encodes a cost function, we are not interested in calculating the energy but in the lowest possible value we can find.

To this end, we might consider using the best observed sample as a cost function during variational optimization. The issue here, is that this can result in a non-smooth optimization surface. To resolve this issue, we can smooth the optimization surface by using not just the best observed sample, but instead average over some fraction of best observed samples. This is exactly what the CVaR estimator accomplishes.
It is empirically shown, that this can lead to faster convergence for combinatorial optimization problems.
Let α be a real number in [0,1] which specifies the fraction of best observed samples which are used to compute the objective function. Observe that if α=1 CVaR is equivalent to a standard expectation value. Similarly, if α=0,then CVaR corresponds to using the best observed sample. Intermediate values of α interpolate between these two objective functions.


For more reference : https://qiskit.org/documentation/optimization/tutorials/08_cvar_optimization.html

#### Change in number of ‘reps’: (Effect of changing the number of 'reps'.ipynb)
##### Why?

'reps' refers to the number of times we repeat the ansatz. In Qiskit 'reps' is set to 1 by default.In this notebook, we will work with the 10 asset scenario using the qasm_simulator.
Increasing the number of reps helps in better approximating optimal functional value, but not under all circumstances.

#### Real World Application
We tried to compare our predictions for the optimal portfolio with real world results.
##### Methodology
We considered the time span between Closing of Indian Stock market on Aug 18 to closing on Aug 23. Found the returns as follows:


######  Returns = Adj Closing Price on Aug 23 - Adj Closing Price on Aug 18


9/10 stocks suffered a loss. All the stocks in the different portfolios chosen by our algorithms suffered a loss.

We selected some portfolios with different combinations of backends, optimizers and alpha values. Thus result can be seen below:

![portfolio returns](https://user-images.githubusercontent.com/94033568/186265528-4b7437ef-03d8-4ac3-af5f-8888060484b4.png)

As you can see for this particular case the portfolio created by Qasm simulator using Gradient descent and alpha = 0.25 outperformed others w.r.t minimum loss percentage.

#### Conclusion:
