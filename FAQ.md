

    
### FAQ 1: Why not have the model persist the state?

Answer: Well, you can trivially turn any skater function into a callable that does that, should you wish: 

       class Predictor:
   
           def __init__(self,f):
                self.f = f
                self.s = s

           def __call__(self,y,k,a,t,e):
                x, x_std, self.s = self.f(y=y,s=self.s,k=k,a=a,t=t,e=e)
                return x, x_std

or write a decorator. Whatever, it's Python. 

Answer: The intent is to produce simple lambda-friendly models and,

- a *reasonable* way to map the most important hyper-parameter choices (we hope),
- that imposes some geometric discipline on the hyper-parameter space in the first place, and
- facilitates comparison of different ways to search hyper-parameters, across packages which have *entirely different conventions* and hyper-parameter spaces. 

### FAQ 2: Why not use the packages, like prophet, directly?

Answer: Maybe you should. Observe that this package wraps *some* functionality, not all by any means. You should use the original
packages for maximum flexibility. However, as noted, you *might* like this package if you want to be able to do this:

        s,k = {}, 3
        for yi,ai in zip(y,a[k:]): 
            xi, xi_std, s = f(y=yi,s=s,k=k,a=ai)

Notice what isn't here: 
 - Pandas dataframes
 - A long list of methods and properties 
 - Column naming conventions 
 - The customary 10-50 lines of setup code before a prediction can be made,
 - The customary need to trace into the code to infer intent, including which parameters are supposed to be supplied. 
 - Possible confusion between variables known in advance and those observed contemporaneously,
 - Possible confusion about prediction horizon,
 - Possible conflation of 3-step ahead prediction with the 1-step ahead prediction applied three times, 
 - Datetime manipulation, and conventions like '5min' which not everyone agrees on. 

There are also limitations of the skater approach. The simple data model in *y*, *a* is not well suited to problems where exogenous data comes and goes, and therefore cannot
easily be represented by a vector of fixed length (you might consider a dictionary interface instead, as with
the river package). 

### FAQ 3: Only Point Estimates and Confidence Intervals?  

Yes, the skater does not return a full distribution - unless you smuggle it into the state. 
However this package was motivated by the desire to create better free turnkey distributional forecasts, at [microprediction.org](https://www.microprediction.org), and you might infer that skaters returning two numbers per horizon might be useful 
as part of a chain of computations that eventually produces a distributional estimate. Skaters can be considered linear transforms
of incoming data, and part of the agenda here is figuring out how to judge skaters
in a manner that better reflects downstream use in distributional estimates. Here the theory of proper scoring rules doesn't really suffice, it would seem. End of aside.  
 
