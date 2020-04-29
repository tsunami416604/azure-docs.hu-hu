---
title: 'Oktatóanyag: a Smartsheet konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063189"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Smartsheet konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Smartsheet és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Smartsheet.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Smartsheet-bérlő](https://www.smartsheet.com/pricing)
* Felhasználói fiók Smartsheet Enterprise vagy Enterprise Premier csomaggal, rendszergazdai engedélyekkel.

## <a name="assign-users-to-smartsheet"></a>Felhasználók Smartsheet rendelése

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Smartsheet való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Smartsheet az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Fontos Tippek a felhasználók Smartsheet való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Smartsheet-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Smartsheet rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

* A Smartsheet és az Azure AD közötti felhasználói szerepkör-hozzárendelések paritásának biztosítása érdekében ajánlott ugyanazokat a szerepkör-hozzárendeléseket használni, amelyek a teljes Smartsheet-felhasználók listájában fel vannak töltve. A felhasználói lista Smartsheet való lekéréséhez navigáljon a **fiók rendszergazdája > a felhasználói felügyelet > további műveletek > a felhasználói lista letöltése (CSV)** elemre.

* Az alkalmazás egyes funkcióinak eléréséhez a Smartsheet-nek több szerepkörrel kell rendelkeznie. Ha többet szeretne megtudni a Smartsheet felhasználói típusairól és engedélyeiről, lépjen a [Felhasználói típusok és engedélyek](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)elemre.

*  Ha egy felhasználó több, a Smartsheet-ban hozzárendelt szerepkörrel rendelkezik, meg **kell** győződnie arról, hogy a szerepkör-hozzárendelések replikálva vannak az Azure ad-ben, így elkerülhető, hogy a felhasználók ne férhessenek hozzá a Smartsheet objektumokhoz. A Smartsheet minden egyes egyedi szerepkörét **hozzá kell rendelni** egy másik csoporthoz az Azure ad-ben. **Ezután a felhasználót hozzá kell adni** a kívánt szerepköröknek megfelelő csoportokhoz. 

## <a name="set-up-smartsheet-for-provisioning"></a>Smartsheet beállítása a kiépítés számára

Az Azure AD-vel való automatikus Smartsheet konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Smartsheet-on.

1. Jelentkezzen be **rendszergazdaként** a **[Smartsheet-portálon](https://app.smartsheet.com/b/home)** , és navigáljon a **fiók rendszergazdája**elemre.

    ![Smartsheet-fiók rendszergazdája](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Lépjen a **biztonsági vezérlők > felhasználó automatikus kiépítés > szerkesztés**gombra.

    ![Smartsheet biztonsági vezérlők](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adja hozzá és ellenőrizze az e-mail tartományokat azon felhasználók számára, akik számára az Azure AD-ből Smartsheet kíván kiépíteni. A **nincs engedélyezve** beállítás megadásával biztosíthatja, hogy a kiépítési műveletek csak az Azure ad-ből származzanak, valamint hogy a Smartsheet-felhasználók listája szinkronban legyen az Azure ad-hozzárendelésekkel.

    ![Smartsheet-felhasználó kiépítés](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Az érvényesítés befejeződése után aktiválni kell a tartományt. 

    ![Smartsheet aktiválása tartomány](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Az **alkalmazásokhoz és az integrációhoz**való navigálással létrehozhatja az automatikus felhasználó-kiépítés az Azure ad-vel való konfigurálásához szükséges **titkos jogkivonatot** .

    ![Smartsheet telepítése](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Válassza az **API-hozzáférés**lehetőséget. Kattintson az **új hozzáférési jogkivonat előállítása**elemre.

    ![Smartsheet telepítése](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Adja meg az API-hozzáférési jogkivonat nevét. Kattintson az **OK** gombra.

    ![Smartsheet telepítése](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Másolja az API-hozzáférési jogkivonatot, és mentse, mivel ez az egyetlen időpontig megtekinthető. Erre szükség van az Azure AD **titkos jogkivonat** mezőjében.

    ![Smartsheet token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Smartsheet hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Smartsheet konfigurálásához hozzá kell adnia a Smartsheet az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Smartsheet**kifejezést, majd az eredmények panelen kattintson a **Smartsheet** elemre. 

    ![Smartsheet az eredmények listájában](common/search-new-app.png)

5. Válassza ki a **Smartsheet (regisztráció)** gombot, amely átirányítja Önt a Smartsheet bejelentkezési oldalára. 

    ![Smartsheet OIDC hozzáadása](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Mivel a Smartsheet egy OpenIDConnect-alkalmazás, a Microsoft munkahelyi fiókjával való bejelentkezéshez válassza a Smartsheet lehetőséget.

    ![Smartsheet OIDC-bejelentkezés](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Sikeres hitelesítés után fogadja el a jóváhagyást kérő lapot. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Smartsheet-fiókjába.

    ![Smartsheet OIDc-beleegyezett](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Automatikus felhasználó-kiépítés beállítása a Smartsheet 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Smartsheet alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Smartsheet az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Smartsheet**lehetőséget.

    ![Az Smartsheet hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://scim.smartsheet.com/v2/` meg a **bérlői URL-címet**. Adja meg a beolvasott és a korábban mentett értéket a **titkos token**Smartsheet. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Smartsheet. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Smartsheet-fiók rendelkezik SysAdmin engedélyekkel, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Smartsheet**lehetőséget.

    ![Smartsheet felhasználói leképezések](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Smartsheet. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Smartsheet felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Smartsheet felhasználói attribútumai](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás Smartsheet való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Smartsheet kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Smartsheet-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Smartsheet nem támogatja a Soft-delete használatát. Ha a felhasználó **aktív** attribútuma hamis értékre van állítva, a Smartsheet véglegesen törli a felhasználót.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
