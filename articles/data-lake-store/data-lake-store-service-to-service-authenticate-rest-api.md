---
title: Szolgáltatások közötti hitelesítés – Data Lake Storage Gen1 – REST API
description: Megtudhatja, hogyan valósítható meg a szolgáltatás és a szolgáltatás közötti hitelesítés Azure Data Lake Storage Gen1 és Azure Active Directory a REST API használatával.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904514"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Szolgáltatások közötti hitelesítés Azure Data Lake Storage Gen1 használatával REST API
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Ebből a cikkből megtudhatja, hogyan használhatja a REST API a szolgáltatások közötti hitelesítés végrehajtásához Azure Data Lake Storage Gen1 használatával. A Data Lake Storage Gen1 REST API használatával történő végfelhasználói hitelesítéssel kapcsolatban lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást**. A Data Lake Storage Gen1 Azure Active Directory használatával kell végrehajtania a [szolgáltatások közötti hitelesítéshez](data-lake-store-service-to-service-authenticate-using-active-directory.md)szükséges lépéseket.

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

Ebben az esetben az alkalmazás a saját hitelesítő adatait biztosítja a műveletek végrehajtásához. Ehhez az alábbi kódrészletben láthatóhoz hasonló POST-kérelmet kell kiadnia:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A kérelem kimenete tartalmaz egy engedélyezési jogkivonatot (amelyet az alábbi kimenetben lévő `access-token` jelöl), amelyet később a REST API-hívásokkal továbbít. Mentse a hitelesítési jogkivonatot szövegfájlba; szüksége lesz rá, amikor Data Lake Storage Gen1 REST-hívásokat végez.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Ez a cikk a **nem interaktív** módszert alkalmazza. További információk a nem interaktív (szolgáltatások közötti) hívásokról: [Szolgáltatások közötti hívások hitelesítő adatok használatával](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használható a szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 REST API használatával történő hitelesítéshez. A következő cikkekből megtudhatja, hogyan használhatja a REST API a Data Lake Storage Gen1 használatához.

* [Fiókkezelés Data Lake Storage Gen1 a REST API használatával](data-lake-store-get-started-rest-api.md)
* [Az adatműveletek Data Lake Storage Gen1 a REST API használatával](data-lake-store-data-operations-rest-api.md)
