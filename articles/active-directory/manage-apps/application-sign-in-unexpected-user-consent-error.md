---
title: Váratlan hiba a kérelemhez való hozzájárulás végrehajtásakor | Microsoft dokumentumok
description: A cikk ismerteti azokat a hibákat, amelyek a kérelemhez való hozzájárulás folyamata során fordulhatnak elő, és hogy mit tehet velük kapcsolatban
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea14e02920cf7ba6c5e0a7b415cb92137c915576
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519708"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Váratlan hiba az alkalmazáshoz való hozzájárulás végrehajtásakor

Ez a cikk ismerteti az alkalmazáshoz való hozzájárulás folyamata során előforduló hibákat. Ha olyan váratlan hozzájárulási kéréseket hárít el, amelyek nem tartalmaznak hibaüzeneteket, olvassa el [az Azure AD hitelesítési forgatókönyvei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)című témakört.

Számos, az Azure Active Directoryval integrálható alkalmazásnak engedélyre van szüksége más erőforrások eléréséhez a működéshez. Ha ezek az erőforrások is integrálva vannak az Azure Active Directoryval, gyakran kérik a hozzáféréshez szükséges engedélyeket a közös jóváhagyási keretrendszer használatával. Megjelenik egy hozzájárulási parancs, amely általában akkor fordul elő, amikor egy alkalmazást először használnak, de az alkalmazás későbbi használatakor is előfordulhat.

Bizonyos feltételeknek meg kell felelniük ahhoz, hogy a felhasználó hozzájáruljon az alkalmazás által igényelt engedélyekhez. Ha ezek a feltételek nem teljesülnek, a következő hibák léphetnek fel.

## <a name="requesting-not-authorized-permissions-error"></a>Nem engedélyezett engedélyek kérése hiba
* **AADSTS90093:** &lt;A clientAppDisplayName&gt; egy vagy több olyan engedélyt kér, amelynek megadása nincs engedélyezve. Lépjen kapcsolatba egy rendszergazdával, aki az Ön nevében beleegyezik ebbe az alkalmazásba.
* **AADSTS90094:** &lt;ClientAppDisplayName&gt; engedélyre van szüksége, hogy hozzáférjen a szervezet erőforrásaihoz, hogy csak egy rendszergazda adhat. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ez a hiba akkor fordul elő, ha egy nem vállalati rendszergazda felhasználó olyan alkalmazást próbál használni, amely csak a rendszergazda által megadott engedélyeket kér. Ezt a hibát megoldhatja egy rendszergazda, aki hozzáférést biztosít az alkalmazáshoz a szervezet nevében.

Ez a hiba akkor is előfordulhat, ha a felhasználó nem járul hozzá egy alkalmazáshoz, mivel a Microsoft észleli, hogy az engedélykérelem kockázatos. Ebben az esetben egy naplózási esemény is naplózza a kategória "ApplicationManagement", tevékenység típusa "Hozzájárulás az alkalmazáshoz" és állapot oka a "Kockázatos alkalmazás észlelt".

## <a name="policy-prevents-granting-permissions-error"></a>A házirend megakadályozza az engedélyhiba megadását
* **AADSTS90093:** A &lt;tenantDisplayName&gt; rendszergazdája olyan házirendet állított be, amely&gt; megakadályozza, hogy megadja az alkalmazás &lt;nevének megadását a kért engedélyekben. Lépjen kapcsolatba &lt;a tenantDisplayName&gt;rendszergazdájával, aki az Ön nevében engedélyt adhat az alkalmazásnak.

Ez a hiba akkor fordul elő, ha a vállalati rendszergazda kikapcsolja a felhasználók számára az alkalmazások hozadékát, majd egy nem rendszergazdai felhasználó megkísérli a jóváhagyást igénylő alkalmazások használatát. Ezt a hibát megoldhatja egy rendszergazda, aki hozzáférést biztosít az alkalmazáshoz a szervezet nevében.

## <a name="intermittent-problem-error"></a>Időszakos problémahiba
* **AADSTS90090:** Úgy tűnik, hogy a bejelentkezési folyamat időszakos problémát észlelt a &lt;clientAppDisplayName&gt;számára megadni próbált engedélyek rögzítésével. Próbálkozzon később.

Ez a hiba azt jelzi, hogy időszakos szolgáltatási oldali probléma lépett fel. Meg oldható, ha megpróbálja újra hozzájárulni az alkalmazáshoz.

## <a name="resource-not-available-error"></a>Az erőforrás nem érhető el hiba
* **AADSTS65005:** Az &lt;alkalmazás clientAppDisplayName&gt; engedélyt kért &lt;egy olyan&gt; erőforrás eléréséhezAppDisplayName, amely nem érhető el. 

Lépjen kapcsolatba az alkalmazás fejlesztőjével.

##  <a name="resource-not-available-in-tenant-error"></a>Az erőforrás nem érhető el bérlői hibában
* **AADSTS65005:** &lt;A&gt; clientAppDisplayName hozzáférést kér &lt;egy olyan&gt; erőforrás-erőforráshozAppDisplayName, amely nem érhető el a szervezet &lt;tenantDisplayName&gt;eszközében. 

Győződjön meg arról, hogy &lt;ez az&gt;erőforrás elérhető, vagy lépjen kapcsolatba a tenantDisplayName rendszergazdájával.

## <a name="permissions-mismatch-error"></a>Engedélyeltérési hiba
* **AADSTS65005:** Az alkalmazás beleegyezést &lt;kért az&gt;erőforrás-erőforrás eléréséhezAppDisplayName . Ez a kérés nem sikerült, mert nem egyezik meg az alkalmazás előzetes konfigurálásával az alkalmazás regisztrációja során. Lépjen kapcsolatba az alkalmazás forgalmazójával.**

Ezek a hibák akkor fordulnak elő, amikor a felhasználó által hozzájárulásba kívánt alkalmazás engedélyeket kér egy olyan erőforrás-alkalmazás eléréséhez, amely nem található meg a szervezet címtárában (bérlőjében). Ennek a helyzetnek több oka is lehet:

-   Az ügyfélalkalmazás fejlesztője helytelenül konfigurálta az alkalmazást, így érvénytelen erőforráshoz való hozzáférést kért. Ebben az esetben az alkalmazás fejlesztőjének frissítenie kell az ügyfélalkalmazás konfigurációját a probléma megoldásához.

-   A célerőforrás-alkalmazást képviselő egyszerű szolgáltatás nem létezik a szervezetben, vagy korábban létezett, de eltávolították. A probléma megoldásához az erőforrás-alkalmazás egyszerű szolgáltatását ki kell építeni a szervezetben, hogy az ügyfélalkalmazás engedélyeket kérhessen hozzá. A szolgáltatásnév az alkalmazás típusától függően számos módon kiépíthető, beleértve a következőket:

    -   Előfizetés beszerzése az erőforrás-alkalmazáshoz (Microsoft által közzétett alkalmazások)

    -   Hozzájárulás az erőforrás-alkalmazáshoz

    -   Az alkalmazásengedélyek megadása az Azure Portalon keresztül

    -   Az alkalmazás hozzáadása az Azure AD alkalmazásgalériából

## <a name="next-steps"></a>További lépések 

[Alkalmazások, engedélyek és hozzájárulás az Azure Active Directoryban (v1-végpont)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Hatókörök, engedélyek és hozzájárulás az Azure Active Directoryban (2.0-s vagy os végpont)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


