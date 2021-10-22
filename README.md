# L1 EGamma Isolation LUT Caliberation

1. Computation of the isolation cut for a given flat efficiency. 
The isolation energy (E_iso) is defined as the difference between the energy deposit in a 6x9 TT window (E_6x9) and the raw energy deposited by the tau/EG (ET_raw) . E_iso depends of ET_raw, iEta and nTT (being this last one a pileup estimator for taus and EG). We reject the background by requiring that the tau is isolated, e.g., this E_iso is smaller than a certain value (E_isocut). The value of the isolation cut is chosen accordingly to a target signal efficiency, that is constant in ieta and nTT but can be varied as a function of ET. It is computed for a given flat efficiency with simulated events in exclusive intervals of the isolation input variables ET_raw, iEta and nTT. 

The script Build_Isolation_WPs_MC_NewCompression_Thomas_nTT_OlivierFlatWP_With2017Layer1.C computes this isolation cuts and gives as output a root file (named Iso_LUTs_Distributions_MC or something similar) which contains histograms labelled by (i,j,k) corresponding to the value of the isocut for each bin (ieta, iEt supercompr., nTT). (For the taus we previously do a supercompression of the Et bins to have more statistics in each bin.)

2. Relaxation of the isocut with pT
After that, we relax the isolation cut with pT to reach a 100% plateau efficiency while controlling the trigger rate.  We do the relaxation linearly, starting from a flat efficiency A up to a certain pT B, then relaxing linearly up to a certain pT C, when the 100% efficiency is reached (see picture). We test different isolation options corresponding to different combinations of the values A/B/C (this is what we call Option 1, 2…). 
To derive these new isocuts for each of the options you can use the script Fill_Isolation_TH3_MC_2017Layer1Calibration.C. 
This gives as ouput another root file (Iso_LUTs_Options_MC or similar) that gives you histograms with the value of the isocuts for each Option for (x,y,z = ieta, iEt supercompr., nTT).

3. Rate estimation:
Now one should check the rates of the different isolation options as a function of the ET threshold. For this we use ZeroBias samples (which you should previously calibrate). 
The code to compute the rates is: Rate_ZeroBias_*.C
Make sure to modify the correct number of bunches for the run you are considering (see here for example https://cmswbm.cern.ch/cmsdb/servlet/FillReport?FILL=6311), as well as the luminosity. 

The code also provides you with the ET thresholds corresponding to a fixed rate you choose. 
Note this is coded for di-tau rate estimation, but it is easy to modify.
The rate is computed as:

N_tot = number of zero bias events
N_pass = number of zero bias events that pass the trigger that we are testing (L1_DoubleIsoTau_30)
Rate = (N_pass/PS)/(N_total/PS) x scale
Scale = frequency at which you see a zero bias event at the LHC (Hz) = = (bunch revolution frequency) x (#bunches)
   = (c/27km) x #bunches = 11245.6 x #bunches (1900-2600)
(multiply by 0.001 to get kHz)

4. Apply the isolation:
The last step is to apply the isolation (relaxed) cuts onto your signal MC samples, and get the turnons.
This is done with the script ApplyIsolationForTurnOns.C, where you have to specify the ET threshold to compute the turnon. It takes as input your signal MC ntuples as well as the Iso_LUTs_Options_MC root file.

You can comment out the shape veto part of the code.
This will give you a root file with the turnons for the different options with the  ET threshold you have specified. 

5. Make the isolation LUT:

You can produce the isolation LUT (text file) with the script MakeTauIsoLUT_MC_NewCompression_WithMarch2017Layer1.C, by giving it as input the Iso_LUTs_Options_MC root file. 

----------------------------------------------------------------------------------------------------------------------

One would need to replace the L1Tau variables with the corresponding L1EG, start from 2018 emulated data (as the uncalibrated energy, calorimeter ieta and nTT are only available in emulation) and try to optimize the WPs.

Improvement ideas (from Thomas Strebler):

“to introduce a bit more smoothness in the sharp efficiency transitions induced by the linear relaxation pattern used so far, I think it would be worth to investigate an efficiency with a quadratic relaxation with pT: namely f(x)=-ax2 + bx + c, such that f(pT_max)=1 and f’(pT_max)=0.”

