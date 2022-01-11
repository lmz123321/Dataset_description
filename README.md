## Dataset description for MIMIC-IV

文档 https://mimic.mit.edu/docs/iv/
下载 https://physionet.org/content/mimiciv/1.0/
保存 Dell/7060/D/数据集
示例PID 
free-text 就是指的原始影响报告 (英文书写)


## introduction 

- 分模块的数据组织

- 数据采集 ICU/emergency department 2008-2019

- no data clean 保证数据反正真实世界情况

- 匿名化: date and time 随机shift到future with an offset; 每个病人的date shift保持一致, 但不同的病人入院时间无法相互比较

## data description

目前 分为5个模组

#### 1.core

Describe patients tracking data.  Demographics, hospital admissions, and in-hospital ward(病房) transfers are described here

包含3个Excel表格

- patients.csv

  demographics for the patient

  **每个病人对应一个的subject_id**

  包含: subject_id, 性别, 匿名化的age和发生时间, 大致的原始发生时间段(3年一个group)

- admissions.csv

  gives information regarding a patient’s admission to the hospital

  **病人(subject_id)每次入院, 对应一个 hadm_id** (**h**spital **adm**ission id)

  包含: subject_id, hadm_id, 入院时间+出院(离开/死亡)时间, 入院分级(按病情紧急程度), 入院前病人位置+出院后病人位置, 病人保险+语言+婚姻状况+保险

- transfers.csv

  a record for each ward stay within a hospitalization

  **病人(subject_id)每次入院(hadm_id)中, 每个physical location(e,g, 病床)对应一个transfer_id**

  包含: subject_id, hadm_id, transfer_id, 转移类型(急诊入院/普通入院/院内转移/Discharge), 转移后的位置(medical ICU, surgeical ICU, medical ward, new babser nurseries等), 转入时间+转出时间

#### 2.hosp

Provides all data acquired from the hospital wide electronic health record. Information covered includes laboratory measurements, microbiology, medication administration, and billed diagnoses.

包含17个表格

- d_hcpcs.csv

  CPT code对照表; CPT code通常显示在账单上, 每个code对应某种收费医疗服务类型

- d_idc_diagnoses.csv

  国际疾病分类标准ICD 疾病诊断部分 code对照表

- d_idc_procedure.csv

  ICD 医疗procedure部分 code对照表; 可以用来判断是否实施了某些procedure, 如heart ultrasound, surgery等

- d_labitems.csv

  实验室测试 itemid code对照表

  每个itemid对应一种检测手段, 该检测手段由一个三元组定义 (label-检测名称, fluid-检测对象 e.g. 血液/尿液, category-检测手段)

- **diagnoses_idc.csv**

  每个病人(subject_id) 每次住院(hadm_id), 被诊断的疾病的ICD code, 主要用于收费

- **drgcodes.csv**

  每个病人(subject_id) 每次住院(hadm_id), 入院病因, e.g. 阴道分娩

- **emar.csv** (3.5GB, 读取方法 DELL 7060 Desktop/ReadLargeCsv.ipynb)

  给药记录 Electronic Medicine Administration Record (eMAR)

  每个病人(subject_id) 每次住院(hadm_id), 所有的服药时间和药品名称

- emar_detail.csv (5.21GB)

  给药记录细节

- hpcsevent.csv

  病人入院的部分付费记录

- **labevent.csv** (12.8GB)

  实验室测试记录

  每个病人(subject_id) 每次住院(hadm_id), 所有的实验室样本(specimen_id)测试记录

  - 测试对象包括血样 尿样等

  - 检测手段(d_labitems.csv)包括 hematology measurements, blood gases, chemistry panels, and less common tests such as **genetic assays**等; 关于无法找到genetic measurement, 已经在社区提问 https://github.com/MIT-LCP/mimic-code/discussions/1167

  - 记录内容包含 测试时间, 参考值, 测试值, 是否正常, 备注等

- **microbiologyevents.csv**

  体液微生物培养 实验测试记录

- pharmacy.csv

  药品服用方法记录

  包含每个病人(subject_id) 每次住院(hadm_id), 所服用药物(pharmacy_id)的计量, 服用方法, 频率, 用药处方等

- poe.csv

  provider over记录, 即病人的医生给病人开了哪些服务订单, e.g. Lab, Radiology, Critical Care

- poe_detail.csv

  provider over记录的细节

- prescriptions.csv

  病人住院期间用过的药品的说明信息

- **precedure_idc.csv**

  病人住院期间为哪些procedure付过费(d_idc_procedure.csv)

- ***services.csv**

  病人住院期间接受过哪些服务, e.g. Cardiac Surgery, Newborn, Vascular Surgical ...

  可以用于检索, e.g. if interested in identifying surgical patients, the recommended method is searching for patients admitted under a surgical service.
#### 3.icu

ICU内部监测系统采集的数据, e.g. intravenous administrations (静脉注射惯例), ventilator settings (呼吸机), and other charted items

包括7个表格

- d_item.csv

  所有events.csv中一些concept的code, e.g 220277-血氧饱和度, 220060-肺静脉舒张压, 220045-心率, 220046-心率过快警告, 220047-心律过慢警告

  检测指标共有3800多种

- **chartevents.cvs**

  ICU病患均配备一个patient’s electronic chart, 用来显示一些关键图表数据, e.g. patients' routine vital signs and any additional information relevant to their care: ventilator settings, laboratory values, code status, mental status等

  该表格包含 每个病人(subject_id) 每次住院(hadm_id) 所在每个床位(stay_id), 不同时间(charttime)采集到的不同种类(ietmid)的图表数据值(value)

  时间密度是小时级别

