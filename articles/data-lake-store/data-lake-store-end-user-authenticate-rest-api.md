---
title: "Végfelhasználói hitelesítési: REST API-t a Data Lake Store az Azure Active Directoryval |} Microsoft Docs"
description: "Megtudhatja, hogyan végfelhasználói hitelesítési elérése a Data Lake Store REST API használatával Azure Active Directory használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5ee13756e4276055a8c9bdd9642d0766c3ca7e5c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-rest-api"></a>A Data Lake Store REST API használatával végfelhasználói hitelesítés
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebből a cikkből megismerheti a végfelhasználói hitelesítést az Azure Data Lake Store REST API használatával kapcsolatos. A szolgáltatások közötti hitelesítéshez a Data Lake Store REST API használatával, lásd: [szolgáltatások közötti hitelesítés a Data Lake Store REST API használatával](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy "Natív" Azure Active Directory-alkalmazás**. Végrehajtotta a lépéseket [végfelhasználói hitelesítés az Azure Active Directory használatával a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](http://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API-hívásokat indítani a Data Lake Store-fiókra.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Végfelhasználói hitelesítés használata javasolt, ha azt szeretné, hogy a felhasználónak be kell jelentkeznie az alkalmazásba, az Azure AD. Az alkalmazás el tudja-e a bejelentkezett felhasználóéval megegyező hozzáférési szinttel rendelkező Azure-erőforrások eléréséhez. A felhasználónak kell ahhoz, hogy az alkalmazás fér a rendszeres időközönként a hitelesítő adatok megadását.

Az eredmény, hogy a végfelhasználói bejelentkezés, hogy az alkalmazás olyan hozzáférési jogkivonatot, és egy frissítési jogkivonat. A hozzáférési jogkivonatot minden Data Lake Store vagy a Data Lake Analytics kérelmet lekérdezi kapcsolni, és alapértelmezés szerint egy órán keresztül érvényes legyen. A frissítési jogkivonat segítségével szerezzen be egy új jogkivonatot, és alapértelmezés szerint legfeljebb két hétig érvényes rendszeresen használatakor. Két különböző szempontok a végfelhasználói bejelentkezés is használhatja.

Ebben az esetben az alkalmazás bejelentkezésre kéri a felhasználót, és minden művelet a felhasználó kontextusában lesz végrehajtva. Hajtsa végre a következő lépéseket:

1. Az alkalmazáson keresztül irányítsa át a felhasználót az alábbi URL-címre:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > A \<REDIRECT-URI> értéket kódolni kell az URL-ben való használatra. A https://localhost esetében tehát használja a következőt: `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    A jelen oktatóanyagban kicserélheti a fenti URL-ben szereplő helyőrző értékeket, és beillesztheti egy webböngésző címsorába. A rendszer átirányítja az Azure bejelentkezési azonosítójával történő hitelesítéshez. Miután sikeresen bejelentkezett, a válasz megjelenik a böngésző címsorában. A válasz az alábbi formátumban jelenik meg:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Rögzítse a válaszban szereplő engedélyezési kódot. Ebben az oktatóanyagban átmásolhatja az engedélyezési kód a böngészőt, és azt a POST kérése a jogkivonat végpontjához fázis címsorába látható módon a következő kódrészletet:
   
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

3. A válasz egy JSON-objektum, amely tartalmazza a hozzáférési token (például `"access_token": "<ACCESS_TOKEN>"`) és egy frissítési (például `"refresh_token": "<REFRESH_TOKEN>"`). Az alkalmazás a hozzáférési jogkivonatot az Azure Data Lake Store-hoz való hozzáféréshez, a frissítési jogkivonatot pedig egy új hozzáférési jogkivonat beszerzéséhez használja, amikor az előző lejár.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat használatával új tokenre kérheti, ahogy az a következő kódrészletet:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

További információk az interaktív felhasználói hitelesítéssel kapcsolatban: [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Az engedélyezési kód engedélyezési folyamata).
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, szolgáltatások közötti hitelesítés használata a hitelesítéshez az Azure Data Lake Store REST API használatával. Most már megtekintheti a következő cikkekben talál arról, hogyan működik az Azure Data Lake Store REST API használatával kommunikálhatnak.

* [A Data Lake Store REST API használatával fiókkezelési műveletekhez](data-lake-store-get-started-rest-api.md)
* [A Data Lake Store REST API használatával adatokat műveletek](data-lake-store-data-operations-rest-api.md)

