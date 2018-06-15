---
title: Hozzájárul az alkalmazás végrehajtása során váratlan hiba |} Microsoft Docs
description: Ismerteti, amelyek hozzájárul ahhoz, hogy az alkalmazások és a rájuk vonatkozó teendők során előforduló hibák
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: bbc0cee8a44773c025c6174eaf7eccaba81b8d1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "26617077"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Hozzájárul az alkalmazás végrehajtása során váratlan hiba

A cikkből megtudhatja, hozzájárul ahhoz, hogy egy alkalmazás során előforduló hibákat. Váratlan hibaelhárítás esetén beleegyezést kér, amely nem tartalmazza a hibaüzeneteket, olvassa el [hitelesítési forgatókönyvek az Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Számos alkalmazás, amelyekbe beépül az Azure Active Directory működéséhez egyéb erőforrásainak elérésére engedély szükséges. Ha ezeket az erőforrásokat is integrálva vannak az Azure Active Directoryval, azok eléréséhez engedélyek gyakran kérik a hozzájárulási közös keretrendszer használatával. 

Az eredmény egy beleegyezést kérő üzenete jelenik meg, amely általában akkor következik be, először az alkalmazás használja, de akkor is előfordulhat egy későbbi használja az alkalmazást.

Bizonyos feltételeknek kell teljesülniük felhasználói számára, hogy az engedélyeket, az alkalmazás futtatásához szükséges. Ha ezek a feltételek nem teljesülnek, több hiba is felléphet. Ezek a következők:

## <a name="requesting-not-authorized-permissions-error"></a>A kért nem engedélyezett engedélyek hiba
* **AADSTS90093:** &lt;clientAppDisplayName&gt; egy vagy több engedélyek, amelynek Ön nem jogosult a megadását kéri. Kérje a rendszergazda, aki is hozzájárul az alkalmazás az Ön nevében.

Ez a hiba akkor fordul elő, amikor egy felhasználó egy vállalati rendszergazdai jogosultságokkal nem kísérli meg, amelyeket csak a rendszergazda adhat engedélyeket igénylő alkalmazások. Ez a hiba a szervezet nevében az alkalmazáshoz való hozzáférés biztosítása a rendszergazda megoldhatók.

## <a name="policy-prevents-granting-permissions-error"></a>Házirend megakadályozza, hogy jogosultságokat hiba
* **AADSTS90093:** rendszergazdája &lt;tenantDisplayName&gt; be van állítva egy házirendet, amely megakadályozza, hogy biztosítása &lt;nevű alkalmazás&gt; az engedélyek e a kért tartalomhoz. Forduljon a rendszergazdához &lt;tenantDisplayName&gt;, akik engedélyeket ennek az alkalmazásnak az Ön nevében.

Ez a hiba akkor fordul elő, amikor a vállalati rendszergazda kikapcsolja alkalmazások, hogy a felhasználók, akkor egy rendszergazdai jogokkal nem rendelkező felhasználó próbál jóváhagyását igénylő alkalmazások. Ez a hiba a szervezet nevében az alkalmazáshoz való hozzáférés biztosítása a rendszergazda megoldhatók.

## <a name="intermittent-problem-error"></a>Probléma hiba
* **AADSTS90090:** úgy tűnik, az engedélyek megadása a próbált rögzítése probléma történt &lt;clientAppDisplayName&gt;. Próbálkozzon újra később.

Ez a hiba azt jelzi, hogy az időszakos szolgáltatás kiszolgálóoldali hiba történt. Ez hozzájárul az alkalmazást újra megpróbálja megoldhatók.

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba
* **AADSTS65005:** az alkalmazás &lt;clientAppDisplayName&gt; kért engedélyeket az erőforrás eléréséhez &lt;resourceAppDisplayName&gt; , amely nem áll rendelkezésre. 

Lépjen kapcsolatba az alkalmazás fejlesztőjének.

##  <a name="resource-not-available-in-tenant-error"></a>Az erőforrás nem érhető el a bérlői hiba
* **AADSTS65005:** &lt;clientAppDisplayName&gt; kér az erőforráshoz való hozzáférés &lt;resourceAppDisplayName&gt; , amely nem érhető el a szervezet &lt;tenantDisplayName &gt;. 

Győződjön meg arról, hogy rendelkezésre áll-e ehhez az erőforráshoz, vagy forduljon a rendszergazdához &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Engedélyek verzióeltérési hiba
* **AADSTS65005:** az alkalmazás hozzáférési erőforrás hozzájárul kért &lt;resourceAppDisplayName&gt;. A kérelem sikertelen volt, mert nem felel meg milyen az alkalmazás előre konfigurált volt app regisztráció során. Lépjen kapcsolatba az alkalmazás vendor.* *

Ezek a hibák összes fordulhat elő, ha a felhasználó megpróbálja járul hozzá az alkalmazás engedélyeket, hogy egy erőforrás-alkalmazás, amely nem található a munkahely címtárában (bérlői). Ez akkor fordulhat elő, több okból:

-   Az ügyfél alkalmazásfejlesztő rendelkezik az alkalmazás nincs megfelelően konfigurálva, azt, hogy hozzáférést igényelhessen érvénytelen erőforrás. Ebben az esetben az alkalmazás fejlesztőjének frissítenie kell a probléma megoldásához az ügyfélalkalmazás konfigurációját.

-   A célalkalmazás erőforrás képviselő egyszerű szolgáltatás nem létezik a szervezeten belül, vagy a korábban létezett, de el lett távolítva. A probléma megoldásához, az erőforrás alkalmazás egy egyszerű szolgáltatást ki kell építenie a szervezet, az ügyfélalkalmazás kérhetnek a szükséges fájlengedélyekkel. Ez azonban a hiba akkor fordulhat elő a egy számos módon, attól függően, hogy az alkalmazás, beleértve a következőket:

    -   Előfizetés (Microsoft közzétett alkalmazásokat) az erőforrás-alkalmazáshoz az beszerzése

    -   Beleegyezik abba, hogy az erőforrás-alkalmazáshoz

    -   Az alkalmazás jogosultságokat az Azure-portálon

    -   Az alkalmazás hozzáadása az Azure AD alkalmazás gyűjteményből

## <a name="next-steps"></a>További lépések 

[Alkalmazások, engedélyek és az Azure Active Directoryban (v1 végpont) hozzájárulás](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Hatókörök, engedélyek és az Azure Active Directoryban (v2.0-végponttól) hozzájárulás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


