---
title: 'Oktatóanyag: az SAP Cloud platform Identity Authentication konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez az SAP Cloud platform Identity Authentication szolgáltatásban.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060424"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Oktatóanyag: az SAP Cloud platform Identity Authentication konfigurálása automatikus felhasználó-kiépítés esetén

Ennek az oktatóanyagnak a célja, hogy bemutassa az SAP Cloud platform Identity Authentication szolgáltatásban végrehajtandó lépéseket, valamint Azure Active Directory (Azure AD) az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat az SAP Cloud platform Identity Authentication hitelesítéshez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [SAP Cloud platform identitás-hitelesítési bérlő](https://cloudplatform.sap.com/pricing.html)
* Felhasználói fiók az SAP Cloud platform identitás-hitelesítéséhez rendszergazdai engedélyekkel.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Felhasználók kiosztása az SAP Cloud platform identitás-hitelesítéséhez

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai férhetnek hozzá az SAP Cloud platform identitás-hitelesítéséhez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az SAP Cloud platform Identity Authentication szolgáltatáshoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Fontos Tippek a felhasználók SAP Cloud platform identitás-hitelesítéshez való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az SAP Cloud platform Identity Authentication szolgáltatáshoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót az SAP Cloud platform identitás-hitelesítéshez rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>SAP Cloud platform-identitás hitelesítésének beállítása a kiépítés számára

1. Jelentkezzen be az [SAP Cloud platform Identity Authentication felügyeleti konzolján](https://sapmsftintegration.accounts.ondemand.com/admin). Navigáljon a **felhasználók & engedélyek > rendszergazdák**.

    ![SAP Cloud platform Identity Authentication felügyeleti konzol](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Hozzon létre egy rendszergazda felhasználót, és válassza ki a felhasználót.  

3.  Az engedélyek konfigurálása területen kapcsolja be a váltás gombot a **felhasználók kezelése** és a **csoportok kezelése**elemnél.

    ![SAP Cloud platform Identity Authentication SCIM hozzáadása](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Egy e-mailt fog kapni a fiók aktiválásához és a jelszó beállításához az **SAP Cloud platform Identity Authentication szolgáltatáshoz**.

4.  Másolja a **felhasználói azonosítót** és a **jelszót**. Ezeket az értékeket a rendszer az SAP Cloud platform Identity Authentication alkalmazás üzembe helyezés lapján, a rendszergazdai Felhasználónév és a rendszergazda jelszava mezőkben adja meg a Azure Portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud platform Identity Authentication hozzáadása a katalógusból

Az SAP Cloud platform Identity Authentication konfigurálása előtt az Azure AD-vel való automatikus kiépítés előtt hozzá kell adnia az SAP Cloud platform Identity Authentication hitelesítést az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájára.

**Az SAP Cloud platform Identity Authentication az Azure AD Application Galleryből való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAP Cloud platform Identity Authentication**elemet, válassza az **SAP Cloud platform Identity Authentication** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SAP Cloud platform Identity Authentication az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Az automatikus felhasználó-kiépítés beállítása az SAP Cloud platform identitás-hitelesítésére 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján a felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához az SAP Cloud platform identitás-hitelesítésében.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést az SAP Cloud platform Identity Authentication esetében, az [SAP Cloud platform Identity Authentication egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Az Azure AD-beli SAP Cloud platform Identity Authentication automatikus felhasználó-kiépítés beállítása:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **SAP Cloud platform Identity Authentication**lehetőséget.

    ![Az SAP Cloud platform Identity Authentication hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a `https://<tenantID>.accounts.ondemand.com/service/scim ` a **bérlői URL-címben**. Adja meg a korábban a **rendszergazdai felhasználónévben** és a **rendszergazdai jelszóban** lekért **felhasználói azonosító** és **jelszó** értékét. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak az SAP Cloud platform Identity Authentication szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az SAP Cloud platform identitás-hitelesítési fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók SAP Cloud platform Identity Authentication**lehetőséget.

    ![SAP Cloud platform Identity Authentication felhasználói leképezések](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Tekintse át az Azure AD-ból az SAP Cloud platform Identity Authentication szolgáltatással szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az SAP Cloud platform Identity Authentication felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![SAP Cloud platform Identity Authentication felhasználói attribútumok](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást az SAP Cloud platform Identity Authentication szolgáltatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az SAP Cloud platform Identity Authentication szolgáltatáshoz szeretne kiépíteni, és válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik az SAP Cloud platform Identity Authentication használatával.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* Az SAP Cloud platform Identity Authentication SCIM-végpontjának bizonyos attribútumokra van szüksége, amelyeknek adott formátumúnak kell lenniük. Ezekről az attribútumokról és az adott formátumról [itt](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)talál további információt.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

