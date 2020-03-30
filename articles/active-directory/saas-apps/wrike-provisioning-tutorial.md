---
title: 'Oktatóanyag: A Wrike konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok wrike-i automatikus kiépítésére és megszüntetésére.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064189"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Oktatóanyag: A Wrike konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Wrike és az Azure Active Directory (Azure AD) által végrehajtott lépéseket az Azure AD konfigurálásához, hogy automatikusan üzembe helyezze a felhasználókat vagy csoportokat a Wrike-re.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részleteket a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)az Azure Active Directoryval című témakörben találja.
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Wrike-bérlő](https://www.wrike.com/price/)
* A Wrike rendszergazdai engedélyekkel rendelkező felhasználói fiókja

## <a name="assign-users-to-wrike"></a>Felhasználók hozzárendelése a Wrike-hoz
Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Wrike-hez. Ezután rendelje hozzá ezeket a felhasználókat vagy csoportokat a Wrike-hez az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Fontos tippek a felhasználók Wrike-hez való hozzárendeléséhez

* Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó wrike az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Wrike-hez rendel, a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-wrike-for-provisioning"></a>A Wrike beállítása kiépítéshez

Mielőtt konfigurálná a Wrike-t az Azure AD-vel való automatikus felhasználói kiépítéshez, engedélyeznie kell a System for Cross-domain Identity Management (SCIM) kiépítését a Wrike-en.

1. Jelentkezzen be a [Wrike felügyeleti konzoljára.](https://www.Wrike.com/login/) Nyissa meg a bérlőazonosítóját. Válassza **az Alkalmazások & integrációk lehetőséget.**

    ![Alkalmazások & integrációk](media/Wrike-provisioning-tutorial/admin.png)

2.  Nyissa meg az **Azure AD-t,** és válassza ki.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Válassza az SCIM lehetőséget. Másolja az **alap URL-címet**.

    ![Kiindulási URL-cím](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Válassza az **API** > **Azure SCIM**lehetőséget.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Megnyílik egy előugró ablak. Adja meg ugyanazt a jelszót, amelyet korábban hozott létre a fiók létrehozásához.

    ![Wrike Token létrehozása](media/Wrike-provisioning-tutorial/password.png)

6.  Másolja a **titkos jogkivonatot,** és illessze be az Azure AD-be. Válassza a **Mentés** lehetőséget a Wrike létesítési beállításának befejezéséhez.

    ![Állandó hozzáférési jogkivonat](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Wrike hozzáadása a galériából

Mielőtt konfigurálja a Wrike-t az Azure AD-vel való automatikus felhasználói kiépítéshez, adja hozzá a Wrike-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

Wrike hozzáadása az Azure AD alkalmazáskatalógusból, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Wrike**, válassza **wrike** az eredménypanelen, majd válassza **hozzáadás** az alkalmazás hozzáadásához.

    ![Wrike az eredménylistában](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Automatikus felhasználói kiépítés konfigurálása a Wrike szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat vagy csoportokat hozzon létre, frissítsen és tiltson le a Wrike-ben az Azure AD-ben lévő felhasználói vagy csoport-hozzárendelések alapján.

> [!TIP]
> A Wrike SAML-alapú egyszeri bejelentkezésének engedélyezéséhez kövesse a [Wrike egyszeri bejelentkezési oktatóanyag utasításait.](wrike-tutorial.md) Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>A Wrike automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![Minden alkalmazás](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Wrike**lehetőséget.

    ![A Wrike hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Automatikus kiépítési mód](common/provisioning-automatic.png)

5. A Rendszergazdai hitelesítő adatok szakaszban adja meg az **alap URL-címet** és az **állandó hozzáférési jogkivonat-értékeket,** amelyeket korábban a **bérlői URL-címés** **a titkos jogkivonat**, illetve. Válassza **a Kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure AD képes legyen a Wrike-hez csatlakozni. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Wrike-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a kiépítési hibaértesítéseket. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítő e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása wrike-ba**lehetőséget.

    ![Wrike felhasználói leképezések](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Tekintse át az Azure AD-ről wrike-re szinkronizált felhasználói attribútumokat az **Attribútum-leképezések** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Wrike felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Wrike felhasználói attribútumok](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. A hatókörszűrők konfigurálásához kövesse a [Hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Wrike, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![A kiépítés állapota bevan kapcsolva](common/provisioning-toggle-on.png)

13. Adja meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Wrike-ba, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ha többet szeretne tudni arról, hogy a felhasználók vagy csoportok mennyi ideig tart a kiépítés, olvassa [el a Mennyi ideig tart a felhasználók kiépítése?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Az Aktuális **állapot** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenységjelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Wrike-on végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
