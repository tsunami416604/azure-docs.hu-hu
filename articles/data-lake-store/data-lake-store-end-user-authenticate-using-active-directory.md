---
title: 'Végfelhasználói hitelesítés: Azure Data Lake Storage Gen1 az Azure Active Directoryban |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 Azure Active Directory használatával
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
ms.openlocfilehash: 2f0638b2449bfd582cb68e26d2043b7bc85342b6
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125952"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 Azure Active Directory használatával
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Az Azure Data Lake Storage Gen1 az Azure Active Directory-hitelesítéshez. Szerzői olyan alkalmazás, amely a Data Lake Storage Gen1 vagy az Azure Data Lake Analytics működik, mielőtt el kell döntenie, hogyan hitelesítheti az alkalmazását az Azure Active Directoryval (Azure AD). Két fő az elérhető lehetőségek:

* Végfelhasználói hitelesítés (Ez a cikk)
* Szolgáltatások közötti hitelesítés (ezt a beállítást, a fenti legördülő listából válasszon)

Mindkét ezek a beállítások az alkalmazás éppen biztosított az OAuth 2.0 jogkivonat, amely a Data Lake Storage Gen1 vagy az Azure Data Lake Analytics felé irányuló kérések csatlakoztatható eredményez.

Ez a cikk ismerteti hogyan hozhat létre egy **végfelhasználói hitelesítés natív Azure AD-alkalmazást**. Az Azure AD-alkalmazás beállítása a szolgáltatások közötti hitelesítéshez, lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Az előfizetés-azonosítójára. Azt az Azure Portalon kérheti le. Ha például érhető el a Data Lake Storage Gen1 fiók panelen.
  
    ![Előfizetés-azonosító beszerzése](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Az Azure AD-tartomány neve. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. Az alábbi képernyőképen a tartománynév van **contoso.onmicrosoft.com**, és a globálisan egyedi Azonosítót zárójelben a bérlő azonosítója. 
  
    ![Első AAD-tartomány](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Az Azure-bérlő azonosítóját. A Bérlőazonosító lekéréséhez kapcsolatos utasításokért lásd: [a Bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Ez a hitelesítési mechanizmus használata az ajánlott módszer, ha azt szeretné, hogy a végfelhasználó jelentkezzen be az alkalmazás Azure AD-n keresztül. Az alkalmazás ezután is képes az ugyanazt a hozzáférési szintet, a felhasználó bejelentkezett az Azure-erőforrások eléréséhez. A végfelhasználó adja meg a hitelesítő adatokat rendszeres időközönként, hogy az alkalmazás megtartásához sorrendben kell.

A végfelhasználói bejelentkezési kellene eredménye, hogy az alkalmazás egy hozzáférési jogkivonatot, és a egy frissítési jogkivonat van-e megadva. Data Lake Storage Gen1 vagy a Data Lake Analytics felé irányuló kérések kapcsolódik a hozzáférési jogkivonatot, és alapértelmezés szerint egy órán keresztül érvényes legyen. A frissítési jogkivonat segítségével szerezzen be egy új hozzáférési jogkivonatot, és alapértelmezés szerint legfeljebb két hétig érvényes legyen. A végfelhasználói bejelentkezési két különböző megközelítést használhat.

### <a name="using-the-oauth-20-pop-up"></a>Az OAuth 2.0 előugró ablak használatával
Az alkalmazás is indíthat egy OAuth 2.0 engedélyezési előugró, amelyben a felhasználó megadhatja a hitelesítő adatait. Ez az előugró ablak az Azure AD-kétfaktoros hitelesítés (2FA) folyamattal is működik, ha szükséges. 

> [!NOTE]
> Ez a metódus még nem támogatott a az Azure AD Authentication Library (ADAL) for Python vagy Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Ha közvetlenül továbbítja a felhasználói hitelesítő adatok
Az alkalmazás az Azure AD közvetlenül biztosíthat felhasználói hitelesítő adatokat. Ez a módszer csak a szervezeti azonosító felhasználói fiókok; használható már nem kompatibilis a személyes / "live ID" felhasználói fiókok, ideértve a fiókokat, a befejezési @outlook.com vagy @live.com. Továbbá a metódus nem kompatibilis az Azure AD-kétfaktoros hitelesítés (2FA) igénylő felhasználói fiókokat.

### <a name="what-do-i-need-for-this-approach"></a>Mi szükséges a módszer?
* Az Azure AD-tartomány neve. Ez a követelmény már szerepel ebben a cikkben az előfeltételként szükséges szoftvert.
* Az Azure AD-bérlő azonosítója. Ez a követelmény már szerepel ebben a cikkben az előfeltételként szükséges szoftvert.
* Az Azure AD **natív alkalmazás**
* Az Azure AD natív alkalmazással az alkalmazás azonosítója
* Átirányítási URI-t az Azure AD natív alkalmazás
* Delegált engedélyek beállítása


## <a name="step-1-create-an-active-directory-native-application"></a>1. lépés: Az Active Directory natív alkalmazás létrehozása

Létrehozhat és konfigurálhat egy natív Azure AD-alkalmazást, a végfelhasználói hitelesítés a Data Lake Storage Gen1 Azure Active Directory használatával. Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../azure-resource-manager/resource-group-create-service-principal-portal.md).

A hivatkozás utasításait, miközben mindenképpen jelölje ki **natív** alkalmazás típusához, az alábbi képernyőképen látható módon:

![Webalkalmazás létrehozása](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "natív alkalmazás létrehozása")

## <a name="step-2-get-application-id-and-redirect-uri"></a>2. lépés: Alkalmazás Azonosítójának lekéréséhez és átirányítási URI

Lásd: [alkalmazás Azonosítójának lekéréséhez](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) lekérni az alkalmazás azonosítója.

Az átirányítási URI azonosító lekéréséhez kövesse az alábbi lépéseket.

1. Az Azure Portalon, válassza ki a **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a létrehozott natív Azure AD-alkalmazást.

2. Az a **beállítások** az alkalmazás paneljén kattintson **átirányítási URI-k**.

    ![Get-átirányítási URI-ja](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Másolja a megjelenített érték.


## <a name="step-3-set-permissions"></a>3. lépés: Engedélyek beállítása

1. Az Azure Portalon, válassza ki a **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a létrehozott natív Azure AD-alkalmazást.

2. Az a **beállítások** az alkalmazás paneljén kattintson **szükséges engedélyek**, és kattintson a **Hozzáadás**.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Az a **API-hozzáférés hozzáadása** panelen kattintson a **API kiválasztása**, kattintson a **az Azure Data Lake**, és kattintson a **kiválasztása**.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Az a **API-hozzáférés hozzáadása** panelen kattintson a **engedélyek kiválasztása**, jelölje be a jelölőnégyzetet, hogy **teljes hozzáférés az Data Lake Store**, és kattintson a **kiválasztása**.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kattintson a **Done** (Kész) gombra.

5. Ismételje meg az utolsó két lépést vonatkozó engedélyeket **Windows Azure szolgáltatásfelügyeleti API** is.
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy natív Azure AD-alkalmazást, és a szükséges információkat az ügyfélalkalmazások számára a .NET SDK-t, a Java SDK, REST API-t és egyéb használatával szerzői gyűjtött. Most már folytathatja a következő cikkeket, hogy hogyan használható az Azure AD-webalkalmazás első hitelesítés a Data Lake Storage Gen1 és végezze el a tároló más műveleteket beszélni.

* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 Java SDK használatával](data-lake-store-end-user-authenticate-java-sdk.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 Python használatával](data-lake-store-end-user-authenticate-python.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 REST API használatával](data-lake-store-end-user-authenticate-rest-api.md)

