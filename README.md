# Поля документа

Поля первого уровня:
- __text_id__ - id текста. 
- __text__ - текст отзыва.
- __relations__ - список, в котором хранится информация о выделенных отношениях между сущностями.
- __entities__ - список, в котором хранится информация о выделенных сущностях.
- __coreference__ - словарь, в котором хранится информация о кореференциях.

Для описания сущностей используются словари со следующими полями (поле [__field__] означает, что оно может отсутствовать):
 - [__ATC__] - код анатомо-терапевтически-химической классификации (если __MedType__==Drugname или __MedType__==Drugclass).
 - __Context__ - список, в котором указано, к каким контекстам в отзыве относится упоминание.
 - [__DisType__] - подтип Disease. Поле может отсутствовать, если __MedEntityType__ != Disease.
 - [__MKB10__] - код классификации МКБ-10 (если __DisType__==Diseasename). 
 - [__MedDRA__] - термин уровня PT из MedDRA (если __MedEntityType__==ADR или __MedEntityType__==Disease и __DisType__==Indication). Если сущность допускает несколько вариантов нормализации, соответствующие концепты записываются в одну строку, разделителем является "|".
 - __MedEntityType__ - основной тип сущности, один из (Medication, Disease, ADR, Note).
 - [__MedFrom__] - распространяется ли препарат на территории России (Domestic) или только за границей (Foreign). Поле присутствует только если __MedType__ == Drugname. 
 - [__MedType__] - подтип Medication (поле может отсутствовать, если это не Medication).
 - __entity_id__ - id сущности.
 - [__medform_standart__] - стандартизированное название формы препарата.
 - __spans__ - список, в котором хранится информация, об отрывках, в которых упоминается сущность, каждый элемент списка - словарь c полями __begin__ и __end__ (конец не включительно), которые указывают индексы границы отрывка.
 - __tag__ - список тегов.
 - __text__ - текст упоминания сущности, сложенный из всех отрывков. 
 - __xmiID__ - ID упоминания в пределах документа. Поле может отсутствовать для корпусов, полученных не из xmi формата.

Каждый элемент списка __relations__ имеет структуру словаря со следущими полями:
__first_entity__, __second_entity__, __relation_type__, __relation_class__
- __first_entity__ - словарь, содержит описание сущности
- __second_entity__ - словарь, содержит описание сущности 
- __relation_type__ - строка, тип отношения, один из ('ADR_Medication:MedTypeDrugname',
 'Disease:DisTypeDiseasename_Disease:DisTypeIndication',
 'Medication:MedTypeDrugname_Disease:DisTypeDiseasename',
 'Medication:MedTypeDrugname_Medication:MedTypeSourceInfodrug')
- __relation_class__ - класс отношения, принимает значение 0 или 1. #TODO Что это?

Каждый элемент списка __entities__ имеет структуру словаря, где ключ это номер сущности, а значение словарь с описанием этой сущности

В __coreference__ представлены кореферентные цепочки в следующем формате словаря с полями:
- __mentions__ содержит список словарей упоминаний. Каждое упоминание - словарь с полями __startPos__, __endPos__, указывающими границы упоминания в тексте
- __clusters__ содержит список цепочек (кластеров). Каждая цепочка это список индексов упоминаний из поля mentions, которые являются кореферентными

# Решаемые задачи
## NER
Рассматривается задача Named Entity Recognition, в которой требуется из текста отзыва выделить упоминания относящиеся к ADR, Disease, Indication, Drug, Source.
Метрика - F1-exact по каждой категории, macro усреднение.
Лучшие результаты:
| Dataset  | ADR  | Disease | Indication | Drug | Source | F1-macro | Paper                                                                                                                                                                                                                                                                                                          |
|-----------|------|---------|------------|------|--------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RDRS 3800 | 60.1 | 87.2    | 67.5       | 96.6 | 72.5   | 76.7     | [Селиванов А.А., Грязнов А.В. Рыбка Р.Б., Сбоев А.Г., Сбоева С.Г., Клюева Ю.А.  Relation Extraction from Texts Containing Pharmacologically Significant Information on base of Multilingual Language Models]                                                                                                   |
| RDRS 2800 | 63.8 | 96.0    | 89.7       | 63.3 | 73.2   | 77.2     | [Сбоев А.Г., Рыбка Р. Б., Наумов А.В., Селиванов А.А, Грязнов А.В., Молошников И.А.   An accuracy comparison of the Joint and Sequential Approaches for End-to-End Related Named Entities Extraction in the Texts of Russian-Language Reviews Based on Neural Networks  IOPscience, - (год публикации - 2022)] |  

