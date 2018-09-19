---
title: 'REST API: Fiókkezelési műveletek az Azure Data Lake Storage Gen1 |} A Microsoft Docs'
description: Fiókkezelési műveletek végrehajtása a Data Lake Storage Gen1 fiók az Azure Data Lake Storage Gen1 és a WebHDFS REST API használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7f22fe7d1c3962e59922bc4e2795ed4f899e3eca
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121668"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Fiókkezelési műveletek az Azure Data Lake Storage Gen1 REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ebben a cikkben megismerheti, hogyan hajthat végre a fiókkezelési műveletek az Azure Data Lake Storage Gen1 a REST API használatával. Fiókkezelési műveletek közé tartozik, egy Data Lake Storage Gen1 törlése egy Data Lake Storage Gen1 fiók, stb-fiók létrehozása. Hogyan hajthat végre fájlrendszerműveleteket a Data Lake Storage Gen1 REST API-val kapcsolatos utasításokért lásd: [fájlrendszerműveletek a Data Lake Storage Gen1 REST API-val](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Ez a cikk a curl használatával bemutatják, hogyan lehet REST API-hívásokat a Data Lake Storage Gen1 fiók ellen.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás (interaktív) végfelhasználói hitelesítésével kapcsolatban lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-end-user-authenticate-rest-api.md).
* (Nem interaktív) az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Hozzon létre egy Data Lake Storage Gen1 fiókot
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/create) definiált REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le  **\<yourstoragegen1name >** a Data Lake Storage Gen1 nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

A fenti parancsban cserélje le a(z) \<`REDACTED`\> részt a korábban kapott engedélyezési jogkivonatra. A kérelem hasznos adatai ezen parancs esetében az **input.json** fájlban találhatók, amely a fenti `-d` paraméterhez lett megadva. Az input.json fájl tartalmai az alábbi kódrészlethez hasonlók:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Törölheti a Data Lake Storage Gen1
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete) definiált REST API-híváson alapul.

A következő cURL-parancs segítségével törölheti a Data Lake Storage Gen1. Cserélje le  **\<yourstoragegen1name >** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>További lépések
* [Fájlrendszerműveletek a Data Lake Storage Gen1 REST API-val](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 REST API-referencia](https://docs.microsoft.com/rest/api/datalakestore/)
* [Nyílt forráskódú Big Data-alkalmazások kompatibilis az Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

