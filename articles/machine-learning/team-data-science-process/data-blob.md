---
title: Azure blobadatok feldolgozása fejlett elemzéssel - Csapatadat-elemzési folyamat
description: Fedezze fel az adatokat, és hozzon létre funkciókat az Azure Blob storage-ban tárolt adatokból a fejlett elemzések használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721098"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Azure blobadatok feldolgozása bővített analitikával
Ez a dokumentum az Azure Blob storage-ban tárolt adatokból származó adatok feltárásával és szolgáltatások generálásával foglalkozik. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Az adatok betöltése pandas adatkeretbe
Annak érdekében, hogy feltárja és manipulálja az adatkészletet, le kell tölteni a blob forrásból egy helyi fájlba, amely ezután betölthető egy Pandas adatkeretben. Az eljáráshoz az alábbi lépéseket kell végrehajtania:

1. Töltse le az adatokat az Azure blobból a következő Python-kódmintát a Blob szolgáltatás használatával. Cserélje le az alábbi kódban lévő változót az Ön egyedi értékeire: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Olvassa be az adatokat egy Pandas adatkeretbe a letöltött fájlból.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Most már készen áll az adatok feltárására és az adatkészlet funkcióinak létrehozására.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Adatok feltárása
Íme néhány példa az adatok Panda segítségével történő felfedezésének módjaira:

1. Sorok és oszlopok számának vizsgálata 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Vizsgálja meg az adatkészlet első vagy utolsó néhány sorát az alábbiak szerint:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Ellenőrizze, hogy az egyes oszlopok at importált adattípus a következő mintakód használatával importálta-e
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Ellenőrizze az alapstatisztikákat az adatkészlet oszlopaihoz az alábbiak szerint
   
        dataframe_blobdata.describe()
5. Nézd meg az egyes oszlopértékek bejegyzéseinek számát az alábbiak szerint:
   
        dataframe_blobdata['<column_name>'].value_counts()
6. A hiányzó értékek megszámlálása az egyes oszlopokban szereplő bejegyzések tényleges számával szemben a következő mintakód használatával
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Ha az adatok egy adott oszlopához hiányoznak értékek, azokat a következőképpen dobhatja ki:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   A hiányzó értékek cseréjének másik módja a módfunkció:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Hisztogram-ábrázolás létrehozása változószámú tárolóval a változó eloszlásának ábrázolására    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. A változók közötti korrelációk megtekintése scatterplot vagy a beépített korrelációs függvény használatával
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Szolgáltatás generálása
A Python használatával a következőképpen hozhatunk létre funkciókat:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Mutatóérték-alapú szolgáltatásgenerálása
A kategorikus funkciók a következőképpen hozhatók létre:

1. Vizsgálja meg a kategorikus oszlop eloszlását:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Mutatóértékek létrehozása az egyes oszlopértékekhez
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. A jelzőoszlop és az eredeti adatkeret összeillesztése 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Magát az eredeti változót távolítsa el:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Binning funkció létrehozása
A binned funkciók létrehozásához a következőképpen járunk el:

1. Oszlopok sorozatának hozzáadása numerikus oszlop helytárolóhoz
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. A binning átalakítása logikai változók sorozatává
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Végül csatlakoztassa a dummy változókat az eredeti adatkerethez
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Adatok írása az Azure blobba és fogyasztás az Azure Machine Learningben
Miután megvizsgálta az adatokat, és létrehozta a szükséges funkciókat, feltöltheti az adatokat (mintavételezett vagy jellemzőre) egy Azure blobba, és felhasználhatja azokat az Azure Machine Learningben a következő lépésekkel: További funkciók hozhatók létre az Azure Machine Learningben Stúdió (klasszikus) is. 

1. Az adatkeret írása helyi fájlba
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Töltse fel az adatokat az Azure blobba az alábbiak szerint:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Most antól az adatok az Azure Machine Learning [Import Data][import-data] moduljával olvashatók a blobból, ahogy az az alábbi képernyőn látható:

![olvasó blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

