---
title: 'REST API: Fiókkezelési műveletek az Azure Data Lake Store-ban | Microsoft Docs'
description: Fiókkezelési műveletek végrehajtása a Data Lake Store-ban az Azure Data Lake Store és a WebHDFS REST API használatával
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
ms.openlocfilehash: 1b8b680736c49c0036600bf4337da6952cf38afb
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573639"
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Fiókkezelési műveletek az Azure Data Lake Store-ban a REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ebből a cikkből megtudhatja, hogyan történik a fiókkezelési műveletek végrehajtása az Azure Data Lake Store-ban a REST API használatával. Fiókkezelési művelet lehet többek között a Data Lake Store-fiókok létrehozása vagy törlése. A Data Lake Store fájlrendszerműveleteinek a REST API használatával való végrehajtásával kapcsolatban lásd: [Fájlrendszerműveletek a Data Lake Store-on a REST API használatával](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API-hívásokat indítani a Data Lake Store-fiókra.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás (interaktív) végfelhasználói hitelesítésével kapcsolatban lásd: [Végfelhasználói hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-end-user-authenticate-rest-api.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban (nem interaktív) lásd: [Szolgáltatások közötti hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-store-account"></a>Data Lake Store-fiók létrehozása
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/create) definiált REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

A fenti parancsban cserélje le a(z) \<`REDACTED`\> részt a korábban kapott engedélyezési jogkivonatra. A kérelem hasznos adatai ezen parancs esetében az **input.json** fájlban találhatók, amely a fenti `-d` paraméterhez lett megadva. Az input.json fájl tartalmai az alábbi kódrészlethez hasonlók:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Data Lake Store-fiók törlése
Ez a művelet az [itt](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete) definiált REST API-híváson alapul.

Az alábbi cURL-parancs segítségével törölheti a Data Lake Store-fiókot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>További lépések
* [Fájlrendszerműveletek a Data Lake Store-on a REST API használatával](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Store REST API-referencia](https://docs.microsoft.com/rest/api/datalakestore/)
* [Az Azure Data Lake Store-ral kompatibilis nyílt forráskódú big data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)

