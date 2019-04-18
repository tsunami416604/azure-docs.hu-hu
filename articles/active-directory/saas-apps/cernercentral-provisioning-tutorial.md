---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Cerner központi |} A Microsoft Docs'
description: Ismerje meg az Azure Active Directory konfigurálása az Automatikus kiépítés felhasználóknak, hogy a Résztvevőlista Cerner közép-India.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00a967d61a5f81fc871488ea48df9cb4cf18c269
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268602"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Cerner központi konfigurálása

Ez az oktatóanyag célja a megjelenítése, hogy a lépéseket kell elvégeznie a Cerner központi és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből egy felhasználó a Résztvevőlista Cerner közép-India.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Egy Azure Active Directory-bérlő
* A bérlő egy Cerner közép-India

> [!NOTE]
> Az Azure Active Directory használatával Cerner központi integrálható a [SCIM](http://www.simplecloud.info/) protokollt.

## <a name="assigning-users-to-cerner-central"></a>Felhasználók hozzárendelése Cerner közép-India

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus fióklétesítés kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálódnak. 

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a felhasználók, akik hozzáférhetnek a Cerner központi jelölik. Ha úgy döntött, az itt leírt utasításokat követve hozzárendelheti ezeket a felhasználókat Cerner központi:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Felhasználók hozzárendelése Cerner központi fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználót az üzembe helyezési konfiguráció teszteléséhez Cerner központi rendelni. További felhasználók és csoportok később is rendelhető.

* Kezdeti tesztelés befejezése után egy-egy felhasználóhoz a Cerner központi javasolja a hozzárendelése a felhasználók Cerner megoldással (nem csak Cerner központi) elérésére szolgál ki kell építeni a Cerner a felhasználó a Résztvevőlista teljes listáját.  Cerner megoldásait használhatja ezt a felhasználót a Résztvevőlista felhasználóinak listája.

* Amikor egy felhasználó hozzárendelése Cerner központi, ki kell választania a **felhasználói** szerepkör-hozzárendelés párbeszédpanelen. A "Alapértelmezett hozzáférés" szerepkörrel rendelkező felhasználók ki vannak zárva, a létesítési.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Cerner központi történő felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD-csatlakozás Cerner központi felhasználói Résztvevőlista Cerner az SCIM felhasználói fiók üzembe helyezési API használatával, és konfigurálása az eszközkiépítési szolgáltatás létrehozása, frissítése, és tiltsa le a hozzárendelt felhasználói fiókok Cerner közép-India alapján felhasználók és csoportok hozzárendelése az Azure ad-ben.

> [!TIP]
> Előfordulhat, hogy meg Cerner központi SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható az Automatikus kiépítés függetlenül ellenére, hogy ezen két funkció kiegészíti egymást. További információkért lásd: a [Cerner központi egyszeri bejelentkezéses oktatóanyag](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítése Cerner középső régiója, az Azure ad-ben:

Felhasználói fiókok Cerner központi üzembe helyezéséhez, szüksége Cerner Cerner központi rendszerfiók kérhet, és hozzon létre egy OAuth tulajdonosi jogkivonat, amely az Azure AD használható Cerner az SCIM-végponthoz csatlakozik. Emellett javasoljuk, hogy az integrációs végezhető el egy Cerner próbakörnyezetben éles környezetben való üzembe helyezés előtt.

1. Az első lépés a személyek a Cerner kezelésének biztosítása érdekében, és az Azure AD-integrációs rendelkezik CernerCare-fiókkal, amely utasítások végrehajtásához szükséges dokumentáció eléréséhez szükséges. Ha szükséges, használja az alábbi URL-címek CernerCare fiókok létrehozásához minden esetben környezetben.

   * A védőfal:  https://sandboxcernercare.com/accounts/create

   * Éles környezetben:  https://cernercare.com/accounts/create  

2. Ezután a rendszer fiókot kell létrehoznia az Azure ad. Kérjen egy rendszer-fiókot a védőfal vagy éles környezetben használja az alábbi utasításokat.

   * Utasítások:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Éles környezetben:  https://cernercentral.com/system-accounts/

3. Következő lépésként hozza létre az OAuth tulajdonosi jogkivonat minden rendszer fiókhoz. Ehhez kövesse az alábbi utasításokat.

   * Utasítások:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Éles környezetben:  https://cernercentral.com/system-accounts/

4. Végül kell beszerezni a Résztvevőlista tartomány felhasználóazonosító Cerner konfigurálásának befejezéséhez az védőfal és az éles környezetekhez. Beszerezni az ennek módjáról további információkért lásd: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Most már Azure AD-felhasználói fiókok kiépítése Cerner konfigurálhatja. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és keresse meg a a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

6. Ha már konfigurált Cerner központi az egyszeri bejelentkezés, a példány Cerner közép keresési mező használatával keresése. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Cerner központi** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Cerner központi, és adja hozzá az alkalmazások listáját.

7. Válassza ki azt a példányt Cerner közép, majd válassza ki a **kiépítési** fülre.

8. Állítsa be a **Kiépítési mód** való **automatikus**.

   ![Kiépítés Cerner közép-India](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Töltse ki a következő mezőket alatt **rendszergazdai hitelesítő adataival**:

   * Az a **bérlői URL-cím** mezőbe írjon be egy URL-címet az alábbi formátumban "Felhasználói Osztálynévjegyzék-tartomány-azonosító" cserélje le a #4. lépésében beszerzett tartomány azonosítója.

    > Sandbox: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Éles környezetben: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Az a **titkos jogkivonat** mezőben adja meg a #3. lépésben létrehozott OAuth tulajdonosi jogkivonat, és kattintson a **kapcsolat tesztelése**.

   * A portál eddig: upperright oldalán kell megjelennie a sikeres címtármódosítást jelző értesítés.

1. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

1. Kattintson a **Save** (Mentés) gombra.

1. Az a **attribútumleképezések** területen tekintse át a felhasználói és csoportattribútum Cerner központi szinkronizálását az Azure ad-ből. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfeleljen a felhasználói fiókok és csoportok Cerner közép-India, a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás a Cerner központi engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

1. Kattintson a **Save** (Mentés) gombra.

Ezzel elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban Cerner központi hozzárendelt csoportokat. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a Cerner központi alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Cerner középső régiója: Közzétételi azonosító adataihoz az Azure AD-vel](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Oktatóanyag: Cerner központi konfigurálása egyszeri bejelentkezéshez az Azure Active Directoryval](cernercentral-tutorial.md)
* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a kiépítési tevékenység](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
