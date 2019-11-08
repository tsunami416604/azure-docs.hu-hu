---
title: Ajánlati beállítások megadása egy tanácsadói szolgáltatási ajánlathoz | Azure piactér
description: Adja meg az ajánlat beállításait egy Azure-vagy Dynamics 365-tanácsadói szolgáltatásban az Azure Marketplace Cloud Partner Portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: e505f9aa1ec08708b85176d5b05fc5b40ceb295e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818130"
---
# <a name="offer-settings-tab"></a>Ajánlatbeállítási lap

Az **új ajánlat** képernyőn az első lépés az ajánlat identitásának létrehozása. Az ajánlat identitása három részből áll: az **ajánlat azonosítója**, a **közzétevő azonosítója**és a **név**. Ezeket a részeket az alábbi részek tartalmazzák.

![Új tanácsadói szolgáltatás ajánlatának létrehozása – ajánlati beállítások lap](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Ajánlat azonosítója *

Ez az azonosító egy egyedi név, amelyet akkor hozunk létre, amikor először küldi el az ajánlatot. Csak kisbetűkből álló alfanumerikus karaktereket, kötőjeleket vagy aláhúzásokat tartalmazhat. Az **ajánlat azonosítója** látható az URL-címben, és hatással van a keresőmotor eredményeire. Példa *yourcompanyname_exampleservice*.

Ahogy az a példában is látható, az **ajánlat azonosítóját** a rendszer hozzáfűzi a KIADÓi azonosítóhoz egy egyedi azonosító létrehozásához. Ez az egyedi azonosító olyan állandó hivatkozásként érhető el, amely a keresőmotoroknak van lefoglalva és indexelve.

>[!Note]
>Az ajánlat élőben való használata után az azonosító nem frissíthető.


### <a name="publisher-id"></a>Közzétevő azonosítója *

Ez az azonosító a fiókhoz kapcsolódik. Miután bejelentkezett a szervezeti fiókjával, a **KÖZZÉTEVŐ azonosítója** megjelenik a legördülő menüben.


### <a name="name"></a>Neve

Ez a karakterlánc a AppSource vagy az Azure Marketplace-ajánlat neveként jelenik meg. A **név** mező legfeljebb 50 karakter hosszú lehet. Előfordulhat, hogy a véleményezőnek szerkesztenie kell a címét az időtartam és az ajánlat típusának hozzáfűzéséhez az ajánlat nevéhez.

Az alábbi példa azt szemlélteti, hogyan történik az ajánlat nevének összegyűjtése. 

![Új tanácsadói szolgáltatási ajánlat létrehozása](media/cppsampleconsultingoffer.png)

Az ajánlat neve négy részből áll:

-   **Időtartam:** A szerkesztő **kirakat részletei** lapján adható meg. Az időtartamot óra, nap vagy hét értékben lehet megadni.
-   **Szolgáltatás típusa:** A szerkesztő **kirakat részletei** lapján adható meg. A szolgáltatások típusai a következők: `Assessment`, `Briefing`, `Implementation`, `Proof of concept`és `Workshop`.
-   **Alaphelyzet:** A felülvizsgáló beszúrta.
-   **Név:** Az **ajánlat beállításai** lapon van meghatározva.

>[!Note]
>A **név** mező legfeljebb 50 karakter hosszú lehet. Előfordulhat, hogy a véleményezőnek szerkesztenie kell a címét az időtartam és az ajánlat típusának hozzáfűzéséhez az ajánlat nevéhez.

A következő lista számos jól megnevezett ajánlatot tartalmaz:

-   Professional Services Essentials: 1 – HR-Összefoglaló
-   Cloud Migration platform: 1 – HR-Összefoglaló
-   PowerApps és Microsoft Flow: 1 napos workshop
-   Azure Machine Learning: 3 – hét PoC
-   Tégla és kattintson a kiskereskedelmi megoldás: 1 – HR-Összefoglaló
-   Saját adatai: 1 – hét workshop
-   Cloud Analytics: 3 napos workshop
-   Power BI képzés: 3 napos workshop
-   Értékesítési felügyeleti megoldás: 1 hetes megvalósítás
-   CRM rövid útmutató: 1 napos workshop
-   Dynamics 365 for Sales: 2 napos Értékelés

Miután kitöltötte az **ajánlat beállításai** lapot, mentse a beküldött adatokat. Az ajánlat neve most a szerkesztő felett jelenik meg, és **minden ajánlatban**megtalálható.

## <a name="next-steps"></a>További lépések

Most megadhatja [a kirakat adatait, és meghatározhatja, hogy közzé kell-e tenni az Azure Marketplace-en vagy a AppSource](./cpp-consulting-service-storefront-details.md).
