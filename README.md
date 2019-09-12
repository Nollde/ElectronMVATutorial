# Electron MVA Tutorial

## Introduction

Imagine you are tasked with retraining the multivariate electron identification algorithm in CMS (aka. the Electron MVA) for the upcoming Run 3. How would you tackle that?

This tutorial explains you exactly how it was done in Run 2, so you could continue where we left off and improve from there.

In general, there are 3 stages you need to work through in this endevor, which all require different skills and tools:

1. **Extract electron data** from CMS simulated events (MC). This step is also known as "producing ntuples".

2. **Train a multivariate classifier**. Traditionally we train here Boosted Decision Trees (BDTs).
   
3. **Implement the classifier in back in CMSSW**. This step is either a straight forward copy-and-paste job if you stick with the BDT algorithm, or involves a lot of C++ programming when you choose to take another one.
   
Here, we will tell you how to use the "ElectronMVANtuplizer" in CMSSW to create the ntuples for the training and how to train the Electron MVA with Python tools.

## Creating the Electron MVA Ntuple

### 1. Check out CMSSW environment

Make sure you initialized your grid proxy. This time we go with a brand new CMSSW release as we deal with Run 3 samples:

```Bash
cmsrel CMSSW_10_6_3
cd CMSSW_10_6_3/src
cmsenv
git cms-addpkg RecoEgamma/ElectronIdentification
```

Wait for the checkout to complete and then change into the directory `RecoEgamma/ElectronIdentification/test`. There you find the configuration file for the ElectronMVANtuplizer, named `TestElectronMVA_cfg.py`.

### 2. Change input files to new Run 3 samples

The Electron MVA should separate prompt electrons from fakes that come from no particular hard process. That's why we will use a **Drell-Yan plus jets** (DY+jets) sample for the training.

Signal (or the positive class in ML language) will be reconstructed electrons that match to a true prompt electron, while the background (or the negative class) will be all unmatched and non-prompt electrons (e.g. electrons in jets). We will ignore electrons from tau decays. All this categorization is done in the ElectronMVANtuplizer.

This is the name of the MC DY+jets sample we will use:
```
/DYJets_incl_MLL-50_TuneCP5_14TeV-madgraphMLM-pythia8/Run3Summer19MiniAOD-2023Scenario_106X_mcRun3_2023_realistic_v3-v1/MINIAODSIM
```

Are you familiar with the `dasgoclient`? You can check the last example you get with `dasgoclient --help` to learn how to find files within a given sample. Please find the path to some file in the DY+jets Run 3 sample and edit `process.source` in `testElectronMVA_cfg.py` to use the new file path.

You might also want to change `process.maxEvents` in the same file to a relatively small number (like 1000 events) to make a test run. Once you know it works, you set it back to `-1`, which means all events in the file.

If it does not work, try a different file in the dataset. The production of the dataset is not finished yet, so you might get unlucky and pick a file that does not exists.

### 3. Run the Ntuplizer

All that is left to do now to produce the ntuple is to issue this command in `RecoEgamma/ElectronIdentification/test`:
    
```Bash
cmsRun testElectronMVA_cfg.py
```

Watch the events fly by. In the end, you will have a new file called `electron_ntuple.root` in the test directory.

## Check out the Ntuples and Train the BDTs

For the main part of the tutorial, please head to [SWAN](https://swan.cern.ch) and create some test project, just to be
sure it works. If you connect to _lxplus_ via SSH and go to you `SWAN_projects` directory in your EOS user area. For me,
this is for example:

```
/eos/user/r/rembserj/SWAN_projects
```

From that directory, clone this repository and refresh SWAN is the browser. You should now see a new project ElectronMVATutorial, in which you find a notebook called `ElectronMVATutorial.ipynb` that you can open. See you there!
