# IoTmalScan
A system can analyze ELF files, and return result including it is malicious or not and its belonging malware family
Author: Owen Chen, Chin-Wei Tien

## Source category

(1) Source code(/source_code)

(2) Example sample for testing(/test_sample)

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

System need the following dependencies and tools:
1. Python3 (version>= 3.5.2): https://www.python.org/
2. Radare2: https://rada.re/r/
3. elfutils: https://sourceware.org/elfutils/
4. Yara-Rules: https://github.com/Yara-Rules/rules
5. Tensorflow: https://www.tensorflow.org/
6. IDA pro: https://www.hex-rays.com/products/ida/support/download_freeware.shtml
7. npm: https://www.npmjs.com/package/download
8. nodejs (version>= 10): https://nodejs.org/zh-tw/download/
9. Scikit-learn: https://scikit-learn.org/stable/install.html
10. sqlite3

Notice:
1. nodejs is ready for building restful server, if you do not use API to use system service and you can ignore the installation
2. Your selected environment should own the GUI interface for the processing of IDA pro command. If there is no GUI, the system will encounter an error.
3. Tools such as elfutils and IDA pro need to setting to the environment variable of your OS, so that program can call the program in their directories

### Training phase

1. You have to use Extract_feature.py to extract features from sample. Then in the execution process, program will save this features of sample into a database file(.db) which locates in 'database' directory. Please put the training samples into directory 'training_sample'. Besides, you have to execute label.py before Extract_feature.py to acquire the label of training samples

#### Executing the following command to generate a label file [data format: csv, output: A file whose name is training_label.txt]

*`python3 label.py <malware_indicator> <sample_path>`*

Ex: python3 label.py 1 /home/username/Desktop/malware/

malware_indicator:{0,1|0: benign sample, 1: malware sample}

sample_path: the (malware/benign) sample saving path

#### Executing the following command

*`python3 Extract_feature.py <tool_elfutiles_src_directory_path> <IDA_binary_idaq64_path> <features_storage_database_name>`*

Ex: python3 Extract_feature.py /home/username/Desktop/elfutils/ /home/username/Desktop/IDA/ feature_saving.db

2. Using ML_detection.py to train and save model in the directory 'model' (file format: .h5)

#### Executing the following command to train and save model

*`python3 ML_detection.py <features_storage_database_name>`*

Ex: python3 ML_detection.py feature_saving.db

### Testing phase

Using elf_analysis_activate.py to generate testing result

#### Executing the following command [please execute program with root privilege to make sure tools has enough privilege]

*`sudo python3 elf_analysis_activate.py <sample_name(sha256_value)> <tool_elfutiles_src_directory_path> <IDA_binary_idaq64_path>`*

Ex: sudo python3 elf_analysis_activate.py Desktop/examplevalue Desktop/elfutils/src/ Desktop/IDA/

response format: array [sha256_value, detection_result, classification_result]

response explaination: 
1. detection_result: {0,1| 0: not malware, 1: malware}
2. classification_result: {malware family name or 'benign'}

## Usage description
There are 2 Restful APIs which are used to upload sample and lookup the analysis result 

upload: upload sample to system

report: lookup analysis result

## Usage <!!! The server is not working for now. Please do not attempt to upload file>
upload:
curl -k https://52.247.210.51:20180/task/upload -F sampleFile=@<sample_path>

report:
curl –k https://52.247.210.51:20180/task/report/<sha256\>
### Notice
  sha256 is one of return results of upload

## return example (JSON format)
upload: {"status":"Success/Failed", "message":"error msg or success msg", "sha256":"<sha256_value>"}

report: {"status":"Success/Failed", "detection":"True/False/None", "classification":"<malware family name\>/benign/None", ("message":"Failed msg")}
### Notice
  sha256 value exists only when success upload sample or repeated sample. Otherwise, it will be empty
  
  message (report) only exists when search analysis result failed. If success, it will not appear in response body 

## Important reminder
If you want to attempt our system, please contact us and attach your ip address for setting into valid user

