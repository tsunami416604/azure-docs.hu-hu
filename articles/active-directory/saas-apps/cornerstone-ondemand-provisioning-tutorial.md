---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása alapkövei OnDemand |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókok alapkövei OnDemand megszüntetése.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127497"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása alapkövei OnDemand konfigurálása

Ez az oktatóanyag bemutatja a alapkövei OnDemand és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítése és a felhasználók vagy csoportok alapkövei OnDemand megszüntetése végrehajtásához szükséges lépéseket.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD felhasználói épül. Ez a szolgáltatás leírása, hogyan működik, és gyakran ismételt kérdések kapcsolatos tudnivalókat lásd: [felhasználói kiépítésének és megszüntetésének biztosítása az Azure Active Directory-as-szoftverszolgáltatások (SaaS) alkalmazások automatizálása](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik:

* Egy Azure AD-bérlő.
* A bérlő egy OnDemand alapkövei.
* Egy felhasználói fiókot a alapkövei OnDemand rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [alapkövei OnDemand webszolgáltatás](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Ez a szolgáltatás alapkövei OnDemand csoportjaink számára érhető el.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Az Azure Marketplace-ről alapkövei OnDemand hozzáadása

Mielőtt alapkövei OnDemand konfigurál az Azure AD-felhasználók automatikus számára, hozzá alapkövei OnDemand a Marketplace-ről a felügyelt SaaS-alkalmazások listája.

A Marketplace-ről alapkövei OnDemand hozzáadásához kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), válassza a bal oldali navigációs panelen, **Azure Active Directory**.

    ![Az Azure Active Directory ikonra](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **alapkövei OnDemand** válassza **alapkövei OnDemand** az eredmény panelen. Az alkalmazás hozzáadásához válassza **Hozzáadás**.

    ![Az eredmények listájában alapkövei OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Felhasználók hozzárendelése alapkövei OnDemand

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók vagy csoportok, amelyek az alkalmazások az Azure AD hozzá lett rendelve a rendszer szinkronizálja.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, döntse el, mely felhasználók vagy csoportok Azure AD-ben alapkövei OnDemand hozzáférésre van szükségük. Ezek a felhasználók vagy csoportok hozzárendelése alapkövei OnDemand, kövesse a [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Felhasználók hozzárendelése alapkövei OnDemand fontos tippek

* Azt javasoljuk, hogy hozzárendelje egyetlen alapkövei OnDemand a felhasználók automatikus konfiguráció tesztelése az Azure AD-felhasználót. Hozzárendelhet további felhasználókat vagy csoportokat később.

* Ha egy felhasználó alapkövei OnDemand rendeli, válassza ki bármilyen érvényes alkalmazásspecifikus szerepkör érhető el, ha a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Felhasználók automatikus átadása az alapkövei OnDemand konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás konfigurálásának lépéseit. Vele létrehozása, frissítése és tiltsa le a felhasználók vagy csoportok alapkövei OnDemand az Azure AD-felhasználó vagy csoport-hozzárendelések alapján.

Felhasználók automatikus átadása alapkövei OnDemand az Azure AD-ben konfigurálásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások** > **minden alkalmazás** > **alapkövei OnDemand**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **alapkövei OnDemand**.

    ![Az alkalmazások listáját a alapkövei OnDemand hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Alapkövei OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be **Kiépítési mód** való **automatikus**.

    ![Alapkövei OnDemand Kiépítési mód](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a rendszergazdai felhasználónevet, a rendszergazdai jelszó és a tartomány a alapkövei OnDemand-fiók:

    * Az a **rendszergazdai felhasználónév** mezőben adja meg a tartományt vagy a rendszergazdai fiók a alapkövei OnDemand bérlő felhasználóneve. Ez például akkor contoso\admin.

    * Az a **rendszergazdai jelszó** mezőben adja meg a jelszót, amely megfelel a rendszergazda felhasználóneve.

    * Az a **tartomány** mezőben adja meg a web service URL-címét a alapkövei OnDemand-bérlő. Ha például a szolgáltatás nem található: `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, és a Contoso tartomány van `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. A web service URL-cím lekérésével további információkért lásd: [e pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. 5. lépés látható a mezők kitöltése után válassza ki a **kapcsolat tesztelése** , győződjön meg arról, hogy az Azure AD alapkövei OnDemand csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, hogy a alapkövei OnDemand rendszergazdai engedélyekkel rendelkezzen, majd próbálkozzon újra.

    ![Kapcsolat a alapkövei OnDemand tesztelése](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Az a **értesítő e-mailt** mezőben adja meg a személy e-mail-címét vagy az üzembe helyezési hiba értesítést szeretne kapni a csoportban. Válassza ki a **e-mail-értesítés küldése, ha hiba történik** jelölőnégyzetet.

    ![Alapkövei OnDemand értesítő e-mailt](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Mentés** gombra.

9. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a alapkövei OnDemand**.

    ![Alapkövei OnDemand-szinkronizálás](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Tekintse át a alapkövei OnDemand a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek alapkövei OnDemand levő felhasználói fiókokat. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Alapkövei OnDemand attribútumleképezések](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Hatókörszűrő konfigurálásához kövesse az utasításokat a [hatókörkezelési szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Ahhoz, hogy az Azure AD létesítési szolgáltatás az OnDemand alapkövei, az a **beállítások** területén módosíthatja **üzembe helyezési állapotra** való **a**.

    ![Alapkövei OnDemand kiépítési állapot](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Adja meg a felhasználókat vagy csoportokat, amelyekhez szeretne üzembe helyezni a alapkövei OnDemand. Az a **beállítások** területen válassza ki a kívánt értékeket **hatókör**.

    ![Alapkövei OnDemand hatókör](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Ha készen áll rendelkezésre, válassza ki a **mentése**.

    ![Alapkövei OnDemand mentése](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó vagy csoport meghatározott **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az újabb szinkronizálás hosszabb időt vesz igénybe. Mindaddig, amíg az Azure AD létesítési szolgáltatás fut bekövetkezésük körülbelül 40 percenként. 

Használhatja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat, a kiépítési tevékenységek jelentésének szakaszt. A jelentés az Azure AD létesítési szolgáltatás a alapkövei OnDemand által végrehajtott összes műveletet ismerteti.

Olvassa el az Azure AD létesítési naplók információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

A alapkövei OnDemand **pozíció** attribútum, amely megfelel a szerepköröket a alapkövei OnDemand portál értéket vár. Érvényes listájának beolvasásához **pozíció** értékek, keresse fel **felhasználói rekord szerkesztése > szervezeti felépítés > pozíció** a alapkövei OnDemand-portálon.

![Cornerstone OnDemand Provisioning Edit User Record](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Alapkövei OnDemand-kiépítés pozíciója](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Alapkövei OnDemand-kiépítés pozíció listája](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
