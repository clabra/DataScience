Linear regression
=======================

We get sample data from an entity with info about loans: rate, amount funded, loan length and applicant's data as FICO range credit worthiness, income, number of credit lines open, etc. 

We wonder if we can get a linear model to calculate rate from the rest of data: ¿which variables take into account and what are the model coefficients?  

Let's start with a very simple model relating rate and FICO range and then we'll see what other variables add to the linear model: 

Interest.Rate = b0 + b1 FICO.Range

Linear model in R language: 
```
lmNoAdjust <- lm(loans$Interest.Rate ~ loans$FICO.Range)
```

For each variable we want to know if it's related we are going to paint residuals coloring each point according to this variable value's ranges. 
The key concept is: if you see non-random distribution of colors in residuals, it means this variable should be in the linear model. 

Transform rates percentages and FICO ranges into numeric values. 

In order to color points: in those variables which value is an interval set a numeric value indicating the interval. In R: 
```
loansRaw$Amount.Funded.By.Investors.Cut <- cut2(loansRaw$Amount.Funded.By.Investors, g=5)
loansRaw$Monthly.Income.Cut <- cut2(loansRaw$Monthly.Income, g=5)
```

Plots in R: 
```
plot(loans$FICO.Range,lmNoAdjust$residuals,col=loans$Amount.Funded.By.Investors.Cut,pch=19)
plot(loans$FICO.Range,lmNoAdjust$residuals,col=loans$Loan.Length,pch=19)
plot(loans$FICO.Range,lmNoAdjust$residuals,col=loans$Monthly.Income.Cut,pch=19)
```

We see non-random patterns for some variables as Loan.Length and Amount.Funded and random ones for the rest. So let's add former ones to model: 

InterestRate = b0 + b1 FICO.Range + f(AmountFunded) + h(LoanLength) + e

In R: 
```
lmFinal <- lm(loans$Interest.Rate ~ loans$FICO.Range + loans$Amount.Funded.Cut + loans$Loan.Length)
```

New plots: 

```
plot(loans$FICO.Range,lmFinal$residuals,col=loans$Loan.Length,pch=19)
```

show random color patterns. 


To get coefficients (intercept b0 and slopes b1...): 
```
summary(lmFinal)
```

To get confident intervals for coefficients: 
```
confint(lmFinal)
```
