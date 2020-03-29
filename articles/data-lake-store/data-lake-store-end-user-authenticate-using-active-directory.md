---
title: 'Végfelhasználói hitelesítés: Azure Data Lake Storage Gen1 az Azure Active Directoryval | Microsoft dokumentumok'
description: Megtudhatja, hogyan érhet el végfelhasználói hitelesítést az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával
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
ms.openlocfilehash: 4c2b774c304e46f9fc68f3beaf64218e614ecad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66234059"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 szolgáltatással az Azure Active Directory használatával
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Az Azure Data Lake Storage Gen1 az Azure Active Directoryt használja a hitelesítéshez. A Data Lake Storage Gen1 vagy az Azure Data Lake Analytics alkalmazással működő alkalmazás létrehozása előtt el kell döntenie, hogyan hitelesíti az alkalmazást az Azure Active Directoryval (Azure AD). A két fő lehetőség a következő:

* Végfelhasználói hitelesítés (ebben a cikkben)
* Szolgáltatás-szolgáltatás hitelesítés (válassza ezt a lehetőséget a fenti legördülő menüből)

Mindkét beállítás eredményeképpen az alkalmazás egy OAuth 2.0-s jogkivonatot kap, amely a Data Lake Storage Gen1 vagy az Azure Data Lake Analytics minden egyes kéréshez csatolódik.

