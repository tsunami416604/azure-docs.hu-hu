---
title: 'REST API: Fiókkezelés-műveletek Azure Data Lake Storage Gen1on | Microsoft Docs'
description: A Azure Data Lake Storage Gen1 és a WebHDFS REST API használatával hajthatja végre a fiók-felügyeleti műveleteket a Data Lake Storage Gen1 fiókban
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "60877105"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Fiókkezelés Azure Data Lake Storage Gen1 a REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ebből a cikkből megtudhatja, hogyan hajthat végre Fiókkezelés-műveleteket Azure Data Lake Storage Gen1 a REST API használatával. A fiókkezelés többek között Data Lake Storage Gen1 fiók létrehozását, Data Lake Storage Gen1 fiók törlését stb. A fájlrendszerbeli műveletek REST API használatával történő végrehajtásával kapcsolatos utasításokért tekintse meg a következőt: [Data Lake Storage Gen1 a REST API](data-lake-store-data-operations-rest-api.md)Data Lake Storage Gen1.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **[curl](https://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API hívásokat kezdeményezni egy Data Lake Storage Gen1-fiókkal.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás végfelhasználói hitelesítéséhez (interaktív) lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-end-user-authenticate-rest-api.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez (nem interaktív) lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/create) definiált REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le ** \<a yourstoragegen1name>** a Data Lake Storage Gen1 nevére.

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

Data Lake Storage Gen1 fiók törléséhez használja a következő cURL-parancsot. Cserélje le ** \<a yourstoragegen1name>** az Data Lake Storage Gen1-fiók nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>További lépések
* [Fájlrendszer-műveletek Data Lake Storage Gen1 a REST API használatával](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Storage Gen1 REST API-hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/)
* [Azure Data Lake Storage Gen1-kompatibilis nyílt forráskódú Big adatalkalmazások](data-lake-store-compatible-oss-other-applications.md)

