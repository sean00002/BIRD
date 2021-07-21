# multi-site-BIRD
## cmdStan: Multi-site BIRD model and its other derived models.
1. `multiple2.stan` 
    - The originla multi-site BIRD model 
2. `functions.stan` 
    - The functions for `multiple2.stan`
3. `multiple2_merge_function.stan`
    - the model merged both `multiple2.stan` and `functions.stan`. All derived models are based on this one in order to prevent bugs of cmdStan reading function block file. 
4. `multiple2_merge_function_new.stan`
    - Modification: Use `theta` as deterministic parameter instead of `q`
    ```
    transformed parameters {
        real<lower=0> theta[N_VARIANTS];
        for(j in 1:N_VARIANTS)
            theta[j] = (q[j]/p[j])/((1-q[j])/(1-p[j]));
    }
    ```
5. `multiple2_merge_function_new2.stan`
    - Modification: Use `p` as deterministic parameter instead of `q`
    ```
    transformed parameters { 
        real<lower=0,upper=1> p[N_VARIANTS];
        for(j in 1:N_VARIANTS)
            p[j]= q[j]/(theta[j]-theta[j]*q[j]+q[j]);
    }
    ```
6. `multiple2_merge_function_new3.stan`
    - Modification: Give `theta` a fixed prior. 
    ```
    model{
    ...
    theta[j] ~ normal(1,1);
    ...
    }
    ```
    
7. `multiple2_merge_function_normal3.stan`
    - Modification: Turn `qi`,`p` and `theta` into normal distribution through logit and log transformation
    ```
    parameters {
    ...
        real p_logit[N_VARIANTS];
        real qi_logit[N_VARIANTS,N_RNA];
        real theta_log[N_VARIANTS];
    ...
    }
    transformed parameters { // ORDER MATTERS!
        real<lower=0,upper=1> p[N_VARIANTS]; // untransformed p
        for(j in 1:N_VARIANTS)
            p[j]=inv_logit(p_logit[j]);
        real<lower=0,upper=1> qi[N_VARIANTS,N_RNA]; //untransformed qi
        for(j in 1:N_VARIANTS){
            for(i in 1:N_RNA){
                qi[j,i] = inv_logit(qi_logit[j,i]);
            }
        }
        real<lower=0> theta[N_VARIANTS];
        for(j in 1:N_VARIANTS){
            theta[j]=exp(theta_log[j]);
        ...
   }
   ```
8. `qi_q.stan`
    - BIRD model breakdown, to simply infer `q` from `qi`

## cmdStan: Other accessory directories 
1. `STANINPUTS`
    - To store all simulated inputs including the visible inputs and inputs for Stan. 
2. `STANOUTPUTS`
    - To store all Stan's outputs 
3. `Required_Python_Packages`
    - All __required__ customized packages for this project. 
4. `BIRD`
    - published single-site BIRD model
5. 



