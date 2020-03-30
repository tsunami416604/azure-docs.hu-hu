---
title: 'Oktatóanyag: A G Suite konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan építheti ki és vonhat vissza automatikusan a felhasználói fiókokat az Azure AD-ről a G Suite-ra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057703"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Oktatóanyag: A G Suite konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a G Suite és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonalizálja a felhasználókat és/vagy csoportokat a G Suite-ba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

> [!NOTE]
> A G Suite-összekötőt nemrég frissítették 2019 októberében. A G Suite-csatlakozón végrehajtott módosítások a következők:
> - További G Suite felhasználói és csoportattribútumok támogatása. 
> - A G Suite célattribútum-nevei megfelelnek az [itt](https://developers.google.com/admin-sdk/directory)meghatározottaknak.
> - Frissítve az alapértelmezett attribútumleképezések.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció g suite-mal való konfigurálásához a következő elemekre van szüksége:

- Egy Azure AD-bérlő
- [G Suite-bérlő](https://gsuite.google.com/pricing.html)
- Rendszergazdai engedélyekkel rendelkező G Suite felhasználói fiókja.

## <a name="assign-users-to-g-suite"></a>Felhasználók hozzárendelése a G Suite-hoz

Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a G Suite-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a G Suite-hoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Fontos tippek a felhasználók G Suite-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a G Suite az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a G Suite-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-g-suite-for-provisioning"></a>A G Suite beállítása a kiépítéshez

A G Suite konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, engedélyeznie kell az SCIM-kiépítést a G Suite-on.

1. Jelentkezzen be a [G Suite Felügyeleti konzolra](https://admin.google.com/) a rendszergazdai fiókjával, és válassza a **Biztonság**lehetőséget. Ha nem látja a hivatkozást, előfordulhat, hogy a hivatkozás a képernyő alján található **További vezérlők** menü alatt van elrejtve.

    ![Válassza a biztonság lehetőséget.][10]

1. A **Biztonság** lapon válassza az **API-referencia lehetőséget.**

    ![Válassza az API-referencia lehetőséget.][15]

1. Válassza **az API-hozzáférés engedélyezése**lehetőséget.

    ![Válassza az API-referencia lehetőséget.][16]

   > [!IMPORTANT]
   > Minden felhasználó, amely szeretne kiépíteni a G Suite, a felhasználónevét az Azure AD-ben **kell** kötni egy egyéni tartományban. A G Suite például bob@contoso.onmicrosoft.com nem fogadja el a hasonló felhasználóneveket. Másrészt, bob@contoso.com elfogadott. Egy meglévő felhasználó tartományát az [itt](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)található utasításokat követve módosíthatja.

1. Miután hozzáadta és ellenőrizte a kívánt egyéni tartományokat az Azure AD-vel, újra ellenőriznie kell azokat a G Suite segítségével. A G Suite tartományainak ellenőrzéséhez olvassa el az alábbi lépéseket:

    a. A [G Suite Felügyeleti konzolon](https://admin.google.com/)válassza a **Tartományok**lehetőséget.

    ![Tartományok kiválasztása][20]

    b. Válassza **a Tartomány vagy tartományalias hozzáadása lehetőséget.**

    ![Új tartomány hozzáadása][21]

    c. Válassza **a Másik tartomány hozzáadása**lehetőséget, majd írja be a hozzáadni kívánt tartomány nevét.

    ![Írja be a tartománynevet][22]

    d. Válassza **a Folytatás és a tartomány tulajdonjogának ellenőrzése**lehetőséget. Ezután kövesse a lépéseket annak ellenőrzéséhez, hogy ön a tartománynév tulajdonosa-e. A domain Google-lal való igazolásával kapcsolatos átfogó útmutatásért [olvassa el a Webhely tulajdonjogának ellenőrzése](https://support.google.com/webmasters/answer/35179)című témakört.

    e. Ismételje meg az előző lépéseket minden olyan további tartomány esetében, amelyet hozzá kíván adni a G Suite-hoz.

1. Ezután határozza meg, hogy melyik rendszergazdai fiókot szeretné használni a G Suite felhasználói kiépítésének kezeléséhez. Nyissa meg a **Rendszergazdai szerepkörök et.**

    ![Google-alkalmazások kiválasztása][26]

1. A fiók **rendszergazdai szerepköre** esetén szerkesztse a szerepkör **jogosultságait.** Győződjön meg arról, hogy engedélyezze az összes **felügyeleti API-jogosultságokat,** hogy ez a fiók kiépíthető legyen.

    ![Google-alkalmazások kiválasztása][27]

## <a name="add-g-suite-from-the-gallery"></a>G Suite hozzáadása a galériából

A G Suite konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a G Suite-ot az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához. 

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

1. A keresőmezőbe írja be a **G Suite**kifejezést, válassza a **G Suite** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![G Suite az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Automatikus felhasználói kiépítés konfigurálása a G Suite programba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a G Suite-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a G Suite-hoz, a [G Suite egyszeri bejelentkezési útmutatóutasításait](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a G Suite Directory API-végpontjáról, olvassa el a [Directory API-t.](https://developers.google.com/admin-sdk/directory)

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>A G Suite automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **G Suite lehetőséget.**

    ![A G Suite hivatkozása az Alkalmazások listában](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

1. A **Rendszergazdai hitelesítő adatok csoportban** válassza az **Engedélyezés lehetőséget.** Megnyit egy Google engedélyezési párbeszédpanelt egy új böngészőablakban.

    ![G Suite engedélyezése](media/google-apps-provisioning-tutorial/authorize.png)

1. Győződjön meg arról, hogy azure AD-engedélyeket szeretne adni a G Suite-bérlő módosításához. Válassza ki az **Elfogadás** lehetőséget.

    ![Engedélyek megerősítése.][28]

1. Az Azure Portalon válassza a **Kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure AD csatlakozni tud-e az alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a G Suite-fiók rendelkezik csapatfelügyeleti engedélyekkel. Ezután próbálkozzon újra az **Engedélyezés** lépéssel.

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a G Suite szolgáltatással**lehetőséget.

    ![G Suite felhasználói leképezései](media/google-apps-provisioning-tutorial/usermappings.png)

1. Tekintse át az Azure AD-ről g suite-ra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok a G Suite felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![G Suite felhasználói attribútumai](media/google-apps-provisioning-tutorial/userattributes.png)

1. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a G Suite szolgáltatással**lehetőséget.

    ![G Suite-csoport leképezések](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Tekintse át az Azure AD-ről g suite-ra szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a G Suite csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra. A felhasználói felület megjeleníti az Azure AD és a G Suite közötti attribútumleképezések alapértelmezett készletét. Az új leképezés hozzáadása gombra kattintva további attribútumokat, például szervezeti egységet adhat hozzá.

    ![G Suite-csoport attribútumai](media/google-apps-provisioning-tutorial/groupattributes.png)

1. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

1. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a G Suite, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

1. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a G Suite-ba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

1. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a G Suite-on végrehajtott összes műveletet.

> [!NOTE]
> Ha a felhasználók már rendelkeznek egy meglévő személyes/fogyasztói fiókkal az Azure AD-felhasználó e-mail-címével, akkor ez némi problémát okozhat, amely a címtár-szinkronizálás végrehajtása előtt megoldható a Google Átviteli eszköz használatával.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Gyakori problémák
* Engedélyezési hibák akkor fordulhatnak elő, ha a kapcsolat létrehozásához használt fiók nem a GSuite rendszergazdája számára készült. Győződjön meg arról, hogy a hozzáférés engedélyezéséhez használt fiók rendszergazdai engedélyekkel **rendelkezik az összes olyan tartományban,** amelyet a felhasználóknak ki kell építeniük. 
* Az Azure AD támogatja a felhasználók letiltását a GSuite-ben, hogy ne férhessenek hozzá az alkalmazáshoz, de nem törli a Felhasználókat a GSuite-ben.

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
