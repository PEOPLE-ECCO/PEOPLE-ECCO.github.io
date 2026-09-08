# Before-After-Control-Impact Assessment

Before-After-Control-Impact (BACI) assessment of conservation actions effectiveness falls under the category of counterfactual analysis. Counterfactual analysis in the context of conservation effectiveness monitoring attempts to establish the difference between the (intended or unintended) outcomes of a conservation action and the outcomes if no action had been taken (Coetzee & Gaston, 2021). Such analysis may support the monitoring, evaluation and learning of conservations efforts, as well as the identification of control site for in situ monitoring.

## Need for counterfactual impact assessment

To fully understand the impact of conservation actions, it is not sufficient to monitor change at the affected area, but one must also assess what change would have happened if the conservation intervention had not occurred (Coetzee & Gaston, 2021; Wauchope et al., 2021). 
In other fields, this could be achieved through an experimental setup with random assignment of treatment and control groups, but this is typically not feasible or desirable in the case of conservation actions or other environmental impacts. 
In such cases, the use of counterfactuals provides the most robust way to assess conservation action effectiveness (Ribas et al., 2020). 
While the counterfactual state can be defined in time (before-after, BA) or in space (control-impact, CI), these two approaches are only valid under a set of assumptions. These assumptions are addressed when combining the spatial and temporal components in a before-after-control-impact (BACI) design (Wauchope et al., 2021), which is therefore the most appropriate evaluation option when data allows.

Applying a counterfactual impact assessment consists of two main steps: 1) identification and matching of control and impact units, and 2) evaluating impact based on one or more variables representing impact.

## Identification and matching of control and impact units

Various methods to pair control units with treatment/impact units have been applied in studies of conservation action effectiveness (Ribas et al., 2020, 2021). 
The simplest way is to randomly select one or more control units per impact unit, with or without restriction to a buffer zone around the impact unit. 
However, such “naïve” matching ignores the non-random selection of conservation action across the landscape, e.g., in remote areas or places unsuitable for other economic activities (Jones & Lewis, 2015). 
Analysis based on random selection can therefore substantially overestimate the impact of conservation actions (Andam et al., 2008; Ribas et al., 2020). 
Statistical matching analysis is a way to overcome the non-random assignment of conservation actions, and consist of three main steps (Ribas et al., 2021a; Schleicher et al., 2020): defining treatment and control units, selecting matching covariates and matching approach, and assessing the quality of matching.


In the case of matching analysis for environmental impact assessment, the treatment and control units can be defined as polygon or point locations, or as the (remotely sensed) pixel. 
Vector units of analysis (polygon or point) have typically been used to represent in situ monitoring sites inside and outside conservation actions, or intervention sites and similar control sites (Jones & Lewis, 2015; Meroni et al., 2017a; Terraube et al., 2020; van der Vliet et al., 2024; Wauchope et al., 2022). 
Raster units of analysis offer, combined with remotely sensed change variables, the flexibility to compare pixels within treatment areas to a potentially large number of control pixels (del Río-Mena et al., 2021; Eklund et al., 2016; Jones & Lewis, 2015).
The trade-off analysis for the spatial unit of assessment performed by the PEOPLE-ECCO project identified a higher flexibility, lower computational requirements, and higher interpretability when using vector units versus pixel units (see PEOPLE-ECCO Algorithm Theoretical Baseline, D3.2). 
Thanks to the flexibility of polygon/point units of analysis, they can be pre-defined to mimic the pixel units of analysis, if desired.