- **datetimeevents.csv**

  记录每天级别需要采集的一些记录数据, e.g. 透析记录

- **ICU stay.csv**

  病人住院某床位期间, 进出ICU的时间+以及在ICU的时长

- **inputevent.csv**

  病人住院某床位期间, input event的记录, e.g. 服药 输液等

- **outputevent.csv**

  病人住院某床位期间, output event的记录, e.g. 排尿, 排积水等

- **procedureevents.csv**

  病人住院某床位期间, procedure的记录, e.g. ventilation(呼吸机), X-ray imaging等

#### 4.ed

contains data for emergency department patients collected while they are in the ED, e.g. 急诊病因, triage(病情分级), 主要指征等

包含7个表格

- diagnosis.csv

  病人住院期间诊断疾病种类 idc_code (d_idc_diagnoses.csv)

- edstays.csv

  病人 进出ED的时间

- medrecon.csv

  medicine recondiliation: 刚进入急诊时, 分诊员会询问病人目前服用了哪些药物

  病人入院前正在服用药物的种类和细节描述

- pyxis.csv

  自动药物分发系统(PyXis) 为病人在急诊室期间分发药品种类的记录, e.g. Cepacol(北美常见的感冒药物)

- triage.csv

  入院分诊记录, 包含主要vital sign, pain, acuity(分级代码), chiefcomplaint

- vitalsign.csv

  病人在急诊期间, 每1-4小时测量一次vital sign的记录

- vitalsign_hl7.csv

  not available yet

  病人在急诊期间, 数字仪器的检测记录

#### 5.cxr

Contains 227,835 imaging studies for 64,588 patients, A total of 377,110 images are available in the dataset.

Each patient may have more than one image studies (follow-up). Each imaging study can contain one or more images, usually a frontal view and a lateral view

- cxr-record-list.csv

  provides a mapping between the image (dicom_id), the study (study_id), and the patient (subject_id)

- cxr-study-list.csv

  provides a mapping between the studies (study_id) and patients (subject_id)

- **Data orignization**

  - version
    - v1.0.0 (MIMIC-CXR-JPG) JPG format images + 14 structured labels from free-text by NLP tool
    - v2.0.0 dicom format images + free-text reports
    
  - **image**
  
    MIMIC-CXR-JPG p10-p19
    
    根据番栋师兄建议, 可以直接用JPG版本(已转换窗宽窗位)
  
  - image metadatas
  
    Dicom的meta data都在mimic-cxr-2.0.0-metadata.csv.gz
  
  - **radiology reports**
  
    所有plain text文件全都在MIMIC-CXR v2.0.0 mimic-cxr-report..zip内
  
  - **srtucture labels**
  
      根据Chexpert定义的13中疾病的label, 都在MIMIC-CXR-JPG mimic-cxr-2.0.0-chexpert.csv.gz和mimic-cxr-2.0.0-negbio.csv.gz内
  

#### 6.note

Free-text的各种临床报告

currently not available, will release in the future; if need, can check MIMIC-III

## download
详见'保存'文件夹的 **MIMIC-IV 下载说明.md**

## extended or similar dataset
- **X-ray, Free-text report, Hierachical 相关**
  - RadGraph: Extracting Clinical Entities and Relations from Radiology Reports 
    - MIMIC-CXR和CheXpert里free-text的自然语言标注
    - 标注两个部分
      - Entity
        - Anatomy(器官 如lung), Observations(definite, uncertain, absent)
      - Relations
        - Entity之间的关系, Suggestive-off, located-at, modify
  - Chest ImaGenome Dataset
    - https://physionet.org/content/chest-imagenome/1.0.0/
    - 针对free-text report的标注
    - 关键词 scene graph per image, relation, hierachy
  - Annotated Question-Answer Pairs for Clinical Notes in the MIMIC-III Database
    -  https://physionet.org/content/mimic-iii-question-answer/1.0.0/
    - 从Clinical Notes中派生的 配对的Question-Answers
  - Publicly Available Clinical BERT Embeddings
    - https://arxiv.org/pdf/1904.03323.pdf
    - Clinical BERT embedding 
  - 从free-text的report中, 用NLP的方法生成structure labels
    - CheXpert
    - ChexPert++ (http://proceedings.mlr.press/v126/mcdermott20a.html)
- **MIMIC 派生 classification/regression benchmarks**
  - HiRID, a high time-resolution ICU dataset
    - 6个任务- mortality prediction, length of stay regression, phenotyping, CF/RF/ACK prediction
    - binary/multi-class classification和regression
    - 不适合treatment-effects
  - Multitask learning and benchmarking with clinical time series data
    - 4个任务: mortality prediction, length of stay prediction, physiologic decline detection, phenotyping
- **MIMIC 派生 disease management benchmarks**
  - Data-driven curation process for describing the blood glucose management in the intensive care unit
    -  https://www.nature.com/articles/s41597-021-00864-4
    - 糖尿病 葡萄糖-胰岛素management; MIMIC-iii 派生数据集
- **MIMIC-IV 数据分析 report**
  -  https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9510098
  -  https://ebooks.iospress.nl/doi/10.3233/SHTI210549
- **其它类似数据集**
  - EuResist Integrated Database; HIV 检测和治疗数据集 
  - eICU, CEDAR
  - ICU for COVID-19 patients: Dutch Data Warehouse (DDW)
- **MIMIC 其它派生数据集**
  - Eye Gaze Data for Chest X-rays
    - https://physionet.org/content/egd-cxr/1.0.0/
    - eye tracking data
  - CheXphoto
    - http://proceedings.mlr.press/v136/phillips20a/phillips20a.pdf
    - 原始高清CXR -> photo of CXR 
