---
title: 'Oktatóanyag: A felhasználóknak való automatikus kiépítés konfigurálásának meglátogatása Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a látogatáshoz.
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
ms.openlocfilehash: 381f9cb2cb9ef196149144d40332a1de3b90f188
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802757"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Oktatóanyag: Az automatikus felhasználó-kiépítés meglátogatásának konfigurálása

Ennek az oktatóanyagnak a célja, hogy bemutassa a felkeresett és Azure Active Directory (Azure AD) az Azure AD konfigurálásához szükséges lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a látogatáshoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Felkeresett bérlő](https://www.visitly.io/pricing/)
* Egy felhasználói fiók látogatása rendszergazdai engedélyekkel.

## <a name="assigning-users-to-visitly"></a>Felhasználók kiosztása a látogatáshoz 

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok számára van szükség az Azure AD-ben való részvételre. A döntés megkezdése után a felhasználókat és/vagy csoportokat a következő útmutatás alapján rendelheti hozzá a látogatáshoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Fontos Tippek a felhasználók a látogatáshoz való hozzárendeléséhez 

* Javasoljuk, hogy egyetlen Azure AD-felhasználóhoz legyen hozzárendelve az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználókat rendel a látogatáshoz, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-visitly-for-provisioning"></a>A telepítés meglátogatása a kiépítés során

Az Azure AD-vel való automatikus felhasználói kiépítés előtt a látogatás előtt engedélyeznie kell a SCIM-létesítést.

1. Jelentkezzen be a [webhelyre](https://app.visitly.io/login). Kattintson az **integrációk** > **gazdagép szinkronizálása**elemre.

    ![Visitly](media/Visitly-provisioning-tutorial/login.png)

2. Görgessen le az **Azure ad kiválasztása szakasz**

    ![Visitly](media/Visitly-provisioning-tutorial/integration.png)

3. Másolja az **API-kulcsot**. Ezek az értékek a Azure Portalban a felkeresett alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében lesznek megadva.

    ![Visitly](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Látogatások hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználói kiépítés megkezdéséhez az Azure AD-alkalmazás-katalógusban fel kell vennie a látogatást a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD Application Galleryből való felkereséshez hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A **keresőmezőbe írja be a felkeresett**elemet, válassza a **látogatás** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Látogatás az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-visitly"></a>Automatikus felhasználó-kiépítés beállítása a látogatáshoz  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy a felhasználók és/vagy csoportok látogatását, frissítését és letiltását az Azure AD-ben, felhasználói és/vagy csoportos hozzárendelések alapján hozza létre.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is lehetővé teszi a látogatás során, a [felkeresett egyszeri bejelentkezési oktatóanyagban](Visitly-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-visitly--in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való felkereséshez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **látogatás**lehetőséget.

    ![A felkeresett hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a ` https://api.visitly.io/v1/usersync/SCIM` **bérlői URL-címben** és a **titkos jogkivonatban** korábban lekért és az **API-kulcs** értékeit. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tud a látogatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a felkeresett fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók látogatása**lehetőséget.

    ![Felkeresett felhasználói leképezések](media/visitly-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok használatával a rendszer megkeresi a felhasználói fiókokat a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Felkeresett felhasználói attribútumok](media/visitly-provisioning-tutorial/userattribute.png)



10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a látogatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket fel szeretne venni a látogatáshoz, és válassza ki a kívánt értékeket a **Settings** ( **hatókör** ) területen.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a látogatás során végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../manage-apps/check-status-user-account-provisioning.md)során.

## <a name="connector-limitations"></a>Összekötő korlátozásai

A felkeresett nem támogatja a nehezen törölhető törléseket.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