Matching covariates should represent all processes likely to impact both the selection for conservation actions and the outcome of interest (Schleicher et al., 2020). 
Commonly used matching covariates include distances to towns, roads, or river networks (Andam et al., 2008; Eklund et al., 2016; Jones & Lewis, 2015; Terraube et al., 2020), elevation and derived terrain parameters (Eklund et al., 2016; Jones & Lewis, 2015; Terraube et al., 2020; Wauchope et al., 2022b), climate (Wauchope et al., 2022b), land use or land cover descriptors (del Río-Mena et al., 2021; Eklund et al., 2016; Meroni et al., 2017a; Wauchope et al., 2022b), population density and demographic parameters (Andam et al., 2008; Terraube et al., 2020; Wauchope et al., 2022), and the size of intervention sites (Andam et al., 2008; Jones & Lewis, 2015). 
Depending on the nature of the study system, specific matching covariates may be appropriate, e.g., water availability to estimate conservation effectiveness for waterbirds (Wauchope et al., 2022b). Expert knowledge on the study system is therefore required of the end-user to select the appropriate matching covariates. In case of doubt whether a covariate is relevant to matching, it is generally advised to err on the side of caution and include the covariate (Schleicher et al., 2020). Multicollinearity in matching covariates is generally considered of minor importance in control-impact matching (McMurry et al., 2015).

During the control-impact matching, users have to specify a set of matching specification including the distance metric (e.g., propensity score, Mahalanobis distance, exact distance), matching algorithm (e.g., nearest neighbour matching, optimal matching, full matching), the number of control units per treatment unit, replacement of control units, and calipers (Ribas et al., 2021a; Schleicher et al., 2020).

## Evaluating impact

Assessing conservation action effectiveness should ideally consider different outcome-oriented dimensions, including ecological outcomes, social outcomes, and social-ecological interactions (Ghoddousi et al., 2022). 
In the PEOPLE-ECCO project, we focus mainly on the ecological outcomes of conservation actions (Kavlin-Castaneda et al., 2025). 
The impact variable in counterfactual impact assessment of conservation actions can be derived from in situ measurements, e.g., species counts at wildlife observation network locations inside and outside protected areas (Terraube et al., 2020; Wauchope et al., 2022). 
Alternatively, the impact variable can be derived from remote sensing data, e.g., vegetation indices (Meroni et al., 2017; van der Vliet et al., 2024), soil wetness metrics (van der Vliet et al., 2024), forest loss (Eklund et al., 2016; Jones & Lewis, 2015; Koskimäki et al., 2021), or ecosystem services (del Río-Mena et al., 2021). 
Remotely sensed impact variables have the advantage of providing wide coverage in space and time, and are therefore especially suitable for impact evaluation in a before-after-control-impact design.


Based on an impact variable, conservation action effectiveness can be quantified using the BACI contrast. 
Different ways have been proposed to calculate the BACI contrast. Meroni et al. (2017) and del Río-Mena et al. (2021) propose a simple difference-in-difference metric, that is followed here:

BACI_contrast=($\mu$<sup>CA</sup>-$\mu$<sup>CB</sup>)-($\mu$<sup>IA</sup>-$\mu$<sup>IB</sup>)

where $\mu$ represents the impact variable and the subscripts C and I, and B and A represent the control and impact/treatment unit, and before and after period, respectively. 
This expression of BACI contrast is conceptually simple and can be used with single observations in the before and after periods. 
It can also easily be simplified to a control-impact metric when only data after the intervention are available. 

# References

Andam, K. S., Ferraro, P. J., Pfaff, A., Sanchez-Azofeifa, G. A., & Robalino, J. A. (2008). Measuring the effectiveness of protected area networks in reducing deforestation. Proceedings of the National Academy of Sciences, 105(42), 16089–16094. https://doi.org/10.1073/pnas.0800437105

Coetzee, B. W. T., & Gaston, K. J. (2021). An appeal for more rigorous use of counterfactual thinking in biological conservation. Conservation Science and Practice, 3(6), e409. https://doi.org/10.1111/csp2.409

del Río-Mena, T., Willemen, L., Vrieling, A., Snoeys, A., & Nelson, A. (2021). Long-term assessment of ecosystem services at ecological restoration sites using Landsat time series. PLOS ONE, 16(6), e0243020. https://doi.org/10.1371/journal.pone.0243020

Eklund, J., Blanchet, F. G., Nyman, J., Rocha, R., Virtanen, T., & Cabeza, M. (2016). Contrasting spatial and temporal trends of protected area effectiveness in mitigating deforestation in Madagascar. Biological Conservation, 203, 290–297. https://doi.org/10.1016/j.biocon.2016.09.033

