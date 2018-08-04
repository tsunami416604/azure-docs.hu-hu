---
title: Védjegyzési útmutató alkalmazások |} A Microsoft Docs
description: A fejlesztőknek készített erőforrásairól az Azure Active Directory egy átfogó útmutatást
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: arielgo, skwan
ms.custom: aaddev
ms.openlocfilehash: a31af674a0a4000396cb1df2e520a651efc33946
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505916"
---
# <a name="branding-guidelines-for-applications"></a>Alkalmazások arculati útmutatóját

Ez a cikk ismerteti az alkalmazások fejlesztése az Azure Active Directory (Azure AD) használandó arculati útmutatóját. Ezen irányelvek segítségével az ügyfelek közvetlen kívánja használni a felhasználó munkahelyi vagy iskolai fiókja, az Azure AD-ben felügyelt vagy a személyes fiókra a regisztráció és bejelentkezés az alkalmazáshoz.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Személyes fiókok vagy munkahelyi vagy iskolai fiókokat a Microsoft

Kétféle felhasználói fiókokat a Microsoft felügyeli:

* **Személyes fiókok** (korábbi nevén Windows Live ID). Ezek a fiókok közti kapcsolatot képviselik *egyes* a Microsoft fogyasztói eszközök és szolgáltatások eléréséhez használt felhasználók és a Microsoft és a rendszer. Ezek a fiókok személyes használatra készültek.
* **Munkahelyi vagy iskolai fiókkal.** Ezek a fiókok a Microsoft által felügyelt, az Azure Active Directory használó szervezetek nevében. Ezek a fiókok segítségével a Microsoft Office 365-höz és más üzleti szolgáltatásaiban jelentkezzen be.

A Microsoft munkahelyi vagy iskolai fiókok általában rendeli hozzá a végfelhasználók számára (az alkalmazottak, a tanulók, a Szövetségi alkalmazottak.) a szervezetek (munkahelyi, iskolai, kormányzati szerv). Ezek a fiókok egyedalakzatokkal közvetlenül a felhőben (az Azure AD platform), vagy az Azure ad-hez a helyszíni címtárból, például a Windows Server Active Directoryval szinkronizált. A Microsoft a *gondviselője* , a munkahelyi vagy iskolai fiókok, de a fiók tulajdonosai és a szervezet által vezérelt.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Az alkalmazás Azure AD-fiókokat hivatkozó

A Microsoft nem teszik elérhetővé a végfelhasználók számára, hogy az Azure vagy az Active Directory márkanevek és sem kell azt.

* Miután a felhasználó be van jelentkezve, használja a szervezet és a lehető legnagyobb mértékben embléma. Ez jobb, mint például a "a szervezet." általános feltételek használata
* Jelenleg nincs bejelentkezve felhasználó, tekintse meg, mint a fiókjaikat "munkahelyi vagy iskolai fiókokhoz" és a Microsoft-embléma használata –, átadására, hogy a Microsoft felügyeli ezeket a fiókokat. Ne használja a feltételek, például a "vállalati fiók", "üzleti fiók" vagy "vállalati fiók" felhasználói zavart okozhat.

## <a name="user-account-pictogram"></a>Felhasználói fiók piktogramot

Ezeket az irányelveket korábbi verziójában javasoljuk a "kék jelvény" piktogramot. Fejlesztői és a felhasználói visszajelzések alapján azonban most javasoljuk, hogy a Microsoft-embléma használatát helyette. A Microsoft-embléma segít a felhasználóknak megérteni, hogy a fiókot használják az Office 365 vagy más Microsoft üzleti szolgáltatásokat, jelentkezzen be az alkalmazás felhasználhatja.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Regisztráció és bejelentkezés az Azure ad-ben

Az alkalmazás lehetséges, hogy jelen külön útvonalak regisztráció és bejelentkezés, és a következő szakaszok vizuális útmutató mindkét forgatókönyvet támogatja.

**Ha az alkalmazás támogatja a végfelhasználói regisztrálási (például ingyenes próbaverzióval vagy freemium modellre)**: megjelenítheti egy **bejelentkezési** gombot, amely lehetővé teszi, hogy a felhasználók férhetnek hozzá az alkalmazás a munkahelyi fiókjával vagy a személyes fiókjával. Azure ad-ben az alkalmazás első használatakor jelennek meg a beleegyezést kérő üzenetet.

**Ha az adott alkalmazáshoz szükséges engedélyeket, amelyek csak a rendszergazdák engedélyezhetik, hogy, vagy ha az adott alkalmazáshoz szükséges a szervezeti licencelési**: külön rendszergazdai beszerzése a felhasználói bejelentkezés. A **"lekérése az alkalmazás" gombra** átirányítja a rendszergazdák jelentkezzen be, majd kérje meg őket a szervezet, amely a hozzáadott előnye, hogy az alkalmazás végfelhasználói beleegyezést kérő újrainduljanak felhasználók nevében hozzájárulását.

## <a name="visual-guidance-for-app-acquisition"></a>Vizuális útmutató alkalmazás beszerzése

