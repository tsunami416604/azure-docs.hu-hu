---
title: 'Oktatóanyag: A Workplace by Facebook konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Workplace között a Facebook által.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603207"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Oktatóanyag: A Workplace by Facebook konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti azokat a lépéseket, amelyeket a Workplace by Facebook és az Azure Active Directory (Azure AD) által az automatikus felhasználói kiépítés konfigurálásához végre kell hajtania. Ha konfigurálva van, az Azure AD automatikusan rendelkezik a felhasználókés csoportok [de-provisions-to Workplace a Facebook](https://work.workplace.com/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md)

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Áttérés az új Munkahelyre a Facebook alkalmazással
Ha már rendelkezel integrációval a Workplace by Facebook szolgáltatással, az alábbi, a közelgő változásokról szóló szakaszban olvashatsz. Ha első alkalommal állítod be a Workplace by Facebook-ot, kihagyhatod ezt a szakaszt, és átléphetsz a támogatott képességekre. 

#### <a name="whats-changing"></a>Mi változik?
* Változások az Azure AD oldalán: A Workplace-ben a felhasználók üzembe létesítésének engedélyezési módszere korábban egy hosszú élettartamú titkos jogkivonat. Hamarosan látni fogja, hogy az engedélyezési módszer az OAuth engedélyezési engedélyre változik. 
* Változások a Munkahely oldalon: Korábban az Azure AD alkalmazás egyéni integráció volt a Workplace by Facebook-ban. Most látni fogja az Azure AD a Workplace integrációk címtárban, mint egy harmadik féltől származó alkalmazás. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Mit kell tennem a meglévő egyéni integráció áttelepítéséhez az új alkalmazásba?
Ha meglévő Workplace-integrációval rendelkezik érvényes jogkivonattal, **nincs szükség műveletre.** Minden héten automatikusan áttelepítjük az ügyfeleket az új alkalmazásba. Ez történik teljesen a színfalak mögött. Ha nem tud várni, és manuálisan szeretne áthelyezni az új alkalmazásra, hozzáadhat egy új Workplace-példányt a katalógusból, és újra konfigurálhatja a kiépítést. A Workplace minden új példánya automatikusan az új alkalmazásverziót fogja használni. 

 
Ha workplace-integrációja karanténban van, akkor ismét érvényes jogkivonatot kell megadnia ahhoz, hogy áttelepítsük Önt. A rendszergazdai hitelesítő adatok szakasz szürkén jelenik meg, de hozzáfűzheti a következő (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)** az URL-címre a hitelesítő adatok újbóli mentéséhez. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hogyan állapítható meg, hogy az alkalmazásomat áttelepítették-e? 
Az alkalmazás áttelepítésekekénél a rendszer eltávolítja a módosítások felázásával kapcsolatos engedélyezési szakaszban lévő szalagcímet, és a titkos jogkivonat-mezőt egy kék engedélyezésgomb váltja fel. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>A rendszergazdai hitelesítő adatok szakasz szürkén jelenik meg az alkalmazásomon, és nem tudom menteni. Hogy miért?
Zároltuk a rendszergazdai hitelesítő adatok szakaszt a meglévő Workplace-ügyfelek számára. Ha a bérlő átlett telepítve az új Workplace-alkalmazásba, újra frissítheti a rendszergazdai hitelesítő adatok szakaszt. Ha nem tud várni, használhatja a fenti URL-t az alkalmazás szerkesztéséhez. 

 
#### <a name="when-will-these-changes-happen"></a>Mikor történnek ezek a változások?
A Workplace minden új példánya már az új integrációs/engedélyezési módszert fogja használni. A meglévő integrációkat májusra fokozatosan áttelepítik. A munkahelyi csapat meghosszabbította a határidőt február-28-tól május-1-ig. 

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Workplace-ben a Facebook szerint
> * Felhasználók eltávolítása a Workplace by Facebook szolgáltatásban, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Workplace között a Facebook által
> * [Egyszeri bejelentkezés a Workplace-be a](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) Facebook által (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés [konfigurálására vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda)
* Munkahely a Facebook által egyszeri bejelentkezés az engedélyezett előfizetésre

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Azure AD próbakörnyezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos próbaverziót kaphat.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat szeretne leképezni az [Azure AD és a Workplace között a Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)által.

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>2. lépés A Workplace by Facebook konfigurálása az Azure AD-vel való kiépítés támogatásához

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek a Facebook alkalmazás által hozzáférésre van szükségük a Workplace-hez. Miután eldöntötted, ezeket a felhasználókat hozzárendelheted a Workplace by Facebook alkalmazáshoz az alábbi utasításokat követve:

*   Javasoljuk, hogy a Facebook egyetlen Azure AD-felhasználót rendel hozzá a Workplace-hez a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

*   Amikor egy felhasználót a Facebook workplace-hez rendel, érvényes felhasználói szerepkört kell választanod. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>3. lépés Workplace hozzáadása a Facebook által az Azure AD alkalmazásgyűjteményből

Adja hozzá a Workplace by Facebook-ot az Azure AD alkalmazásgalériából, és kezdje meg a Facebook által a Workplace-en való kiépítést. Ha korábban már beállítottad a Workplace by Facebook for SSO-t, használhatod ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendelsz a Workplace by Facebook szolgáltatáshoz, az **Alapértelmezett hozzáféréstől**eltérő szerepkört kell választanod. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Workplace by Facebook lehetőséget.**

    ![A Workplace by Facebook hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok csoportban** kattintson az **Engedélyezés gombra.** A Facebook engedélyezési oldala átirányítja a Workplace-re. Adja meg munkahelyét a Facebook felhasználónevének alapján, és kattintson a **Folytatás** gombra. Kattintson **a Kapcsolat tesztelése** lehetőségre, ha meg szeretné tudni, hogy az Azure AD a Facebookon keresztül tud-e csatlakozni a Workplace-hez. Ha a kapcsolat nem sikerül, győződj meg arról, hogy a Workplace by Facebook-fiókod rendszergazdai jogosultságokkal rendelkezik, majd próbálkozz újra.

    ![Kiépítés](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![engedélyezés](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory-felhasználók szinkronizálása a Facebook által a workplace-re**lehetőséget.

9. Tekintse át az Azure AD-ről a Workplace szolgáltatásba szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Facebook Workplace felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy döntesz, hogy megváltoztatod az [egyező célattribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)biztosítanod kell, hogy a Workplace by Facebook API támogassa a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |displayName|Sztring|
   |Aktív|Logikai|
   |cím|Logikai|
   |e-mailek[típus eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |name.formatált|Sztring|
   |addresses[type eq "work"].formatált|Sztring|
   |addresses[type eq "work"].streetAddress|Sztring|
   |addresses[type eq "work"].locality|Sztring|
   |addresses[type eq "work"].region|Sztring|
   |addresses[type eq "work"].country|Sztring|
   |addresses[type eq "work"].postalCode|Sztring|
   |címek[type eq "other"].formatált|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers[type eq "fax"].érték|Sztring|
   |külső azonosító|Sztring|
   |preferredLanguage (elsődleges nyelv)|Sztring|
   |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:menedzser|Sztring|
   |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:részleg|Sztring|

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Workplace by Facebook, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozd meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Facebook által ki szeretnél építeni a Workplace-re, ha kiválasztod a kívánt értékeket a **Hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

1. A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen vagy sikertelenül kiépítve
2. Ellenőrizze a [folyamatjelzőt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotának és a befejezéshez szükséges közelségnek a megtekintéséhez.
3. Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
*  Ha egy felhasználót sikertelenül hozlétre, és a "1789003" kóddal rendelkező naplóesemény azt jelenti, hogy a felhasználó nem ellenőrzött tartományból származik.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../manage-apps/check-status-user-account-provisioning.md)
