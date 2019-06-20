---
title: Biztonságos fejlesztési, ajánlott eljárások a Microsoft Azure
description: Gyakorlati tanácsok segítséget nyújtanak a biztonságosabb kódíráshoz fejlesztése és biztonságosabb-alkalmazás üzembe helyezése a felhőben.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144463"
---
# <a name="secure-development-best-practices-on-azure"></a>Biztonságos fejlesztési, ajánlott eljárások az Azure-ban
A cikksorozat bemutatja, biztonsági tevékenységeket és a vezérlőket, fontolja meg a felhőalapú alkalmazások fejlesztése során. A Microsoft biztonságos fejlesztési Életciklussal (SDL) és a biztonsági kérdések és fogalmak kell figyelembe venni a életciklusának minden fázisában fázisai terjed ki. A célja, hogy a segítségével meghatározhatja a tevékenységeket és az Azure-szolgáltatások, amelyek segítségével a életciklusának minden fázisában tervezéséhez, fejlesztéséhez és üzembe helyezése nagyobb biztonságot.

A javaslatok a cikkekben az funkciót az Azure biztonsági és a platformért felületéről származnak. A megfontolandó egy adott fázisban az alkalmazásfejlesztési projekt hivatkozásként van listázva ezeket a cikkeket is használhatja, de javasoljuk, hogy akkor is olvassa végig minden cikk az elejétől a végéig legalább egyszer. Olvasása az összes cikk bemutatja, előfordulhat, hogy elmulasztása korábbi szakaszában, a projekt fogalmakat. Ezek a fogalmak végrehajtási viselkednek a termék segítségével biztonságos szoftvereket, oldja meg a biztonsági megfelelőségi követelményeknek, és csökkentheti a fejlesztési költségeket.

Ezek a cikkek célja, hogy egy erőforrás szoftver tervezők, fejlesztők számára, és tesztelők minden szintjén, hogy hozhat létre és helyezzen üzembe biztonságos Azure-alkalmazásokat.

## <a name="overview"></a>Áttekintés

Biztonsági egyik legfontosabb szempontja bármely alkalmazás, és már nem egy egyszerű dolog kellhet. Szerencsére az Azure biztosít, amelyek segítségével számos szolgáltatás biztonságos alkalmazását a felhőben. Ezek a cikkek címe tevékenységeket és az Azure-szolgáltatások valósítható meg a szoftver fejlesztési életciklus során fejlesztése a biztonságosabb kódíráshoz, és biztonságosabb-alkalmazás üzembe helyezése a felhőben minden egyes szakaszában.

## <a name="security-development-lifecycle"></a>Biztonságos fejlesztési életciklus

