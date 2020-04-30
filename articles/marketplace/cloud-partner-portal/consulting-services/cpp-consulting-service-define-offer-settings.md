---
title: Ajánlati beállítások megadása egy tanácsadói szolgáltatási ajánlathoz | Azure piactér
description: Adja meg az ajánlat beállításait egy Azure-vagy Dynamics 365-tanácsadói szolgáltatásban az Azure Marketplace Cloud Partner Portal.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 406a37a1ef946b1c3ceb0d7b02ba318f423dcf53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146692"
---
# <a name="offer-settings-tab"></a>Ajánlatbeállítási lap

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a Consulting Service-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse a [tanácsadási szolgáltatás létrehozása – áttekintés című](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-consulting-service-offer) témakör utasításait.

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
-   **Szolgáltatás típusa:** A szerkesztő **kirakat részletei** lapján adható meg. A szolgáltatások `Assessment` `Briefing` `Implementation`típusai:,,, és `Workshop` `Proof of concept`
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
