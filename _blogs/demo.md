---
title: "Demo"
date: 2018-11-18T12:33:46+10:00
featured: true
order: 1
layout: service
---

## Packages needed to use this code (use with Python 3.x)


```python
import numpy as np
import matplotlib.pyplot as plt
import math
import pandas as pd
from scipy import stats
from tqdm import tqdm
```

## `draws` function takes simulation parameters as input and returns number of occurances for the cutoff value.

### Simulation parameters

*runs* : number of montecarlo draws.  
*N* : total population.  
*p* : probability that a person has antibodies.  
*ts* : probability that if a person realy has antibodies then the test gives a positive result.  
*tr* : probability that if the person has no antibodies then the test gives a positive result.  
*rv* : number of occurences of cutoff value in resulting distribution.  


```python
def draws(runs,N,p,ts,tr,cutoff):
    draw_1 = np.random.binomial(N,p,runs)
    draw_false_1 = N - draw_1

    realised_p = draw_1/N

    draw_2_1=list()
    for d_1 in draw_1:
        new_dist=np.random.binomial(d_1,ts,runs)
        for ele in new_dist:
            draw_2_1.append(ele)
            #progress.update(1)

    draw_2_false = list()
    for d_false_1 in draw_false_1:
        new_dist = np.random.binomial(d_false_1,tr,runs)
        for ele in new_dist:
            draw_2_false.append(ele)
            #progress.update(1)

    final_positive = np.asarray(draw_2_false) + np.asarray(draw_2_1)

    w = 1 #parameter to change class width
    n = math.ceil((max(final_positive) - min(final_positive))/w)

    hist = np.histogram(final_positive, bins = n)
    start = cutoff-0.05*cutoff
    end = cutoff+0.05*cutoff
    s=0
    rv=0
    for i in range(len(hist[0])):
        if hist[1][i]>=start and hist[1][i]<=end:
            s+=hist[0][i]
            if hist[1][i]==cutoff:
                rv=hist[0][i]
                #print(hist[0][i])
    #print("Probability that Antibody positive people is between %f and %f: %f"%(start,end,s/len(final_positive)))

    return rv
```

## `plot_boundary` function plots the number of occurances of cutoff value for each base rate and calculates upper and lower boundaries on the basis of values provided.


```python
def plot_boundary(runs,ps,N,hits,left,right,cutoff):
    boundry_left = 0
    for i in range(0,len(hits)):
        boundry_left+=hits[i]
        if boundry_left>=left*sum(hits):
            break
    print('Left Boundary: %f'%(ps[i]))
    boundry_left=ps[i]

    boundry_right = 0
    for j in range(0,len(hits)):
        boundry_right+=hits[j]
        if boundry_right>=right*sum(hits):
            break
    print('Right Boundary: %f'%(ps[j]))
    boundry_right=ps[j]

    med = 0
    for k in range(0,len(hits)):
        med+=hits[k]
        if med>=0.5*sum(hits):
            break
    print('Median: %f'%(ps[k]))

    med=ps[k]

    z = hits/sum(hits)

    plt.plot(ps,z)
    plt.xlabel('Value of $p$')
    plt.ylabel('Probability of %ds'%(cutoff))
    plt.axvline(med,color='r',linestyle='--',label='Median')
    plt.axvline(boundry_left,linestyle='--',marker="1",label='$L_{0.025}$')
    plt.axvline(boundry_right,linestyle=':',marker = "x",label='$U_{0.975}$')
    plt.title('N = %d'%(N))
    plt.legend()
    plt.tight_layout()
    plt.savefig('Values of p runs_%d N_%d cutoff_%d.png'%(runs,N,cutoff),dpi=500)
    plt.show()
    plt.close()

    return boundry_left,boundry_right,med
```

## `main` function accepts all parameter values and can be used to execute the analysis.  
**You may change the simulation parameters here**  
*runs\_pass* : number of montecarlo draws.  
*N\_pass* : total population.  
*p\_pass* : probability that a person has antibodies.  
*ts\_pass* : probability that if a person realy has antibodies then the test gives a positive result.  
*tr\_pass* : probability that if the person has no antibodies then the test gives a positive result.  
*cutoff\_pass* : number of people who are expected to have antibodies based on base rate.  
*p\_pass* : staring base rate.  
*p\_step* : change in base rate for the next simulation.  
*p\_limit* : ending base rate.  
*left\_pass* : left boundary setting.  
*right\_pass* : right boundary setting.  


```python
def main():
    runs_pass =1000
    N_pass = 10000
    ts_pass = 0.75 #probability of test success
    tr_pass = 0.05
    cutoff_pass = 640
    hits_pass = list()
    p_pass = 0.00
    p_limit = 0.04
    p_step = 0.001
    ps_pass=list()
    left_pass = 0.025
    right_pass = 0.975
    progress = tqdm(total=((p_limit-p_pass)/p_step))
    while p_pass<=p_limit:
        p_pass = p_pass + p_step
        hits_pass.append(draws(runs_pass,N_pass,p_pass,ts_pass,tr_pass,cutoff_pass))
        ps_pass.append(p_pass)
        progress.update(1)
    left_boundary,right_boundary,median = plot_boundary(runs_pass,ps_pass,N_pass,hits_pass,left_pass,right_pass,cutoff_pass)

if __name__ == "__main__":
    main()
```

    100%|██████████| 40/40.0 [00:29<00:00,  1.39it/s]

    Left Boundary: 0.013000
    Right Boundary: 0.027000
    Median: 0.020000

## Example plot for default simulation parameters.

![png](output_7_2.png)
