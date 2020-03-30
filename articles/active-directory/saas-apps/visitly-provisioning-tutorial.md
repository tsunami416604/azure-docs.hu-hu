---
title: 'Oktatóanyag: A Visitly konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a Visitly szolgáltatásba való kiépítésre.
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
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063162"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Oktatóanyag: A Visitly konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Visitly és az Azure Active Directory (Azure AD) által végrehajtott lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat vagy csoportokat a Visitly számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részleteket a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása](../app-provisioning/user-provisioning.md)az Azure Active Directoryval című témakörben találja.
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A Visitly bérlő](https://www.visitly.io/pricing/)
* A Visitly rendszergazdai engedélyekkel rendelkező felhasználói fiókja

## <a name="assign-users-to-visitly"></a>Felhasználók hozzárendelése a Visitly-hoz 

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Visitly szolgáltatáshoz. Ezután rendelje hozzá ezeket a felhasználókat vagy csoportokat a Visitly-hoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Fontos tippek a felhasználók Visitly-hoz való hozzárendeléséhez 

* Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó a Visitly az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót rendel a Visitly programhoz, a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-visitly-for-provisioning"></a>A Visitly beállítása kiépítéshez

Mielőtt konfigurálja a Visitly automatikus felhasználói kiépítésaz Azure AD, engedélyeznie kell a System for Cross-domain Identity Management (SCIM) kiépítése a Visitly.

1. Jelentkezzen be a [Visitly .](https://app.visitly.io/login) Válassza **az Integrációk** > **állomás szinkronizálása**lehetőséget.

    ![Állomás szinkronizálása](media/Visitly-provisioning-tutorial/login.png)

2. Válassza ki az **Azure AD** szakaszt.

    ![Az Azure AD szakasz](media/Visitly-provisioning-tutorial/integration.png)

3. Másolja az **API-kulcsot**. Ezek az értékek a **Titkos jogkivonat** mezőben a Visitly alkalmazás **kiépítés** lapján az Azure Portalon.

    ![API-kulcs](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Visitly hozzáadása a galériából

A Visitly konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez adja hozzá a Visitly-t az Azure AD-alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

A Visitly hozzáadása az Azure AD alkalmazáskatalógusból, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Visitly**( Látogatás , **látogatás** lehetőséget az eredménypanelen ), majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![Látogatás az eredmények listájában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Automatikus felhasználói kiépítés konfigurálása a Visitly szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat vagy csoportokat hozzon létre, frissítsen és tiltson le a Visitly szolgáltatásban az Azure AD-ben lévő felhasználói vagy csoport-hozzárendelések alapján.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezés engedélyezéséhez kövesse a [Visitly egyszeri bejelentkezési oktatóanyag utasításait.](Visitly-tutorial.md) Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Automatikus felhasználói kiépítés konfigurálása a Visitly szolgáltatáshoz az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![Minden alkalmazás](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Visitly**lehetőséget.

    ![A Visitly hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Automatikus kiépítési mód](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok `https://api.visitly.io/v1/usersync/SCIM` szakaszban adja meg a **bérlői URL-cím** és a **titkos jogkivonat**korábbi részében beolvasott **API-kulcs** értékeket. Válassza **a Kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure AD képes legyen csatlakozni a Visitly szolgáltatáshoz. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Visitly-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a kiépítési hibaértesítéseket. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **a Visitly szolgáltatáshoz az Azure Active Directory-felhasználók szinkronizálása**lehetőséget.

    ![Látogatási felhasználói leképezések](media/visitly-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből az **Attribútumleképezések** szakaszban a Visitly szolgáltatásba szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a Visitly felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Látogatási felhasználói attribútumok](media/visitly-provisioning-tutorial/userattribute.png)

10. A hatókörszűrők konfigurálásához kövesse a [Hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Az Azure AD-kiépítési szolgáltatás engedélyezése a Visitly, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![A kiépítés állapota bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat vagy csoportokat, amelyeket a Visitly-ba szeretne kiépíteni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ha többet szeretne tudni arról, hogy a felhasználók vagy csoportok mennyi ideig tart a kiépítés, olvassa [el a Mennyi ideig tart a felhasználók kiépítése?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Az Aktuális **állapot** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenységjelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Visitly-on végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Összekötő korlátai

A Visitly nem támogatja a kemény törléseket. Minden csak a lágy törlés.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