Jones, K. W., & Lewis, D. J. (2015). Estimating the Counterfactual Impact of Conservation Programs on Land Cover Outcomes: The Role of Matching and Panel Regression Techniques. PLOS ONE, 10(10), e0141380. https://doi.org/10.1371/journal.pone.0141380

Kavlin-Castaneda, M., Dean, A., Munk, M., Van doninck, J., Bijker, W., Rieke, M., & Willemen, L. (2025). PEOPLE-ECCO Requirement Baseline. Zenodo. https://doi.org/10.5281/zenodo.15396616

Koskimäki, T., Eklund, J., Moulatlet, G. M., & Tuomisto, H. (2021). Impact of individual protected areas on deforestation and carbon emissions in Acre, Brazil. Environmental Conservation, 48(3), 217–224. https://doi.org/10.1017/S0376892921000229

McMurry, T. L., Hu, Y., Blackstone, E. H., & Kozower, B. D. (2015). Propensity scores: Methods, considerations, and applications in the Journal of Thoracic and Cardiovascular Surgery. The Journal of Thoracic and Cardiovascular Surgery, 150(1), 14–19. https://doi.org/10.1016/j.jtcvs.2015.03.057

Meroni, M., Schucknecht, A., Fasbender, D., Rembold, F., Fava, F., Mauclaire, M., Goffner, D., Di Lucchio, L. M., & Leonardi, U. (2017). Remote sensing monitoring of land restoration interventions in semi-arid environments with a before–after control-impact statistical design. International Journal of Applied Earth Observation and Geoinformation, 59, 42–52. https://doi.org/10.1016/j.jag.2017.02.016

Ribas, L. G. dos S., Pressey, R. L., Loyola, R., & Bini, L. M. (2020). A global comparative analysis of impact evaluation methods in estimating the effectiveness of protected areas. Biological Conservation, 246, 108595. https://doi.org/10.1016/j.biocon.2020.108595

Ribas, L. G. S., Pressey, R. L., & Bini, L. M. (2021). Estimating counterfactuals for evaluation of ecological and conservation impact: An introduction to matching methods. Biological Reviews, 96(4), 1186–1204. https://doi.org/10.1111/brv.12697

Schleicher, J., Eklund, J., D. Barnes, M., Geldmann, J., Oldekop, J. A., & Jones, J. P. G. (2020). Statistical matching for conservation science. Conservation Biology, 34(3), 538–549. https://doi.org/10.1111/cobi.13448

Terraube, J., Van doninck, J., Helle, P., & Cabeza, M. (2020). Assessing the effectiveness of a national protected area network for carnivore conservation. Nature Communications, 11(1), Article 1. https://doi.org/10.1038/s41467-020-16792-7

van der Vliet, M., Malbeteau, Y., Ghent, D., Haas, S. de, Veal, K. L., van der Zaan, T., Sinha, R., Dash, S. K., Houborg, R., & de Jeu, R. A. M. (2024). Quantifiable impact: Monitoring landscape restoration from space. A regreening case study in Tanzania. Frontiers in Environmental Science, 12. https://doi.org/10.3389/fenvs.2024.1352058

Wauchope, H. S., Amano, T., Geldmann, J., Johnston, A., Simmons, B. I., Sutherland, W. J., & Jones, J. P. G. (2021). Evaluating Impact Using Time-Series Data. Trends in Ecology & Evolution, 36(3), 196–205. https://doi.org/10.1016/j.tree.2020.11.001

Wauchope, H. S., Jones, J. P. G., Geldmann, J., Simmons, B. I., Amano, T., Blanco, D. E., Fuller, R. A., Johnston, A., Langendoen, T., Mundkur, T., Nagy, S., & Sutherland, W. J. (2022). Protected areas have a mixed impact on waterbirds, but management helps. Nature, 605(7908), 103–107. https://doi.org/10.1038/s41586-022-04617-0







