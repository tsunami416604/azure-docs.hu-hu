---
title: 'Oktatóanyag: Intelligens adatlap konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a Smartsheet szolgáltatásba való kiépítésének kiépítésével.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063189"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Oktatóanyag: Intelligens adatlap konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Smartsheet és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok intelligens lapba történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Smartsheet-bérlő](https://www.smartsheet.com/pricing)
* Felhasználói fiók egy vállalati vagy nagyvállalati premier szintű csomagban, rendszergazdai engedélyekkel.

## <a name="assign-users-to-smartsheet"></a>Felhasználók hozzárendelése az intelligens adatlaphoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Smartsheethez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Smartsheet-hez az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Fontos tippek a felhasználók intelligens adatlaphoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve smartsheet az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Smartsheet-hez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

* A Smartsheet és az Azure AD közötti felhasználói szerepkör-hozzárendelések paritásának biztosítása érdekében ajánlott ugyanazokat a szerepkör-hozzárendeléseket használni a teljes Smartsheet felhasználói listában. Ha be szeretné olvasni ezt a felhasználói listát az intelligens fájlból, keresse meg **a Fiókfelügyeletet > a felhasználókezelés > további műveletek et > a felhasználói lista letöltése (csv)** lapot.

* Az alkalmazás bizonyos funkcióinak eléréséhez az intelligens lapnak több szerepkörrel kell rendelkeznie a felhasználónak. Ha többet szeretne tudni a felhasználói típusokról és engedélyekről az Intelligens lapban, olvassa el a [Felhasználótípusok és engedélyek című részt.](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)

*  Ha egy felhasználó több szerepkört rendelt a Smartsheet, biztosítania **kell,** hogy ezek a szerepkör-hozzárendelések replikálódnak az Azure AD-ben, hogy elkerüljék a forgatókönyv, ahol a felhasználók elveszíthetik a hozzáférést smartsheet objektumok véglegesen. A Smartsheet minden egyes egyedi szerepkörét egy másik csoporthoz **kell** rendelni az Azure AD-ben. A **felhasználót** ezután hozzá kell adni a kívánt szerepköröknek megfelelő csoportokhoz. 

## <a name="set-up-smartsheet-for-provisioning"></a>Smartsheet beállítása kiépítéshez

Mielőtt konfigurálná a Smartsheet automatikus felhasználói kiépítés az Azure AD,szüksége lesz, hogy az SCIM kiépítése a Smartsheet.

1. Jelentkezzen be **rendszergazdaként** az **[intelligenslap-portálon,](https://app.smartsheet.com/b/home)** és keresse meg a **Fiókkezelő**t.

    ![Smartsheet fiók adminisztrátora](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Nyissa meg **a Biztonsági vezérlők > a felhasználók automatikus kiépítési > szerkesztése.**

    ![Intelligens lap biztonsági vezérlői](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adja hozzá és érvényesítse az e-mail tartományokat az Azure AD-ből a Smartsheet-be kiépíteni kívánt felhasználók számára. Válassza **a Nincs engedélyezve** lehetőséget, ha azt szeretné, hogy az összes létesítési művelet csak az Azure AD-ből származik, és győződjön meg arról is, hogy a Smartsheet felhasználói listája szinkronban van az Azure AD-hozzárendelésekkel.

    ![Intelligens lap felhasználói nak kiépítése](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Az ellenőrzés befejezése után aktiválnia kell a tartományt. 

    ![Intelligens lap tartomány aktiválása](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Hozza létre a **titkos jogkivonatot,** amely az automatikus felhasználói kiépítés konfigurálásához szükséges az Azure AD-vel az **alkalmazások és integrációk**elemre való navigálással.

    ![Intelligens lap telepítése](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Válassza **az API Access**lehetőséget. Kattintson **az Új hozzáférési jogkivonat létrehozása gombra.**

    ![Intelligens lap telepítése](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Adja meg az API-hozzáférési jogkivonat nevét. Kattintson az **OK** gombra.

    ![Intelligens lap telepítése](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Másolja az API-hozzáférési jogkivonatot, és mentse, mivel ez lesz az egyetlen alkalom, amikor megtekintheti. Ez szükséges az Azure AD **titkos jogkivonat** mezőjében.

    ![Intelligens lap token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Intelligens adatlap hozzáadása a galériából

A Smartsheet konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Smartsheet-et az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Intelligens lapot**, és válassza az **Intelligens lap** lehetőséget az eredménypanelen. 

    ![Intelligens adatlap az eredménylistában](common/search-new-app.png)

5. Válassza a **Regisztráció smartsheet-re** gombot, amely átirányítja a Smartsheet bejelentkezési oldalára. 

    ![Intelligens lap OIDC hozzáadása](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Mivel a Smartsheet egy OpenIDConnect alkalmazás, a Microsoft munkahelyi fiókjával jelentkezhet be a Smartsheet szolgáltatásba.

    ![Smartsheet OIDC bejelentkezés](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Smartsheet-fiókjába.

    ![Smartsheet OIDc hozzájárulása](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Automatikus felhasználói kiépítés konfigurálása az intelligens adatlapra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Smartsheetben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Az automatikus felhasználói kiépítés konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Intelligens lap lehetőséget.**

    ![Az Alkalmazások listában található Smartsheet hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://scim.smartsheet.com/v2/` csoportban adja meg a **bérlői URL-címet.** Adja meg a Smartsheet ből korábban beolvasott és mentett értéket **a Titkos jogkivonatban.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen csatlakozni az intelligens laphoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az intelligenslap-fiók rendelkezik SysAdmin engedélyekkel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása intelligens adatlappal**lehetőséget.

    ![Intelligens lap felhasználói leképezései](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Tekintse át az Azure AD-ről a Smartsheet-re szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Smartsheet felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Intelligens lap felhasználói attribútumai](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez smartsheet, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Smartsheet alkalmazásba, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Smartsheet-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Smartsheet nem támogatja a soft-deletes-t. Ha egy felhasználó **aktív** attribútuma Hamis, a Smartsheet véglegesen törli a felhasználót.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