Gyakorlati tanácsok követése biztonságos szoftverfejlesztői szükséges biztonsági integrálása a karbantartás, függetlenül a projekt módszertan követelmény elemzést a szoftver fejlesztési életciklus minden fázisában ([vízesés](https://en.wikipedia.org/wiki/Waterfall_model), [Agilis](https://en.wikipedia.org/wiki/Agile_software_development), vagy [fejlesztési és üzemeltetési](https://en.wikipedia.org/wiki/DevOps)). A nagy adatok nyomán és operatív biztonsági hibára derült kiaknázását fejlesztők vannak ismertetése, hogy biztonsági kell foglalkozni a fejlesztési folyamat során.

A probléma megoldását a később a fejlesztési életciklus során, a minél több, javítsa ki a rendszer díjmentesen. Biztonsági kérdések sem jelentenek kivételt. A szoftverfejlesztés korai szakaszaiban biztonsági problémák figyelmen kívül hagyása, ha a minden egyes fázis, a következő előfordulhat, hogy a biztonsági réseket, az előző fázis öröklik. A végtermékben fog rendelkezik halmozott, több biztonsági problémák és megsértése lehetőségét. Biztonsági beépítése a fejlesztési életciklus valamennyi fázisában segít korai tényleges problémákat, és segít csökkenteni a fejlesztési költségeket.

A Microsoft fázisai követjük [biztonságos fejlesztési Életciklussal (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) tevékenységek és az Azure-szolgáltatások, amelyek segítségével biztonságos szoftverfejlesztési gyakorlatban teljesítése életciklusának minden fázisában.

Az SDL fázisai a következők:

  - Képzés
  - Követelmények
  - Tervezés
  - Megvalósítás
  - Ellenőrzés
  - Kiadás
  - Válasz

![Biztonságos fejlesztési életciklus](./media/secure-dev-overview/01-sdl-phase.png)

Ezekben a cikkekben azt csoportosíthatja az SDL fázisok tervezési fejlesztése és üzembe helyezése.

## <a name="engage-your-organizations-security-team"></a>A szervezet biztonsági csapat léphet.

A szervezeténél esetleg egy hivatalos alkalmazás biztonsági program, amely segítséget nyújt a biztonsági tevékenységek a fejlesztési életciklus során le. Ha a szervezete biztonsági és megfelelőségi csapatok, ügyeljen arra, hogy az alkalmazás fejlesztésének megkezdése előtt bevonhatja őket. Kérje meg őket minden egyes fázisban az SDL-ből, hogy vannak-e olyan feladatokat kihagyta.

Tisztában vagyunk vele, hogy számos olvasók előfordulhat, hogy rendelkezik-e folytatni a biztonsági vagy megfelelőségi csapat. Ezek a cikkek nyújt segítséget a biztonsági kérdéseket és a döntések figyelembe kell vennie az SDL-ből, minden egyes fázisban.

## <a name="resources"></a>További források

További tudnivalók a biztonságos alkalmazások fejlesztésére és biztonságossá tétele az alkalmazások az Azure-ban a a következőket:

[A Microsoft biztonságos fejlesztési Életciklussal (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – az SDL egy olyan szoftverfejlesztési folyamat, amelynek segítségével a fejlesztők biztonságosabb szoftvereket. Segít a biztonsági megfelelőségi követelményeknek cím szoftverfejlesztési költségek csökkentése mellett.

[Nyissa meg a Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP egy online Közösség, amely ingyenesen elérhető cikkeket, metodológiákra, dokumentáció, eszközök és technológiák web application security mezőjében.

[Hogyan lehet továbbítani rá balra, mint például a főnökétől](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – online cikkekben sorozata, amely ismerteti a különböző típusú alkalmazás biztonsági tevékenységek a fejlesztők hozhat létre a biztonságosabb kódíráshoz kell végrehajtani.

[A Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/) – a Microsoft az Azure AD identity service és a fejlesztői platform továbbfejlesztett változata. Egy teljes körű platform, amely olyan hitelesítési szolgáltatás, nyílt forráskódú kódtárai, alkalmazás regisztrálása és a konfiguráció, teljes körű fejlesztői dokumentáció, Kódminták és egyéb fejlesztői tartalom áll. A Microsoft identity platform támogatja a szabványos protokollok, például az OAuth 2.0 és OpenID Connect.

[Ajánlott biztonsági eljárások az Azure-megoldások](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – használni megtervezésére, üzembe helyezése és kezelése a felhőalapú megoldások az Azure ajánlott biztonsági eljárások gyűjteménye. Ez a tanulmány van egy erőforrást, informatikai szakemberek számára készült. Ez tartalmazhat tervezők, a tervezőket, fejlesztők és tesztelők, akik létrehozása és biztonságos Azure-megoldások üzembe helyezése.

[Biztonsági és megfelelőségi tervek az Azure-ban](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – az Azure biztonsági és megfelelőségi tervek, amelyek segítségével erőforrásokat hozhat létre, és indítsa el a felhőalapú alkalmazások, amelyek megfelelnek a szigorú előírásoknak és szabványoknak.

## <a name="next-steps"></a>További lépések
Az alábbi cikkeket azt javasoljuk, hogy a biztonsági vezérlők és tevékenységek, amelyek segítségével tervezéséhez, fejlesztéséhez és helyezzen üzembe biztonságos alkalmazásokat.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
- [Biztonságos alkalmazások üzembe helyezése](secure-deploy.md)
