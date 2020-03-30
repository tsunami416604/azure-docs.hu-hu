---
title: 'Oktatóanyag: A Foodee konfigurálása az Azure Active Directory használatával történő automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a Foodee-re való kiépítésre.
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
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057813"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Oktatóanyag: A Foodee konfigurálása automatikus felhasználói kiépítéshez

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Active Directoryt (Azure AD) a Foodee és az Azure AD-ben, hogy automatikusan kiépítsék vagy megszüntetjék a felhasználókat vagy csoportokat a Foodee-ben.

> [!NOTE]
> A cikk ismerteti az összekötő, amely az Azure AD felhasználói kiépítési szolgáltatás ra épül. Ha tudni szeretné, hogy mit csinál ez a szolgáltatás, és hogyan működik, és válaszokat kaphat a gyakori kérdésekre, olvassa el [a felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az Azure alkalmazási feltételek funkcióiról az előzetes verziójú funkciókhoz további információt a [Microsoft Azure előzetes verziók kiegészítő használati feltételei című részében](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)talál.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy teljesítette az alábbi előfeltételeket:

* Egy Azure AD-bérlő
* [A Foodee bérlő](https://www.food.ee/about/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Foodee-ben

## <a name="assign-users-to-foodee"></a>Felhasználók hozzárendelése a Foodee-hez 

Az Azure AD egy *feladatkiosztásnevű* koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Foodee-hez. Miután elkészítette ezt a meghatározást, ezeket a felhasználókat vagy csoportokat hozzárendelheti a Foodee-hez, ha követi a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz című utasításait.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Fontos tippek a felhasználók Foodee-hez való hozzárendeléséhez 

Felhasználók hozzárendelésekénél tartsa szem előtt az alábbi tippeket:

* Azt javasoljuk, hogy csak egy Azure AD-felhasználót rendeljen hozzá a Foodee-hez az automatikus felhasználói kiépítés konfigurációjának teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

* Amikor egy felhasználót a Foodee-hez rendel, válassza ki a **Hozzárendelés** ablaktáblában az érvényes alkalmazásspecifikus szerepkört, ha az elérhető. Az alapértelmezett *hozzáférési* szerepkört használó felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-foodee-for-provisioning"></a>A Foodee beállítása kiépítéshez

Mielőtt konfigurálja a Foodee-t az Azure AD használatával történő automatikus felhasználói kiépítéshez, engedélyeznie kell a System for Cross-domain Identity Management (SCIM) kiépítést a Foodee-ben.

1. Jelentkezzen be a [Foodee,](https://www.food.ee/login/)majd válassza ki a bérlőazonosítóját.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. A **Vállalati portál csoportban**válassza **az Egyszeri bejelentkezés lehetőséget.**

    ![A Foodee Enterprise Portal bal oldali ablaktábla menüje](media/Foodee-provisioning-tutorial/scim.png)

1. Másolja az **api-token** mezőben lévő értéket későbbi használatra. Adja meg a **titkos jogkivonat** mezőben a **Kiépítés** lapon a Foodee alkalmazás az Azure Portalon.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Add Foodee a galériából

A Foodee konfigurálásához az Azure AD használatával automatikus felhasználói kiépítéshez hozzá kell adnia a Foodee-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

Ha hozzá szeretné adni a Foodee-t az Azure AD alkalmazásgyűjteményből, tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory parancs](common/select-azuread.png)

1. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablaktábla tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

1. A keresőmezőbe írja be a **Foodee**, válassza **foodee** az eredménypanelen, majd válassza **hozzáadás** az alkalmazás hozzáadásához.

    ![Foodee az eredmények listájában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Automatikus felhasználói kiépítés konfigurálása a Foodee szolgáltatásba 

Ebben a szakaszban konfigurálja az Azure AD-kiépítési szolgáltatás létrehozása, frissítése és letiltása a felhasználók vagy csoportok Foodee felhasználói vagy csoport-hozzárendelések az Azure AD-ben.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezést is engedélyezheti a Foodee számára, ha követi a [Foodee egyszeri bejelentkezési útmutató](Foodee-tutorial.md)utasításait. Az automatikus felhasználói kiépítéstől függetlenül konfigurálhatja az egyszeri bejelentkezést, bár ez a két szolgáltatás kiegészíti egymást.

Konfigurálja a Foodee automatikus felhasználói kiépítését az Azure AD-ben az alábbi módon:

1. Az [Azure portalon](https://portal.azure.com)válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az **Alkalmazások** listában válassza a **Foodee**lehetőséget.

    ![A Foodee link az Alkalmazások listában](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1. A **Kiépítési mód** legördülő listában válassza az **Automatikus**lehetőséget.

    ![Kiépítés lap](common/provisioning-automatic.png)

1. A **Rendszergazdai hitelesítő adatok csoportban**tegye a következőket:

   a. A **Bérlő URL-címe** mezőbe írja be a **https:\//concierge.food.ee/scim/v2** korábban beolvasott értéket.

   b. A **titkos jogkivonat** mezőbe írja be a korábban beolvasott **API-token** értékét.
   
   c. Annak biztosításához, hogy az Azure AD csatlakozni tud a Foodee-hez, válassza a **Kapcsolat tesztelése**lehetőséget. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Foodee-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![A Kapcsolat tesztelése hivatkozás](common/provisioning-testconnection-tenanturltoken.png)

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, majd jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Az értesítési e-mail szövegmező](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **Leképezések**csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása foodee-hez**lehetőséget.

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/usermapping.png)

1. Az **Attribútum-leképezések**csoportban tekintse át az Azure AD-ről foodee-re szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a Foodee *felhasználói fiókjainak* egyeztetésére szolgálnak a frissítési műveletekhez. 

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/userattribute.png)

1. A módosítások véglegesítéséhez válassza a **Mentés gombot.**
1. A **Leképezések csoportban**válassza **az Azure Active Directory-csoportok szinkronizálása a Foodee-vel**lehetőséget.

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Az **Attribútum-leképezések**csoportban tekintse át az Azure AD-ről foodee-re szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a Foodee *csoportfiókjainak* egyeztetésére szolgálnak a frissítési műveletekhez.

    ![Foodee felhasználói leképezések](media/Foodee-provisioning-tutorial/groupattribute.png)

1. A módosítások véglegesítéséhez válassza a **Mentés gombot.**
1. Konfigurálja a hatókörszűrőket. Ennek módjáról a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található utasítások ban olvashat.

1. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Foodee, a **Beállítások** szakaszban módosítsa a **kiépítési állapot** **on.**

    ![A kiépítés állapota kapcsoló](common/provisioning-toggle-on.png)

1. A **Beállítások csoportBan**a **Hatókör** legördülő listában adja meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Foodee-be.

    ![A Kiépítési hatókör legördülő lista](common/provisioning-scope.png)

1. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![A Konfiguráció stacikálása gomb](common/provisioning-configuration-save.png)

Az előző művelet elindítja a **Hatókör** legördülő listában definiált felhasználók vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ: [Mennyi ideig tart a felhasználók kiépítése?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Az Aktuális **állapot** szakasz segítségével figyelheti a folyamatot, és követheti a létesítési tevékenységjelentésre mutató hivatkozásokat. A jelentés ismerteti az azure AD-kiépítési szolgáltatás a Foodee által végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
