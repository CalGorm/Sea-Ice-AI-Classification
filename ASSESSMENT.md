# Environmental and Societal Assessment

## Environmental cost

This project compares three machine-learning approaches for binary sea-ice/lead classification and then investigates whether the spectral input can be reduced without sacrificing predictive performance. The environmental assessment therefore focuses on both **model complexity** and **input dimensionality**.

CodeCarbon was used to estimate the energy consumption and associated carbon dioxide equivalent (CO2eq) emissions of Random Forest training. Because individual Random Forest fits were very short, the main environmental comparison measured five complete training runs for each spectral configuration so that the workload was long enough to produce a more stable estimate.

For the 21-band Random Forest workload, five training runs consumed **0.00044974 kWh** and produced an estimated **0.00021173 kg CO2eq**. Repeating the same five-training-run workload using only Bands 1, 2, 3, 4 and 15 consumed **0.00018756 kWh** and produced an estimated **0.00008830 kg CO2eq**. This corresponds to an energy and emissions reduction of approximately **58.3%** for the reduced-band workload.

This result is consistent with the independently measured training-time comparison. Mean Random Forest training time fell from **14.206 s** using all 21 bands to **5.716 s** using the top five bands, a reduction of **59.8%**. Importantly, the reduced input did not reduce predictive performance in this experiment: across five Random Forest seeds, mean test accuracy was **93.05%** for 21 bands and **93.66%** for the five-band model.

The absolute CodeCarbon values are very small and depend on the Google Colab environment, hardware allocation and regional electricity assumptions. They should therefore not be interpreted as universal carbon footprints. The more defensible conclusion is the relative one: for this workload, reducing spectral dimensionality substantially reduced measured computation, energy use and estimated emissions while maintaining comparable accuracy.

The comparison between model families also suggests that greater architectural complexity did not automatically produce better results. Random Forest achieved **93.42%** test accuracy, essentially matching the ViT at **93.35%**, while mean prediction time on the same 1,580 test samples was **0.0828 s** for Random Forest versus **1.3279 s** for ViT. This indicates that, for this dataset, a simpler model may provide a more efficient accuracy–computation trade-off.

## Societal framing

Sea-ice and lead classification is relevant to polar environmental monitoring because leads influence exchanges of heat, moisture and energy between the ocean and atmosphere. Satellite Earth observation can support researchers and organisations that need spatially extensive observations of remote polar environments where repeated in-situ monitoring is difficult.

However, an automated classification product should not be treated as an infallible representation of the environment. The labels used to train the models were created manually and therefore contain potential interpretation and annotation uncertainty. The experiments were also based on a limited labelled region from one image. A model that performs well here may perform differently under other illumination conditions, seasons, geographical regions or sensors.

The rollout maps further show that models with similar overall accuracy can still produce different spatial predictions. If such outputs were used operationally, users would need information about model uncertainty, training data coverage and known failure modes rather than being shown only a binary map. Human interpretation would remain important, particularly where model outputs could influence scientific conclusions or operational decisions.

There is also a broader design question around computational resources. More complex AI systems can require greater compute without necessarily improving environmental monitoring outcomes. In this project, the ViT did not outperform Random Forest, and the reduced-band Random Forest retained comparable accuracy with lower measured computational and energy cost. This supports a principle of using the **simplest model and smallest input representation that are adequate for the task**, while still validating performance across independent data.

Overall, the project demonstrates both the potential and the limits of AI4EO. Machine learning can help scale environmental classification, but responsible deployment requires attention to data quality, generalisation, interpretability, computational cost and clear communication of uncertainty.
