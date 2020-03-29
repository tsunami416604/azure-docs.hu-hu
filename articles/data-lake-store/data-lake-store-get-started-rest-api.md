---
title: 'REST API: Fiókkezelési műveletek az Azure Data Lake Storage Gen1 szolgáltatáson | Microsoft dokumentumok'
description: Az Azure Data Lake Storage Gen1 és a WebHDFS REST API használatával fiókkezelési műveleteket hajthat végre a Data Lake Storage Gen1 fiókban
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877105"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Fiókkezelési műveletek az Azure Data Lake Storage Gen1 szolgáltatáson a REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ebben a cikkben megtudhatja, hogyan hajthatja végre a fiókkezelési műveleteket az Azure Data Lake Storage Gen1 a REST API használatával. A fiókkezelési műveletek közé tartozik a Data Lake Storage Gen1 fiók létrehozása, a Data Lake Storage Gen1 fiók törlése stb. A DATA Lake Storage Gen1 FÁJLrendszer-műveleteinek REST API használatával történő végrehajtásáról a [Data Lake Storage Gen1 fájlrendszer-műveletei REST API használatával kapcsolatos tudnivalókat.](data-lake-store-data-operations-rest-api.md)

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL .](https://curl.haxx.se/)** Ez a cikk cURL-t használ, hogy bemutassa, hogyan lehet REST API-hívásokat egy Data Lake Storage Gen1 fiók ellen.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás (interaktív) végfelhasználói hitelesítése a [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával .NET SDK használatával](data-lake-store-end-user-authenticate-rest-api.md)című témakörben található.
* Az alkalmazás szolgáltatás-szolgáltatás hitelesítése (nem interaktív) a [Szolgáltatás-szolgáltatás hitelesítése a Data Lake Storage Gen1 szolgáltatással a .NET SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md)című témakörben található.


## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/create) definiált REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje ** \<le storagegen1name>** a Data Lake Storage Gen1 nevét.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

A fenti parancsban cserélje le a(z) \<`REDACTED`\> részt a korábban kapott engedélyezési jogkivonatra. A kérelem hasznos adatai ezen parancs esetében az **input.json** fájlban találhatók, amely a fenti `-d` paraméterhez lett megadva. Az input.json fájl tartalmai az alábbi kódrészlethez hasonlók:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 fiók törlése
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete) definiált REST API-híváson alapul.

A következő cURL-paranccsal törölheti a Data Lake Storage Gen1-fiókot. Cserélje ** \<le storagegen1name>-t** a Data Lake Storage Gen1 fiók nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>További lépések
* [A Data Lake Storage Gen1 fájlrendszer-műveletei REST API használatával.](data-lake-store-data-operations-rest-api.md)

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Storage Gen1 REST API-referencia](https://docs.microsoft.com/rest/api/datalakestore/)
* [Az Azure Data Lake Storage Gen1-tal kompatibilis nyílt forráskódú Big Data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)

