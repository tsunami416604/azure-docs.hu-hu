---
title: 'Oktatóanyag: a G Suite konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a G Suite-be.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8239b25f5ebf94d7368190f3169d0637d9943df1
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720290"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Oktatóanyag: a G Suite konfigurálása automatikus felhasználók kiépítési feladatokhoz

Ennek az oktatóanyagnak a célja, hogy bemutassa a G Suite-ban végrehajtandó lépéseket és Azure Active Directory (Azure AD) az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a G Suite-t.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).

> [!NOTE]
> A G Suite-összekötőt nemrég, október 2019-én frissítették. A G Suite-összekötőn végrehajtott módosítások a következők:
- További támogatás a G Suite-felhasználók és-csoportok további attribútumaihoz. 
- A G Suite-cél attribútumainak neve megegyezik az [itt](https://developers.google.com/admin-sdk/directory)definiált értékekkel.
- Az alapértelmezett attribútum-hozzárendelések frissítve.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a G Suite-nal való konfigurálásához a következő elemek szükségesek:

- Azure AD-bérlő
- [G Suite-bérlő](https://gsuite.google.com/pricing.html)
- A G Suite felhasználói fiókja rendszergazdai jogosultságokkal.

## <a name="assign-users-to-g-suite"></a>Felhasználók társítása G Suite-hoz

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok férhetnek hozzá a G Suite-hoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat a G Suite-hoz:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Fontos Tippek a felhasználóknak a G Suite-hoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a G Suite-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználó a G Suite-hoz van hozzárendelve, akkor a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-g-suite-for-provisioning"></a>A G Suite beállítása a kiépítés számára

Mielőtt konfigurálja a G Suite-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a SCIM-létesítést a G Suite-ban.

1. Jelentkezzen be a [G Suite felügyeleti konzolra](https://admin.google.com/) a rendszergazdai fiókjával, majd válassza a **Biztonság**elemet. Ha nem látja a hivatkozást, akkor előfordulhat, hogy a képernyő alján található **további vezérlők** menü alatt rejtve van.

    ![Válassza a biztonság elemet.][10]

1. A **Biztonság** lapon válassza az **API-hivatkozás**lehetőséget.

    ![Válassza az API-referenciát.][15]

1. Válassza az **API-hozzáférés engedélyezése**lehetőséget.

    ![Válassza az API-referenciát.][16]

   > [!IMPORTANT]
   > Minden, a G Suite-nak kiépíteni kívánt felhasználó számára az **Azure ad-** beli felhasználónevét egy egyéni tartományhoz kell kötni. Például a G Suite nem fogadja el a bob@contoso.onmicrosoft.com hasonló felhasználóneveket. Másfelől bob@contoso.com fogadjuk el. A meglévő felhasználó tartományát az [itt](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)található útmutatás alapján módosíthatja.

1. Miután hozzáadta és ellenőrizte a kívánt egyéni tartományokat az Azure AD-vel, újra ellenőriznie kell azokat a G Suite-val. A G Suite tartományának ellenőrzéséhez tekintse meg a következő lépéseket:

    a. A [G Suite felügyeleti konzolon](https://admin.google.com/)válassza a **tartományok**lehetőséget.

    ![Tartományok kiválasztása][20]

    b. Válassza **a tartomány hozzáadása vagy a tartományi alias**lehetőséget.

    ![Új tartomány hozzáadása][21]

    c. Válassza a **másik tartomány hozzáadása**lehetőséget, majd írja be a hozzáadni kívánt tartomány nevét.

    ![Írja be a tartománynevet][22]

    d. Válassza **a Folytatás lehetőséget, és ellenőrizze a tartomány tulajdonjogát**. Ezután kövesse a lépéseket annak ellenőrzéséhez, hogy a tartománynév tulajdonosa-e. A tartomány a Google-vel való ellenőrzésével kapcsolatos részletes utasításokért lásd: [a hely tulajdonjogának ellenőrzése](https://support.google.com/webmasters/answer/35179).

    e. Ismételje meg az előző lépéseket a G Suite-ba felvenni kívánt további tartományok esetében.

1. Ezután határozza meg, hogy melyik rendszergazdai fiókot szeretné használni a G Suite-ban a felhasználók üzembe helyezésének kezeléséhez. Navigáljon a **rendszergazdai szerepkörök**elemre.

    ![Google Apps kiválasztása][26]

1. A fiók **rendszergazdai szerepköréhez** szerkessze az adott szerepkörhöz tartozó **jogosultságokat** . Ügyeljen arra, hogy az összes **rendszergazdai API-jogosultságot** engedélyezze, hogy ez a fiók használható legyen az üzembe helyezéshez.

    ![Google Apps kiválasztása][27]

## <a name="add-g-suite-from-the-gallery"></a>G csomag hozzáadása a gyűjteményből

Ha a G Suite-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez szeretné konfigurálni, a G Suite-t hozzá kell adnia az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához. 

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

1. A keresőmezőbe írja be a **g Suite**kifejezést, válassza a **g Suite** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![G Suite az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Automatikus felhasználó-kiépítés beállítása a G Suite-ba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a G Suite-ban lévő felhasználókat és/vagy csoportokat.

> [!TIP]
> Dönthet úgy is, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a G Suite-hoz, a [g Suite egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

> [!NOTE]
> A G Suite címtár API-végpontjának megismeréséhez tekintse meg a [Directory API](https://developers.google.com/admin-sdk/directory)-t.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>A G Suite automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **G Suite**elemet.

    ![A G Suite-hivatkozás az alkalmazások listájában](common/all-applications.png)

1. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban válassza az **Engedélyezés**lehetőséget. Megnyílik egy Google-engedélyezési párbeszédpanel egy új böngészőablakban.

    ![G Suite engedélyezése](media/google-apps-provisioning-tutorial/authorize.png)

1. Győződjön meg arról, hogy az Azure AD-beli engedélyeket szeretné megadni a G Suite-bérlő módosításához. Válassza ki az **Elfogadás** lehetőséget.

    ![Erősítse meg az engedélyeket.][28]

1. A Azure Portal válassza a **kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure ad csatlakozhasson az alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a G Suite-fiókja rendelkezik a csoport rendszergazdai engedélyeivel. Ezután próbálja megismételni az **Engedélyezés** lépést.

1. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a G Suite-** ba lehetőséget.

    ![G Suite felhasználói leképezések](media/google-apps-provisioning-tutorial/usermappings.png)

1. Tekintse át az Azure AD-ból a G Suite-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a G Suite-ban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![G Suite felhasználói attribútumok](media/google-apps-provisioning-tutorial/userattributes.png)

1. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a G csomagba**lehetőséget.

    ![G Suite csoportos megfeleltetések](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Tekintse át az Azure AD-ból a G Suite-be szinkronizált csoport attribútumait az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a G Suite-ban a frissítési műveletekhez tartozó csoportokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra. A felhasználói felület megjeleníti az Azure AD és a G Suite közötti attribútum-hozzárendelések alapértelmezett készletét. Az új leképezés hozzáadása lehetőségre kattintva további attribútumokat is hozzáadhat, például a szervezeti egységet.

    ![G Suite-csoport attribútumai](media/google-apps-provisioning-tutorial/groupattributes.png)

1. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. A G Suite-hoz készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

1. Adja meg a G Suite-hoz kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Settings (beállítások** ) szakaszban a megfelelő értékeket választja ki a **hatókörben** .

    ![Kiépítési hatókör](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a G Suite-on végrehajtott összes műveletet ismertetik.

> [!NOTE]
> Ha a felhasználók már rendelkeznek egy meglévő személyes/fogyasztói fiókkal az Azure AD-felhasználó e-mail-címével, akkor előfordulhat, hogy a címtár-szinkronizálás végrehajtása előtt a Google átadási eszköz használatával feloldható néhány probléma.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Gyakori problémák
* Az engedélyezési hibák akkor fordulhatnak elő, ha a kapcsolat létesítéséhez használt fiók nem a GSuite rendszergazdájának tagja. Győződjön meg arról, hogy a hozzáférés engedélyezéséhez használt fiók rendszergazdai engedélyekkel rendelkezik az **összes olyan tartományon** , amelyet a felhasználóknak telepíteniük kell. 

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
