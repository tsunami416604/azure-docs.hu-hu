---
title: "REST API: Fájlrendszerműveletek az Azure Data Lake Store-ban | Microsoft Docs"
description: "Fájlrendszerműveletek végrehajtása a Data Lake Store-on WebHDFS REST API-k használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 62cfc713c3b7ce8f7c0a7ff4dd5e80802f1843b7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Fájlrendszerműveletek az Azure Data Lake Store-ban a REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebből a cikkből megtudhatja, hogyan hajthat végre fájlrendszerműveleteket a WebHDFS REST API-k és az Azure Data Lake Store REST API-k használatával. A Data Lake Store fiókkezelési műveleteinek REST API használatával való végrehajtásával kapcsolatban lásd: [Fiókkezelési műveletek a Data Lake Store-ban a REST API használatával](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-fiók**. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait.

* **[cURL](http://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API-hívásokat indítani a Data Lake Store-fiókra.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás (interaktív) végfelhasználói hitelesítésével kapcsolatban lásd: [Végfelhasználói hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-end-user-authenticate-rest-api.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban (nem interaktív) lásd: [Szolgáltatások közötti hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Mappák létrehozása
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Az előző parancsban cserélje le a \<`REDACTED`\> részt a korábban kapott hitelesítő jogkivonatra. Ez a parancs egy **mytempdir** nevű könyvtárat hoz létre a Data Lake Store-fiók gyökérkönyvtárában.

Ha a művelet sikeresen befejeződik, a következő kódrészlethez hasonló választ kell kapnia:

    {"boolean":true}

## <a name="list-folders"></a>Mappák listázása
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

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
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

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
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) definiált WebHDFS REST API-híváson alapul.

Az adatok beolvasása a Data Lake Store-fiókból egy kétlépéses folyamat.

* Először küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` végponthoz. Ez a hívás visszaadja azt a helyet, ahová a következő GET kérelmet kell küldenie.
* Ezután küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` végponthoz. Ez a hívás megjeleníti a fájl tartalmát.

Mivel azonban az első és második lépésben nincs különbség a bemeneti paraméterek között, az első kérelem elküldéséhez használhatja az `-L` paramétert. A `-L` kapcsoló lényegében egyesít két kérelmet, és megismételteti a kérelmet a cURL-paranccsal az új helyen. Végül megjelenik az összes kérelemhívás kimenete az alábbi kódrészletben látható módon. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

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
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl átnevezéséhez használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Fájl törlése
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl törléséhez használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

A következőhöz hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Következő lépések
* [Fiókkezelési műveletek a Data Lake Store-ban a REST API használatával](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Lásd még:
* [Azure Data Lake Store REST API-referencia](https://docs.microsoft.com/rest/api/datalakestore/)
* [Az Azure Data Lake Store-ral kompatibilis nyílt forráskódú big data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)

