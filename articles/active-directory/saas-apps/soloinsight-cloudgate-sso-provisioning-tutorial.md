---
title: 'Oktatóanyag: a Soloinsight-CloudGate egyszeri bejelentkezés konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Soloinsight-CloudGate SSO számára.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063194"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Oktatóanyag: a Soloinsight konfigurálása – CloudGate SSO automatikus felhasználó-kiépítés esetén

Ennek az oktatóanyagnak a célja, hogy bemutassa a Soloinsight-CloudGate SSO és a Azure Active Directory (Azure AD) által elvégzendő lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok automatikus kiépítéséhez és a Soloinsight-CloudGate egyszeri bejelentkezéshez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Soloinsight – CloudGate SSO-bérlő](https://www.soloinsight.com/)
* Felhasználói fiók a Soloinsight – CloudGate egyszeri bejelentkezés rendszergazdai engedélyekkel.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Felhasználók kiosztása Soloinsight-CloudGate SSO-hoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok számára van szükség az Azure AD-ben a Soloinsight-CloudGate SSO eléréséhez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a következő utasítások követésével rendelheti hozzá a Soloinsight-CloudGate SSO-hoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Fontos Tippek a felhasználók Soloinsight-CloudGate egyszeri bejelentkezéshez való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Soloinsight-CloudGate SSO-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Soloinsight-CloudGate SSO-hoz, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Soloinsight-CloudGate SSO beállítása a kiépítés számára

1. Jelentkezzen be a [Soloinsight-CLOUDGATE SSO felügyeleti konzolra](https://soloinsight.sigateway.com/login). Navigáljon az **adminisztráció > rendszerbeállítások elemre**.

    ![Soloinsight – CloudGate SSO felügyeleti konzol](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Válassza az **általános**lehetőséget.

    ![Soloinsight – CloudGate SSO – SCIM hozzáadása](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Görgessen le a lap végéig a **bérlői URL-cím** és a **titkos jogkivonat**lekéréséhez. Másolja a **titkos jogkivonatot**. Ez az érték a Soloinsight CloudGate SSO-alkalmazás létesítés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.

    ![Soloinsight – CloudGate SSO-létrehozási token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt a Soloinsight-CloudGate SSO konfigurálása előtt hozzá kell adnia a Soloinsight-CloudGate SSO-t az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Soloinsight-CloudGate SSO-t az Azure AD-alkalmazás-katalógusból szeretné hozzáadni, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Soloinsight-CLOUDGATE SSO**, válassza a **SOLOINSIGHT-CloudGate SSO** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Soloinsight – CloudGate SSO az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Automatikus felhasználó-kiépítés konfigurálása a Soloinsight-CloudGate SSO-hoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és letilthat a Soloinsight-CloudGate SSO-ban az Azure AD-ben a felhasználók és/vagy csoportok hozzárendelései alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Soloinsight-CloudGate SSO számára, a [Soloinsight-CLOUDGATE SSO egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása a Soloinsight-CloudGate egyszeri bejelentkezéshez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Soloinsight-CLOUDGATE SSO**elemet.

    ![Az Soloinsight-CloudGate SSO-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://sigateway.com/scim/v2/sync/serviceproviderconfig` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a SOLOINSIGHT CloudGate SSO-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Soloinsight-CloudGate SSO-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók SOLOINSIGHT – CloudGate SSO**elemet.

    ![Soloinsight – CloudGate SSO felhasználói leképezések](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az Soloinsight-CloudGate SSO elemre az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Soloinsight-CloudGate SSO felhasználói fiókjainak a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Soloinsight – CloudGate SSO felhasználói attribútumok](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása SOLOINSIGHT – CloudGate SSO**elemet.

    ![Soloinsight – CloudGate SSO csoportos megfeleltetések](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ről szinkronizált Soloinsight-CloudGate SSO-t az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Soloinsight-CloudGate SSO-ben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Soloinsight – CloudGate SSO-csoport attribútumai](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Soloinsight-CloudGate egyszeri bejelentkezéshez, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Soloinsight-CloudGate SSO számára szeretne kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által az Soloinsight-CloudGate SSO-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

