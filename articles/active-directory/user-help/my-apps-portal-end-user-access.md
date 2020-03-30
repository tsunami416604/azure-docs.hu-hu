---
title: Alkalmazások megkeresése & használata a Saját alkalmazások portálon – Azure AD
description: Megtudhatja, hogyan keresheti meg a Saját alkalmazások portált, majd hogyan érheti el a szervezet felhőalapú alkalmazásait.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: ab3ab25df6ce41d4045a270754830d501fe40689
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347171"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>Bejelentkezés és alkalmazások indítása a Saját alkalmazások portálról

A munkahelyi vagy iskolai fiókját a webalapú Saját alkalmazások portálon a következőkre **használhatja:**

- A szervezet felhőalapú alkalmazásainak megtekintése és indítása
- A profil- és fiókadatok frissítése
- A **csoportokra** vonatkozó információk megtekintése
- **Hozzáférés-felülvizsgálatok** végrehajtása az alkalmazásokhoz és csoportokhoz

Ha nem fér hozzá a **Saját alkalmazások** portálhoz, kérjen engedélyt az ügyfélszolgálattól.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> Ez a tartalom a Saját alkalmazások felhasználóinak készült. Ha Ön rendszergazda, a felhőalapú alkalmazások beállításával és kezelésével kapcsolatos további információkért az [Alkalmazáskezelési dokumentációban](https://docs.microsoft.com/azure/active-directory/manage-apps)talál.

## <a name="supported-browsers"></a>Támogatott böngészők

A **My Apps** portál az alábbi webböngészők bármelyikéből elérhető:

- Google Chrome

- Mozilla Firefox, 26.0-s vagy újabb verzió

- Microsoft Edge

- Internet Explorer, 11-es verzió (korlátozott támogatás)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>A My Apps Biztonságos bejelentkezés bővítmény letöltése és telepítése

Töltse le és telepítse a My Apps Secure Bejelentkezésbővítményt, ha a rendszer kéri. Ez a bővítmény segít elindítani a szervezet bármely felhőalapú alkalmazását, amely egyetlen bejelentkezési folyamatot igényel. Ha a szervezet már beállította önt egyszeri bejelentkezésre, a bővítmény automatikusan települ, és kihagyhatja ezt a szakaszt.

Ez a bővítmény segít:

- Jelentkezzen be közvetlenül az alkalmazásokba a bejelentkezési oldalról.

- Indítsa el az alkalmazásokat a **Gyorskeresés** funkcióval.

- A Legutóbb használt alkalmazások megtekintése a **Legutóbb használt** szakaszban.

- Belső vállalati URL-címeket használjon távoli távolságban [az alkalmazásproxyval.](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

### <a name="to-download-and-install-the-extension"></a>A bővítmény letöltése és telepítése

Töltse le és telepítse a bővítményt a használt böngésző alapján:

- **Google Chrome.** A Chrome Internetes áruházban nyissa meg a [Saját alkalmazások biztonságos bejelentkezési bővítményfunkcióját,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) és válassza a **Hozzáadás a Chrome-hoz**lehetőséget.

- **Mozilla Firefox** A Firefox bővítmények lapon nyissa meg a [Saját alkalmazások biztonságos bejelentkezési bővítményfunkcióját,](https://addons.mozilla.org/firefox/addon/access-panel-extension/) és válassza a **Firefox Add-ons** **Hozzáadás a Firefoxhoz**lehetőséget .

- **Microsoft Edge** A Microsoft Store-ban nyissa meg a [Saját alkalmazások biztonságos bejelentkezési](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) bővítményfunkcióját, és válassza a **Get lehetőséget** a Microsoft Edge örökölt böngészőbővítményének lekérnie.  
Ha az új Microsoft Edge böngészőt (Edge on Chromium) használja, akkor az exteniont a [Microsoft Edge Addons Store-ból szerezheti be.](https://microsoftedge.microsoft.com/addons/category/EdgeExtensionsEditorsPick)

A címsor jobb oldalán egy ikon kerül a **címsorba,** amely lehetővé teszi a bejelentkezést és a bővítmény testreszabását.

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>A Saját alkalmazások portál módosítása a bővítmény használatával

Megadhatja, hogy hány alkalmazást szeretne megtekinteni a **Legutóbb használt szakaszban,** és eldöntheti, hogy engedélyezi-e a szervezet belső URL-címeit az átirányításhoz.

1. Válassza az új **My Apps Secure Sign-in Extension** ikont ![](media/my-apps-portal/my-apps-portal-extension-icon.png) a címsor tól **jobbra,** majd a Bejelentkezés gombra a **kezdéshez**.

2. Kattintson a **Settings** jobb ![gombbal a Beállítások ikonra,](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)majd válassza a **Beállítások parancsot.**

3. A **Beállítások** mezőben adja meg, hogy hány legutóbbi alkalmazást szeretne látni a portálon, és hogy engedélyezi-e a szervezet belső URL-címeinek átirányítását, hogy távolról is használhassa őket.

    ![A bővítmény Beállítások lapja, amely a rendelkezésre álló testreszabásokat jeleníti meg](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>A Saját alkalmazások portál elérése és használata eszköz szerint

A Saját alkalmazások portált a számítógépén, egy Intune által felügyelt böngészőből, illetve iOS vagy Android rendszerű mobileszközről érheti el és használhatja.

![Alkalmazások lap a Saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>A Saját alkalmazások portál elérése és használata a számítógépen

Ha rendelkezik engedéllyel a szervezet felhőalapú alkalmazásai eléréséhez és használatához, a **Saját alkalmazások** portálon keresztül érheti el őket.

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába a számítógépén.

2. Egy támogatott webböngészőben nyissa https://myapps.microsoft.commeg a megnyitását, illetve nyissa meg a megnyitását, vagy `https://myapps.microsoft.com/contoso.com`használja a szervezet által biztosított hivatkozást, ha azok egy testreszabott lapra, például a.

    Megjelenik **az Alkalmazások** lap, amely a szervezet összes olyan felhőalapú alkalmazását jeleníti meg, amelyek et ön használhatja.

3. Az **Alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazás számára, ahol bejelentkezhet (ha szükséges), vagy elkezdheti használni az alkalmazást.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>A Saját alkalmazások portál elérése és használata Intune által felügyelt böngészőben

Megtekintheti és használhatja szervezete alkalmazásait egy Intune felügyelt böngészőjében iOS- és Android-eszközökön.

1. Mobileszközén töltse le és telepítse az Intune Felügyelt böngésző alkalmazást az [Apple App Store-ból](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) és a [Google Play Áruházból.](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)

2. Nyissa meg az Intune felügyelt https://myapps.microsoft.comböngésző alkalmazását, nyissa meg a t, vagy használja a https://myapps.microsoft.com/contoso.comszervezet által biztosított hivatkozást, ha azok egy testreszabott lapra, például a rendszerre irányítják.

    Megjelenik **az Alkalmazások** lap, amely a szervezet tulajdonában lévő és az Ön számára használható összes felhőalapú alkalmazást jeleníti meg.

3. Az **Alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazás számára, ahol bejelentkezhet (ha szükséges), vagy elkezdheti használni az alkalmazást.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>A My Apps portál elérése és használata iOS-eszközön

Megtekintheti és használhatja a **My Apps** portált iPhone vagy iPad készülékről, amely en 7-es vagy újabb verziót futtat. A [My Apps mobilalkalmazást](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) is telepítheti, hogy hozzáférjen a szervezet alkalmazásaihoz az iOS-eszközökön.

1. Mobileszközén indítson el egy webböngészőalkalmazást, például a Safarit.

2. Nyissa https://myapps.microsoft.commeg a t, vagy használja a szervezet által biztosított hivatkozást, ha azok egy testreszabott lapra irányítják, például https://myapps.microsoft.com/contoso.com.

    Megjelenik **az Alkalmazások** lap, amely a szervezet összes olyan felhőalapú alkalmazását jeleníti meg, amelyek et ön használhatja.

3. Az **Alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazás számára, ahol bejelentkezhet (ha szükséges), vagy elkezdheti használni az alkalmazást.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>A My Apps portál elérése és használata Android-eszközön

Tekintse meg és használja a **My Apps** portált Android-eszközön.

1. Mobileszközén indítson el egy webböngészőalkalmazást, például a Google Chrome-ot.

2. Nyissa https://myapps.microsoft.commeg a t, vagy használja a szervezet által biztosított hivatkozást, ha azok egy testreszabott lapra irányítják, például https://myapps.microsoft.com/contoso.com.

    Megjelenik **az Alkalmazások** lap, amely a szervezet tulajdonában lévő és az Ön számára használható összes felhőalapú alkalmazást jeleníti meg.

3. Az **Alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazás számára, ahol bejelentkezhet (ha szükséges), vagy elkezdheti használni az alkalmazást.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Új alkalmazás hozzáadása a Saját alkalmazások portálhoz

Ha a rendszergazda engedélyt adott Önnek, hozzáadhat egy új alkalmazást az **Alkalmazások** laphoz.

1. Az **Alkalmazások** lapon tegye az alábbiak egyikét:
    - Ha az eredeti Saját alkalmazások élményben van része, válassza **az Alkalmazás hozzáadása** lehetőséget a látható módon.

      ![Alkalmazások hozzáadása lap a Saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - Ha a frissített Saját alkalmazások felületet választja, válassza **az Önkiszolgáló alkalmazások hozzáadása**lehetőséget.

       ![Alkalmazások hozzáadása lap a Saját alkalmazások portálon myapplications.microsoft.com](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. Jelölje ki a hozzáadni kívánt alkalmazást a megadott listából, majd válassza a **Hozzáadás gombot.**

3. Az alkalmazás felkerül az Alkalmazások lap **listájára.**

    Egyes alkalmazások hozzáadása előtt rendszergazdai jóváhagyásra lehet szükség. Ebben az esetben az alkalmazás nem kerül az **Alkalmazások** lapra, amíg a rendszergazda jóvá nem hagyja.

## <a name="start-a-cloud-based-app"></a>Felhőalapú alkalmazás indítása

Az elérhető felhőalapú alkalmazások bármelyikét elindíthatja a **Saját alkalmazások** portálon. Csak azokat az alkalmazásokat fogja látni, amelyek használatára engedéllyel rendelkezik.

- Az **Alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazás számára, ahol bejelentkezhet (ha szükséges), vagy elkezdheti használni az alkalmazást.

## <a name="activities-in-the-my-apps-portal"></a>Tevékenységek a Saját alkalmazások portálon

Miután megnyitotta a **Saját alkalmazások** portált, a következőket teheti:

- Az alkalmazásokat a szervezet által létrehozott és biztosított különböző kategóriákba rendezheti. További információt az [Access és a Gyűjtemények használata a Saját alkalmazások portálon című témakörben talál.](my-applications-portal-workspaces.md)

- Az alkalmazásoknak adott engedélyek áttekintése, frissítése és visszavonása. További információt az [Alkalmazásengedélyek szerkesztése és visszavonása a Saját alkalmazások portálon című témakörben talál.](my-applications-portal-permissions-saved-accounts.md)

>[!Note]
>Ha nem lát elérhető gyűjteményeket vagy kategóriákat, az azt jelenti, hogy a rendszergazda nem állított be vagy osztott meg önnel. További segítségért vagy engedélyekért forduljon a szervezet ügyfélszolgálatához a megosztott gyűjtemények megtekintéséhez.

## <a name="next-steps"></a>További lépések

Miután megtérül az **Alkalmazások** laphoz, a következőket teheti:

- [Profilinformációk módosítása](my-apps-portal-end-user-update-profile.md)

- [A csoportokkal kapcsolatos adatok megtekintése és frissítése](my-apps-portal-end-user-groups.md)

- [Saját hozzáférés felülvizsgálatainak elvégzése](my-apps-portal-end-user-access-reviews.md)
