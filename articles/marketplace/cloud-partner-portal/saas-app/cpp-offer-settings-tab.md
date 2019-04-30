---
title: Azure SaaS-alkalmazás ajánlat beállításai |} A Microsoft Docs
description: SaaS-alkalmazás ajánlat az Azure Marketplace-en ajánlat beállításainak konfigurálása.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 8b8810d3dc899a87b99422c093b6901ed9683325
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101132"
---
# <a name="saas-application-offer-settings-tab"></a>SaaS-alkalmazás ajánlat beállítások lap

Ez a cikk azt ismerteti, hogy az ajánlat az ajánlat beállításainak konfigurálása.

A **SaaS-alkalmazás > Új ajánlat** oldal megnyílik az aktuális az a **ajánlat beállítások** fülre. 

Az ajánlat beállítások lap segítségével konfigurálhatja a **ajánlat identitás**, a következő képernyőfelvételen látható módon. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges.

![Ajánlatbeállítási lap](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>Az ajánlat nastavení Identity

Ajánlat identitás alatt meg kell adnia a mezők kitöltése a következő táblázat ismerteti. 


|  **Mező neve**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    Ajánlat azonosítója  |  Közzétevő-profilon belül az ajánlat egyedi azonosítója. Ez az azonosító termék URL-címek és a számlázási jelentésekben látható lesz. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és korlátozva, legfeljebb 50 karakter hosszú lehet. Vegye figyelembe, hogy ez a mező után egy ajánlatot élesíti zárolva van. Például ha egy kiadó, a Contoso, ajánlat azonosítója minta virtuális gép egy ajánlatot tesz közzé, azt fog megjelenni, az Azure piactéren: https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview.                 |
|  Közzétevő azonosítója    |  A Gyártóazonosítóval a Marketplace-en az egyedi azonosítója. Az ajánlatok kell lennie az összes csatlakoztatott a közzétevő-azonosítót. Az ajánlat mentése után nem lehet módosítani a a Gyártóazonosítóval...                |
|  Name (Név)      |   Ez az ajánlat megjelenített neve. Ez a név fog megjelenni, az Azure Marketplace-en és az Azure Portalon. Legfeljebb 50 karakterből állhat. A termék számára egy felismerhető márkanevet adjon meg. Itt a vállalat neve ne tartalmazza, hacsak nem értékesített hogyan. Ha ez az ajánlat a saját webhelyen található már termékmarketinggel, ellenőrizze, hogy a név pontosan hogyan megjelenik a webhelyen.               |

Válassza ki **mentése** a folyamat mentéséhez.

## <a name="next-steps"></a>További lépések

[Technikai információs lap](./cpp-technical-info-tab.md)
