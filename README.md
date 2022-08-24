# QuantumVal


## Quantum approximate optimisation algorithms for real-world scenarios --> by Strangeworks

## Name of the team : QuantumVal
 
 
### Name of all the members : Abha Naik, Shivani  Rajput and Grishma Prasad


Abha Naik :
                  Discord Id: ABHANAIKIM(IST)#5826
                  
                  Github Id: https://github.com/AbhaSNK
                    
                  Email Id: abhanaikimq@gmail.com


Shivani Rajput :
                  Discord Id: Shivani Rajput#6848
                  
                  Github Id: https://github.com/ShivaniRajput11
                    
                  Email Id: shivanirajput1880@gmail.com


Grishma Prasad :  Discord Id: Grishma#4171
                     
                  Github Id: https://github.com/gprs1809
                   
                  Email Id: grishmaprs@gmail.com

Note: We had initially worked on a separate repo and then, we forked the challenge repo to just add our work. You can see each contribution here: https://github.com/AbhaSNK/QuantumVal--WomaniumHack2022

We thank Ayushi Dubal for her valuable insights and feedback.


## Introduction:

Problem : We explore QAOA with CVAR approach for portfolio optimization application using different number of assets, backends, classical optimizers, initial points and number of reps of the alternating problem and mixer Hamiltonians, and analyze the results.

We also referred to https://qiskit.org/textbook/ch-applications/qaoa.html for our understanding of QAOA. 

We have initially solved the problem classically using NumPyMinimumEigenSolver(). For solving it using QAOA, the problem is first mapped to a Quadratic program. Linear equality to penalty converter is used on the quadratic problem so that the constraints are taken into account in the objective, making the problem simpler. We convert it to an ising model post that and then fit CVaR QAOA to the problem. 


#### Data Extraction: 

We used Yahoo! Finance to get the historical data over 3 months of 10 Indian Stocks: 


MARUTI.NS , ONGC.NS, TATASTEEL.NS, HINDALCO.NS, ICICIBANK.NS, BRITANNIA.NS, ULTRACEMCO.NS, WIPRO.NS, APOLLOHOSP.NS, JUBLFOOD.NS


We used variance method to calculate individual stock risk and covariance to calculate the inter-related risk of all the stocks.
You can have an indepth look at the data in the attached excel sheet (FINANCEDATA 3 MON.xlsx)


#### Backends:
We started with the well-known cobyla optimizer and qasm simulator, followed by fake backends with noise models. The fake backends are built to mimic the behaviors of IBM Quantum systems using system snapshots. The system snapshots contain important information about the quantum system such as coupling map, basis gates, qubit properties (T1, T2, error rate, etc.) which are useful for testing the transpiler and performing noisy simulation of the system. We followed https://qiskit.org/documentation/apidoc/providers_fake_provider.html and https://qiskit.org/documentation/tutorials/simulators/2_device_noise_simulation.html for the purpose. 


### CVaR QAOA: 
The standard approach to calculating the expectation value of a Hamiltonian w.r.t. a state is to take the sample mean of the measurement outcomes. This corresponds to an estimator of the energy. However in several problem settings with a diagonal Hamiltonian, e.g. in combinatorial optimization where the Hamiltonian encodes a cost function, we are not interested in calculating the energy but in the lowest possible value we can find.

To this end, we might consider using the best observed sample as a cost function during variational optimization. The issue here, is that this can result in a non-smooth optimization surface. To resolve this issue, we can smooth the optimization surface by using not just the best observed sample, but instead average over some fraction of best observed samples. This is exactly what the CVaR estimator accomplishes. 
It is empirically shown, that this approach can lead to faster convergence for combinatorial optimization problems.
Let α be a real number in [0,1] which specifies the fraction of best observed samples which are used to compute the objective function. Observe that if α=1 CVaR is equivalent to a standard expectation value. Similarly, if α=0,then CVaR corresponds to using the best observed sample. Intermediate values of α interpolate between these two objective functions. For more reference : https://qiskit.org/documentation/optimization/tutorials/08_cvar_optimization.html. We use CVaR expectation for which we passed a list of alpha values. The alpha values we tried are [1.0, 0.5, 0.25, 0.3, 0.4, 0.45].

In summary, following are the variations we tried:
#### Optimizers :
We tried different classical Optimizers as different optimizers work differently and can affect cost with respect to different alpha values, optimizer time, number of iterations. The optimizers we tried are: COBYLA, CG, POWELL and gradient descent. 

### Alphas:
Alpha list: [1.0, 0.5, 0.25, 0.3, 0.4, 0.45]

### Platforms:
Simulated the result for 8 and 10 assets case on IBM Quantum Cloud and in our local environments. We noticed that it took significantly more time to execute on the cloud vs running locally. 

### different backends:
We tried to run it on Qasm simulator and fake backends i.e. Fake Mumbai, Fake Washington , Fake Toronto 

### different reps and initial points:
we also altered the number of reps to see if that causes any differences in optimal values and costs. We also tried checking if choosing different initial points makes a difference. 


### An important note: As we ran our experiments multiple times and individually, we noticed that the results and the conclusions can differ at times with different runs even though we set the seed value. Therefore, it didn't seem appropriate to make strict conclusions based on our results.


Our conclusions:

1. The Fake Backend Washington is not the best choice as it has large no of Qubits i.e.127 which is not require for our use case and was taking a relatively longer optimizer time to show the result.
2. For a particular case of Optimisers, it is showing different costs w.r.t alphas when trying on simulator and on fake backends. 
3. The cost for an alpha value decreases as the number of iterations for the alpha value decreases ,and that particular alpha would lead to faster convergence. This particular case would be considered as our optimal choice. There is no fixed alpha value that emerges to be best, in general.
4. Choosing smaller values for parameters in the initial points led to a faster convergence in our use case.
5. Adding more repetitions did not improve the optimal value, but increased the cost. So we concluded that for our use case, 1 rep was sufficient. However, increasing the number of reps can help in improving optimal function value in other real world use cases. The reason for this is that with more reps, one would have more parameters which would enable in learning the objective function or the cost function much better, but this is definitely not true in every case. 
6. We noticed that it took significantly more time to execute on the cloud vs running locally. 


#### Real World Application
We tried to compare our predictions for the optimal portfolio with real world results.
##### Methodology
We considered the time span between Closing of Indian Stock market on Aug 18 to closing on Aug 23. Found the returns as follows:


######  Returns = Adj Closing Price on Aug 23 - Adj Closing Price on Aug 18


9/10 stocks suffered a loss. All the stocks in the different portfolios chosen by our algorithms suffered a loss.

We selected some portfolios with different combinations of backends, optimizers and alpha values. Thus result can be seen below:

![portfolio returns](https://user-images.githubusercontent.com/94033568/186265528-4b7437ef-03d8-4ac3-af5f-8888060484b4.png)

As you can see for this particular case the portfolio created by Qasm simulator using Gradient descent and alpha = 0.25 outperformed others w.r.t minimum loss percentage.

#### Conclusion: Portfolio optimization is a complex task involving several factors. Thus, our conclusion from the real world data is just based on our observation for this much smaller optimization problem. Also, Qasm simulator is not a real quantum backend, which means that this is our conclusion based on an ideal quantum system, which we are still trying to create. We also noticed that for this small problem, running it classically was significantly faster than running it on qasm simulator or any other fake backend with noise. 
