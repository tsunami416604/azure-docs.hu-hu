---
title: Az Azure Nagyvállalati Szerződés számlájának áttekintése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők az Azure Nagyvállalati Szerződésekhez tartozó használati adatok és a hozzájuk tartozó számlák.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2020
ms.author: banders
ms.openlocfilehash: 8e674ec1c2d4c47d3b4e56bd788c963c81ea23e9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016204"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Az Azure Nagyvállalati Szerződés számlájának ismertetése

A Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek akkor kapnak számlát, amikor túllépik a vállalat kreditkeretét, vagy olyan szolgáltatást használnak, amelyet nem fedez a kredit.

A szervezet kreditjébe beleszámít a pénzügyi kötelezettségvállalás is. A pénzügyi Kötelezettségvállalás az az összeg, amelyet a szervezet előre befizet az Azure-szolgáltatások használatára. Amennyiben pénzügyi kötelezettségvállalási befizetéseket kíván hozzáadni a Nagyvállalati Szerződéséhez, lépjen kapcsolatba a Microsoft ügyfélkapcsolati munkatársával vagy viszonteladójával.

Ez az oktatóanyag csak azokra az Azure-ügyfelekre vonatkozik, akik Azure Nagyvállalati Szerződéssel rendelkeznek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A számlázott díjtételek áttekintése
> * Szolgáltatási kerettúllépési díjtételek áttekintése
> * A Marketplace-számla áttekintése

## <a name="prerequisites"></a>Előfeltételek

A számlán szereplő díjak áttekintésére és ellenőrzésére a vállalati rendszergazdáknak van lehetőségük. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](../manage/understand-ea-roles.md). Ha nem tudja, hogy ki a szervezet vállalati rendszergazdája, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Számlázott díjtételek áttekintése a legtöbb ügyfél esetén

Az alábbi szakasz nem vonatkozik az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre.

Akkor kap Azure-számlát, ha sor kerül a következő események egyikére a számlázási ciklus folyamán:

- **Szolgáltatási keret túllépése**: A szervezet használati díjai meghaladják a kreditegyenlegét.
- **Külön számlázott tételek**: Olyan szolgáltatások, amelyeket nem fed le a szervezeti kredit. A következő szolgáltatások számlázása mindig külön történik, a szervezet kreditegyenlegétől függetlenül:
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
- **A Marketplace díjai**: Az Azure Marketplace-beli vásárlásokat és használatot nem fedezi a szervezeti kredit. Ezért a Marketplace díjairól mindig számlát kap, a kreditegyenlegétől függetlenül. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A számla feltünteti az összes Azure-használatot és az esetleges Marketplace-beli díjakat. Ha rendelkezik kreditegyenleggel, a rendszer alkalmazza azt az Azure-használatra.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget az Azure-számlájával. A **Használat összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Azure EA Portalra](https://ea.azure.com). Ezután válassza a **Jelentések** lehetőséget. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Az **Összes használat** és az **Azure Marketplace** összegének meg kellene egyeznie a számlán szereplő **teljes bővített összeggel**. További részleteket a díjakról a **használati adatok letöltését** ismertető cikkben talál.  

![Képernyőkép a használati adatok letöltésére szolgáló lapról](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Számlázott díjtételek áttekintése egyéb ügyfelek esetén

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre vonatkozik.

Akkor kap egy vagy több Azure-számlát, ha sor kerül a következő események egyikére:

- **Szolgáltatási keret túllépése**: A szervezet használati díjai meghaladják a kreditegyenlegét.
- **Külön számlázott tételek**: Olyan szolgáltatások, amelyeket nem fed le a szervezeti kredit. A következő szolgáltatásokat számlázzuk ki Önnek:
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
- **A Marketplace díjai**: Az Azure Marketplace-beli vásárlásokat és használatot nem fedezi a vállalati kredit; ezekről külön számla készül. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A szolgáltatási keret túllépéséből adódó díjakról és a számlázási időszak során külön számlázott díjakról egyetlen számlát kap. Ez tartalmazza a fenti két típusba tartozó díjakat. A Marketplace-beli díjakról mindig külön számlát kap.

## <a name="review-service-overage-charges-for-other-customers"></a>Szolgáltatási kerettúllépési díjtételek áttekintése egyéb ügyfelek esetén

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget a szolgáltatási kerettúllépés számlájával. A szolgáltatási kerettúllépés számlája olyan használatra vonatkozik, amely túllépi a szervezeti keretet, és/vagy a kredit által nem fedezett szolgáltatásokra. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Azure EA Portalra](https://ea.azure.com), majd válassza a **Jelentések** lehetőséget. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Az **Összes használat** összegének egyezne kell a szolgáltatási kerettúllépés számláján szereplő **teljes bővített összeggel**. További információt a díjakról a **Használati adatok letöltése** > **Speciális jelentés letöltése** alatt talál. A jelentés nem tartalmazza az adókat és a foglalási vagy piactéri díjakat.  

![Képernyőkép a Használati adatok letöltése lapon található Speciális jelentés letöltése lehetőségről.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

A következő lista számba veszi a számlákon és az Enterprise Portal **Használat összefoglalása** lapon előforduló kifejezéseket és leírásokat:

|Számlázási kifejezés|A Használat összefoglalása lap kifejezései|Leírás|
|---|---|---|
|Teljes bővített összeg|Összes használat|A teljes adók nélküli használati díj az adott időszakban, a kredit alkalmazása előtt.|
|Kötelezettségvállalás felhasználása|Kötelezettségvállalás felhasználása|Az adott időszak során alkalmazott kredit.|
|Összes értékesítés|Kerettúllépés összesen|A teljes, a kredit mennyiségét meghaladó használati díj. Az összeg az adókat nem tartalmazza.|
|Adó összege|Nem alkalmazható|Az adott időszakban keletkező összes értékesítésre vonatkozó adó összege.|
|Teljes összeg|Nem alkalmazható|A számla fizetendő összege a kredit alkalmazása és az adók hozzáadása után.|

### <a name="review-marketplace-invoice"></a>A Marketplace-számla áttekintése

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Azure Marketplace-re vonatkozó teljes összeget a piactéri számlájával az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján. A piactéri számlán csak az Azure Marketplace-beli vásárlások és használat szerepel. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

Jelentkezzen be az [Enterprise Portalra](https://ea.azure.com), majd válassza a **Jelentések**lehetőséget. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  

![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Az **Azure Marketplace-re** vonatkozó teljes összegnek meg kell egyeznie a piactéri számla **összes értékesítésre** vonatkozó összegével. További információt a használaton alapuló díjakról a **használati adatok letöltését** ismertető cikkben talál. A **Marketplace díjai** területen válassza a **Letöltés** lehetőséget. A Marketplace-ár magában foglalja a közzétevő által meghatározott adót. Az ügyfelek nem kapnak külön számlát a közzétevőtől a tranzakciós adó beszedéséhez.

![Képernyőkép a Marketplace díjai alatt megjelenő letöltési lehetőségről.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A számlázott díjtételek áttekintése
> * Szolgáltatási kerettúllépési díjtételek áttekintése
> * A Marketplace-számla áttekintése

Folytassa a következő cikkel, ha többet szeretne megtudni az Azure EA Portal használatáról.

> [!div class="nextstepaction"]
> [Ismerkedés az Azure EA Portallal](../manage/ea-portal-get-started.md)
