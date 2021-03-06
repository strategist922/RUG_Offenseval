# RUG SemEval 2019 task 6/a - Binary OffensEval
This repository contains the scripts that generate the models and the submissions for the OffensEval task A. Note that due to size limitations, the word embeddings file is not included in the resources folder, one has to insert it before running

## Models
The predictions generated by the following three models were submitted to the OffensEval task A.

### Concatenated SVM
The system is an extension of the RUG model for germinal 2018. The original model contains a concatenation of word count vectorization, character—Ngrams count vectorization and word embeddings. For the embeddings the Glove tweet embeddings model was used. The predictor is a linear support vector machine model. For this task, this original model was extended by 6 features:
⁃    TweetTokenization (nltk) for better tokenisation
⁃    TF-IDF based most important word selection, and calculation of the tweet proximity to these words, based on embeddings distance
⁃    Same proximity calculation, but for the most important character N-grams
⁃    Using the most important words again, but representing the tweet not based on proximity, but on one-hot presence encoding of the important words in the tweet
⁃    Vectorizing the distribution of the POS-tags in the tweet
⁃    Approximating the sentence complexity by the length of the sentence subtrees, using space model
In this version, the features are simply concatenated, and one SVM was trained on the feature union.

### Ensemble SVM
This model essentialy uses the same features as the previous one, but instead of concatenating them into one long feature vector, we trained a separate model for each additional feature, so we generate 6 models where each was trained on the original representation plus one of the feature representations. On the top of these 6 models we train an ensemble SVM classifier.

### Naive SVM
Current research on similar topics (germeval offensive language detection) show, that a very helpful feature in this task is calculating the proximity of a tweet to certain most important terms. In the mentioned models the most important terms were extracted from the training set. As the training set is relatively small and the language is English, with this model we try a naive approach with defining the most important terms based on a list of offensive English words (), and calculate the minimum, maximum and average proximities to these terms based on the Glove twitter embeddings. The classifier is a linear SVM model. 

## Usage
### Folder structure
The Data folder contains the training data in the train folder, and the final test (evaluation) data in the test folder. The Gold standard folder is currently empty as the GS data has not been published yet. The resources folder contains the hard coded offensive words list for the naive model, and the embeddings file has to be placed here, name as 'glove.twitter.27B.200d.txt' (if a different embedding file is used, the paths have to be renamed in all the scripts). The Models folder contains the serialized, trained models, and the Output folder contains the predictions generated by the models. The Scripts folder contains the scripts for model and prediction generation, as discussed in the next section. The relative paths are hard coded in the script, when changed, the corresponding changes have to be made in the scripts.

### Running
After inserting the mentioned embeddings file, the program should be able to run. There are three scripts that generate the trained models: 
    - Naive_SVM_LR generates the trained model of the naive approach. It is set to train an SVM, but an LR is also possible, it is commented out.
    - SVM_concat generates the concatenated model. Originally the code was implemented to allow iterating through different combinations of included features, so in the main we can set up a combination, and in the runFitting function it actually trains and stores the model. In the current version it is set to include all implemented features and only run the training once with those.
    - SVM_ensemble generates the ensemble model. The script is a modified version of the concatenated model script. It iterates over the possible features, always turns on one, runs the fitting function, and then turns it off and turns on the next feature. Essentially it generates and stores a trained model for each feature, with the other features not included. After all the feature models are generated, it runs an SVM training on the predictions of the distinct feature models, and stores the trained ensemble SVM model.

All generated models are stored in the Models folder as joblib files. One can perform predictions on the test data by running the prediction script. On the top of the script there are three boolean variables that determine for which model we want to produce the prediction. The test instances for the prediction are stored in the Data/test folder, the path is hard coded to the current file, so for other test files it has to be overwritten in the prediction script. The script will generate and place the predictions in the Outputs folder with names corresponding to the method.

