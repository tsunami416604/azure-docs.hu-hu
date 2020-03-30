---
title: 'Oktatóanyag: A Keeper Password Manager konfigurálása & Digital Vault az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Keeper Password Manager & Digital Vault szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057521"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Oktatóanyag: A Keeper Password Manager & digital vault konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Keeper Password Manager & Digital Vault és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a Keeper Password Manager & Digital Vault számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A Keeper Password Manager & Digital Vault bérlő](https://keepersecurity.com/pricing.html?t=e)
* A Keeper Password Manager egyik felhasználói fiókja rendszergazdai engedélyekkel & Digital Vault.A user account in Keeper Password Manager & Digital Vault with Admin permissions.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Keeper Password Manager hozzáadása & Digital Vault a galériából

A Keeper Password Manager & Digital Vault konfigurálása az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia a Keeper Password Manager & Digital Vault-ot az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Keeper Password Manager & Digital Vault ot az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Keeper Password Manager & Digital Vault**kifejezést, válassza a Keeper Password Manager & Digital **Vault** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Keeper Password Manager & Digital Vault az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Felhasználók hozzárendelése a Keeper Password Manager & Digital Vault hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Keeper Password Manager & Digital Vault hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Keeper Password Manager & Digital Vault-hoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Fontos tippek a felhasználók Keeper Password Manager & Digital Vault hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve keeper Password Manager & Digital Vault az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel a Keeper Password Manager & Digital Vault hoz, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Automatikus felhasználói kiépítés konfigurálása a Keeper Password Manager & Digital Vault szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Keeper Password Manager & Digital Vault ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Keeper Password Manager & Digital Vault számára, a [Keeper Password Manager & Digital Vault egyszeri bejelentkezési oktatóanyagutasításait](keeperpasswordmanager-tutorial.md)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Az Azure AD-ben a Keeper Password Manager & Digital Vault automatikus felhasználói kiépítésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Keeper Password Manager & Digital Vault**lehetőséget.

    ![A Keeper Password Manager & Digital Vault hivatkozással az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet** és a keeper password manager **titkos jogkivonatát** & Digital Vault fiókját a 6.

6. Jelentkezzen be a [Keeper Admin konzolra.](https://keepersecurity.com/console/#login) Kattintson a **Rendszergazda** elemre, és jelöljön ki egy meglévő csomópontot, vagy hozzon létre egy újat. Nyissa meg a **Kiépítés lapot,** és válassza **a Metódus hozzáadása lehetőséget.**

    ![Állattartó Admin konzol](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Válassza az **SCIM (Rendszer tartományok közötti identitáskezeléshez**) lehetőséget.

    ![Állattartó hozzáadása SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Kattintson **a Kiépítési jogkivonat létrehozása gombra.**

    ![Üzembentartó végpont létrehozása](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Másolja az **URL-cím** és **a jogkivonat** értékeit, és illessze be őket **a bérlői URL-címbe** és **titkos jogkivonatba** az Azure AD-ben. Kattintson a **Mentés** gombra a kiépítési beállítás elvégzéséhez a Kapus.

    ![Üzembentartó token létrehozása](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Az & 5. **Test Connection** Ha a kapcsolat sikertelen, győződjön meg arról, hogy a Keeper Password Manager & Digital Vault-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása a Keeper Password Manager & Digital Vault szolgáltatással**lehetőséget.

    ![Állattartó felhasználói leképezései](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Tekintse át az Azure AD-ről a Keeper Password Manager & Digital Vault szolgáltatással szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Keeper Password Manager & Digital Vault felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Az őriző felhasználói attribútumai](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. A **Leképezések** csoportban válassza az **Azure Active Directory-csoportok szinkronizálása a & Digital Vault**szolgáltatással lehetőséget.

    ![Üzembentartói csoport leképezései](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Tekintse át az Azure AD-ről a Keeper Password Manager & Digital Vault szolgáltatással szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Keeper Password Manager & Digital Vault csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Üzembentartói csoport attribútumai](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

15. Az Azure AD-létesítési szolgáltatás engedélyezéséhez a Keeper Password Manager & Digital Vault, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

16. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Keeper Password Manager & Digital Vault ba a **Beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

17. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Keeper Password Manager & Digital Vault összes műveletét.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Keeper Password Manager & Digital Vault **e-maileket** és **felhasználónévhez** ugyanazt a forrásértéket kell igényelnie, mivel az attribútumok bármely frissítése módosítja a másik értéket.
* Állattartó Jelszó Igazgató & Digitális Trezor nem támogat felhasználó töröl, egyetlen megbénít. A letiltott felhasználók zárolva jelennek meg a Keeper Admin Console felhasználói felületén.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

