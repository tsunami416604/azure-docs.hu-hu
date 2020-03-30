---
title: A fejlesztéssel kapcsolatos gyakorlati tanácsok biztonságossá tétele a Microsoft Azure-ban
description: Gyakorlati tanácsok a biztonságosabb kód fejlesztéséhez és egy biztonságosabb alkalmazás felhőbeli üzembe helyezéséhez.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934872"
---
# <a name="secure-development-best-practices-on-azure"></a>Az Azure-ban bevált fejlesztési gyakorlati tanácsok biztonságossá tétele
Ez a cikksorozat bemutatja a biztonsági tevékenységek és ellenőrzések, amelyeket figyelembe kell venni, amikor alkalmazásokat fejleszt a felhőben. A Microsoft biztonsági fejlesztési életciklusának (SDL) fázisait, valamint az életciklus egyes fázisaisorán figyelembe veendő biztonsági kérdéseket és fogalmakat is figyelembe vesszük. A cél az, hogy segítsen meghatározni a tevékenységeket és az Azure-szolgáltatásokat, amelyek segítségével az életciklus minden fázisában egy biztonságosabb alkalmazás tervezése, fejlesztése és üzembe helyezése.

A cikkekben található javaslatok az Azure biztonságával kapcsolatos tapasztalatainkból és ügyfeleink tapasztalataiból származnak. Használhatja ezeket a cikkeket, mint a referencia, amit meg kell fontolnia egy adott szakaszában a fejlesztési projekt, de azt javasoljuk, hogy olvassa el az összes cikket az elejétől a végéig legalább egyszer. Az összes cikk elolvasása olyan fogalmakat mutat be, amelyeket a projekt korábbi szakaszaiban esetleg kihagyott. Ezeknek a fogalmaknak a termék kiadása előtt történő megvalósítása segíthet biztonságos szoftverek létrehozásában, a biztonsági megfelelőségi követelmények teljesítésében és a fejlesztési költségek csökkentésében.

Ezek a cikkek a szoftvertervezők, -fejlesztők és -tesztelők számára szolgálnak minden szinten, akik biztonságos Azure-alkalmazásokat hoznak létre és telepítenek.

## <a name="overview"></a>Áttekintés

A biztonság az egyik legfontosabb szempontja minden alkalmazás, és ez nem egy egyszerű dolog, hogy jobb. Szerencsére az Azure számos olyan szolgáltatást nyújt, amelyek segítségével biztonságossá teheti alkalmazását a felhőben. Ezek a cikkek a szoftverfejlesztési életciklus minden szakaszában megvalósíthatók, így biztonságosabb kódot fejleszthet ki, és biztonságosabb alkalmazást helyezhet üzembe a felhőben.

## <a name="security-development-lifecycle"></a>Biztonságos fejlesztési életciklus

