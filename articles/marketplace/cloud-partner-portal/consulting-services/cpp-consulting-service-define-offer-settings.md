---
title: Tanácsadói szolgáltatásajánlat ajánlatbeállításainak meghatározása | Azure Piactér
description: Adja meg az ajánlatbeállításokat egy Azure-beli vagy Dynamics 365 tanácsadási szolgáltatási ajánlatban az Azure Marketplace-hez való felhőpartnerportálon.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 7b2c9d136a32e54ce5b5db8f6ce9ba8cd63a4f3c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745647"
---
# <a name="offer-settings-tab"></a>Ajánlatbeállítási lap

>[!Important]
>2020. március 30-tól megkezdjük a Tanácsadási szolgáltatási ajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse a [Consulting service creation overview című](https://aka.ms/AzureCreateConsultingService) témakör utasításait.

Az **Új ajánlat** képernyőn az első lépés az ajánlat identitásának létrehozása. Az ajánlat identitása három részből áll: **Ajánlatazonosító,** **Kiadóazonosító**és **Név**. E részek mindegyikét a következő szakaszok ismertetik.

![Új tanácsadási szolgáltatásajánlat létrehozása – Ajánlatbeállítások lap](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Ajánlatazonosító*

Ez az azonosító egy egyedi név, amelyet az ajánlat első elküldésekor hoz létre. Csak kisalfanumerikus karakterekből, kötőjelekből vagy aláhúzásjelekből állhat. Az **ajánlatazonosító** látható az URL-ben, és hatással van a keresőmotor találatai. Erre példa a *yourcompanyname_exampleservice*.

Amint az a példában látható, az **ajánlatazonosító** hozzáfűzi a közzétevő azonosítóját, hogy hozzon létre egy egyedi azonosítót. Ez az egyedi azonosító állandó kapcsolatként jelenik meg, amelyet a keresőmotorok lefoglalhatnak és indexelhetnek.

>[!Note]
>Miután egy ajánlat él, az azonosító nem frissíthető.


### <a name="publisher-id"></a>Közzétevő azonosítója*

Ez az azonosító a fiókjához kapcsolódik. Miután bejelentkezett a szervezeti fiókjával, a **Publisher-azonosító** megjelenik a legördülő menüben.


### <a name="name"></a>Név*

Ez a karakterlánc ajánlatnévként jelenik meg az AppSource-on vagy az Azure Marketplace-en. A **Név** mező legfeljebb 50 karakter ből állhat. Előfordulhat, hogy a véleményezőnek meg kell adnia a címet, hogy hozzáfűzje az időtartamot és az ajánlat típusát az ajánlat nevéhez.

A következő példa bemutatja, hogyan történik az ajánlat neve. 

![Új tanácsadási szolgáltatásajánlat létrehozása](media/cppsampleconsultingoffer.png)

Az ajánlat neve négy részből áll:

-   **Időtartam:** A szerkesztő **Kirakatrészletei** lapján definiálva. Az időtartam órákban, napokban vagy hetekben fejezhető ki.
-   **A szolgáltatás típusa:** A szerkesztő **Kirakatrészletei** lapján definiálva. A szolgáltatások `Assessment`típusai `Proof of concept`a `Workshop`, `Briefing`, `Implementation`, , és .
-   **Elöljárószó:** A véleményező szúrta be.
-   **Név:** Az Ajánlat **beállításai** lapon definiálva.

>[!Note]
>A **Név** mező legfeljebb 50 karakter ből állhat. Előfordulhat, hogy a véleményezőnek meg kell adnia a címet, hogy hozzáfűzje az időtartamot és az ajánlat típusát az ajánlat nevéhez.

Az alábbi lista számos jól elnevezett ajánlatnevet tartalmaz:

-   Alapvető fontosságú a szakmai szolgáltatások: 1 órás eligazítás
-   Felhőmigrációs platform: 1 órás eligazítás
-   PowerApps és Microsoft Flow: 1 napos workshop
-   Azure Machine Learning: 3-Wk PoC
-   Tégla és kattintson a kiskereskedelmi megoldás: 1 hr-es eligazítás
-   Hozd a saját adatok: 1-Wk Workshop
-   Cloud Analytics: 3 napos workshop
-   Power BI-oktatás: 3 napos workshop
-   Értékesítéskezelési megoldás: 1 hetes megvalósítás
-   CRM rövid útmutató: 1 napos workshop
-   Dynamics 365 for Sales: 2 napos értékelés

Miután kitöltötte az **Ajánlatbeállításai** lapot, mentse el a beküldött anyagot. Az ajánlat neve most a szerkesztő felett jelenik meg, és megtalálja az **Összes ajánlat ban.**

## <a name="next-steps"></a>További lépések

Most megadhatja [a kirakatrészleteit, és meghatározhatja, hogy az Azure Marketplace-en vagy az AppSource-on tegye-e közzé.](./cpp-consulting-service-storefront-details.md)
