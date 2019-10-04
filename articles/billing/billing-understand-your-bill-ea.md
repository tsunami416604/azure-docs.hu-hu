---
title: Az Azure nagyvállalati elszámolásainak ismertetése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők az Azure Nagyvállalati Szerződésekhez tartozó használati adatok és a hozzájuk tartozó számlák.
author: adpick
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 02ddaf4878367524291ce54a4e12fc9b4fee4cd2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490287"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Az Azure Nagyvállalati Szerződés számlájának ismertetése

A Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek akkor kapnak számlát, amikor túllépik a vállalat kreditkeretét, vagy olyan szolgáltatást használnak, amelyet nem fedez a kredit.

A szervezet kreditjébe beleszámít a pénzügyi kötelezettségvállalás is. A pénzügyi Kötelezettségvállalás az az összeg, amelyet a szervezet előre befizet az Azure-szolgáltatások használatára. Amennyiben pénzügyi kötelezettségvállalási befizetéseket kíván hozzáadni a Nagyvállalati Szerződéséhez, lépjen kapcsolatba a Microsoft ügyfélkapcsolati munkatársával vagy viszonteladójával.  

## <a name="invoices-for-most-customers"></a>Számlák a legtöbb ügyfél esetén

Az alábbi szakasz nem vonatkozik az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre. Ha a fenti országok vagy régiók egyikében működik a vállalkozása, tekintse meg a [Számlák egyéb ügyfelek esetén](#invoices-for-other-customers) című szakaszt.

Akkor kap Azure-számlát, ha sor kerül a következők egyikére a számlázási ciklus folyamán:

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

## <a name="review-charges-for-most-customers"></a>Díjak áttekintése a legtöbb ügyfél esetén
Az alábbi szakasz nem vonatkozik az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre. Ha a fenti országok egyikében működik a vállalkozása, tekintse meg a [Díjak áttekintése egyéb ügyfelek esetén](#review-charges-for-other-customers) című szakaszt.

A számlán szereplő díjak áttekintésére és ellenőrzésére a vállalati rendszergazdáknak van lehetőségük. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md). Ha nem tudja, hogy ki a szervezet vállalati rendszergazdája, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

A számla feltünteti az összes Azure-használatot és az esetleges Marketplace-beli díjakat. Ha rendelkezik kreditegyenleggel, a rendszer alkalmazza azt az Azure-használatra.

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget az Azure-számlájával. A **Használat összefoglalásának** összegei az adót nem tartalmazzák.

1. Jelentkezzen be az [Enterprise Portalra](https://ea.azure.com).
1. Válassza a **Jelentések** elemet.
1. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  
    ![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. Az **Összes használat** és az **Azure Marketplace** összegének meg kellene egyeznie a számlán szereplő **teljes bővített összeggel**.
1. További részleteket a díjakról a **használati adatok letöltését** ismertető cikkben talál.  
    ![Képernyőkép a használati adatok letöltésére szolgáló lapról](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>Számlák egyéb ügyfelek esetén

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő Azure-ügyfelekre vonatkozik.

A következő esetekben kaphat egy vagy több Azure-számlát:

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
- **A Marketplace díjai**: Az Azure Marketplace-beli vásárlásokat és használatot nem fedezi a vállalati kredit; ezekről külön számla készül. Az Enterprise Portalon egy vállalati rendszergazda engedélyezheti vagy tilthatja le a Marketplace-beli vásárlásokat.

A szolgáltatási keret túllépéséből adódó díjakról és a számlázási időszak során külön számlázott díjakról egyetlen számlát kap. Ez tartalmazza a fenti két típusba tartozó díjakat. A Marketplace-beli díjakról mindig külön számlát kap.

## <a name="review-charges-for-other-customers"></a>Díjak áttekintése egyéb ügyfelek esetén

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

A számlán szereplő díjak áttekintésére és ellenőrzésére a vállalati rendszergazdáknak van lehetőségük. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md). Ha nem tudja, hogy ki a szervezet vállalati rendszergazdája, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Szolgáltatási kerettúllépés számlájának áttekintése

Hasonlítsa össze az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján található teljes összeget a szolgáltatási kerettúllépés számlájával. A szolgáltatási kerettúllépés számlája olyan használatra vonatkozik, amely túllépi a szervezeti keretet, és/vagy a kredit által nem fedezett szolgáltatásokra. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

1. Jelentkezzen be az [Enterprise Portalra](https://ea.azure.com).
1. Válassza a **Jelentések** elemet.
1. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  
    ![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. Az **Összes használat** összegének egyezne kell a szolgáltatási kerettúllépés számláján szereplő **teljes bővített összeggel**.  
1. További információt a díjakról a **Használati adatok letöltése** > **Speciális jelentés letöltése** alatt talál. A jelentés nem tartalmazza az adókat és a foglalási vagy piactéri díjakat.  
      ![Képernyőkép a Használati adatok letöltése lapon található Speciális jelentés letöltése lehetőségről.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

A következő lista számba veszi a számlákon és az Enterprise Portal **Használat összefoglalása** lapon előforduló kifejezéseket és leírásokat:

|Számlázási kifejezés|A Használat összefoglalása lap kifejezései|Leírás|
|---|---|---|
|Teljes bővített összeg|Összes használat|A teljes adók nélküli használati díj az adott időszakban, a kredit alkalmazása előtt.|
|Kötelezettségvállalás felhasználása|Kötelezettségvállalás felhasználása|Az adott időszak során alkalmazott kredit.|
|Összes értékesítés|Kerettúllépés összesen|A teljes, a kredit mennyiségét meghaladó használati díj. Az összeg az adókat nem tartalmazza.|
|Adó összege|Nem alkalmazható|Az adott időszakban keletkező összes értékesítésre vonatkozó adó összege.|
|Teljes összeg|Nem alkalmazható|A számla fizetendő összege a kredit alkalmazása és az adók hozzáadása után.|

### <a name="marketplace-invoice"></a>Marketplace-számla

Az alábbi szakasz kizárólag az Ausztráliában, Japánban vagy Szingapúrban tevékenykedő ügyfelekre vonatkozik.

Hasonlítsa össze az Azure Marketplace-re vonatkozó teljes összeget a piactéri számlájával az Enterprise Portal **Jelentések** > **Használat összefoglalása** lapján. A piactéri számlán csak az Azure Marketplace-beli vásárlások és használat szerepel. A **Használati összefoglalásának** összegei az adót nem tartalmazzák.

1. Jelentkezzen be az [Enterprise Portalra](https://ea.azure.com).
1. Válassza a **Jelentések** elemet.
1. A képernyő jobb felső sarkában módosítsa a nézetet **M**-ről **C**-re, majd válassza ki a számlán szereplő időszakot.  
     ![Képernyőkép az M + C lehetőségről a Használat összefoglalása lapon.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. Az **Azure Marketplace-re** vonatkozó teljes összegnek meg kell egyeznie a piactéri számla **összes értékesítésre** vonatkozó összegével.
1. További információt a használaton alapuló díjakról a **használati adatok letöltését** ismertető cikkben talál. A **Marketplace díjai** területen válassza a **Letöltés** lehetőséget. A jelentés nem tartalmazza az adókat és az egyszeri vásárlásokat.  
     ![Képernyőkép a Marketplace díjai alatt megjelenő letöltési lehetőségről.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Az Azure használati adatainak és díjainak megtekintése és letöltése](billing-download-azure-daily-usage.md)
