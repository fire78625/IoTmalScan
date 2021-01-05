# IoTmalScan
A system can analyze ELF files, and return result including it is malicious or not and its belonging malware family
Author: Owen Chen, Chin-Wei Tien

## Source category

(1) Source code(/source_code)

(2) Example sample for testing(/test_sample) : Valid sample will be put into this directory

(3) Figure of detection and classification performance(/result_showing)

## Introduction 

There are many researches and tools about the malware detection and classification of executable(PE) in Windows platform.

However, there are just a few researches on the executable of Linux-based platform. 

Therefore, we try to make a system to automate detecting a new executable (ELF) is malware or not and classifying its belonging malware family. (If it is judged to be a malware)

## System architecture

![](https://github.com/fire78625/IoTmalScan/blob/master/result_showing/Figure1.png)

## Adopted features

In our model, we adopted 19 features.

There are 7 normal static features and 12 opcode features which are defined by ourselves.

Static features:

![](https://github.com/fire78625/IoTmalScan/blob/master/result_showing/Figure17.png)

Opcodes categories:

| Type | Type1 | Type2 | Type3 | Type4 | Type5 | Type6 | Type7 | Type8 | Type9 | Type10 | Type11 | Type12 |
|:-----:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:-------:|:-------:|:-------:|
| Name | Logic | Control and status | Memory | Stack | Procedure | Prefixed |　System I/O | Arithmetic | System | Branch | Execution time | Others |

## Model performance

In our system, there are 2 different models which are used to detect and classify the unknown input separately.

As the following figures shows, our models have great performance in detection and classification.

### Detection performance
![](https://github.com/fire78625/IoTmalScan/blob/master/result_showing/Figure10.png)

### Classification performance
![](https://github.com/fire78625/IoTmalScan/blob/master/result_showing/Figure11.png)

## Experimental enviroment setting

Operation System: Ubuntu 16.04 LTS Client (or at least Linux-based system recommended)

System need the following dependencies and tools: [you can follow url or command to install]
1. Python3 (version>= 3.5.2): https://www.python.org/
2. Radare2: https://rada.re/r/
3. elfutils: https://sourceware.org/elfutils/
4. Yara-Rules: https://github.com/Yara-Rules/rules
5. Tensorflow: https://www.tensorflow.org/
6. IDA pro: https://www.hex-rays.com/products/ida/support/download_freeware.shtml
7. Scikit-learn: https://scikit-learn.org/stable/install.html
8. sqlite3 : pip3 install sqlite
9. numpy: pip3 install numpy

Notice:
1. Your selected environment should own the GUI interface for the processing of IDA pro command. If there is no GUI, the system will encounter an error.
2. Tools such as elfutils and IDA pro need to setting to the environment variable of your OS, so that program can call the program in their directories

### Training phase

1. You have to use Extract_feature.py to extract features from sample. Then in the execution process, program will save this features of sample into a database file(.db) which locates in 'database' directory. Please put the training samples into directory 'training_sample'. Besides, you have to execute label.py before Extract_feature.py to acquire the label of training samples for detection 

#### Executing the following command to generate a detection label file [data format: csv, output: A file whose name is training_detection_label.txt in IoTmalScan directory]

*`python3 label.py <malware_indicator> <sample_path>`*

Ex: python3 label.py 1 /home/username/Desktop/malware/

malware_indicator:{0,1|0: benign sample, 1: malware sample}

sample_path: the (malware/benign) sample saving path

#### Label of classification

1. You have to download the reports of training samples from VirusTotal, please use the format of filename: <hashvalue>.txt
2. The final result label file should put in the directory 'VTreport'

#### Execute the following command to generate a classification label file [filename: training_classification_label.txt]

*`python3 analyze_virustotal_report.py`*

!!! Notice for using newer samples in VirusTotal, we set a limit only parse the report of sample which was found after 2012. If you want to change this limit, please modify code directly

#### Executing the following command [please execute program with root privilege to make sure tools has enough privilege]

*`sudo python3 Extract_feature.py <tool_elfutiles_src_directory_path> <IDA_binary_idaq64_path> <features_storage_database_name>`*

Ex: sudo python3 Extract_feature.py /home/username/Desktop/elfutils/ /home/username/Desktop/IDA/ feature_saving.db

2. Using ML_detection.py to train and save model in the directory 'model' (file format: .h5)

#### Executing the following command to train and save model [model will save in directory 'model', model names are detection_model.h5 and classification_model.h5]

*`python3 ML_detection.py <features_storage_database_name>`*

Ex: python3 ML_detection.py feature_saving.db

*`python3 ML_classification.py <features_storage_database_name>`*

Ex: python3 ML_classification.py feature_saving.db

### Testing phase

Using elf_analysis_activate.py to generate testing result

#### Executing the following command [please execute program with root privilege to make sure tools has enough privilege]

*`sudo python3 elf_analysis_activate.py <sample_name(hashvalue.extname)> <tool_elfutiles_src_directory_path> <IDA_binary_idaq64_path>`*

Ex: sudo python3 elf_analysis_activate.py Desktop/examplevalue.elf Desktop/elfutils/src/ Desktop/IDA/

response format: array [sha256_value, detection_result, classification_result]

response explaination: 
1. detection_result: {0,1| 0: not malware, 1: malware}
2. classification_result: {malware family name or 'benign'}


