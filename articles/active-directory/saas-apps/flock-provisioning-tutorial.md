---
title: 'Oktatóanyag: A Flock konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat a Flock szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: cd7aae05b064657c7b9072402f4bc4d4d7fef7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057863"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>Oktatóanyag: A Flock konfigurálása az automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Flock és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és deknaktszoljon felhasználókat és/vagy csoportokat a Flock-be.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A Flock bérlő](https://flock.com/pricing/)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a Flock-ben.

## <a name="assigning-users-to-flock"></a>Felhasználók hozzárendelése az özszflockhoz 

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Flock-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Flock-hez rendelheti az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>Fontos tippek a felhasználók Flock-hez való hozzárendeléséhez 

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve flock az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel az Özszflock hoz, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-flock--for-provisioning"></a>Az Állomány beállítása a kiépítéshez

Mielőtt konfigurálná a Flock automatikus felhasználói kiépítésaz Azure AD, engedélyeznie kell az SCIM kiépítése a Flock.

1. Jelentkezzen be a [Flock](https://web.flock.com/?). Kattintson a **Beállítások ikon** > **A csapat kezelése gombra.**

    ![Flock](media/flock-provisioning-tutorial/icon.png)

2. Válassza **az Hitelesítés és a Kiépítés lehetőséget.**

    ![Flock](media/Flock-provisioning-tutorial/auth.png)

3. Másolja az **API-jogkivonatot**. Ezeket az értékeket a **titkos jogkivonat** mezőben adja meg az Azure Portalon a Flock-alkalmazás kiépítés lapján.

    ![Flock](media/Flock-provisioning-tutorial/provisioning.png)


## <a name="add-flock--from-the-gallery"></a>Add Flock a galériából

A Flock konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Flock-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Az Azure AD alkalmazásgyűjteményből való flock hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Állomány**kifejezést, válassza az **Állomány** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Állomány az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Automatikus felhasználói kiépítés konfigurálása a Flock szolgáltatásba  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználói és/vagy letiltása az Azure AD-ben lévő felhasználói és/vagy csoport-hozzárendelések alapján hozzon létre, frissítsen és tiltson le felhasználókat és/vagy csoportokat az Azure AD-ben.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Flock számára, a [Flock Single sign-on tutorial](Flock-tutorial.md)utasításait követve. Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Az Azure AD-ben a Flock automatikus felhasználói kiépítésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Flock**lehetőséget.

    ![A Flock hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok `https://api.flock-staging.com/v2/scim` szakaszban adja meg a bérlői **URL-cím,** illetve a **titkos jogkivonat** korábbi részében lekért **API-token** értékeket. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Flock.Click Test Connection to ensure Azure AD can connect to Flock. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Flock-fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása az állományba**lehetőséget.

    ![Flock felhasználói leképezések](media/flock-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ről flock-ra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Flock felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Flock felhasználói attribútumok](media/flock-provisioning-tutorial/userattribute.png)

11. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

12. Az Azure AD-létesítési szolgáltatás engedélyezése flock, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

13. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Flock szolgáltatásba szeretne kiépíteni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ha többet szeretne tudni arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, lásd: [Mennyi ideig tart a felhasználók kiépítése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Az Aktuális **állapot** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenységjelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az özszenlén végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)



## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)