Ez a cikk arról szól, hogyan hozhat létre egy **Azure AD natív alkalmazást a végfelhasználói hitelesítéshez.** Az Azure AD-alkalmazások konfigurálásáról a szolgáltatás-szolgáltatás hitelesítéshez a [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 használatával az Azure Active Directory használatával című](data-lake-store-authenticate-using-active-directory.md)témakörben talál útmutatást.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Az előfizetés azonosítója. Lekérheti az Azure Portalon. Például a Data Lake Storage Gen1 fiók panelen érhető el.
  
    ![Előfizetés-azonosító beszerezni](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Az Azure AD tartománynév. Az egér relai vonala az Azure Portal jobb felső sarkában lebeg. Az alábbi képernyőképen a tartománynév **contoso.onmicrosoft.com,** a zárójelben lévő GUID pedig a bérlőazonosító. 
  
    ![AAD-tartomány bekéselése](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Az Azure-beli bérlőazonosító. A bérlői azonosító lekérésének módjáról [a Bérlőazonosító beolvasása](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)című témakörben talál.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Ez a hitelesítési mechanizmus az ajánlott megközelítés, ha azt szeretné, hogy egy végfelhasználó jelentkezzen be az alkalmazásba az Azure AD-n keresztül. Az alkalmazás ezután ugyanolyan szintű hozzáféréssel érheti el az Azure-erőforrásokat, mint a bejelentkezett végfelhasználó. A végfelhasználónak rendszeresen meg kell adnia a hitelesítő adatait ahhoz, hogy az alkalmazás fenntarthassa a hozzáférést.

Az eredmény a végfelhasználói bejelentkezés eredménye, hogy az alkalmazás kap egy hozzáférési jogkivonatot és egy frissítési jogkivonatot. A hozzáférési jogkivonat a Data Lake Storage Gen1 vagy a Data Lake Analytics minden egyes kérelemhez kapcsolódik, és alapértelmezés szerint egy óráig érvényes. A frissítési jogkivonat egy új hozzáférési jogkivonat beszerzésére használható, és alapértelmezés szerint legfeljebb két hétig érvényes. A végfelhasználói bejelentkezéshez két különböző megközelítést használhat.

### <a name="using-the-oauth-20-pop-up"></a>Az OAuth 2.0 előugró ablak használata
Az alkalmazás előugró ablakot indíthat el az OAuth 2.0 engedélyezési ablakban, amelyben a végfelhasználó megadhatja a hitelesítő adatait. Ez az előugró ablak szükség esetén az Azure AD kétfaktoros hitelesítési (2FA) folyamattal is működik. 

> [!NOTE]
> Ez a módszer még nem támogatott az Azure AD hitelesítési könyvtár (ADAL) python vagy Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>A felhasználói hitelesítő adatok közvetlen átadása
Az alkalmazás közvetlenül biztosíthatja a felhasználói hitelesítő adatokat az Azure AD.Your application can directly provide user credentials to Azure AD. Ez a módszer csak szervezeti azonosító felhasználói fiókokkal működik; nem kompatibilis a személyes / "live ID" felhasználói fiókokkal, beleértve a @outlook.com vagy @live.com. Továbbá ez a módszer nem kompatibilis az Azure AD kétfaktoros hitelesítést (2FA) igénylő felhasználói fiókokkal.

### <a name="what-do-i-need-for-this-approach"></a>Mire van szükségem ehhez a megközelítéshez?
* Azure AD tartománynév. Ez a követelmény már szerepel a cikk előfeltételében.
* Azure AD-bérlőazonosító. Ez a követelmény már szerepel a cikk előfeltételében.
* Azure AD **natív alkalmazás**
* Az Azure AD natív alkalmazás alkalmazásazonosítója
* Az Azure AD natív alkalmazás URI-jának átirányítása
* Delegált engedélyek beállítása


## <a name="step-1-create-an-active-directory-native-application"></a>1. lépés: Natív Active Directory-alkalmazás létrehozása

Hozzon létre és konfiguráljon egy Azure AD natív alkalmazást a végfelhasználói hitelesítéshez a Data Lake Storage Gen1 használatával az Azure Active Directory használatával. További információt az [Azure AD-alkalmazás létrehozása című témakörben talál.](../active-directory/develop/howto-create-service-principal-portal.md)

Miközben követi a hivatkozás utasításait, győződjön meg róla, hogy a **Natív** lehetőséget választja az alkalmazás típusához, ahogy az a következő képernyőképen látható:

![Webalkalmazás létrehozása](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Natív alkalmazás létrehozása")

## <a name="step-2-get-application-id-and-redirect-uri"></a>2. lépés: Alkalmazásazonosító beszerezniése és URI-átirányítás

Lásd: [Az alkalmazásazonosító beolvasása](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) az alkalmazásazonosító lekéréséhez.

Az átirányítási URI beolvasásához tegye a következő lépéseket.

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, kattintson az **Alkalmazásregisztrációk**elemre, majd keresse meg és kattintson a létrehozott Azure AD natív alkalmazásra.

2. Az alkalmazás **Beállítások** paneljén kattintson az **Irányított URI-k átirányítása gombra.**

    ![Átirányítási URI beszereznie](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Másolja a megjelenített értéket.


## <a name="step-3-set-permissions"></a>3. lépés: Engedélyek beállítása

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, kattintson az **Alkalmazásregisztrációk**elemre, majd keresse meg és kattintson a létrehozott Azure AD natív alkalmazásra.

2. Az alkalmazás **Beállítások** paneljén kattintson a **Kötelező engedélyek gombra,** majd a **Hozzáadás**gombra.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Az **API Access hozzáadása** panelen kattintson az **API kiválasztása**elemre, kattintson az Azure **Data Lake**elemre, majd a **Kijelölés gombra.**

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Az **API Access hozzáadása** panelen kattintson az **Engedélyek kiválasztása**gombra, jelölje be a jelölőnégyzetet, ha teljes hozzáférést szeretne adni a Data Lake **Store áruházhoz,** majd kattintson a **Kijelölés gombra.**

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kattintson a **Done** (Kész) gombra.

5. Ismételje meg az utolsó két lépést a **Windows Azure Service Management API-hoz** való engedélyek megadásához is.
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy Azure AD natív alkalmazást, és összegyűjtötte a szükséges információkat az ügyfélalkalmazásokban, amelyeket a .NET SDK, Java SDK, REST API stb. Most folytathatja a következő cikkeket, amelyek arról beszélnek, hogyan használhatja az Azure AD webalkalmazást először hitelesíti a Data Lake Storage Gen1, majd egyéb műveleteket az áruházban.

* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával Java SDK használatával](data-lake-store-end-user-authenticate-java-sdk.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával a .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával python használatával](data-lake-store-end-user-authenticate-python.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 szolgáltatással rest API használatával](data-lake-store-end-user-authenticate-rest-api.md)

