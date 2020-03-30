---
title: 'Oktatóanyag: A Zendesk konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és megszüntetje a felhasználói fiókokat a Zendesk számára.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062744"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Oktatóanyag: A Zendesk konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag bemutatja a Zendesk és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és csoportok zendeski automatikus kiépítéséhez és kiteljesítésének mentesítéséhez.

> [!NOTE]
> Ez az oktatóanyag egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatásra épül. A szolgáltatás működéséről, működéséről és a gyakori kérdésekről a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása az Azure Active Directoryval című](../app-provisioning/user-provisioning.md)témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy a következőket ismerteel:

* Egy Azure AD-bérlő.
* Zendesk-bérlő a Professional csomaggal vagy jobban engedélyezve.
* A Zendesk rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Zendesk hozzáadása az Azure Piactérről

Mielőtt konfigurálja a Zendesket az Azure AD-vel való automatikus felhasználói kiépítéshez, adja hozzá a Zendesket az Azure Piactérről a felügyelt SaaS-alkalmazások listájához.

Ha zendesket szeretne hozzáadni a Piactérről, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory ikonja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írd be a **Zendesk** et, és válaszd ki a **Zendesk** lehetőséget az eredménypanelről. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Zendesk az eredménylistában](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Felhasználók hozzárendelése a Zendeskhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Zendeskhez. Ha ezeket a felhasználókat vagy csoportokat hozzá szeretné rendelni a Zendeskhez, kövesse a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című útmutató utasításait.

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Fontos tippek a felhasználók Zendeskhez való hozzárendeléséhez

* Ma a Zendesk-szerepkörök automatikusan és dinamikusan töltődnek fel az Azure Portal felhasználói felületén. Mielőtt Zendesk szerepköröket rendelnél a felhasználókhoz, győződj meg arról, hogy a Zendesk kezdeti szinkronizálása befejeződött a Zendesk legújabb szerepköreinek lekéréséhez a Zendesk-bérlőben.

* Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó a Zendesk a kezdeti automatikus felhasználói kiépítési konfiguráció teszteléséhez. A tesztek sikeresse után később további felhasználókat vagy csoportokat rendelhet hozzá.

* Amikor egy felhasználót hozzárendel a Zendeskhez, a hozzárendelés párbeszédpanelen válasszon ki egy érvényes alkalmazásspecifikus szerepkört, ha rendelkezésre áll. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Automatikus felhasználói kiépítés konfigurálása a Zendesk szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit. Segítségével felhasználókat vagy csoportokat hozhat létre, frissíthet és tilthatjon le a Zendeskben az Azure AD felhasználói vagy csoport-hozzárendelései alapján.

> [!TIP]
> Saml-alapú egyszeri bejelentkezést is engedélyezhet a Zendesk számára. Kövesse a [Zendesk egyszeri bejelentkezési](zendesk-tutorial.md)útmutatóutasításait. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Automatikus felhasználói kiépítés konfigurálása a Zendesk hez az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások** > **Minden alkalmazás** > **Zendesk**.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zendesk**lehetőséget.

    ![A Zendesk hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Zendesk kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Zendesk kiépítési mód](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg zendesk-fiókjának rendszergazdai felhasználónevét, titkos jogkivonatát és tartományát. Ilyen értékek például a következők:

   * A **Rendszergazdai felhasználónév** mezőben add meg a Zendesk-bérlő rendszergazdai fiókjának felhasználónevét. Például: admin@contoso.com.

   * A **Titkos jogkivonat** mezőben töltse ki a titkos jogkivonatot a 6.

   * A **Domain (Tartomány)** mezőben töltse ki a Zendesk-bérlő altartományát. Például egy bérlői `https://my-tenant.zendesk.com`URL-címmel rendelkező fiók esetében az altartomány a **bérlőm.**

6. A Zendesk-fiók titkos tokenje az **Admin** > **API-beállítások****API** > ban található. Győződjön meg arról, hogy a **tokenaccess** **beállítása Engedélyezve.**

    ![Zendesk rendszergazdai beállítások](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk titkos token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Miután kitöltötte az 5. **Test Connection** Ha a kapcsolat nem sikerül, ellenőrizd, hogy a Zendesk-fiókod rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozz újra.

    ![Zendesk tesztkapcsolat](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Az **Értesítési e-mail mezőbe** írja be a személy vagy csoport e-mail címét a kiépítési hibaértesítések fogadásához. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Zendesk értesítő e-mail](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a Zendesk szolgáltatással**lehetőséget.

    ![Zendesk felhasználói szinkronizálás](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Tekintse át az Azure AD és a Zendesk között szinkronizált felhasználói attribútumokat az **Attribútum-leképezések** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Zendesk felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Zendesk egyező felhasználói attribútumok](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Zendesk szolgáltatással**lehetőséget.

    ![Zendesk csoport szinkronizálása](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Tekintse át az Azure AD és a Zendesk között szinkronizált csoportattribútumokat az **Attribútum-leképezések** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Zendesk csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Zendesk egyező csoport attribútumai](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. A hatókörszűrők konfigurálásához kövesse a [hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Zendesk számára a **Beállítások** szakaszban módosítsa a **Kiépítés állapotát** **Be**állásra.

    ![Zendesk kiépítési állapot](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Határozza meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Zendeskbe. A **Beállítások csoportban** jelölje ki a kívánt értékeket a **Hatókör területen.**

    ![Zendesk hatókör](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Zendesk mentés](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ezek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenység jelentésre mutató hivatkozásokat. A jelentés ismerteti az Azure AD-kiépítési szolgáltatás a Zendesk által végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Zendesk csak **ügynöki** szerepkörrel rendelkező felhasználók számára támogatja a csoportok használatát. További információt a [Zendesk dokumentációjában](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)talál.

* Ha egy egyéni szerepkör van rendelve egy felhasználó vagy csoport, az Azure AD automatikus felhasználói kiépítési szolgáltatás is hozzárendeli az alapértelmezett szerepkör **ügynök.** Csak az ügyintézők rendelhetők egyéni szerepkörhöz. További információt a [Zendesk API dokumentációjában](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)talál. 

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
