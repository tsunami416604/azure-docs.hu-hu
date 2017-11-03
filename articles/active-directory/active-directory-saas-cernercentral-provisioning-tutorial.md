---
title: "Oktatóanyag: Cerner központi konfigurálása az automatikus felhasználó-átadási az Azure Active Directoryval |} Microsoft Docs"
description: "Útmutató: Azure Active Directory konfigurálása automatikusan rendelkezni felhasználóknak, hogy a Résztvevőlista Cerner közép-India"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.openlocfilehash: 84613b7f8d7bd031d492a62da0bc53be96ac45a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>Oktatóanyag: Cerner központi konfigurálása az automatikus felhasználó létesítése

Ez az oktatóanyag célja mutatjuk be, a lépéseket kell elvégeznie a Cerner központi és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-egy felhasználó Résztvevőlista Cerner közép-India. 


## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active Directory-bérlő
*   A Cerner központi bérlő 

> [!NOTE]
> Az Azure Active Directory használatával Cerner központi integrálható a [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-cerner-central"></a>Felhasználók hozzárendelése Cerner központi

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok számára "rendelt" az Azure AD alkalmazás szinkronizálva. 

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt meg kell határoznia, mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik Cerner központi elérésére. Ha úgy döntött, itt utasításokat követve hozzárendelheti ezek a felhasználók Cerner központi:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Felhasználók hozzárendelése Cerner központi fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó a kiépítési konfigurációjának tesztelése rendelendő Cerner központi. További felhasználók és/vagy csoportok később is rendelhető.

* Kezdeti tesztelés befejezése után egy-egy felhasználóhoz, Cerner központi azt javasolja, hogy hozzárendelése a teljes listát a felhasználók bármely Cerner megoldás (nem csak Cerner központi) eléréséhez szánt Cerner tartozó felhasználói Résztvevőlista kell létrehozni.  Cerner megoldásait használja ki a felhasználók a felhasználói Résztvevőlista listájában.

*   Amikor egy felhasználó hozzárendelése Cerner központi, ki kell választania a **felhasználói** szerepkör-hozzárendelés párbeszédpanelen. A "Default hozzáférés" szerepkörrel rendelkező felhasználók kiépítés tartoznak.


## <a name="configuring-user-provisioning-to-cerner-central"></a>A felhasználók átadása a Cerner központi konfigurálása

Ez a szakasz végigvezeti az Azure AD Cerner központi felhasználói Résztvevőlista Cerner tartozó SCIM felhasználói fiók kiépítése API használatával való kapcsolódás és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Cerner központi alapján felhasználók és csoportok hozzárendelése az Azure ad-ben.

> [!TIP]
> Dönthet úgy is engedélyezni SAML-alapú egyszeri bejelentkezést a Cerner központi utasításai [Azure-portálon (https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, abban az esetben, ha ez a két funkció egészítik ki egymást. További információkért lásd: a [Cerner központi egyszeri bejelentkezés az oktatóanyag](active-directory-saas-cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítés Cerner központi Azure AD-ben:


Ahhoz, hogy a felhasználói fiókok Cerner központi telepítéséhez, szüksége lesz egy Cerner központi rendszer fiókot kérhetnek Cerner, és létrehozzon egy OAuth tulajdonosi jogkivonatot, amely az Azure AD használatával Cerner tartozó SCIM végponthoz kapcsolódni. Ajánlott továbbá, hogy az integrációs végezhető el egy Cerner védőfal mögötti környezet üzemi környezetben üzembe helyezése előtt.

1.  Az első lépés annak a személynek a Cerner kezelése érdekében, és az Azure AD-integrációs kell CernerCare-fiókkal, amely utasítások végrehajtásához szükséges dokumentáció eléréséhez szükséges. Ha szükséges, használja az alábbi URL-címek CernerCare fiókokat létrehozni minden esetben környezetben.

   * Védőfal: https://sandboxcernercare.com/accounts/create

   * Éles: https://cernercare.com/accounts/create  

2.  A következő rendszerfiók léteznie kell az Azure AD. Az alábbi utasításokat követve a védőfal és éles környezetekhez rendszerfiók kérelem.

   * Útmutató: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Védőfal: https://sandboxcernercentral.com/system-accounts/

   * Éles: https://cernercentral.com/system-accounts/

3.  Ezt követően készítse el az OAuth tulajdonosi jogkivonat minden rendszer fiók. Ehhez kövesse az alábbi utasításokat.

   * Útmutató: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Védőfal: https://sandboxcernercentral.com/system-accounts/

   * Éles: https://cernercentral.com/system-accounts/

4. Végezetül beszerzése szükséges felhasználói Résztvevőlista tartomány azonosítók Cerner a konfigurálás befejezéséhez a védőfal mind az üzemi környezetben. Ez beszerzésére vonatkozó további információkért lásd:: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Azure AD-felhasználói fiókok létesítése Cerner most konfigurálhatja. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

6. Ha már konfigurált Cerner központi egyszeri bejelentkezést, keresse meg a példányát használja a keresőmezőt Cerner központi. Máskülönben válassza **Hozzáadás** keresse meg a **Cerner központi** az alkalmazás katalógusában. Válassza ki a Cerner központi a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

7.  Jelölje ki a Cerner központi példányát, majd válassza ki a **kiépítési** fülre.

8.  Állítsa be a **kiépítési üzemmódját** való **automatikus**.

   ![Kiépítés Cerner központi](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Töltse ki a következő mezőket a **rendszergazdai hitelesítő adataival**:

   * Az a **bérlői URL-cím** mezőbe írja be az alábbi formátumban "Felhasználói Résztvevőlista-tartományi-azonosító" cseréje a tartomány azonosítója #4. lépésben beszerzett URL.

> Védőfal: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Éles: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Az a **titkos Token** mezőben adja meg a #3. lépésében létrehozott OAuth tulajdonosi jogkivonatot, és kattintson a **kapcsolat tesztelése**.

   * A portál upperright oldalán egy sikeres következő értesítésnek kell megjelennie.

10. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

11. Kattintson a **Save** (Mentés) gombra. 

12. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói és csoportattribútum Cerner központi az Azure ad-szinkronizálandó. A kiválasztott attribútumok **egyező** tulajdonságok használt a felhasználói fiókok és a frissítési műveletek Cerner központi csoportok kereséséhez. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

13. Az Azure AD szolgáltatás a Cerner központi kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

14. Kattintson a **Save** (Mentés) gombra. 

Ezzel elindítja a kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban Cerner központi rendelt csoportok. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, körülbelül 20 percenként történik, amíg fut a szolgáltatás kiépítését az Azure AD-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az Cerner központi alkalmazás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

## <a name="additional-resources"></a>További források

* [Cerner központi: Az Azure AD identity adatok közzététele](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Oktatóanyag: Cerner központi konfigurálása egyszeri bejelentkezéshez az Azure Active Directoryval](active-directory-saas-cernercentral-tutorial.md)
* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Következő lépések
* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).
