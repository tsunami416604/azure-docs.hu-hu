---
title: Szolgáltatás-szolgáltatás hitelesítése - Data Lake Storage Gen1 - REST API
description: Ismerje meg, hogyan érheti el a szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 és az Azure Active Directory segítségével a REST API használatával.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904514"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 szolgáltatással REST API használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Ebben a cikkben megtudhatja, hogyan használhatja a REST API-t szolgáltatás-szolgáltatás hitelesítéshez az Azure Data Lake Storage Gen1 használatával. A REST API használatával a Data Lake Storage Gen1-tel való végfelhasználói hitelesítést a [Data Lake Storage Gen1 használatával a REST API használatával című](data-lake-store-end-user-authenticate-rest-api.md)témakörben található.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a Szolgáltatás-szolgáltatás hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-service-to-service-authenticate-using-active-directory.md)

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

Ebben a forgatókönyvben az alkalmazás saját hitelesítő adatokat biztosít a műveletek végrehajtásához. Ehhez a következő kódrészletben láthatóhoz hasonló POST-kérelmet kell kiadnia:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A kérelem kimenete tartalmaz egy engedélyezési `access-token` jogkivonatot (az alábbi kimenetben jelezve), amelyet később átad a REST API-hívásoknak. Mentse a hitelesítési jogkivonatot egy szövegfájlba; szüksége lesz rá, amikor REST-hívásokat kezdeményez a Data Lake Storage Gen1-hez.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Ez a cikk a **nem interaktív** módszert alkalmazza. További információk a nem interaktív (szolgáltatások közötti) hívásokról: [Szolgáltatások közötti hívások hitelesítő adatok használatával](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a szolgáltatás-szolgáltatás hitelesítést a Data Lake Storage Gen1 használatával a REST API használatával. Most már tekintse meg a következő cikkeket, amelyek arról beszélnek, hogyan használhatja a REST API-t a Data Lake Storage Gen1 használatához.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 szolgáltatáson rest API használatával](data-lake-store-get-started-rest-api.md)
* [Adatműveletek a Data Lake Storage Gen1 szolgáltatáson rest API használatával](data-lake-store-data-operations-rest-api.md)
