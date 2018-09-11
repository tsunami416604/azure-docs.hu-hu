---
title: Váratlan hiba történt, amikor beleegyezést ad egy alkalmazás |} A Microsoft Docs
description: A cikk ismerteti, beleegyezik abba, hogy egy alkalmazás, és mit tehet velük kapcsolatban a folyamat során előforduló hibák
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 70413d3467b2f9d5591e6138ed1a7347db58264b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356699"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Váratlan hiba történt, amikor beleegyezést ad egy alkalmazás

Ez a cikk ismerteti, beleegyezik abba, hogy egy alkalmazás folyamata során előforduló hibákat. Ha hibaelhárítást váratlan beleegyezést kérő, amely nem tartalmazza a hibaüzeneteket, lásd: [hitelesítési forgatókönyvek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Számos alkalmazás, amelyekbe beépül az Azure Active Directory szükséges engedélyekkel ahhoz, hogy működni egyéb erőforrások eléréséhez. Ha ezeket az erőforrásokat is integrálhatók az Azure Active Directoryval, hozzáférési engedélyeket gyakran kérik a közös hozzájárulási keretrendszer használatával. A beleegyezést kérő üzenetet jelenik meg, amely általában akkor fordul elő, először egy alkalmazást használnak, de akkor is előfordulhat az alkalmazás egy későbbi használatra.

Bizonyos feltételeknek kell teljesülniük, egy felhasználó, hogy engedélyt adjanak az alkalmazáshoz szükséges engedélyeket. Ha ezek a feltételek nem teljesülnek, a következő hibák akkor fordulhatnak elő.

## <a name="requesting-not-authorized-permissions-error"></a>Nem jogosult engedélyeket hiba kérése
* **AADSTS90093:** &lt;clientAppDisplayName&gt; egy vagy több olyan engedélyeket, amelynek Ön nem jogosult a megadását kéri. Forduljon a rendszergazdához, aki engedélyezhetik, hogy az alkalmazás az Ön nevében.

Ez a hiba akkor fordul elő, amikor egy felhasználó egy vállalati rendszergazdai jogosultságokkal nem kísérel meg, amely csak a rendszergazda biztosíthat engedélyeket kér az alkalmazás használatát. Ez a hiba megoldhatók a rendszergazda az alkalmazást a szervezet nevében való hozzáférést.

## <a name="policy-prevents-granting-permissions-error"></a>Házirend miatt engedélyek hiba
* **AADSTS90093:** az rendszergazdájának &lt;tenantDisplayName&gt; által beállított szabályzat, amely megakadályozza, hogy jegykiadó &lt;nevű alkalmazás&gt; kér, az engedélyeket. Lépjen kapcsolatba az rendszergazdájának &lt;tenantDisplayName&gt;, akik az engedélyeket ehhez az alkalmazáshoz az Ön nevében.

Ez a hiba akkor fordul elő, ha egy vállalati rendszergazda kikapcsolja lehetővé teszi a felhasználók számára, hogy engedélyt adjanak az alkalmazásokat, majd egy nem rendszergazdai felhasználó megpróbálja használni olyan alkalmazás, amely engedélyt kell kapnia. Ez a hiba megoldhatók a rendszergazda az alkalmazást a szervezet nevében való hozzáférést.

## <a name="intermittent-problem-error"></a>Időnként problémákat tapasztalt hiba
* **AADSTS90090:** úgy tűnik, a bejelentkezési folyamat észlelt egy időszakos probléma rögzítése az engedélyek megadása a próbált &lt;clientAppDisplayName&gt;. Próbálkozzon újra később.

Ez a hiba azt jelzi, hogy történt-e egy szolgáltatás időszakos ügyféloldali hiba történt. Megoldható úgy, hogy engedélyt adjanak az újból az alkalmazást.

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba
* **AADSTS65005:** az alkalmazás &lt;clientAppDisplayName&gt; a kért erőforrás elérésére jogosult &lt;resourceAppDisplayName&gt; , amely nem érhető el. 

Lépjen kapcsolatba az alkalmazás fejlesztőjével.

##  <a name="resource-not-available-in-tenant-error"></a>Az erőforrás nem érhető el a bérlő hiba
* **AADSTS65005:** &lt;clientAppDisplayName&gt; erőforrásokhoz való hozzáférést kér &lt;resourceAppDisplayName&gt; , amely nem áll rendelkezésre a szervezet &lt;tenantDisplayName &gt;. 

Győződjön meg arról, hogy az erőforrás elérhető legyen, vagy lépjen kapcsolatba az rendszergazdájának &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Engedélyek verzióeltérési hiba
* **AADSTS65005:** az alkalmazás hozzáférési erőforrás hozzájárulást kért &lt;resourceAppDisplayName&gt;. A kérelem sikertelen volt, mert nem felel meg hogyan az alkalmazás előre konfigurálva lett alkalmazás regisztrációja során. Forduljon az alkalmazás vendor.* *

Ezek a hibák összes fordulhatnak elő, amikor egy felhasználó megpróbál beleegyezik abba, hogy az alkalmazás által kért, amely a szervezet címtárában (bérlő) nem található erőforrás-alkalmazás elérésére jogosult. Ez a helyzet akkor fordulhat elő, több okból:

-   Az ügyféloldali alkalmazás fejlesztője rendelkezik az alkalmazás nem megfelelően konfigurálva, okozza, hogy a kérés érvénytelen erőforrást eredményezett a hozzáférést. Ebben az esetben az alkalmazás fejlesztőjének frissítenie kell a probléma megoldásához az ügyfélalkalmazás konfigurációját.

-   Egy egyszerű szolgáltatást a célként megadott erőforrás-alkalmazás jelölő nem létezik a szervezeten belül, vagy a korábban létezett, de el lett távolítva. A probléma megoldásához, egy egyszerű szolgáltatást az erőforrás-alkalmazás ki kell építeni a szervezeten belüli, az ügyfélalkalmazás hozzá engedélyeket kérhet. Az egyszerű szolgáltatás építhető számos módon, attól függően, az alkalmazás, többek között:

    -   Az erőforrás-alkalmazás (a Microsoft közzétett alkalmazások) előfizetés beszerzése

    -   Beleegyezik abba, hogy az erőforrás-alkalmazás

    -   Az Azure Portalon az Alkalmazásengedélyek megadása

    -   Az alkalmazás hozzáadása a az Azure AD Alkalmazáskatalógusában

## <a name="next-steps"></a>További lépések 

[Alkalmazások, engedélyek és jóváhagyás az Azure Active Directoryban (v1-végpontra)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Hatókörök, engedélyek és jóváhagyás az Azure Active Directoryban (v2.0-végpontra)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


