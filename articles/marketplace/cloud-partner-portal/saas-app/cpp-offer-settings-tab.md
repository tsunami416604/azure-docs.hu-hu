---
title: Azure SaaS-alkalmazás ajánlat beállításai |} Az Azure Marketplace-en
description: SaaS-alkalmazás ajánlat az Azure Marketplace-en ajánlat beállításainak konfigurálása.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6903226ecfe1478b340e390c783c4e57af778f3e
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943520"
---
# <a name="saas-application-offer-settings-tab"></a>SaaS-alkalmazás ajánlat beállítások lap

Ez a cikk azt ismerteti, hogy az ajánlat az ajánlat beállításainak konfigurálása.

A **SaaS-alkalmazás > Új ajánlat** oldal megnyílik az aktuális az a **ajánlat beállítások** fülre. 

Az ajánlat beállítások lap segítségével konfigurálhatja a **ajánlat identitás**, a következő képernyőfelvételen látható módon. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges.

![Ajánlatbeállítási lap](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>Az ajánlat nastavení Identity

Ajánlat identitás alatt meg kell adnia a mezők kitöltése a következő táblázat ismerteti. Kötelező mezőt csillag (*) rendszer feltehetően.

|    Mező neve      |    Leírás    |
|  ---------------   |  ---------------  |
|  **Ajánlat azonosítója\***    |  Közzétevő-profilon belül az ajánlat egyedi azonosítója. Ez az azonosító termék URL-címek és a számlázási jelentésekben látható lesz. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és korlátozva, legfeljebb 50 karakter hosszú lehet. Vegye figyelembe, hogy ez a mező után egy ajánlatot élesíti zárolva van. Például ha egy kiadó, a Contoso, ajánlat azonosítója minta virtuális gép egy ajánlatot tesz közzé, azt fog megjelenni, az Azure piactéren: https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview.                 |
|  **Közzétevő azonosítója\***    |  A Gyártóazonosítóval a Marketplace-en az egyedi azonosítója. Az ajánlatok kell lennie az összes csatlakoztatott a közzétevő-azonosítót. Az ajánlat mentése után nem lehet módosítani a a Gyártóazonosítóval...                |
|  **Név\***      |   Ez az ajánlat megjelenített neve. Ez a név fog megjelenni, az Azure Marketplace-en és az Azure Portalon. Legfeljebb 50 karakterből állhat. A termék számára egy felismerhető márkanevet adjon meg. Itt a vállalat neve ne tartalmazza, hacsak nem értékesített hogyan. Ha ez az ajánlat a saját webhelyen található már termékmarketinggel, ellenőrizze, hogy a név pontosan hogyan megjelenik a webhelyen.               |
|  |  |

Válassza ki **mentése** a folyamat mentéséhez.

## <a name="next-steps"></a>További lépések

[Technikai információs lap](./cpp-technical-info-tab.md)
