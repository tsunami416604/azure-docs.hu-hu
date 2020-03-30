---
title: 'Oktatóanyag: A TheOrgWiki konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok automatikus kiépítésére és a TheOrgWiki-re való kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063143"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Oktatóanyag: A TheOrgWiki konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a TheOrgWiki és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és deknaktssel kiszolgálják a felhasználókat és/vagy csoportokat a TheOrgWiki-re.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy OrgWiki bérlő](https://www.theorgwiki.com/welcome/).
* A TheOrgWiki rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assign-users-to-theorgwiki"></a>Felhasználók hozzárendelése a TheOrgWiki-hez

Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a TheOrgWiki-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a TheOrgWiki-hez rendelheti az alábbi utasítások követésével:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Fontos tippek a felhasználók TheOrgWiki-hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve a TheOrgWiki az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a TheOrgWiki-hez rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha van ilyen) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-theorgwiki-for-provisioning"></a>A TheOrgWiki beállítása kiépítésre

A TheOrgWiki konfigurálása az Azure AD automatikus felhasználói kiépítéséhez engedélyeznie kell az SCIM-kiépítést a TheOrgWiki-n.

1. Jelentkezzen be a [TheOrgWiki Admin Konzolba.](https://www.theorgwiki.com/login/) Kattintson a **Felügyeleti konzolgombra.**

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/login.png)

2. A Felügyeleti konzolban kattintson a **Beállítások fülre**. 

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Nyissa meg a **Szolgáltatásfiókok .**

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Kattintson a **+Szolgáltatás fiók gombra.** A **Szolgáltatásfiók típusa csoportban**válassza a **Token based (Token Based**) lehetőséget. Kattintson a **Mentés** gombra.

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Másolja az **aktív tokeneket**. Ezt az értéket a Titkos jogkivonat mezőben adja meg a TheOrgWiki-alkalmazás kiépítés lapján az Azure Portalon.
     
    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>TheOrgWiki hozzáadása a galériából

A TheOrgWiki konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a TheOrgWiki-t az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TheOrgWiki**elemet , és válassza a **TheOrgWiki** lehetőséget az eredménypanelen. 

    ![TheOrgWiki az eredménylistában](common/search-new-app.png)

5. Válaszd ki a **Regisztráció a TheOrgWiki-re** gombot, amely átirányít a TheOrgWiki bejelentkezési oldalára. 

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/image00.png)

6.  A jobb felső sarokban válassza a **Bejelentkezés**lehetőséget.

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/image02.png)

7. Mivel a TheOrgWiki egy OpenIDConnect alkalmazás, a Microsoft munkahelyi fiókjával jelentkezz be az OrgWiki-be.

    ![TheorgWiki scim hozzáadása](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. A sikeres hitelesítés tkövetően az alkalmazás automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a TheOrgWiki-fiókjába.

    ![OrgWiki SCIM hozzáadása](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Automatikus felhasználói kiépítés konfigurálása a TheOrgWiki szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TheOrgWikiben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>A TheOrgWiki automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **TheOrgWiki**lehetőséget.

    ![Az OrgWiki hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` csoportban adja meg a **bérlői URL-címet.** 

    Például: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Az **altartomány értéke** csak a TheOrgWiki kezdeti regisztrációs folyamata során állítható be.
 
6. Adja meg a token értékét a **Titkos token** mezőben, amelyet korábban a TheOrgWiki-ből kért be. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a TheOrgWiki-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy TheOrgWiki-fiókja rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a TheOrgWiki szolgáltatással**lehetőséget.

    ![TheOrgWiki felhasználói leképezések](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Tekintse át az Azure AD és a TheOrgWiki között szinkronizált felhasználói attribútumokat az **Attribútum- leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a TheOrgWiki felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![TheorgWiki felhasználói attribútumai](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

12. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a TheOrgWiki, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

13. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az OrgWiki-be, ha kiválasztja a kívánt értékeket a **Hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ha többet szeretne tudni arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, lásd: [Mennyi ideig tart a felhasználók kiépítése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Az Aktuális **állapot** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenységjelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a TheOrgWiki-n végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)