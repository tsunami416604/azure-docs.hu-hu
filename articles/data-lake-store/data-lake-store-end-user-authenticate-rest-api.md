---
title: 'Végfelhasználói hitelesítés: REST API az Azure Data Lake Storage Gen1 szolgáltatással az Azure Active Directory használatával | Microsoft dokumentumok'
description: Megtudhatja, hogyan valósítható meg a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával rest API használatával
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
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877822"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 használatával REST API használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megtudhatja, hogyan használhatja a REST API-t az Azure Data Lake Storage Gen1 végfelhasználói hitelesítéséhez. A SERVICE-To-service hitelesítés a Data Lake Storage Gen1 rest API használatával, lásd: [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 rest API használatával.](data-lake-store-service-to-service-authenticate-rest-api.md)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a végfelhasználói hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-end-user-authenticate-using-active-directory.md)

* **[cURL .](https://curl.haxx.se/)** Ez a cikk cURL-t használ, hogy bemutassa, hogyan lehet REST API-hívásokat egy Data Lake Storage Gen1 fiók ellen.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Végfelhasználói hitelesítés az ajánlott megközelítés, ha azt szeretné, hogy egy felhasználó jelentkezzen be az alkalmazásba az Azure AD használatával. Az alkalmazás ugyanolyan szintű hozzáféréssel érheti el az Azure-erőforrásokat, mint a bejelentkezett felhasználó. A felhasználónak rendszeresen meg kell adnia a hitelesítő adatait ahhoz, hogy az alkalmazás fenntarthassa a hozzáférést.

A végfelhasználói bejelentkezés eredménye, hogy az alkalmazás kap egy hozzáférési jogkivonatot és egy frissítési jogkivonatot. A hozzáférési jogkivonat a Data Lake Storage Gen1 vagy a Data Lake Analytics minden egyes kérelemhez kapcsolódik, és alapértelmezés szerint egy óráig érvényes. A frissítési jogkivonat egy új hozzáférési jogkivonat beszerzésére használható, és alapértelmezés szerint legfeljebb két hétig érvényes, ha rendszeresen használják. A végfelhasználói bejelentkezéshez két különböző megközelítést használhat.

Ebben az esetben az alkalmazás bejelentkezésre kéri a felhasználót, és minden művelet a felhasználó kontextusában lesz végrehajtva. Hajtsa végre a következő lépéseket:

1. Az alkalmazáson keresztül irányítsa át a felhasználót az alábbi URL-címre:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > A \<REDIRECT-URI> értéket kódolni kell az URL-ben való használatra. Tehát, https://localhosta `https%3A%2F%2Flocalhost`, használatra )

    A jelen oktatóanyagban kicserélheti a fenti URL-ben szereplő helyőrző értékeket, és beillesztheti egy webböngésző címsorába. A rendszer átirányítja az Azure bejelentkezési azonosítójával történő hitelesítéshez. Miután sikeresen bejelentkezett, a válasz megjelenik a böngésző címsorában. A válasz az alábbi formátumban jelenik meg:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Rögzítse a válaszban szereplő engedélyezési kódot. Ebben az oktatóanyagban az engedélyezési kódot a webböngésző címsorából másolhatja, és a POST-kérelemben átadhatja a token végpontnak, ahogy az a következő kódrészletben látható:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > Ebben az esetben a \<REDIRECT-URI> kódolása nem szükséges.
   > 
   > 

3. A válasz egy JSON-objektum, amely egy `"access_token": "<ACCESS_TOKEN>"`hozzáférési jogkivonatot (például `"refresh_token": "<REFRESH_TOKEN>"`) és egy frissítési jogkivonatot (például) tartalmaz. Az alkalmazás a hozzáférési jogkivonatot használja az Azure Data Lake Storage Gen1 elérésekor, és a frissítési jogkivonatot egy másik hozzáférési jogkivonat betöltéséhez, amikor egy hozzáférési jogkivonat lejár.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Amikor a hozzáférési jogkivonat lejár, a frissítési jogkivonat használatával kérhet új hozzáférési jogkivonatot, ahogy az a következő kódrészletben látható:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

További információk az interaktív felhasználói hitelesítéssel kapcsolatban: [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Az engedélyezési kód engedélyezési folyamata).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 rest API használatával. Most már tekintse meg az alábbi cikkeket, amelyek arest API használatával az Azure Data Lake Storage Gen1 használatával kapcsolatosak.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 szolgáltatáson rest API használatával](data-lake-store-get-started-rest-api.md)
* [Adatműveletek a Data Lake Storage Gen1 szolgáltatáson rest API használatával](data-lake-store-data-operations-rest-api.md)

