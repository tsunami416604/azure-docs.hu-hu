---
title: "Irányelvek Branding alkalmazásokhoz |} Microsoft Docs"
description: "Az Azure Active Directory fejlesztőknek készített erőforrásairól átfogó útmutatóját"
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mtillman
editor: 
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: 51adb13e15312130841c065f5678209508457559
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="branding-guidelines-for-applications"></a>Az alkalmazások arculati útmutatóját
Ez a témakör ismerteti az Azure Active Directoryval (Azure AD) alkalmazások fejlesztése során használjon arculati útmutatóját. Ezeket az irányelveket az ügyfelek közvetlen szeretnének használni a munkahelyi vagy iskolai fiókkal, az Azure AD-ban kezelt vagy személyes fiókot a regisztrációt, és jelentkezzen be az alkalmazás segítségével.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Személyes fiókokat és a munkahelyi vagy iskolai fiókjait a Microsoft
A Microsoft kezeli a felhasználói fiókok kétféle:

* **Személyes fiókok** (korábbi nevén Windows Live ID). Ezek a fiókok közti kapcsolatot képviselik *egyedi* Microsoft fogyasztói eszközök és szolgáltatások eléréséhez használt felhasználók és a Microsoft és a vannak. Ezek a fiókok személyes használatra lettek tervezve.
* **Munkahelyi vagy iskolai fiókját.** Ezeket a fiókokat a Microsoft által felügyelt Azure Active Directory címtárszolgáltatást használó szervezeteknek nevében. Ezek a fiókok segítségével a Microsoft Office 365 és más üzleti szolgáltatásaiban jelentkezzen be.

A Microsoft munkahelyi vagy iskolai fiókok általában rendeli hozzá a végfelhasználók számára (az alkalmazottak, a diákok, szövetségi alkalmazottak) a vállalatuk (munkahelyi, iskolai, kormányzati Ügynökség). Ezek a fiókok vagy közvetlenül a felhőben, az Azure AD platform, a lemezkép vagy az egy a helyszíni címtár, például Windows Server Active Directory szinkronizált az Azure ad. A Microsoft a *konfigurációelem gondnoka képes legyen* a munkahelyi vagy iskolai fiókok, de a fiók tulajdonosa, és a szervezet által vezérelt.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Az alkalmazás az Azure AD-fiókok hivatkozik
A Microsoft nem teszi közzé az Azure vagy az Active Directory védjegyek, és egyik sem végfelhasználóknak meg kell.

* Ha felhasználó jelentkezik be, a szervezet nevét és a lehető legnagyobb mértékben embléma kell használnia. Ez jobb, mint például a "a szervezet" általános kifejezések használatával.
* Amikor a felhasználó nem jelentkezik be, tekintse át a fiókok, mint a "munkahelyi vagy iskolai fiókok" és a Microsoft embléma használja, hogy ezeket a fiókokat a Microsoft által felügyelt közvetíteni. Ne használja a "vállalati fiók", "üzleti fiók" vagy "vállalati fiók" feltételek, amelyek felhasználói zavart okozhat.

## <a name="user-account-pictogram"></a>Felhasználói fiók piktogramot
Ezeket az irányelveket korábbi verzióiban javasoljuk, használja a "kék jelvény" piktogramot. A felhasználói és a fejlesztői visszajelzései alapján, most javasoljuk a Microsoft embléma használatát helyette. Ez segít megérteni, hogy a fiókot használják a Office 365 vagy a Microsoft egyéb üzleti szolgáltatások jelentkezzen be az alkalmazás felhasználhatja a felhasználóknak.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Iratkozik fel, és az Azure AD bejelentkezés
Az alkalmazás-előfizetési és a bejelentkezési külön elérési jelenthet, és a következő szakaszok visual útmutatás nyújtása a mindkét forgatókönyvet.

**Ha az alkalmazás támogatja a felhasználó bejelentkezési fel (pl. ingyenes próba vagy freemium modell)**: megjelenítheti a **bejelentkezési** gomb, amely lehetővé teszi az alkalmazás a munkahelyi fiókkal vagy személyes fiókkal. Az Azure AD egy jóváhagyási kérése az első alkalommal az alkalmazás megjelenik.

