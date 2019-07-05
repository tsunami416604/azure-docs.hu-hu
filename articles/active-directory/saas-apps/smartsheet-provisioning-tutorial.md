---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása a Smartsheet |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének felhasználói fiókokat a Smartsheethez.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: zchia
ms.openlocfilehash: e9d0d996b961c31732642c9476f529bfb7561fbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453397"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Smartsheet konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Smartsheet és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Smartsheethez bemutatásához.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [Egy Smartsheet-bérlő](https://www.smartsheet.com/pricing)
* A Smartsheet vállalati vagy nagyvállalati Premier csomag rendszergazda engedélyekkel rendelkező felhasználói fiókkal.

## <a name="assign-users-to-smartsheet"></a>Felhasználók hozzárendelése a Smartsheethez

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Smartsheet hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Smartsheet utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Felhasználók hozzárendelése Smartsheet fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Smartsheethez a felhasználók automatikus konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

* A Smartsheethez való hozzárendelésekor a felhasználó, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

* Ahhoz, hogy a felhasználó szerepkör-hozzárendelések Smartsheet és az Azure AD között a paritásos, ajánlott ugyanabban a szerepkör-hozzárendeléseket a teljes Smartsheet felhasználói listában vehető igénybe. A Felhasználólista smartsheetből lekéréséhez lépjen **Fiókadminisztrátor > felhasználók kezelése > További műveletek > Felhasználólista letöltése (csv)** .

* Az alkalmazás egyes funkcióinak eléréséhez Smartsheet egy felhasználónak szüksége van több szerepkört. Többet szeretne megtudni a felhasználói típusok és engedélyek a Smartsheet, lépjen a [felhasználói típusai és engedélyek](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Ha egy felhasználó hozzárendelve az Smartsheet, több szerepkörrel rendelkezik az Ön **kell** győződjön meg arról, hogy a szerepkör-hozzárendelések replikálása az Azure AD egy olyan forgatókönyvet, ahol felhasználók megszakadhat a Smartsheet-objektumok elérése véglegesen elkerülése érdekében. Minden egyedi szerepkör Smartsheet- **kell** egy másik csoporthoz hozzárendelni az Azure ad-ben. A felhasználó **kell** majd adni a szükséges szerepkörök megfelelő csoportok. 

## <a name="set-up-smartsheet-for-provisioning"></a>A Smartsheet beállítása a kiépítéshez

Konfigurálja a Smartsheet az Azure AD-felhasználók automatikus, mielőtt szüksége lesz a Smartsheet SCIM-kiépítés engedélyezése.

1. Jelentkezzen be egy **SysAdmin (rendszergazda)** a a **[Smartsheet-portál](https://app.smartsheet.com/b/home)** , és keresse meg **Fiókadminisztrátor**.

    ![Smartsheet-fiók rendszergazdai](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Lépjen a **biztonsági vezérlők > felhasználó automatikus kiépítés > szerkesztése**.

    ![A Smartsheet biztonsági vezérlőket](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adja hozzá, és ellenőrizze az e-mail-tartománnyal, azoknak a felhasználóknak, üzembe helyezése az Azure ad-ből a Smartsheethez tervezi. Válasszon **nincs engedélyezve a** győződjön meg arról, hogy az összes üzembe helyezési műveletek csak az Azure ad-ből származnak, és bizonyosodjon meg arról, hogy az Azure AD-hozzárendelések szinkronban-e a Smartsheet Felhasználólista.

    ![A Smartsheet-felhasználó kiépítési](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Érvényesítés befejezése után a a tartomány aktiválni kell. 

    ![A Smartsheet tartomány aktiválása](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Hozzon létre a **titkos jogkivonat** felhasználók automatikus átadása az Azure ad-vel való konfigurálásához szükséges **alkalmazások fejlesztése és integrációja**.

    ![A Smartsheet-telepítés](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Válasszon **API-hozzáférés**. Kattintson a **új hozzáférési jogkivonat létrehozása**.

    ![A Smartsheet-telepítés](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Határozza meg az API hozzáférési jogkivonat nevét. Kattintson az **OK** gombra.

    ![A Smartsheet-telepítés](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Másolja be az API hozzáférési tokent, és mentse, ez lesz az egyetlen alkalom is megtekintheti. Ez a szükséges a **titkos jogkivonat** mezőt az Azure ad-ben.

    ![A Smartsheet-jogkivonat](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>A Smartsheet hozzáadása a katalógusból

A Smartsheet az Azure AD-felhasználók automatikus konfigurálása, szüksége az Azure AD alkalmazáskatalógusában Smartsheet hozzáadása a felügyelt SaaS-alkalmazások listája.

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Smartsheet**válassza **Smartsheet** az eredmények panelen. 

    ![Az eredmények listájában Smartsheet](common/search-new-app.png)

5. Válassza ki a **a Smartsheet-előfizetési** gomb, amely átirányítja Önt a Smartsheet a bejelentkezési lapot. 

    ![Smartsheet OIDC hozzáadása](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Mivel Smartsheet OpenIDConnect alkalmazás, válassza ki a Smartsheethez a Microsoft munkahelyi fiókjával bejelentkezni.

    ![A Smartsheet OIDC bejelentkezés](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. A sikeres hitelesítés után fogadja el a hozzájárulást kérő lap hozzájárulási kérése. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a Smartsheet-fiók, a rendszer átirányítja.

    ![A Smartsheet OIDc jóváhagyás](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Felhasználók automatikus átadása a Smartsheethez konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD felhasználói és/vagy a csoport-hozzárendelések alapján Smartsheet-csoportok.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása a Smartsheet az Azure ad-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Smartsheet**.

    ![A Smartsheet-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban bemeneti `https://scim.smartsheet.com/v2/` a **bérlői URL-cím**. Bemeneti érték olvassa be, és a smartsheetből korábban mentett **titkos jogkivonat**. Kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Smartsheet csatlakozhat. Ha a kapcsolat nem sikerül, ellenőrizze a Smartsheet-fiók rendelkezik-e rendszergazdai jogosultságokkal, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Smartsheethez**.

    ![A Smartsheet Felhasználóleképezéseket](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Tekintse át a felhasználói attribútumok a Smartsheethez a szinkronizált Azure AD-ből a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok Smartsheet-frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![A Smartsheet felhasználói attribútumok](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD létesítési szolgáltatás a Smartsheet engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

12. Határozza meg a felhasználók és/vagy a csoportok, adja meg a Smartsheethez kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

13. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely a Smartsheet-kiszolgáló üzembe helyezése az Azure AD által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* A Smartsheet nem támogatja a soft-törlések. Ha egy felhasználó **aktív** attribútumot hamis értékre van állítva, a Smartsheet véglegesen törli a felhasználót.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
