# IoTmalScan
A system can analyze ELF files, and return result including it is malicious or not and its belonging malware family

## Usage description
There are 2 Restful APIs which are used to upload sample and lookup the analysis result 

upload: upload sample to system

report: lookup analysis result

System need following dependencies and tools:
1. Python3 (version>= 3.5.2): https://www.python.org/
2. Radare2: https://rada.re/r/
3. elfutils: https://sourceware.org/elfutils/
4. Yara-Rules: https://github.com/Yara-Rules/rules
5. Tensorflow: https://www.tensorflow.org/
6. IDA pro: https://www.hex-rays.com/products/ida/support/download_freeware.shtml
7. npm: https://www.npmjs.com/package/download
8. nodejs (version>= 10): https://nodejs.org/zh-tw/download/

### Notice
IDA pro needs GUI to output during process. If there is no GUI in your system, it will error when analyze sample.
Thus, need to install GUI interface package such as X-window ...

## Usage
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
