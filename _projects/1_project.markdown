---
layout: page
title: Natural Gas Storage Valuation
description: Three complementary modules to properly valuate a natural gas storage facility.
img: /assets/img/e_cube.jpg
importance: 4
category: work
github: https://github.com/SebastianPartarrieu/mines_strat_gaz_nat
---

This [project](https://github.com/SebastianPartarrieu/mines_strat_gaz_nat) was carried out during a computer science course within the Mines ParisTech Cycle Ingénieur Civil and in collaboration with [E_Cube](https://www.linkedin.com/company/e-cube-strategy-consultants/) (a consultancy specialized in the energy sector) along with two other students of Mines ParisTech.

## Problem statement
Natural gas distribution infrastructures aren't able to service the whole of France's territory on a moment's notice. Thus, adequate storage facilities are crucial in order to ensure the seasonal fuel's availability during the entire year and across France. These storage facilities are often large underground reservoirs (aquifers or salt caverns) and can be purchased by independent entities -during a state-sponsored auction- looking to buy, store and resell natural gas.

This so-called trader has to comply with a number of external constraints, both physical and political. For example, physical constraints on the undergound reservoirs include having to maintain a certain quantity of fuel inside or else it might shrink. Another is the maximal input and output rates of gas from the storage facility. More political in nature, regulation forces the trader to have a large propotion of his reservoir filled at the beginning of winter and more generally dictates minimal and maximal quantities the trader is allowed to store at different moments during the year.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/nat_gas_workflow.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    Natural gas from production to consumption
</div>

Taking all of this into account, it is crucial for the natural gas trader to have access to a trustworthy valuation for the different storage facilities when the state-sponsored auction takes place. This is where this project comes in.

## Solution

This project proposes an end-to-end solution to valuate a certain storage facility's worth. Three main modules were developped in python to tackle this complex problem, the first being a data scrapping module (use of selenium) to go and gather crucial data regarding the French (and other European) natural gas market data. The second computes a number of price diffusion scenarios using the scraped intrinsic market characteristics along with quantitative models specific to commodities markets (heavy use of numpy, scipy and pandas). The third uses the price diffusions to solve the non-linear (and therefore non trivial!) optimisation problem of finding an optimal buying/selling strategy given all the physical and political constraints mentioned above (again, numpy scipy and pandas). Once the multiple optimisations have been solved along the price diffusion scenarios, we can compute useful statistics on the distribution of possible profits generated from the natural gas storage facility. This allows the trader to make an informed decision on what profits he can expect and hence, whether or not to bid for a specific reservoir during the auction.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/project_workflow.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    Project workflow
</div>

We also built a relatively simple web app (that you can access locally once you've cloned the github) to view the results, here is what some of the views look like: 
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/spot_forward_prices.png' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/diffusion_prix.png' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/profits_gen.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    On the left, historical spot and forward prices scraped from the internet using our first module. In the middle, a single price diffusion scenario and the corresponding optimal volume of gas inside the reservoir to maximize revenue within the set of constraints. Finally, on the right, the profit distribution generated from the multiple optimisations conducted on the large number of price diffusions generated.
</div>

For those of you that have the time and want a more in-depth explanation of how all of this works, go see our github repository's [wiki](https://github.com/paplessix/mines_strat_gaz_nat/wiki). Details of the actual algorithms used, as well as an installation guide and a few tips are all available there!
