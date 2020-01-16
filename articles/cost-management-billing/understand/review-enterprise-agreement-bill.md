---
title: Az Azure Nagyvállalati Szerződés-számla áttekintése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők az Azure Nagyvállalati Szerződésekhez tartozó használati adatok és a hozzájuk tartozó számlák.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: 2beb1d3e4468ff96f629dd6ec7015376a7f3a904
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987263"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Az Azure Nagyvállalati Szerződés számlájának ismertetése

A Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek akkor kapnak számlát, amikor túllépik a vállalat kreditkeretét, vagy olyan szolgáltatást használnak, amelyet nem fedez a kredit.

A szervezet kreditjébe beleszámít a pénzügyi kötelezettségvállalás is. A pénzügyi Kötelezettségvállalás az az összeg, amelyet a szervezet előre befizet az Azure-szolgáltatások használatára. Amennyiben pénzügyi kötelezettségvállalási befizetéseket kíván hozzáadni a Nagyvállalati Szerződéséhez, lépjen kapcsolatba a Microsoft ügyfélkapcsolati munkatársával vagy viszonteladójával.

Ez az oktatóanyag csak Azure-Nagyvállalati Szerződést használó Azure-ügyfelekre vonatkozik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számlázott díjak áttekintése
> * A szolgáltatással kapcsolatos költségek áttekintése
> * Marketplace-számla áttekintése

## <a name="prerequisites"></a>Előfeltételek

A számlán szereplő díjak áttekintésére és ellenőrzésére a vállalati rendszergazdáknak van lehetőségük. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](../manage/understand-ea-roles.md). Ha nem tudja, hogy ki a szervezet vállalati rendszergazdája, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>A legtöbb ügyfélhez tartozó számlázott díjak áttekintése

Az alábbi szakasz nem vonatkozik az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre.

Akkor kap Azure-számlát, ha sor kerül a következő események egyikére a számlázási ciklus folyamán:

- **Szolgáltatás**: a szervezet használati díjai túllépik a kreditek egyenlegét.
- **Külön számlázott díjak**: a szervezet által használt szolgáltatások nem vonatkoznak a kreditre. A következő szolgáltatások számlázása mindig külön történik, a szervezet kreditegyenlegétől függetlenül:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (havi)
    - Visual Studio Enterprise (éves)
    - Visual Studio Professional (havi)
    - Visual Studio Professional (éves)
- **Marketplace-díjak**: az Azure Marketplace-beli vásárlásokat és a használatot nem a szervezet kreditje szabályozza. Ezért a Marketplace díjairól mindig számlát kap, a kreditegyenlegétől függetlenül. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A számla feltünteti az összes Azure-használatot és az esetleges Marketplace-beli díjakat. Ha rendelkezik kreditegyenleggel, a rendszer alkalmazza azt az Azure-használatra.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget az Azure-számlájával. A **Használat összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Azure EA portálra](https://ea.azure.com). Ezután válassza a **jelentések**elemet. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép, amely az M + C beállítást mutatja a használat összegzésében.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Az **Összes használat** és az **Azure Marketplace** összegének meg kellene egyeznie a számlán szereplő **teljes bővített összeggel**. További részleteket a díjakról a **használati adatok letöltését** ismertető cikkben talál.  

![A letöltési használat lapot ábrázoló képernyőfelvétel](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Számlázott díjak áttekintése más ügyfelek számára

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre vonatkozik.

Akkor kap egy vagy több Azure-számlát, ha sor kerül a következő események egyikére:

- **Szolgáltatás**: a szervezet használati díjai túllépik a kreditek egyenlegét.
- **Külön számlázott díjak**: a szervezet által használt szolgáltatások nem vonatkoznak a kreditre. A következő szolgáltatásokat számlázzuk ki Önnek:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (havi)
    - Visual Studio Enterprise (éves)
    - Visual Studio Professional (havi)
    - Visual Studio Professional (éves)
- **Marketplace-díjak**: az Azure Marketplace-beli vásárlásokat és a használatot nem fedi le a szervezete kreditje, és a számlázás külön történik. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A szolgáltatási keret túllépéséből adódó díjakról és a számlázási időszak során külön számlázott díjakról egyetlen számlát kap. Ez tartalmazza a fenti két típusba tartozó díjakat. A Marketplace-beli díjakról mindig külön számlát kap.

## <a name="review-service-overage-charges-for-other-customers"></a>Tekintse át a szolgáltatási kereten túli díjakat más ügyfelek számára

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget a szolgáltatási kerettúllépés számlájával. A szolgáltatási kerettúllépés számlája olyan használatra vonatkozik, amely túllépi a szervezeti keretet, és/vagy a kredit által nem fedezett szolgáltatásokra. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Azure EA portálra](https://ea.azure.com) , majd válassza a **jelentések**elemet. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép, amely az M + C beállítást mutatja a használat összegzésében.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Az **Összes használat** összegének egyezne kell a szolgáltatási kerettúllépés számláján szereplő **teljes bővített összeggel**. További információt a díjakról a **Használati adatok letöltése** > **Speciális jelentés letöltése** alatt talál. A jelentés nem tartalmazza az adókat és a foglalási vagy piactéri díjakat.  

![A letöltési használat lapon látható speciális jelentés letöltését bemutató képernyőkép.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

A következő lista számba veszi a számlákon és az Enterprise Portal **Használat összefoglalása** lapon előforduló kifejezéseket és leírásokat:

|Számlázási kifejezés|A Használat összefoglalása lap kifejezései|Leírás|
|---|---|---|
|Teljes bővített összeg|Összes használat|A teljes adók nélküli használati díj az adott időszakban, a kredit alkalmazása előtt.|
|Kötelezettségvállalás felhasználása|Kötelezettségvállalás felhasználása|Az adott időszak során alkalmazott kredit.|
|Összes értékesítés|Kerettúllépés összesen|A teljes, a kredit mennyiségét meghaladó használati díj. Az összeg az adókat nem tartalmazza.|
|Adó összege|Nem alkalmazható|Az adott időszakban keletkező összes értékesítésre vonatkozó adó összege.|
|Teljes összeg|Nem alkalmazható|A számla fizetendő összege a kredit alkalmazása és az adók hozzáadása után.|

### <a name="review-marketplace-invoice"></a>Marketplace-számla áttekintése

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Azure Marketplace-re vonatkozó teljes összeget a piactéri számlájával az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján. A piactéri számlán csak az Azure Marketplace-beli vásárlások és használat szerepel. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Enterprise Portalra](https://ea.azure.com) , majd válassza a **jelentések**elemet. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép, amely az M + C beállítást mutatja a használat összegzésében.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Az **Azure Marketplace-re** vonatkozó teljes összegnek meg kell egyeznie a piactéri számla **összes értékesítésre** vonatkozó összegével. További információt a használaton alapuló díjakról a **használati adatok letöltését** ismertető cikkben talál. A **Marketplace díjai** területen válassza a **Letöltés** lehetőséget. A jelentés nem tartalmazza az adókat és az egyszeri vásárlásokat.  

![Képernyőfelvétel a Piactéri díjak területen látható letöltési lehetőségről.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számlázott díjak áttekintése
> * A szolgáltatással kapcsolatos költségek áttekintése
> * Marketplace-számla áttekintése

Folytassa a következő cikkel, hogy többet tudjon meg az Azure EA Portal használatával.

> [!div class="nextstepaction"]
> [Ismerkedés az Azure EA Portallal](../manage/ea-portal-get-started.md)
