---
title: 'Végfelhasználói hitelesítés: REST API-t Azure Data Lake Storage Gen1 az Azure Active Directory |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a REST API használatával az Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 1e952e32142672946fa987b763032dad66f564a9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537881"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a REST API használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megismerkedhet a REST API használata az Azure Data Lake Storage Gen1 végfelhasználói hitelesítés végrehajtásához. Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 REST API használatával, lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 REST API-val](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre a "Natív" Azure Active Directory-alkalmazás**. El kell végeznie a lépések [végfelhasználói hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. Ez a cikk a curl használatával bemutatják, hogyan lehet REST API-hívásokat a Data Lake Storage Gen1 fiók ellen.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Végfelhasználói hitelesítés használata az ajánlott módszer, ha azt szeretné, hogy a felhasználót, hogy jelentkezzen be az alkalmazás Azure AD használatával. Az alkalmazás el tudja-e a bejelentkezett felhasználóéval megegyező hozzáférési szinttel rendelkező Azure-erőforrások eléréséhez. A felhasználónak kell sorrendben rendszeres időközönként, hogy az alkalmazás karbantartása hozzáférés megadására.

A végfelhasználói bejelentkezési kellene eredménye, hogy az alkalmazás egy hozzáférési jogkivonatot, és a egy frissítési jogkivonat van-e megadva. Data Lake Storage Gen1 vagy a Data Lake Analytics felé irányuló kérések kapcsolódik a hozzáférési jogkivonatot, és alapértelmezés szerint egy órán keresztül érvényes legyen. A frissítési jogkivonatok használhatók egy új hozzáférési jogkivonat beszerzése, és ha rendszeresen használt alapértelmezés szerint legfeljebb két hétig érvényes legyen. A végfelhasználói bejelentkezési két különböző megközelítést használhat.

Ebben az esetben az alkalmazás bejelentkezésre kéri a felhasználót, és minden művelet a felhasználó kontextusában lesz végrehajtva. Hajtsa végre az alábbi lépéseket:

1. Az alkalmazáson keresztül irányítsa át a felhasználót az alábbi URL-címre:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > A \<REDIRECT-URI> értéket kódolni kell az URL-ben való használatra. Tehát például https://localhost, használjon `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    A jelen oktatóanyagban kicserélheti a fenti URL-ben szereplő helyőrző értékeket, és beillesztheti egy webböngésző címsorába. A rendszer átirányítja az Azure bejelentkezési azonosítójával történő hitelesítéshez. Miután sikeresen bejelentkezett, a válasz megjelenik a böngésző címsorában. A válasz az alábbi formátumban jelenik meg:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Rögzítse a válaszban szereplő engedélyezési kódot. Ebben az oktatóanyagban átmásolhatja a hozzáférési kód a webböngészőben, és azt a BEJEGYZÉSBEN található a jogkivonat-végpont kérése pass címsorában az alábbi kódrészletben látható módon:
   
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

3. A válasz egy JSON-objektum, amely tartalmazza a hozzáférési jogkivonat (például `"access_token": "<ACCESS_TOKEN>"`) és egy frissítési (például `"refresh_token": "<REFRESH_TOKEN>"`). Az alkalmazás használja a hozzáférési jogkivonatot az Azure Data Lake Storage Gen1 és a frissítési jogkivonat elérésekor új hozzáférési jogkivonat beszerzéséhez, ha a hozzáférési jogkivonat lejár.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Ha a hozzáférési jogkivonat lejár, kérhet egy új hozzáférési jogkivonatot a frissítési jogkivonat használatával, az alábbi kódrészletben látható módon:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

További információk az interaktív felhasználói hitelesítéssel kapcsolatban: [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Az engedélyezési kód engedélyezési folyamata).
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan service-to-service-hitelesítés használata az Azure Data Lake Storage Gen1 hitelesítést, a REST API használatával. Most már megtekintheti a következő cikkek, amelyek beszélgetnek az Azure Data Lake Storage Gen1 használata a REST API használatával.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 REST API használatával](data-lake-store-get-started-rest-api.md)
* [A Data Lake Storage Gen1 Adatműveletek – REST API használatával](data-lake-store-data-operations-rest-api.md)

