
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Az Azure Data Factory egy adatintegrációs szolgáltatás. Lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre a felhőben. A munkafolyamatok egy vagy több *folyamatként* valósulnak meg. A folyamatok összehangolják és automatizálják az adatok áthelyezését és átalakítását. A folyamatok a következő lépéseket hajtják végre az adatokkal:

1. Beolvassák az adatokat a különböző adattárolókból.
2. Átalakítják vagy feldolgozzák az adatokat, például a következő számítási szolgáltatásokkal:
    - Azure HDInsight Hadoop
    - Spark
    - Azure Data Lake Analytics
    - Azure Machine Learning
3. Közzéteszik a kimeneti adatokat az adattárakban.
    - A közzététel célpontja lehet például egy Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják az adatokat. 

A folyamatokat a Data Factory használatával ütemezheti.