Az "az alkalmazás letöltése" hivatkozásra kell átirányítja a felhasználót az Azure AD-hozzáférés (Engedélyezés) lapot, hogy az alkalmazás hozzáférhessen a céges adatokhoz, amelyeket a Microsoft által üzemeltetett engedélyezése a szervezet rendszergazdája. Megtudhatja, hogyan kérjen hozzáférést tárgyalja a [alkalmazások az Azure Active Directoryval való integrálását](quickstart-v1-integrate-apps-with-azure-ad.md) cikk.

Miután a rendszergazdák hozzájárulás az alkalmazáshoz, kiválaszthatnak adja hozzá a felhasználói az Office 365 alkalmazás indítója élmény (elérhető-e a gofri, vagy olyan [ https://portal.office.com/myapps ](https://portal.office.com/myapps)). Ha szeretné hirdesse meg ezt a funkciót, használhatja a kifejezések, mint az "A szervezet az alkalmazás hozzáadása" és a egy gomb megjelenítése, például az alábbi példában:

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-branding-guidelines/add-to-my-org.png)

Azt javasoljuk azonban, hogy magyarázó szöveg helyett gombokat írni. Példa:

> *Ha már használja az Office 365 vagy a Microsoft más üzleti szolgáltatásra, akkor is hozzáférést < your_app_name > a szervezet adatai. Ez lehetővé teszi a felhasználók < your_app_name > meglévő munkahelyi fiókjával.*

Letöltése a hivatalos Microsoft-embléma használható az alkalmazásban, kattintson a jobb gombbal a megfelelőt, használatához, és mentse a számítógépre.

| Adategység                                | PNG formátum | SVG-formátum |
| ------------------------------------ | ---------- | ---------- |
| Microsoft logó  | ![Microsoft-embléma PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.png) | ![SVG-Microsoft-embléma](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Jelentkezzen be a vizuális útmutató

Az alkalmazás megjelenjen egy bejelentkezés gombot, amely átirányítja a felhasználókat a bejelentkezési végpont, amely megfelel a protokoll használatával integrálása az Azure ad-ben. Az alábbi szakasz részletesen mi gomb hasonlóan kell kinéznie.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogramot és az "A Microsoft bejelentkezés"

A Microsoft-embléma közötti társítás, és a "Sign in with Microsoft" feltételeket képviselő egyedi más identitásszolgáltatókkal többek között az Azure AD az alkalmazás is támogatja. Ha nincs elég hely a "Sign in with Microsoft", rendben, lerövidítheti a "Sign-in." A gombok világos vagy sötét színsémát is használhat.

Az alábbi ábrán látható, a Microsoft által ajánlott redlines az alkalmazását az eszközök használata esetén. A redlines vonatkozik a "Sign in with Microsoft" vagy a "Bejelentkezés" rövidebb változatban.

![Jelentkezzen be Microsoft redlines](./media/active-directory-branding-guidelines/Sign-in-with-Microsoft-redlines.png)

A hivatalos az alkalmazásban használt képek letöltéséhez kattintson a jobb gombbal szeretné használni, és mentse a számítógépre egy.

| Adategység                                | PNG formátum | SVG-formátum |
| ------------------------------------ | ---------- | ---------- |
| Bejelentkezés Microsofttal (sötét téma)  | ![Jelentkezzen be a gomb sötét téma PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.png) | ![Jelentkezzen be Microsoft gomb sötét téma SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.svg) |
| Bejelentkezés Microsofttal (világos téma) | ![Jelentkezzen be a gomb világos téma PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.png) | ![Jelentkezzen be Microsoft gomb világos téma SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.svg) |
| Jelentkezzen be (sötét téma)                 | ![Bejelentkezés röviden gomb sötét téma PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.png) | ![Röviden gomb sötét téma SVG aláírása](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.svg) |
| Jelentkezzen be (világos téma)                | ![Bejelentkezés röviden gomb világos téma PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.png) | ![Röviden gomb világos téma SVG aláírása](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.svg) |


## <a name="branding-dos-and-donts"></a>Márkajelzési feljegyzés és nem ajánlott műveletek

**Tegye** használata "munkahelyi vagy iskolai fiók" az a "Sign in with Microsoft" gombra, adja meg a végfelhasználókkal ismeri fel, hogy használhatja-e, hogy további magyarázatot. **Nem** használata más feltételek, például a "vállalati fiók", "üzleti fiók" vagy "vállalati fiók."

**Nem** "Office 365-azonosító" vagy "Azure-azonosító". Az Office 365 is az a név egy fogyasztó kínál a Microsofttól, amely az Azure AD nem használ a hitelesítéshez.

**Nem** alter, a Microsoft-embléma.

**Nem** elérhetővé tenni a végfelhasználók számára, hogy az Azure-ban vagy az Active Directory márkái. Rendben azonban ezek a kifejezések használata a fejlesztők, informatikai szakemberek és rendszergazdák.

## <a name="navigation-dos-and-donts"></a>Navigációs feljegyzés és nem ajánlott műveletek

**Tegye** teszik lehetővé a felhasználók jelentkezzen ki, és váltson át egy másik felhasználói fiókból. Bár a legtöbb ember twitteren a Microsoft/Facebook vagy a Google egyetlen személyes fiókkal rendelkezik, személyek rendelt gyakran több szervezet. Több bejelentkezett felhasználó támogatása hamarosan elérhető lesz.
