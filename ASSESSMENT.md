# Environmental and Societal Assessment

## Environmental cost

This project compares Random Forest, CNN and Vision Transformer models for binary sea-ice/lead classification and then tests whether the spectral input to the Random Forest can be reduced from 21 bands to five without reducing predictive performance. The environmental assessment focuses on the parts of the workflow for which direct measurements were collected and avoids extending those measurements beyond what was recorded.

CodeCarbon was used to estimate energy consumption and associated carbon dioxide equivalent emissions in Google Colab. The measured stages were:

| Stage | Energy | Estimated emissions |
|---|---:|---:|
| Data loading | 0.00002704 kWh | 0.00001273 kg CO2eq |
| 21-band RF, single training run | 0.00009695 kWh | 0.00004564 kg CO2eq |
| 5-band RF, single training run | 0.00009777 kWh | 0.00004603 kg CO2eq |
| 21-band RF, five training runs | 0.00044974 kWh | 0.00021173 kg CO2eq |
| 5-band RF, five training runs | 0.00018756 kWh | 0.00008830 kg CO2eq |

The single-run measurements were very small and noisy, so they were not used as the main comparison. The more stable counterfactual comparison used five complete Random Forest training runs for each spectral configuration. Reducing the input from 21 bands to Bands 1, 2, 3, 4 and 15 reduced measured energy use and CodeCarbon-estimated emissions by approximately 58.3%. This agrees closely with the independent timing experiment, where mean Random Forest training time fell from 14.206 s to 5.716 s, a reduction of 59.8%.

The reduced input also maintained predictive performance. Across five Random Forest seeds, mean test accuracy was 93.05% using all 21 bands and 93.66% using the five selected bands. The main environmental finding is therefore relative rather than absolute: for this experiment, a smaller spectral input reduced computation, energy use and estimated emissions without reducing classification accuracy.

The comparison between model families gives additional context on computational efficiency. Random Forest achieved 93.42% test accuracy and ViT achieved 93.35%, while mean prediction time on the same 1,580 test samples was 0.0828 s for Random Forest and 1.3279 s for ViT. CNN prediction time was 0.3778 s and its test accuracy was lower at 78.48%. These timings are hardware-dependent and are not carbon measurements, but they show that greater architectural complexity did not improve classification performance for this dataset.

CNN and ViT training energy were not measured directly, so no carbon values are claimed for those stages. Re-running trained deep-learning models solely to obtain retrospective carbon figures was not part of the completed experiment. Water use was also not measured because the Colab infrastructure information needed for a defensible estimate was not available. The reported CodeCarbon values are small and depend on hardware allocation and electricity assumptions, so the relative reduction between matched Random Forest workloads is more reliable than treating the absolute values as universal footprints.

## Societal framing

Sea-ice and lead classification is relevant to polar environmental monitoring because leads affect exchanges of heat, moisture and energy between the ocean and atmosphere. The potential audience includes polar researchers, Earth-observation analysts and organisations that need spatial information about remote sea-ice environments. The scale of impact in this project is limited: it is a proof-of-concept classification study rather than an operational product, and it uses a restricted labelled region from one image.

Earth observation adds value because satellites can repeatedly observe large and difficult-to-access polar regions where dense in-situ monitoring would be expensive, dangerous or impossible to maintain. Automated classification can help convert those images into spatial information more quickly, but the model output should support interpretation rather than replace it.

The single most decision-relevant result is that the simpler Random Forest matched ViT accuracy while being much faster at inference, and that reducing the Random Forest input from 21 bands to five preserved accuracy while lowering measured training cost. For similar datasets, this suggests that increasing model or spectral complexity should be justified by a demonstrated performance gain rather than assumed to be beneficial.

For an end user, the clearest communication would combine the classification map with a short performance summary, the spectral bands used, the training-data coverage and explicit limitations. A binary map on its own could give a false impression of certainty. The rollout results already show that models with similar global accuracy can produce different spatial predictions, so uncertainty and validation coverage would need to be communicated alongside any operational output.

The main limitations are the manually produced labels, the small labelled area, the use of one image, the lack of independent ground truth across the full rollout region, the model-specific nature of Random Forest feature importance and the hardware dependence of timing and CodeCarbon estimates. These limitations mean the results should not be assumed to generalise to other seasons, locations, illumination conditions or sensors without further validation.