A biztonságos szoftverfejlesztés bevált gyakorlatainak követése megköveteli a biztonság integrálását a szoftverfejlesztési életciklus minden fázisába, a követelményelemzéstől a karbantartásig, függetlenül a projekt módszertanától[(vízesés,](https://en.wikipedia.org/wiki/Waterfall_model) [agilis](https://en.wikipedia.org/wiki/Agile_software_development)vagy [DevOps](https://en.wikipedia.org/wiki/DevOps)). A nagy horderejű adatszivárgások és az operatív biztonsági hibák kihasználása nyomán egyre több fejlesztő érti meg, hogy a biztonsági intézkedéseket a fejlesztési folyamat során kezelni kell.

Minél később old meg egy problémát a fejlesztési életciklusban, annál többe kerül a javítás. A biztonsági problémák sem kivételek. Ha figyelmen kívül hagyja a szoftverfejlesztés korai fázisaiban felmerülő biztonsági problémákat, az azt követő fázisok örökölhetik az előző fázis biztonsági réseit. A végtermék több biztonsági problémát és a jogsértés lehetőségét is felhalmozta. A fejlesztés életciklusának minden fázisába beépíti a biztonságot, és segít a problémák korai felzárkóztatásában, és segít csökkenteni a fejlesztési költségeket.

A Microsoft security development [lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) fázisait követjük, hogy olyan tevékenységeket és Azure-szolgáltatásokat vezessünk be, amelyek segítségével az életciklus minden fázisában biztonságos szoftverfejlesztési gyakorlatokat valósíthat meg.

Az SDL fázisok a következők:

  - Képzés
  - Követelmények
  - Tervezés
  - Megvalósítás
  - Ellenőrzés
  - Kiadás
  - Válasz

![Biztonságos fejlesztési életciklus](./media/secure-dev-overview/01-sdl-phase.png)

Ezekben a cikkekben az SDL-fázisokat tervezésbe, fejlesztésbe és üzembe helyezésbe csoportosítjuk.

## <a name="engage-your-organizations-security-team"></a>A szervezet biztonsági csapatának bevonása

Előfordulhat, hogy a szervezet rendelkezik egy hivatalos alkalmazásbiztonsági programmal, amely a fejlesztési életciklus során az elejétől a végéig segíti a biztonsági tevékenységeket. Ha a szervezet rendelkezik biztonsági és megfelelőségi csapatokkal, az alkalmazás fejlesztése előtt mindenképpen vegye igénybe őket. Kérdezze meg őket az SDL minden fázisában, hogy vannak-e olyan feladatok, amelyeket kihagyott.

Tisztában vagyunk azzal, hogy sok olvasó nem rendelkezik biztonsági vagy megfelelőségi csapattal. Ezek a cikkek segítséget nyújtanak az SDL egyes fázisaiban figyelembe veendő biztonsági kérdések és döntések megoldásában.

## <a name="resources"></a>Források

Az alábbi erőforrások segítségével többet tudhat meg a biztonságos alkalmazások fejlesztéséről, és az Azure-beli alkalmazások biztonságáról:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – Az SDL a Microsoft szoftverfejlesztési folyamata, amely segít a fejlesztőknek biztonságosabb szoftverek létrehozásában. Segít a biztonsági megfelelőségi követelmények teljesítésében, miközben csökkenti a fejlesztési költségeket.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) - OWASP egy online közösség, amely termel szabadon elérhető cikkek, módszerek, dokumentáció, eszközök és technológiák területén a webes alkalmazások biztonságát.

[Pushing Left, Like a Boss](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) - Egy sor online cikkek, amely felvázolja a különböző típusú alkalmazás biztonsági tevékenységek, hogy a fejlesztők kell befejezni, hogy hozzon létre biztonságosabb kódot.

[Microsoft identity platform](../../active-directory/develop/index.yml) – A Microsoft identitásplatform az Azure AD identitásszolgáltatási szolgáltatás és fejlesztői platform fejlődése. Ez egy teljes funkcionalitású platform, amely egy hitelesítési szolgáltatásból, nyílt forráskódú kódtárakból, alkalmazásregisztrációból és -konfigurációból, teljes fejlesztői dokumentációból, kódmintákból és egyéb fejlesztői tartalmakból áll. A Microsoft identity platform támogatja az iparági szabványnak megfelelő protokollokat, például az OAuth 2.0-s és az OpenID Connect protokollt.

[Az Azure-megoldások biztonsági gyakorlati tanácsai](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – A felhőalapú megoldások azure-beli tervezése, üzembe helyezése és kezelése során használható biztonsági gyakorlati tanácsok gyűjteménye. Ez a dokumentum célja, hogy egy forrás az informatikai szakemberek. Ez magában foglalhatja a tervezők, építészek, fejlesztők és tesztelők, akik biztonságos Azure-megoldásokat hoznak létre és telepítenek.

[Biztonsági és megfelelőségi tervrajzok az Azure-ban](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – az Azure biztonsági és megfelelőségi tervrajzai olyan erőforrások, amelyek segítségével olyan felhőalapú alkalmazásokat hozhat létre és indíthat el, amelyek megfelelnek a szigorú előírásoknak és szabványoknak.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben olyan biztonsági vezérlőket és tevékenységeket javasoljuk, amelyek segítségével biztonságos alkalmazásokat tervezhet, fejleszthet és helyezhet üzembe.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
- [Biztonságos alkalmazások telepítése](secure-deploy.md)
