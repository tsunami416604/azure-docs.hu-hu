---
title: 'Végfelhasználói hitelesítési: Data Lake Store az Azure Active Directoryhoz |} Microsoft Docs'
description: Megtudhatja, hogyan végfelhasználói hitelesítés az Azure Active Directory használatával a Data Lake Store elérése
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
ms.openlocfilehash: 7280cd971e9857c494dfd1cb77d528e4737ed9d2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624148"
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>A Data Lake Store az Azure Active Directoryval végfelhasználói hitelesítés
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store az Azure Active Directory-hitelesítéshez. Egy alkalmazás, amely az Azure Data Lake Store vagy az Azure Data Lake Analytics szerzői előtt el kell döntenie, hogyan hitelesítheti az alkalmazás Azure Active Directory (Azure AD). A két fő elérhető lehetőségek a következők:

* Végfelhasználói hitelesítési (Ez a cikk)
* Szolgáltatások közötti hitelesítés (mentse ezt a beállítást, a fenti legördülő)

Mindkét ezeket a beállításokat az alkalmazás OAuth 2.0-tokenhez, lekérdezi az összes kérelmet az Azure Data Lake Store vagy az Azure Data Lake Analytics csatolva alatt megadott eredményez.

Ez a cikk létrehozása bemutat egy **végfelhasználói hitelesítéshez natív Azure AD-alkalmazást**. Az Azure AD alkalmazás konfigurációja a szolgáltatások közötti hitelesítéshez, lásd: [szolgáltatások közötti hitelesítés az Azure Active Directory használatával a Data Lake Store](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Az előfizetés-azonosító. Azure-portálról le tudja kérni. Például érhető el a Data Lake Store-fiók paneljén.
  
    ![Előfizetés-azonosító lekérése](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Az Azure AD-tartomány neve. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. Az alábbi képernyőképen látható, a tartománynév nem **contoso.onmicrosoft.com**, a szögletes zárójelben GUID pedig a bérlői. 
  
    ![Az AAD tartományi beolvasása](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Az Azure-bérlőhöz azonosítójával. Hogyan lehet lekérni a bérlő azonosítója, lásd: [a bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
A hitelesítési módszer használata javasolt, ha azt szeretné, hogy a felhasználó bejelentkezni az Azure AD alkalmazás. Az alkalmazás elindulhatott hitelesítésszolgáltatóéval azonos szintű hozzáférés, a végfelhasználó, a rendszer az Azure-erőforrások eléréséhez. A végfelhasználó rendszeres időközönként annak érdekében, hogy az alkalmazás fér a hitelesítő adatok megadását.

Az eredmény, hogy a végfelhasználói bejelentkezés, hogy az alkalmazás olyan hozzáférési jogkivonatot, és egy frissítési jogkivonat. A hozzáférési jogkivonatot minden Data Lake Store vagy a Data Lake Analytics kérelmet lekérdezi kapcsolni, és alapértelmezés szerint egy órán keresztül érvényes legyen. A frissítési jogkivonat segítségével szerezzen be egy új jogkivonatot, és alapértelmezés szerint legfeljebb két hétig érvényes legyen. Két különböző szempontok a végfelhasználói bejelentkezés is használhatja.

### <a name="using-the-oauth-20-pop-up"></a>Az OAuth 2.0 előugró használatával
Az alkalmazás OAuth 2.0 engedélyezési előugró, amelyben a felhasználók megadhatják hitelesítő adataikat is elindíthatja. Az előugró ablak az Azure AD-kéttényezős hitelesítést (2FA) folyamat során is működik, ha szükséges. 

> [!NOTE]
> Ez a metódus még nem támogatott az az Azure AD Authentication Library (ADAL) Python vagy Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Felhasználói hitelesítő adatok közvetlenül benyújtása
Az alkalmazás közvetlenül adni a hitelesítő adatokat az Azure ad Szolgáltatásba. Ez a módszer csak használható a szervezeti azonosító felhasználói fiókok; Ez nem kompatibilis a személyes / a "live ID" felhasználói fiók, beleértve a fiókokat, a befejezési @outlook.com vagy @live.com. Továbbá ez a metódus nem található felhasználói fiókok Azure AD-kéttényezős hitelesítést (2FA) igénylő kompatibilis.

### <a name="what-do-i-need-for-this-approach"></a>Mire van szükségem az ezt a módszert használja?
* Az Azure AD-tartomány nevét. Ez a követelmény már szerepel a cikk az előfeltételek.
* Az Azure AD-bérlő azonosítója. Ez a követelmény már szerepel a cikk az előfeltételek.
* Az Azure AD **natív alkalmazás**
* Az Azure AD natív alkalmazás azonosítója
* Az Azure AD natív alkalmazás átirányítási URI
* Delegált engedélyek beállítása


## <a name="step-1-create-an-active-directory-native-application"></a>1. lépés: Az Active Directory natív alkalmazás létrehozása

Hozzon létre, és a végfelhasználói hitelesítéshez az Azure AD natív alkalmazások konfigurálása az Azure Data Lake Store az Azure Active Directoryval. Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../azure-resource-manager/resource-group-create-service-principal-portal.md).

A hivatkozás utasításait követve, miközben gondoskodjon róla, hogy **natív** alkalmazás típusra, a következő képernyőfelvételen látható módon:

![A webalkalmazás létrehozása](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "hozzon létre natív alkalmazás")

## <a name="step-2-get-application-id-and-redirect-uri"></a>2. lépés: Alkalmazásazonosító beszerzése és átirányítási URI

Lásd: [Alkalmazásazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) való lekérése az alkalmazáshoz.

Az átirányítási URI-JÁNAK beolvasása, hajtsa végre az alábbi lépéseket.

1. Válassza ki az Azure-portálon **Azure Active Directory**, kattintson **App regisztrációk**, majd található, és kattintson a létrehozott natív Azure AD-alkalmazást.

2. Az a **beállítások** az alkalmazás paneljén kattintson **átirányítási URI-azonosítók**.

    ![Get-átirányítási URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Másolja a megjelenített érték.


## <a name="step-3-set-permissions"></a>3. lépés: Engedélyek beállítása

1. Válassza ki az Azure-portálon **Azure Active Directory**, kattintson **App regisztrációk**, majd található, és kattintson a létrehozott natív Azure AD-alkalmazást.

2. Az a **beállítások** az alkalmazás paneljén kattintson **szükséges engedélyek**, és kattintson a **Hozzáadás**.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. A a **API-hozzáférés hozzáadása** panelen kattintson a **API kiválasztása**, kattintson a **Azure Data Lake**, és kattintson a **válassza**.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Az a **API-hozzáférés hozzáadása** panelen kattintson **engedélyként válassza**, jelölje be a jelölőnégyzetet, hogy a **teljes hozzáférés a Data Lake store**, és kattintson a **kiválasztása**.

    ![ügyfél-azonosító](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kattintson a **Done** (Kész) gombra.

5. Ismételje meg az utolsó két lépést vonatkozó engedélyeket **Windows Azure szolgáltatásfelügyeleti API** is.
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben egy natív Azure AD-alkalmazást létrehozni, és a szükséges információkat az ügyfél-alkalmazások használata a .NET SDK-val, a Java SDK-t, a REST API-t, a stb szerzői összegyűjtött. Most már folytathatja, az alábbi cikkek tartalmazzák, amely először a Data Lake Store hitelesítéséhez, és végezze el más műveleteket végez a tároló az Azure AD-webes alkalmazás használatával kapcsolatban.

* [Végfelhasználói-user-hitelesítés a Data Lake Store Java SDK használatával](data-lake-store-end-user-authenticate-java-sdk.md)
* [Végfelhasználói hitelesítési a Data Lake Store .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md)
* [Végfelhasználói hitelesítési a Data Lake Store pythonos környezetekben](data-lake-store-end-user-authenticate-python.md)
* [A Data Lake Store REST API használatával végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-rest-api.md)

