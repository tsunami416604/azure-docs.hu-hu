---
title: 'Oktatóanyag: A Zscaler One konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok zscaler One-ra történő automatikus kiépítésére és megszüntetésére.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064172"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler One konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag bemutatja a Zscaler One és az Azure Active Directory (Azure AD) végrehajtásának lépéseit az Azure AD konfigurálásához a felhasználók és csoportok zscaler One-ra történő automatikus kiépítéséhez és a kiépítéshez való hozzá.

> [!NOTE]
> Ez az oktatóanyag egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatásra épül. A szolgáltatás működéséről, működéséről és a gyakori kérdésekről a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása az Azure Active Directoryval című](../active-directory-saas-app-provisioning.md)témakörben talál.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy a következőket ismerteel:

* Egy Azure AD-bérlő.
* Egy Zscaler 1 bérlő.
* Egy felhasználói fiók a Zscaler One-ban rendszergazdai engedélyekkel.

> [!NOTE]
> Az Azure AD-kiépítési integráció a Zscaler One SCIM API-t. Ez az API a Zscaler One fejlesztői számára érhető el az Enterprise csomaggal rendelkező fiókok számára.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Zscaler One hozzáadása az Azure Piactérről

Mielőtt konfigurálja a Zscaler One-t az Azure AD-vel való automatikus felhasználói kiépítéshez, adja hozzá a Zscaler One-t az Azure Piactérről a felügyelt SaaS-alkalmazások listájához.

Zscaler One hozzáadása a Piactérről, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory ikonja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler One értéket,** és válassza a **Zscaler One lehetőséget** az eredménypanelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Zscaler One az eredménylistában](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Felhasználók hozzárendelése a Zscaler One-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Zscaler One-hoz. Ha ezeket a felhasználókat vagy csoportokat a Zscaler One-hoz szeretné hozzárendelni, kövesse a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)című útmutató utasításait.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Fontos tippek a felhasználók Zscaler One-hoz való hozzárendeléséhez

* Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó zscaler One az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

* Amikor egy felhasználót hozzárendel a Zscaler One-hoz, a hozzárendelés párbeszédpanelen válassza ki az érvényes alkalmazásspecifikus szerepkört, ha rendelkezésre áll. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Automatikus felhasználói kiépítés konfigurálása a Zscaler One rendszerbe

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit. Segítségével hozhat létre, frissíthet és letilthatja a felhasználókat vagy csoportokat a Zscaler One-ban az Azure AD felhasználói vagy csoport-hozzárendelései alapján.

> [!TIP]
> Saml-alapú egyszeri bejelentkezést is engedélyezhet a Zscaler One számára. Kövesse az utasításokat a [Zscaler Egy egyszeri bejelentkezés bemutató](zscaler-One-tutorial.md). Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Automatikus felhasználói kiépítés konfigurálása a Zscaler One számára az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások** > lehetőséget Minden**zscaler one****alkalmazás** > .

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler One**lehetőséget.

    ![A Zscaler One hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Zscaler 1 kiépítés](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Zscaler 1 kiépítési mód](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban töltse ki a **bérlői URL-cím** és a **titkos jogkivonat** mezőket a Zscaler One fiók beállításaival a 6.

6. A bérlői URL-cím és a titkos jogkivonat beszerzéséhez nyissa meg a **Felügyeleti** > **hitelesítési beállítások a** Zscaler One portál felhasználói felületén. A **Hitelesítés típusa csoportban**válassza az **SAML**lehetőséget.

    ![Zscaler 1 hitelesítési beállítások](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Az **SAML konfigurálása** lehetőséget az **SAML konfigurálása** beállítás megnyitásához válassza.

    ![Zscaler One konfigurálása SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Válassza **az Scim-alapú kiépítés engedélyezése** lehetőséget az Alap **URL-cím** és a **tulajdonosi jogkivonat**beállításainak bekéréséhez. Ezután mentse a beállításokat. Másolja az **alap URL-beállítást** **a bérlői URL-címre** az Azure Portalon. Másolja a **tulajdonosi token** beállítást **titkos jogkivonatra** az Azure Portalon.

7. Miután kitöltötte az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Zscaler One fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Zscaler One tesztkapcsolat](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Az **Értesítési e-mail mezőbe** írja be a személy vagy csoport e-mail címét a kiépítési hibaértesítések fogadásához. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Zscaler One értesítési e-mail](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Zscaler One rendszerhez**lehetőséget.

    ![Zscaler Egy felhasználó szinkronizálása](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD és a Zscaler One között szinkronizált felhasználói attribútumokat az **Attribútum-leképezések** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler One felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Zscaler One egyező felhasználói attribútumok](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Zscaler One szolgáltatással**lehetőséget.

    ![Zscaler Egy csoport szinkronizálása](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD és a Zscaler One között szinkronizált csoportattribútumokat az **Attribútumleképezések** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler One csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Zscaler Egy egyező csoportattribútum](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. A hatókörszűrők konfigurálásához kövesse a [hatókörszűrő oktatóanyagának utasításait.](./../active-directory-saas-scoping-filters.md)

15. Az Azure AD-létesítési szolgáltatás zscaler one engedélyezéséhez a **Beállítások** szakaszban módosítsa **a kiépítés állapotát** **Be**állapotra.

    ![Zscaler 1 kiépítési állapot](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Adja meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Zscaler One-ba. A **Beállítások csoportban** jelölje ki a kívánt értékeket a **Hatókör területen.**

    ![Zscaler egy hatókör](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Zscaler One mentés](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ezek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenység jelentésre mutató hivatkozásokat. A jelentés ismerteti az Azure AD-kiépítési szolgáltatás zscaler one által végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../active-directory-saas-provisioning-reporting.md)olvashat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
