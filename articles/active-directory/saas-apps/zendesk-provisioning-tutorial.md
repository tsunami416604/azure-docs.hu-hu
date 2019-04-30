---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Zendesk |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókokat a Zendeskhez megszüntetése.
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
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114665"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Zendesk konfigurálása

Ez az oktatóanyag bemutatja a Zendesk és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítése és a felhasználók és csoportok a Zendeskhez megszüntetése végrehajtásához szükséges lépéseket.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD felhasználói épül. Ez a szolgáltatás leírása, hogyan működik, és gyakran ismételt kérdések kapcsolatos tudnivalókat lásd: [felhasználói kiépítésének és megszüntetésének biztosítása az Azure Active Directory-as-szoftverszolgáltatások (SaaS) alkalmazások automatizálása](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik:

* Egy Azure AD-bérlő.
* Egy Zendesk bérlőt a [vállalati](https://www.zendesk.com/product/pricing/) a terv és jobban engedélyezve van.
* Egy felhasználói fiókot a Zendeskben rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Zendesk Rest API-val](https://developer.zendesk.com/rest_api/docs/core/introduction). Az API-t a Zendesk Teams vállalati csomaggal vagy jobb érhető el.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Zendesk hozzáadása az Azure Marketplace-ről

Zendesk beállítása az Azure AD-felhasználók automatikus, mielőtt hozzá Zendesk az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listája.

A Marketplace-ről Zendesk hozzáadásához kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), válassza a bal oldali navigációs panelen, **Azure Active Directory**.

    ![Az Azure Active Directory ikonra](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zendesk** válassza **Zendesk** az eredmény panelen. Az alkalmazás hozzáadásához válassza **Hozzáadás**.

    ![A Zendesk a találatok listájában](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Felhasználók hozzárendelése Zendesk

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók vagy csoportok, amelyek az alkalmazások az Azure AD hozzá lett rendelve a rendszer szinkronizálja.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, döntse el, mely felhasználók vagy csoportok Azure AD-ben a Zendesk hozzáférésre van szükségük. Ezek a felhasználók vagy csoportok hozzárendelése Zendesk, kövesse a [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Felhasználók hozzárendelése Zendesk fontos tippek

* Még ma, Zendesk-szerepköröket a rendszer dinamikusan és automatikusan kitölti az Azure Portal felhasználói felületén. Zendesk-szerepkört rendel felhasználókhoz, előtt ellenőrizze, hogy befejeződött-e egy kezdeti szinkronizálást elleni Zendesk beolvasni a legújabb szerepköröket, a Zendesk-bérlőben.

* Azt javasoljuk, hogy hozzárendelje egyetlen Azure AD-felhasználót a Zendeskhez a kezdeti felhasználók automatikus konfiguráció teszteléséhez. Hozzárendelhet további felhasználókat vagy csoportokat később követően a tesztek sikere esetén.

* Felhasználó hozzárendelése a Zendeskhez, jelölje ki bármely érvényes alkalmazásspecifikus szerepkör érhető el, ha a hozzárendelés párbeszédpanel. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Felhasználók automatikus átadása a Zendeskhez konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás konfigurálásának lépéseit. Vele létrehozása, frissítése és tiltsa le a felhasználókat és csoportokat a Zendeskben az Azure AD-felhasználó vagy csoport-hozzárendelések alapján.

> [!TIP]
> Emellett engedélyezheti az SAML-alapú egyszeri bejelentkezés Zendesk. Kövesse az utasításokat a [Zendesk egyszeri bejelentkezéses oktatóanyag](zendesk-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, azonban ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurálja a felhasználók automatikus átadása Zendesk az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások** > **minden alkalmazás** > **Zendesk**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zendesk**.

    ![A Zendesk-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zendesk-Kiépítési mód](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adjon meg a rendszergazda felhasználóneve, titkos jogkivonat és tartományát, a Zendesk-fiókjába. Példák a következő értékek a következők:

   * Az a **rendszergazdai felhasználónév** mezőben adja meg a rendszergazdai fiók, a Zendesk-bérlő felhasználóneve. Például: admin@contoso.com.

   * Az a **titkos jogkivonat** mezőben adja meg a titkos jogkivonat 6. lépésben leírtak szerint.

   * Az a **tartomány** mezőben adja meg az altartományt, a Zendesk-bérlő. Például egy fiókot a bérlői URL-címet `https://my-tenant.zendesk.com`, az altartomány van **saját bérlő**.

6. A Zendesk-fiókja titkos jogkivonat található **rendszergazdai** > **API** > **beállítások**. Győződjön meg arról, hogy **hozzáférés a jogkivonatokhoz** értékre van állítva **engedélyezve**.

    ![Zendesk-rendszergazdai beállítások](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk titkos jogkivonat](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 5. lépés látható a mezők kitöltése után válassza ki a **kapcsolat tesztelése** , győződjön meg arról, hogy az Azure AD a Zendeskhez csatlakozik. Ha a kapcsolat nem sikerül, ellenőrizze, hogy Zendesk-fiókja rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![Kapcsolat Zendesk tesztelése](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Az a **értesítő e-mailt** mezőben adja meg a személy e-mail-címét vagy az üzembe helyezési hiba értesítést szeretne kapni a csoportban. Válassza ki a **e-mail-értesítés küldése, ha hiba történik** jelölőnégyzetet.

    ![Zendesk értesítő e-mailt](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kattintson a **Mentés** gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Zendeskhez**.

    ![Zendesk-felhasználók szinkronizálása](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Tekintse át a a Zendeskhez, az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Zendesk a frissítési műveleteket. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Zendesk egyező felhasználói attribútumok](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportok a Zendeskhez**.

    ![Zendesk-csoport szinkronizálása](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Tekintse át a csoportattribútumok szinkronizált Azure AD-ből, a Zendesk a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zendeskben csoportjai megfelelően szolgálnak. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Zendesk egyező csoportattribútumok](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Hatókörszűrő konfigurálásához kövesse az utasításokat a [hatókörkezelési szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Ahhoz, hogy az Azure AD létesítési szolgáltatás a Zendesk, a a **beállítások** területén módosíthatja **üzembe helyezési állapotra** való **a**.

    ![Zendesk-kiépítési állapot](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Adja meg a felhasználókat vagy csoportokat, amelyekhez szeretne üzembe helyezni a Zendeskhez. Az a **beállítások** területen válassza ki a kívánt értékeket **hatókör**.

    ![Zendesk-hatókör](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Ha készen áll rendelkezésre, válassza ki a **mentése**.

    ![Zendesk mentése](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó vagy csoport meghatározott **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az újabb szinkronizálás hosszabb időt vesz igénybe. Mindaddig, amíg az Azure AD létesítési szolgáltatás fut bekövetkezésük körülbelül 40 percenként. 

Használhatja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat, a kiépítési tevékenységek jelentésének szakaszt. A jelentés a Zendesk-kiszolgáló üzembe helyezése az Azure AD által végrehajtott összes műveletet ismerteti.

Olvassa el az Azure AD létesítési naplók információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Zendesk rendelkező felhasználók számára csoportok használatát támogatja **ügynök** szerepkörök csak. További információkért lásd: a [Zendesk-dokumentáció](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Ha egy egyéni szerepkör van rendelve egy felhasználóhoz vagy csoporthoz, az Azure AD felhasználók automatikus szolgáltatás is hozzárendeli az alapértelmezett szerepkör **ügynök**. Csak az ügynökök egy egyéni szerepkört is rendelhető. További információkért lásd: a [Zendesk-API dokumentációja](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
