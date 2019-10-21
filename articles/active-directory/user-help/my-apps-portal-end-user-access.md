---
title: Alkalmazások elérése és használata a saját alkalmazások portálon – Azure Active Directory | Microsoft Docs
description: Útmutató a saját alkalmazások portál eléréséhez.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 901067e198616dba390e0e1162ec26a0b11768f1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "68383137"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>Alkalmazások elérése és használata a saját alkalmazások portálján

Használhatja munkahelyi vagy iskolai fiókját a webalapú **saját alkalmazások** portálján, hogy megtekintse és elindítsa a szervezete felhőalapú alkalmazásait, hogy frissítse a profilját és a fiókadatok adatait, és megtekintse a **csoportok** adatait, és elvégezze a következő műveleteket **: alkalmazások és csoportok hozzáférési felülvizsgálatai** . Ha nincs hozzáférése a **saját alkalmazások** portálhoz, akkor engedélyt kell adnia az ügyfélszolgálatnak.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, további információt talál arról, hogyan állíthatja be és kezelheti felhőalapú alkalmazásait az [alkalmazás-felügyeleti dokumentációban](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Támogatott böngészők

A **My apps** portált a következő webböngészőkből érheti el:

- Google Chrome

- Mozilla Firefox, 26,0-es vagy újabb verzió

- Microsoft Edge

- Internet Explorer, 11-es verzió (korlátozott támogatás)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>A saját alkalmazások biztonságos bejelentkezési bővítményének letöltése és telepítése

Ha a rendszer kéri, töltse le és telepítse a saját alkalmazások biztonságos bejelentkezési bővítményét. Ez a bővítmény segítséget nyújt a szervezete bármely felhőalapú alkalmazásának elindításához, amely megköveteli az egyszeri bejelentkezési folyamat használatát. Ha a szervezet már beállította az egyszeri bejelentkezést, a bővítmény automatikusan települ, és kihagyhatja ezt a szakaszt.

Ez a bővítmény a következőket teszi lehetővé:

- Jelentkezzen be közvetlenül az alkalmazásokba a bejelentkezési oldalról.

- Indítsa el bármelyik alkalmazást a **gyors keresés** funkció használatával.

- Tekintse meg a legutóbbi **használat** szakaszban használt alkalmazásokat.

- A belső vállalati URL-címek használata az [alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)használatával.

### <a name="to-download-and-install-the-extension"></a>A bővítmény letöltése és telepítése

Töltse le és telepítse a bővítményt az Ön által használt böngésző alapján:

- **Google Chrome.** A Chrome webáruházban lépjen a [saját alkalmazások biztonságos bejelentkezési bővítmény](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) szolgáltatásra, majd válassza a **Hozzáadás a Chrome-hoz**lehetőséget.

- **Mozilla Firefox** A **Firefox-bővítmények** lapon lépjen a [saját alkalmazások biztonságos bejelentkezési bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/) szolgáltatásra, majd válassza a **Hozzáadás a Firefoxhoz**lehetőséget.

- **Microsoft Edge** A Microsoft Store lépjen a [saját alkalmazások biztonságos bejelentkezési bővítmény](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) szolgáltatásra, majd válassza a **beolvasás**lehetőséget.

A **címsor** jobb oldalán megjelenik egy ikon, amely lehetővé teszi a bejelentkezést, és testreszabja a bővítményt.

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Saját alkalmazások portál módosítása a bővítmény használatával
Kiválaszthatja, hogy hány alkalmazást szeretne megtekinteni a **legutóbb használt** szakaszban, és döntse el, hogy engedélyezi-e a szervezet belső URL-címeinek átirányítását.

1. Válassza ki az új **saját alkalmazások biztonságos bejelentkezési bővítmény** ikont, ![Extension ikont ](media/my-apps-portal/my-apps-portal-extension-icon.png) a **címsortól** jobbra, majd válassza a bejelentkezés lehetőséget a **kezdéshez**.

2. Kattintson a jobb gombbal a **Beállítások** ikonra ![Settings ikon ](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), majd válassza a **Beállítások**lehetőséget.

3. A **Beállítások** mezőben adja meg a portálon megjeleníteni kívánt legutóbbi alkalmazások számát, valamint azt, hogy engedélyezi-e a szervezet belső URL-címeinek átirányítását, hogy távolról is használhassa őket.

    ![A bővítmény beállítások lapja, amely az elérhető testreszabásokat mutatja](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>A My apps portál elérése és használata eszköz szerint
Elérheti és használhatja a saját alkalmazások portált a számítógépén, egy Intune által felügyelt böngészőből vagy egy iOS vagy Android rendszerű mobileszközön.

![Alkalmazások lap a saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>A saját alkalmazások portál elérése és használata a számítógépen
Ha rendelkezik engedéllyel a szervezet felhőalapú alkalmazásainak eléréséhez és használatához, a **saját alkalmazások** portálon érheti el őket.

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába.

2. Nyissa meg a webböngészőt, és lépjen a https://myapps.microsoft.com ra, vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például https://myapps.microsoft.com/contoso.com.

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

3. Az **alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazáshoz, ahol bejelentkezhet (ha szükséges), vagy megkezdheti az alkalmazás használatát.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>A My apps portál elérése és használata Intune által felügyelt böngészőben

Az iOS-és Android-eszközökön Intune Managed Browser megtekintheti és használhatja szervezete alkalmazásait.

1. A mobileszközön töltse le és telepítse a Intune Managed Browser alkalmazást az [Apple App Store áruházból](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) és a [Google Play áruházból](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Nyissa meg a Intune Managed Browser alkalmazást, lépjen a https://myapps.microsoft.com ra, vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például https://myapps.microsoft.com/contoso.com.

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

3. Az **alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazáshoz, ahol bejelentkezhet (ha szükséges), vagy megkezdheti az alkalmazás használatát.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>A My apps portál elérése és használata iOS-eszközön

Az iOS 7-es vagy újabb verzióját futtató iPhone-vagy iPad-eszközön megtekintheti és használhatja a **My apps** portált. Telepítheti a [saját alkalmazások Mobile alkalmazást](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) is, hogy hozzáférjen a szervezet alkalmazásaihoz az iOS-eszközökön. 

1. A mobileszközön indítson el egy webböngészőt, például a Safari alkalmazást.

2. Lépjen a https://myapps.microsoft.com ra, vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például https://myapps.microsoft.com/contoso.com.

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

3. Az **alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazáshoz, ahol bejelentkezhet (ha szükséges), vagy megkezdheti az alkalmazás használatát.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>A My apps portál elérése és használata Android-eszközön

A **My apps** portál megtekintése és használata Android-eszközön.

1. A mobileszközön indítsa el a webböngésző alkalmazást, például a Google Chrome-ot.

2. Lépjen a https://myapps.microsoft.com ra, vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például https://myapps.microsoft.com/contoso.com.

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

3. Az **alkalmazások** lapon válassza ki a használni kívánt alkalmazást.

    Megnyílik egy új lap az alkalmazáshoz, ahol bejelentkezhet (ha szükséges), vagy megkezdheti az alkalmazás használatát.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Új alkalmazás hozzáadása a saját alkalmazások portálján

Ha a rendszergazda engedélyt adott Önnek, új alkalmazást adhat hozzá az **alkalmazások** laphoz.

1. Az **alkalmazások** lapon válassza az **alkalmazás hozzáadása**lehetőséget.

    Megjelenik az **Alkalmazások hozzáadása** lap.

    ![Alkalmazások hozzáadása lap a saját alkalmazások portálján](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Válassza ki a hozzáadni kívánt alkalmazást a megadott listából, majd válassza a **Hozzáadás**lehetőséget.

3. Az alkalmazás hozzá lesz adva a listához az **alkalmazások** oldalon.

    Egyes alkalmazásokhoz a Hozzáadás előtt rendszergazdai jóváhagyásra lehet szükség. Ebben az esetben az alkalmazás nem kerül be az **alkalmazások** oldalra, amíg a rendszergazda jóvá nem hagyja.

## <a name="next-steps"></a>Következő lépések

Az **alkalmazások** oldal elolvasása után a következőket teheti:

- [Profil adatainak módosítása](my-apps-portal-end-user-update-profile.md)

- [A csoportok kapcsolódó információinak megtekintése és frissítése](my-apps-portal-end-user-groups.md)

- [Saját hozzáférés felülvizsgálatainak elvégzése](my-apps-portal-end-user-access-reviews.md)
