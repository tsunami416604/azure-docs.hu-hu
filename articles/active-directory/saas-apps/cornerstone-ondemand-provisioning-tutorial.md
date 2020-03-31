---
title: 'Oktatóanyag: Az Azure Active Directoryval való automatikus felhasználói kiépítéshez konfigurálja a Cornerstone OnDemand szolgáltatást | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok cornerstone OnDemand-re való automatikus kiépítésére és megszüntetésére.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058424"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Oktatóanyag: A Sarokkő igényének konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag bemutatja a Cornerstone OnDemand és az Azure Active Directory (Azure AD) végrehajtásának lépéseit az Azure AD konfigurálásához, hogy a felhasználók vagy csoportok automatikusan kiépítsék és megszüntetzék a Cornerstone OnDemand szolgáltatást.

> [!NOTE]
> Ez az oktatóanyag egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatásra épül. A szolgáltatás működéséről, működéséről és a gyakori kérdésekről a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása az Azure Active Directoryval című](../app-provisioning/user-provisioning.md)témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy a következőket ismerteel:

* Egy Azure AD-bérlő.
* Sarokköve OnDemand bérlő.
* A Cornerstone OnDemand rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

> [!NOTE]
> Az Azure AD-kiépítési integráció a [Cornerstone OnDemand webszolgáltatásra](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)támaszkodik. Ez a szolgáltatás elérhető a Cornerstone OnDemand csapatok.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Sarokköves igény hozzáadása az Azure Piactérről

Mielőtt konfigurálja a Cornerstone OnDemand automatikus felhasználói kiépítést az Azure AD-vel, adja hozzá a Cornerstone OnDemand-ot a Piactérről a felügyelt SaaS-alkalmazások listájához.

A Cornerstone OnDemand hozzáadása a Piactérről, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory ikonja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Cornerstone OnDemand** kifejezést, és válassza a **Cornerstone OnDemand** lehetőséget az eredménypanelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Sarokköve OnDemand az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Felhasználók hozzárendelése a Cornerstone OnDemand-hez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Cornerstone OnDemand-hez. Ha ezeket a felhasználókat vagy csoportokat a Cornerstone OnDemand-hoz szeretné hozzárendelni, kövesse a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című útmutató utasításait.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Fontos tippek a felhasználók Cornerstone OnDemand-hez való hozzárendeléséhez

* Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó cornerstone OnDemand az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

* Amikor egy felhasználót hozzárendel a Cornerstone OnDemand-hez, a hozzárendelés párbeszédpanelen jelöljön ki egy érvényes alkalmazásspecifikus szerepkört, ha rendelkezésre áll. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Automatikus felhasználói kiépítés konfigurálása a Cornerstone OnDemand rendszerbe

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit. Segítségével hozhat létre, frissíthet és letilthatja a felhasználókat vagy csoportokat a Cornerstone OnDemand szolgáltatásban az Azure AD felhasználói vagy csoport-hozzárendelései alapján.

Konfigurálása automatikus felhasználói kiépítés cornerstone OnDemand az Azure AD-ben, kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza a **Vállalati alkalmazások** > **lehetőséget Minden alkalmazás** > **Cornerstone OnDemand**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Cornerstone OnDemand**lehetőséget.

    ![A Cornerstone OnDemand hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Sarokköve OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa **a létesítési módot** **automatikusra.**

    ![Sarokköve OnDemand kiépítési mód](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. A **Rendszergazdai hitelesítő adatok** csoportban adja meg a Cornerstone OnDemand fiókjának rendszergazdai felhasználónevét, rendszergazdai jelszavát és tartományát:

    * A **Rendszergazdai felhasználónév** mezőben töltse ki a tartományt vagy a rendszergazdai fiók felhasználónevét a Cornerstone OnDemand bérlőn. Egy példa a contoso\admin.

    * A **Rendszergazdai jelszó** mezőben adja meg a rendszergazdai felhasználónévnek megfelelő jelszót.

    * A **Domain (Tartomány)** mezőben töltse ki a sarokkő ondemand-bérlő webszolgáltatás URL-címét. A szolgáltatás például a `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`területén található, a Contoso esetében pedig a `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`tartomány. A webszolgáltatás URL-címének beolvasásáról további információt a [pdf című dokumentumban](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)talál.

6. Miután kitöltötte az 5. **Test Connection** Ha a kapcsolat sikertelen, győződjön meg arról, hogy a Cornerstone OnDemand fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![Sarokköve On Demand teszt kapcsolat](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Az **Értesítési e-mail mezőbe** írja be a személy vagy csoport e-mail címét a kiépítési hibaértesítések fogadásához. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Sarokköve OnDemand értesítési e-mail](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a Cornerstone OnDemand (Alapszabályrendszer) lehetőséget.**

    ![Sarokköve OnDemand szinkronizálás](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Tekintse át az Azure AD-ről cornerstone OnDemand kapcsolatra szinkronizált felhasználói attribútumokat az **Attribútum-leképezések** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Rendszer a Cornerstone OnDemand felhasználói fiókjainak egyeztetésére szolgál a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Sarokköve OnDemand attribútum leképezések](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. A hatókörszűrők konfigurálásához kövesse a [hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Cornerstone OnDemand, a **Beállítások** szakaszban módosítsa **a kiépítés állapotát** **on.**

    ![Sarokköve OnDemand kiépítési állapot](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Adja meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Cornerstone OnDemand-ba. A **Beállítások csoportban** jelölje ki a kívánt értékeket a **Hatókör területen.**

    ![Sarokköve On Demand hatókör](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Sarokköve OnDemand Mentés](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ezek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenység jelentésre mutató hivatkozásokat. A jelentés ismerteti az Azure AD-kiépítési szolgáltatás cornerstone OnDemand által végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat.

## <a name="connector-limitations"></a>Összekötő korlátai

A Cornerstone OnDemand **Position** attribútum olyan értéket vár, amely megfelel a Cornerstone OnDemand portálon lévő szerepköröknek. Az érvényes **pozícióértékek** listájának lekéréséhez nyissa meg **a Felhasználói rekord szerkesztése > szervezeti felépítés > pozíció** a Sarokkő OnDemand portálon.

![Sarokköve OnDemand kiépítése Felhasználói rekord szerkesztése](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Sarokköve OnDemand kiépítési pozíció](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Sarokköve OnDemand kiépítési pozíció lista](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
