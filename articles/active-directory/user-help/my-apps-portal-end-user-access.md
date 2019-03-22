---
title: Elérheti és használni az alkalmazásokat a saját alkalmazások portál – Azure Active Directory |} A Microsoft Docs
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
ms.openlocfilehash: eaed7357221f5b766bfb8b9e1a9031c08854ac9a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340124"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>A saját alkalmazások portál alkalmazások elérése és használata
A munkahelyi vagy iskolai fiók használata a web-alapú **saját alkalmazások** portálon megtekintheti, és indítsa el a számos szervezet felhőalapú alkalmazásokat, a profil és fiók információk megjelenítéséhez frissíteni, a **csoportok** információkat, és végrehajtásához **hozzáférési felülvizsgálatokkal** az alkalmazások és a csoportokat. Ha nem rendelkezik hozzáféréssel a **saját alkalmazások** portál, forduljon a Segélyszolgálathoz engedélyt.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, és a felhőalapú alkalmazások kezelése a további tájékoztatást talál a [Alkalmazásfelügyelet dokumentációja](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Támogatott böngészők
Szeretné a **saját alkalmazások** portál, az alábbi böngészők bármelyike:

- Google Chrome

- A Mozilla Firefox 26.0 vagy újabb verzió

- Microsoft Edge

- Internet Explorer 11 (korlátozott támogatás) verziója

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Töltse le és telepítse a saját alkalmazások biztonságos bejelentkezési bővítménye
Töltse le és telepítse a saját alkalmazások biztonságos bejelentkezési bővítménye, ha kéri. Ez a bővítmény segítségével indítsa el a szervezet felhőalkalmazás egyetlen bejelentkezési folyamat használatát igénylő. Ha a szervezet már állított regisztrálásához egyszeri bejelentkezést, a rendszer automatikusan telepíti a bővítményt, és ezt a szakaszt kihagyhatja.

Ez a bővítmény segítségével: 

- Bejelentkezés közvetlenül az alkalmazásba a bejelentkezési oldalról.

- Indítsa el a minden alkalmazás használatával az **Gyorskeresés** funkció.

- Tekintse meg az utolsó alkalmazásokat használ a **a legutóbb használt** szakaszban.

- Használja a belső vállalati URL-címek, miközben a távoli [alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Töltse le és telepítse a bővítményt
Töltse le és telepítse a bővítményt, a használt böngészőtől alapján:

- **Google Chrome.** A Chrome-ban webes Store, nyissa meg a [saját alkalmazások biztonságos bejelentkezési bővítménye](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) funkciót, és válassza ki **Chrome hozzá**.

- **Mozilla Firefox** származó a **Firefox bővítmények** lapon kattintson a [saját alkalmazások biztonságos bejelentkezési bővítménye](https://addons.mozilla.org/firefox/addon/access-panel-extension/) funkciót, és válassza ki **Firefox hozzá**.

- **A Microsoft Edge** , a Microsoft Store, nyissa meg a [saját alkalmazások biztonságos bejelentkezési bővítménye](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) funkciót, és válassza ki **első**.

Ikont jobb oldalán található hozzáadódik a **cím** sáv, így be- és testre szabhatja a bővítményt.

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Módosíthatja a saját alkalmazások portál, a bővítmény használata
Kiválaszthatja, hogy hány alkalmazások megtekintéséhez a **a legutóbb használt** szakaszt, és döntse el, hogy a szervezet belső URL-átirányítás-e.

1. Válassza ki az új **saját alkalmazások biztonságos bejelentkezési bővítménye** ikon ![bővítmény ikonra](media/my-apps-portal/my-apps-portal-extension-icon.png) jobb oldalán a **cím** sávot, és válassza ki **jelentkezzen be az első lépésekhez**.

2. Kattintson a jobb gombbal a **beállítások** ikon ![beállítások ikon](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), majd válassza ki **beállítások**.

3. Az a **beállítások** mezőbe válassza ki a legutóbbi alkalmazások meg szeretné tekinteni a portálon, és hogy, hogy a szervezet céljai a belső URL-címeket, így átirányítani a segítségükkel távolról.

    ![A bővítményt, amely a rendelkezésre álló testreszabások beállításai lapján](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Hozzáférés és az eszköz által saját alkalmazások portál használatához
Elérheti, és a saját alkalmazások portál ezen a számítógépen, az Intune által felügyelt böngészőben, vagy egy iOS vagy Android rendszerű.

![Saját alkalmazások portál alkalmazások lapján](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>A számítógépen a saját alkalmazások portál használatának és elérésének
Ha rendelkezik engedéllyel, hogy eléri és használja a szervezet felhőalapú alkalmazásokat, beszerezheti a keresztül működnek a **saját alkalmazások** portálon.

1.  Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2.  Nyissa meg a webböngészőjét, és nyissa meg https://myapps.microsoft.com, vagy használja a szervezet által biztosított hivatkozást. Például akkor lehet, hogy átirányítja egy testre szabott lap a szervezete számára például https://myapps.microsoft.com/contoso.com.

    A **alkalmazások** lap jelenik meg, a felhőalapú alkalmazások megjelenítése, a szervezet tulajdonában és elérhető a használatra.

3. Az a **alkalmazások** lapra, jelölje be az alkalmazást elindítja a használatával.
    
    Új oldal megnyílik az alkalmazás, ahol be (ha szükséges), illetve az alkalmazás használatának megkezdéséhez.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Hozzáférés és a egy Intune által felügyelt böngészőben a saját alkalmazások portál használatához
Megtekintheti és használhatja a munkahelyi alkalmazásokat az Intune Managed Browser az iOS és Android rendszerű eszközökön.

1. A mobileszközén, töltse le és telepítse az Intune Managed Browser alkalmazást, a a [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) és a [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Nyissa meg az Intune Managed Browser alkalmazást, ugorjon a https://myapps.microsoft.com, vagy használja a szervezet által biztosított hivatkozást. Például akkor lehet, hogy átirányítja egy testre szabott lap a szervezete számára például https://myapps.microsoft.com/contoso.com.

    A **alkalmazások** lap jelenik meg, a felhőalapú alkalmazások megjelenítése, a szervezet tulajdonában és elérhető a használatra.

3. Az a **alkalmazások** lapra, jelölje be az alkalmazást elindítja a használatával.
    
    Új oldal megnyílik az alkalmazás, ahol be (ha szükséges), illetve az alkalmazás használatának megkezdéséhez.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Elérése és használata a saját alkalmazások portál iOS-eszközön
Megtekintheti és használhatja a **saját alkalmazások** portál verziója iOS 7 vagy újabb rendszerű iPhone vagy iPad eszköz. Emellett telepíthet a [saját alkalmazások mobilalkalmazás](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) érheti el a munkahelyi alkalmazásokat az iOS-eszközökön. 

1. A mobileszközön egy webböngésző-alkalmazást, például a Safari elindításához.

2. Lépjen a https://myapps.microsoft.com, vagy használja a szervezet által biztosított hivatkozást. Például akkor lehet, hogy átirányítja egy testre szabott lap a szervezete számára például https://myapps.microsoft.com/contoso.com.

    A **alkalmazások** lap jelenik meg, a felhőalapú alkalmazások megjelenítése, a szervezet tulajdonában és elérhető a használatra.

3. Az a **alkalmazások** lapra, jelölje be az alkalmazást elindítja a használatával.
    
    Új oldal megnyílik az alkalmazás, ahol be (ha szükséges), illetve az alkalmazás használatának megkezdéséhez.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Elérése és használata a saját alkalmazások portál Android-eszközön
Megtekintheti és használhatja a **saját alkalmazások** portál Android-eszközön.

1. A mobileszközön egy webböngésző-alkalmazást, például a Google Chrome elindításához.

2. Lépjen a https://myapps.microsoft.com, vagy használja a szervezet által biztosított hivatkozást. Például akkor lehet, hogy átirányítja egy testre szabott lap a szervezete számára például https://myapps.microsoft.com/contoso.com.

    A **alkalmazások** lap jelenik meg, a felhőalapú alkalmazások megjelenítése, a szervezet tulajdonában és elérhető a használatra.

3. Az a **alkalmazások** lapra, jelölje be az alkalmazást elindítja a használatával.
    
    Új oldal megnyílik az alkalmazás, ahol be (ha szükséges), illetve az alkalmazás használatának megkezdéséhez.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Új alkalmazás hozzáadása a saját alkalmazások portál
Ha a rendszergazda engedélyt adott, az új alkalmazás is hozzáadhat a **alkalmazások** lapot.

1. Az a **alkalmazások** lapon jelölje be **alkalmazás hozzáadása**.

    A **alkalmazások hozzáadása** lap jelenik meg.

    ![Adja hozzá az alkalmazások lapon, a saját alkalmazások portál](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Válassza ki a kívánt alkalmazást, adja hozzá a megadott listáról, és válassza ki **Hozzáadás**.

3. Az alkalmazás a a listához való hozzáadásakor a **alkalmazások** lapot.

    Egyes alkalmazások hozzáadni kívánt rendszergazdai jóváhagyás lehet szükség. Ebben a helyzetben az alkalmazás nem adódik hozzá a **alkalmazások** oldalon, amíg a rendszergazda engedélyezi.

## <a name="next-steps"></a>További lépések
Után kap a **alkalmazások** lapon, a következő műveleteket hajthatja végre:

- [Módosítsa a profiladatok](my-apps-portal-end-user-update-profile.md).

- [Megtekintése és módosítása a csoportok kapcsolatos információkat](my-apps-portal-end-user-groups.md).

- [Hajtsa végre a saját hozzáférési felülvizsgálatok](my-apps-portal-end-user-access-reviews.md).