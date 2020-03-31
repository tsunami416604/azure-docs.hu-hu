---
title: 'REST API: Fájlrendszer-műveletek az Azure Data Lake Storage Gen1 szolgáltatáson | Microsoft dokumentumok'
description: Fájlrendszer-műveletek végrehajtása a WebHDFS REST API-k használatával az Azure Data Lake Storage Gen1 szolgáltatáson
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878783"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Fájlrendszer-műveletek az Azure Data Lake Storage Gen1 szolgáltatáson rest API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebben a cikkben megtudhatja, hogyan használhatja a WebHDFS REST API-k és a Data Lake Storage Gen1 REST API-kat az Azure Data Lake Storage Gen1 fájlrendszer-műveletek végrehajtásához. A Data Lake Storage Gen1 REST API használatával végzett fiókkezelési műveletekről a [Data Lake Storage Gen1 fiókkezelési műveletei rest API használatával kapcsolatos tudnivalókat.](data-lake-store-get-started-rest-api.md)

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 fiók.** Kövesse az [Azure Data Lake Storage Gen1 használatának első lépéseit az Azure Portal használatával című útmutatóban.](data-lake-store-get-started-portal.md)

* **[cURL .](https://curl.haxx.se/)** Ez a cikk cURL-t használ, hogy bemutassa, hogyan lehet REST API-hívásokat egy Data Lake Storage Gen1 fiók ellen.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás (interaktív) végfelhasználói hitelesítése a [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával .NET SDK használatával](data-lake-store-end-user-authenticate-rest-api.md)című témakörben található.
* Az alkalmazás szolgáltatás-szolgáltatás hitelesítése (nem interaktív) a [Szolgáltatás-szolgáltatás hitelesítése a Data Lake Storage Gen1 szolgáltatással a .NET SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md)című témakörben található.


## <a name="create-folders"></a>Mappák létrehozása
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje ** \<le az üzlet nevét>** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Az előző parancsban cserélje le a \<`REDACTED`\> részt a korábban kapott hitelesítő jogkivonatra. Ez a parancs létrehoz egy **mytempdir** nevű könyvtárat a Data Lake Storage Gen1 fiók gyökérmappája alatt.

Ha a művelet sikeresen befejeződik, a következő kódrészlethez hasonló választ kell kapnia:

    {"boolean":true}

## <a name="list-folders"></a>Mappák listázása
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje ** \<le az üzlet nevét>** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

Az előző parancsban cserélje le a \<`REDACTED`\> részt a korábban kapott hitelesítő jogkivonatra.

Ha a művelet sikeresen befejeződik, a következő kódrészlethez hasonló választ kell kapnia:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Adatok feltöltése
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje ** \<le az üzlet nevét>** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

Az előző szintaxisban a **-T** paraméter adja meg a feltöltendő fájl helyét.

A kimenet a következő kódrészlethez hasonló:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Adatok olvasása
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) definiált WebHDFS REST API-híváson alapul.

Adatok olvasása egy Data Lake Storage Gen1 fiók egy kétlépéses folyamat.

* Először küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` végponthoz. Ez a hívás visszaadja azt a helyet, ahová a következő GET kérelmet kell küldenie.
* Ezután küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` végponthoz. Ez a hívás megjeleníti a fájl tartalmát.

Mivel azonban az első és második lépésben nincs különbség a bemeneti paraméterek között, az első kérelem elküldéséhez használhatja az `-L` paramétert. A `-L` kapcsoló lényegében egyesít két kérelmet, és megismételteti a kérelmet a cURL-paranccsal az új helyen. Végül megjelenik az összes kérelemhívás kimenete az alábbi kódrészletben látható módon. Cserélje ** \<le az üzlet nevét>** a Data Lake Storage Gen1 fiók nevére.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Fájl átnevezése
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl átnevezéséhez használja a következő cURL-parancsot. Cserélje ** \<le az üzlet nevét>** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Fájl törlése
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl törléséhez használja a következő cURL-parancsot. Cserélje ** \<le az üzlet nevét>** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

A következőhöz hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>További lépések
* [A Data Lake Storage Gen1 fiókkezelési műveletei rest api használatával.](data-lake-store-get-started-rest-api.md)

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Storage Gen1 REST API-referencia](https://docs.microsoft.com/rest/api/datalakestore/)
* [Az Azure Data Lake Storage Gen1-tal kompatibilis nyílt forráskódú Big Data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)

