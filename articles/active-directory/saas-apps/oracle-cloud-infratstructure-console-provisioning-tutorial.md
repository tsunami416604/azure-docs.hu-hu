---
title: 'Oktatóanyag: Az Oracle Cloud Infrastructure Console konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan építhet idát ki és vonhat vissza automatikusan a felhasználói fiókok azure AD-ből az Oracle Cloud Infrastructure Console-ba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378950"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Oktatóanyag: Az Oracle Cloud Infrastructure Console konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti az Oracle Cloud Infrastructure Console és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseit. Ha konfigurálva van, az Azure AD automatikusan lehetővé teszi és de-rendelkezések et a felhasználók és csoportok [oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Oracle Cloud Infrastructure Console-ban
> * Felhasználók eltávolítása az Oracle Cloud Infrastructure Console-ban, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Oracle Cloud Infrastructure Console között
> * Csoportok és csoporttagságok kiépítése az Oracle Cloud Infrastructure Console szolgáltatásban
> * [Egyszeri bejelentkezés az](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) Oracle Cloud Infrastructure Console szolgáltatásba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Az Oracle Cloud Infrastructure Control [bérlője](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Az Oracle Cloud Infrastructure Control rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat szeretne leképezni az [Azure AD és az Oracle Cloud Infrastructure Console között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>2. lépés Az Oracle Cloud Infrastructure Console konfigurálása az Azure AD-vel való kiépítés támogatására

1. Jelentkezzen be az Oracle Cloud Infrastructure Console felügyeleti portáljára. A képernyő bal felső sarkában keresse meg az **Identitás > összevonása**.

    ![Oracle Admin](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Kattintson az Oracle Identity Cloud Service Console melletti oldalon megjelenő URL-címre.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Új **identitásszolgáltató** létrehozásához kattintson az Identitásszolgáltató hozzáadása elemre. Mentse az IdP-azonosítót a bérlői URL részeként való használathoz. Kattintson a plusz ikonra az **Alkalmazások** fül mellett, ha oauth ügyfelet és grant IDCS identitástartomány-rendszergazdai approle-t szeretne létrehozni.

    ![Az Oracle Cloud ikonja](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Kövesse az alábbi képernyőképeket az alkalmazás konfigurálásához. Miután a konfiguráció befejeződött kattintson a **Mentés gombra.**

    ![Oracle konfiguráció](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle token kiállítási házirend](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Az alkalmazás konfigurációs lapján **bontsa** ki az Általános információ lehetőséget az ügyfélazonosító és az ügyféltitkos adat lekéréséhez.

    ![Oracle token létrehozása](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Titkos token létrehozásához Base64 kódolja az ügyfélazonosítót és az ügyféltitkos kulcsot az **ügyfélazonosító:Ügyféltitkos**formátumban. Mentse el a titkos tokent. Ez az érték az Oracle Cloud Infrastructure Console-alkalmazás Azure Portalon való kiépítési lapján a **Titkos jogkivonat** mezőben lesz megadva.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>3. lépés Oracle Cloud Infrastructure Console hozzáadása az Azure AD alkalmazásgyűjteményéből

Adja hozzá az Oracle Cloud Infrastructure Console szolgáltatást az Azure AD alkalmazásgalériából az Oracle Cloud Infrastructure Console-ra való kiépítés kezeléséhez. Ha korábban már beállította az Oracle Cloud Infrastructure Console for SSO-t, használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel az Oracle Cloud Infrastructure Console szolgáltatáshoz, ki kell választania az **Alapértelmezett hozzáféréstől eltérő szerepkört.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása az Oracle Cloud Infrastructure Console szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Az Oracle Cloud Infrastructure Console automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Oracle Cloud Infrastructure Console lehetőséget.**

    ![Az Oracle Cloud Infrastructure Console hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** csoportban adja `https://<IdP ID>.identity.oraclecloud.com/admin/v1` meg a bérlő **iURL-címét** a formátumban. Például: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Adja meg a titkos jogkivonat ot, amelyet korábban beolvastak a **Titkos jogkivonatban.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az Oracle Cloud Infrastructure Console konzolhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az Oracle Cloud Infrastructure Console-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Kiépítés](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása az Oracle Cloud Infrastructure Console szolgáltatással**lehetőséget.

9. Tekintse át az Azure AD és az Oracle Cloud Infrastructure Console között szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az Oracle Cloud Infrastructure Console felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja az [egyező célattribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)gondoskodnia kell arról, hogy az Oracle Cloud Infrastructure Console API támogatja-e a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |userName (Felhasználónév)|Sztring|
      |Aktív|Logikai|
      |cím|Sztring|
      |e-mailek[típus eq "work"].value|Sztring|
      |preferredLanguage (elsődleges nyelv)|Sztring|
      |name.givenName|Sztring|
      |name.familyName|Sztring|
      |addresses[type eq "work"].formatált|Sztring|
      |addresses[type eq "work"].locality|Sztring|
      |addresses[type eq "work"].region|Sztring|
      |addresses[type eq "work"].postalCode|Sztring|
      |addresses[type eq "work"].country|Sztring|
      |addresses[type eq "work"].streetAddress|Sztring|
      |urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:alkalmazottSzám|Sztring|
      |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:részleg|Sztring|
      |urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:costCenter|Sztring|
      |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:osztás|Sztring|
      |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:menedzser|Referencia|
      |urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:szervezet|Sztring|
      |urn:ietf:params:scim:sémák:oracle:idcs:extension:user:User:bypassNotification|Logikai|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Logikai|

10. A **Leképezések** csoportban válassza az **Azure Active Directory-csoportok szinkronizálása az Oracle Cloud Infrastructure Console szolgáltatással**lehetőséget.

11. Tekintse át az Azure AD és az Oracle Cloud Infrastructure Console között szinkronizált csoportattribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az Oracle Cloud Infrastructure Console csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |külső azonosító|Sztring|
      |tagok|Referencia|

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez az Oracle Cloud Infrastructure Console,módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. A **Beállítások** szakasz **hatókörében** válassza ki a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

* A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen vagy sikertelenül kiépítve
* Ellenőrizze a [folyamatjelzőt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotának és a befejezéshez szükséges közelségnek a megtekintéséhez.
* Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../manage-apps/check-status-user-account-provisioning.md)
