---
title: Azure Data Lake Storage Gen1-fiók kezelése REST-tel
description: A WebHDFS-REST API használatával elvégezheti a fiókok felügyeleti műveleteit egy Azure Data Lake Storage Gen1-fiókon.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: cc3f848dc0718e1d721cecbfeb53ca034580ae81
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85509253"
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
* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **[curl](https://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API hívásokat kezdeményezni egy Data Lake Storage Gen1-fiókkal.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás végfelhasználói hitelesítéséhez (interaktív) lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-end-user-authenticate-rest-api.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez (nem interaktív) lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/create) definiált REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstoragegen1name>** a Data Lake Storage Gen1 nevét.

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

Data Lake Storage Gen1 fiók törléséhez használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstoragegen1name>** a Data Lake Storage Gen1 fiók nevét.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Következő lépések
* [Fájlrendszer-műveletek Data Lake Storage Gen1 a REST API használatával](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Storage Gen1 REST API-hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/)
* [Azure Data Lake Storage Gen1-kompatibilis nyílt forráskódú Big adatalkalmazások](data-lake-store-compatible-oss-other-applications.md)