**Ha az alkalmazás, amely csak a rendszergazdák is hozzájárul engedélyekkel kell rendelkeznie, vagy ha az alkalmazás használatához szervezeti licencelési**: a felhasználói bejelentkezési admin beszerzési kell egymástól. A **"beolvasása az alkalmazás" gomb** átirányítja a rendszergazdák számára, hogy jelentkezzen be, majd kérje meg őket a hozzájárulás a szervezetben lévő felhasználók nevében. További előnye, letiltása, végfelhasználók beleegyezést kér az alkalmazás azt.

## <a name="visual-guidance-for-app-acquisition"></a>Visual-útmutató a alkalmazás beszerzése
A "az alkalmazás letöltése" hivatkozást kell átirányítja a felhasználót az Azure AD-hozzáférés (engedélyezése) lapon, hogy a szervezetek rendszergazda hitelesítése az alkalmazásnak, hogy a szervezeti adatokat a Microsoft által futtatott. Részletes információt kérjen hozzáférést ismerteti a [alkalmazások integrálása az Azure Active Directoryval](active-directory-integrating-applications.md) cikk.

Után a rendszergazdák járul hozzá az alkalmazást, akkor választhat adja hozzá a felhasználói Office 365 alkalmazás indító élmény (elérhető a waffle és a [https://portal.office.com/myapps](https://portal.office.com/myapps)). Ha szeretné ezt a képességet hirdetményt, használhatja a feltételek "A szervezet az alkalmazás hozzáadása" és egy ehhez hasonló gomb megjelenítése:

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-branding-guidelines/add-to-my-org.png)

Azonban azt javasoljuk, hogy ahelyett, hogy a gombok magyarázó szöveget írni. Példa:

> *Ha már az Office 365 vagy a Microsoft más üzleti szolgáltatást, akkor egyszerűen < your_app_name > hozzáférést biztosíthat a szervezet adatait. Ez lehetővé teszi a felhasználók meglévő munkahelyi fiókkal < your_app_name > elérésére.*
> 
> 

## <a name="visual-guidance-for-sign-in"></a>Visual-útmutató a bejelentkezés
Az alkalmazás meg a bejelentkezési gomb, amely átirányítja a felhasználókat a bejelentkezési végpontot, amely megfelel az Azure AD integrálása használt protokoll. A következő szakasz részletesen bemutatja a mi gombot hasonlóan kell kinéznie.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogramot és az "Jelentkezzen be Microsoft"
A Microsoft embléma a társítás, és a "Sign in with Microsoft" feltételeket, amelyek egyedileg jelöli az egyéb identitás-szolgáltatóktól többek között az Azure AD az alkalmazás is támogatja. Nincs elég hely a "Sign in with Microsoft", akkor az ok gombra. Rövidítse le azt a "Bejelentkezés".

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-branding-guidelines/sign-in-light.png)

A gombok sötét színsémát használhatja.

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Márkajelzési helyes, és nem ajánlott műveletek
**Tegye** felhasználásával "munkahelyi vagy iskolai fiók" a "Sign in with Microsoft" gombra kattintva további magyarázatot ismeri fel, hogy használhatják végfelhasználók segítségével. **Nem** használjon más feltételek, például a "vállalati fiók", "üzleti fiók" vagy "vállalati fiók."

**Nem** "Office 365 ID" vagy "Azure ID". Az Office 365 esetén is kínál a Microsoft, amely nem használja az Azure AD-hitelesítéshez felhasználóinak.

**Nem** a Microsoft embléma alter.

**Nem** teszi közzé az Azure vagy az Active Directory márkákat végfelhasználóknak. Ez így megfelelő azonban fejlesztők számára, az informatikai szakemberek és a rendszergazdák használni ezeket a feltételeket.

## <a name="navigation-dos-and-donts"></a>Navigációs javaslatok és nem ajánlott műveletek
**Tegye** teszik lehetővé a felhasználók számára jelentkezzen ki, és váltson másik felhasználói fiókot. Amíg a legtöbben ugyanazt a személyes fiókot a Microsoft/Facebook/Google/Twitter, személyek társított gyakran több szervezet. Több bejelentkezett felhasználó támogatása hamarosan elérhető.

