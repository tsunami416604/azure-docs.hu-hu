---
title: Azure Blobadatok feldolgozása bővített analitikával – csoportos adatelemzési folyamat
description: Adatok feltárása és szolgáltatások készítése a fejlett analitika használata az Azure Blob storage-ban tárolt adatok alapján.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721098"
---
# <a name="heading"></a>Azure Blob-adatfeldolgozás speciális analitikai szolgáltatásokkal
Ez a dokumentum ismerteti a megismerését adatok és az Azure Blob storage-ban tárolt adatok alapján előállító szolgáltatások. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Az adatok betöltése az adatok Pandas keret
Az adatkészletek feltárásához és kezeléséhez le kell tölteni a blob-forrásból egy helyi fájlba, amelyet azután be lehet tölteni egy Panda-adatkeretbe. Ez az eljárás követéséhez lépései a következők:

1. Töltse le az Azure Blob adatait az alábbi Python-kóddal Blob service használatával. Az adott értékeket cserélje le az alábbi kódot a változót: 
   
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
2. Az adatokat olvas be egy Pandas-adatkeretbe a letöltött fájl.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Most már készen áll az adatok, és hozzon létre ehhez az adatkészlethez funkcióinak.

## <a name="blob-dataexploration"></a>Adatelemzés
Íme néhány példa többféle módon lehet adatokat Pandas használatával:

1. Vizsgálja meg a sorok és oszlopok száma 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Vizsgálja meg az első vagy utolsó néhány sort az adatkészletben, az alábbi:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Ellenőrizze az adattípust, minden oszlop lett importálva, a következő mintakód segítségével
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Az alapszintű statisztikák az oszlopok az adatkészlet a következő ellenőrzése
   
        dataframe_blobdata.describe()
5. A következő tekintse meg a minden oszlop értékét a bejegyzések száma
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Hiányzó értékek száma és az egyes oszlopokban a következő mintakód segítségével bejegyzések tényleges száma
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Ha egy adott oszlop a hiányzó értékek az adatokban, akkor is el kell dobni ezeket a következő:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Cserélje le a hiányzó értékek másik módja, a függvény a mód:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Dobozok száma változó segítségével jeleníti meg a változó terjesztési hisztogram rajzot létrehozása    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Tekintse meg változókat a teszteredményekből, vagy pedig a beépített korrelációs függvény közötti összefüggéseket
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Szolgáltatás létrehozása
Azt is készítése a Python használatával a következő funkciók:

### <a name="blob-countfeature"></a>Kijelző érték-alapú funkciójának generálása
Kategorikus funkciók módon hozhatók létre:

1. Vizsgálja meg a terjesztési a kategorikus oszlopok:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Kijelző értékek készítése az oszlop értékeit tartalmazza
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Csatlakozzon a kijelző oszlopot az eredeti adathalmaz 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Távolítsa el az eredeti változó maga:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Dobozolási szolgáltatás létrehozása
Binned szolgáltatások létrehozásához, hogy folytassa a következőképpen:

1. Adja hozzá az oszlopok egy numerikus oszlopot bin sorozata
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. A logikai változók a feladatütemezési dobozolás konvertálása
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Végül csatlakoztassa a helyőrző változók térjen vissza az eredeti adathalmaz
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Az Azure blobba való visszaírás és a Azure Machine Learning
Az adatok vizsgálatát és a szükséges funkciók létrehozását követően feltöltheti az adatok (minta vagy featurized) egy Azure-blobba, és felhasználhatja azokat Azure Machine Learning a következő lépések végrehajtásával: további funkciók hozhatók létre a Azure Machine Learning Studio (klasszikus) is. 

1. Helyi fájl az adathalmaz írása
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Töltse fel az adatokat az Azure-blobba a következőképpen:
   
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
3. Most az adatok beolvashatók a blobból a Azure Machine Learning [importálási][import-data] modul használatával, ahogy az alábbi képernyőn látható:

![olvasó blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