Список статей:
- Сбоев А.Г., Молошников И.А., Селиванов А.А.? Рыльков Г.В., Рыбка Р.Б. The two-stage algorithm for extraction of the significant pharmaceutical named entities and their relations in the Russian-language reviews on medications on base of the XLM-RoBERTa language model Springer's Studies in Computational Intelligence, - (год публикации - 2022)
- Сбоев А.Г., Рыбка Р. Б., Наумов А.В., Селиванов А.А, Грязнов А.В., Молошников И.А. An accuracy comparison of the Joint and Sequential Approaches for End-to-End Related Named Entities Extraction in the Texts of Russian-Language Reviews Based on Neural Networks IOPscience, - (год публикации - 2022)
- Сбоев А.Г., Сбоева С.Г., Молошников И.А., Грязнов. А.В., Рыбка Р.Б., Наумов А.В., Селиванов А.А., Рыльков Г.В., Ильин В.А. Analysis of the Full-Size Russian Corpus of Internet Drug Reviews with Complex NER Labeling Using Deep Learning Neural Networks and Language Models Applied Sciences (Switzerland), Т. 12. – №. 1. – С. 491. (год публикации - 2022) https://doi.org/10.3390/app12010491	
- Селиванов А.А., Грязнов А.В. Рыбка Р.Б., Сбоев А.Г., Сбоева С.Г., Клюева Ю.А. Relation Extraction from Texts Containing Pharmacologically Significant Information on base of Multilingual Language Models Sissa Medialab Srl, том 429 (год публикации - 2022)
## RE
Рассматривается задача Relation Extraction, в которой нужно определить наличие связи между двумя сущностями. Рассматриваются связи четырех типов: ADR–Drugname, Drugname–SourceInfodrug, Drugname–Diseasename, Diseasename–Indication.  
Лучшие результаты

| Dataset   | Entities origin | ADR–Drugname | Drugname–Diseasename | Drugname–SourceInfodrug | Diseasename–Indication | F1-macro | Paper                                                                                                                                                                                                                                                                                                       |
|-----------|-----------------|--------------|----------------------|-------------------------|------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RDRS 3800 | Gold            | -            | -                    | -                       | -                      | 87.2     | [Селиванов А.А., Грязнов А.В. Рыбка Р.Б., Сбоев А.Г., Сбоева С.Г., Клюева Ю.А. Relation Extraction from Texts Containing Pharmacologically Significant Information on base of Multilingual Language Models Sissa Medialab Srl, том 429 (год публикации - 2022)]                                             |
| RDRS 3800 | Predicted       | -            | -                    | -                       | -                      | 60.1     | [Селиванов А.А., Грязнов А.В. Рыбка Р.Б., Сбоев А.Г., Сбоева С.Г., Клюева Ю.А. Relation Extraction from Texts Containing Pharmacologically Significant Information on base of Multilingual Language Models Sissa Medialab Srl, том 429 (год публикации - 2022)]                                             |
| RDRS 2800 | Gold            | -            | -                    | -                       | -                      | -        | -                                                                                                                                                                                                                                                                                                           |
| RDRS 2800 | Predicted       | 51.2         | 69.4                 | 49.2                    | 38.6                   | 52.1     | [Сбоев А.Г., Рыбка Р. Б., Наумов А.В., Селиванов А.А, Грязнов А.В., Молошников И.А. An accuracy comparison of the Joint and Sequential Approaches for End-to-End Related Named Entities Extraction in the Texts of Russian-Language Reviews Based on Neural Networks IOPscience, - (год публикации - 2022)] |

Список статей:
- Сбоев А.Г., Молошников И.А., Селиванов А.А.? Рыльков Г.В., Рыбка Р.Б. The two-stage algorithm for extraction of the significant pharmaceutical named entities and their relations in the Russian-language reviews on medications on base of the XLM-RoBERTa language model Springer's Studies in Computational Intelligence, - (год публикации - 2022)
- Сбоев А.Г., Селиванов А.А., Рыбка Р.Б., Молошников И.А., Рыльков Г.В. Evaluation of Machine Learning Methods for Relation Extraction Between Drug Adverse Effects and Medications in Russian Texts of Internet User Reviews Proceedings of Science, - (год публикации - 2022)
- Сбоев А.Г., Рыбка Р. Б., Наумов А.В., Селиванов А.А, Грязнов А.В., Молошников И.А. An accuracy comparison of the Joint and Sequential Approaches for End-to-End Related Named Entities Extraction in the Texts of Russian-Language Reviews Based on Neural Networks IOPscience, - (год публикации - 2022)
- Сбоев А.Г., Селиванов А.А., Молошников И.А.,Рыбка Р.Б., Грязнов А.В., Сбоева С.Г., Рыльков Г.В. Extraction of the Relations among Significant Pharmacological Entities in Russian-Language Reviews of Internet Users on Medications Big Data and Cognitive Computing; MDPI AG (Switzerland), 6(1), 10 (год публикации - 2022) https://doi.org/10.3390/bdcc6010010	
- Селиванов А.А., Грязнов А.В. Рыбка Р.Б., Сбоев А.Г., Сбоева С.Г., Клюева Ю.А. Relation Extraction from Texts Containing Pharmacologically Significant Information on base of Multilingual Language Models Sissa Medialab Srl, том 429 (год публикации - 2022)
