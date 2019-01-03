---
title: Az Azure és Dynamics 365 tanácsadási szolgáltatási ajánlat - definiálása ajánlat beállításai |} A Microsoft Docs
description: Útmutató definiálása kínálnak a Cloud Partner portálra az Azure-ban vagy a Dynamics 365 tanácsadási szolgáltatási ajánlat beállításait.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cb52aea56e9c1183af9ca76201ab65c62a58f22
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972171"
---
# <a name="offer-settings-tab"></a>Az ajánlat (beállítások) lapján

Az a **új ajánlat** képernyő, az első lépése, hogy az ajánlat identitás létrehozása. Az ajánlat identitás három részből áll: **Ajánlat azonosítója**, **közzétevő-azonosító**, és **neve**. A következő szakaszok ezeket a részeket mindegyike foglalkozik.

![Egy új tanácsadási szolgáltatási ajánlat létrehozása - ajánlat (beállítások) lapján](media/consultingoffer-settings-tab.png)

*Ajánlat azonosítója*

Ezt az azonosítót hoz létre, amikor először küld az ajánlat egyedi neve. Csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak, kell állnia. A **Ajánlatazonosító** meg fognak jelenni az URL-címet, és hatások keresési összetevő motorjának eredménye. Ha például *yourcompanyname_exampleservice*

A példában látható módon a **ajánlat azonosítója** lekérdezi hozzáfűzi a Gyártóazonosítóval hozhat létre egy egyedi azonosítója. Ez lehet lefoglalt és indexelik a keresőprogramok állandó hivatkozásként van közzétéve.

*Miután egy ajánlatot az élő, annak azonosítója nem lehet frissíteni.*

*Közzétevő azonosítója*

Ez az azonosító van a fiókjával kapcsolatban. Ha a munkahelyi fiókjával jelentkezzen be a **közzétevő** azonosítója megjelenik a legördülő menüből.

*Name (Név)*

Ez a karakterlánc, mi az appsource-on vagy az Azure Marketplace-en ajánlat nevét jeleníti meg. A *neve* mező kitöltése legfeljebb 50 karakter hosszúságú lehet.  A felülvizsgáló szükség lehet, hogy a Hozzáfűzés időtartamát az ajánlat nevét a cím szerkesztése és a csomag típusa.

Edgewater Fullscope szerint a következő példa bemutatja, hogyan van-e összeállítva ajánlat nevét. Az ajánlat neve jelenik meg:

![Egy új, tanácsadási szolgáltatási ajánlat létrehozása](media/cppsampleconsultingoffer.png)

Az ajánlat neve négy részből áll:

-   **Időtartam:** – az a **kirakat részletek** a szerkesztő lapján. Időtartam órában, napban vagy hétben a jelöl.
-   **Szolgáltatás típusa:** – meghatározva a **kirakat részletek** a szerkesztő lapján. A szolgáltatások típusok a következők `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, és `Workshop`.
-   **Preposition:** – a véleményező beszúrva
-   **Név:** – az a **ajánlat beállítások** lap.

>[!Note]
>A név mező kitöltése legfeljebb 50 karakter hosszúságú lehet. A neve, küld el kell szerkesztheti a felülvizsgáló idejére engedélyezéséhez és ajánlattípus hozzáfűzni kívánt nevét.

Az alábbi lista tartalmazza a több jól elnevezett ajánlat neve:

-   Professzionális szolgáltatások alapok: 1 – hr Briefing
-   Felhőplatform-áttelepítés: 1 – hr Briefing
-   A PowerApps és Microsoft Flow: 1 napos Workshop
-   Az Azure Machine Learning-szolgáltatások: 3 – hét PoC
-   A tégla, majd kattintson a kiskereskedelmi megoldást: 1 – hr Briefing
-   Saját adatok importálása: 1 – hét Workshop
-   Felhőbeli elemzési: 3 napos Workshop
-   A Power BI képzés: 3 napos Workshop
-   Értékesítési megoldásról: 1 – hét végrehajtása
-   CRM rövid útmutató: 1 napos Workshop
-   Dynamics 365 for Sales: Értékelés 2 nap

Befejezése után a **ajánlat beállítások** lapon mentheti a beküldött elemet. Az ajánlat neve megjelenik a szerkesztő felett, és visszaállítja az összes kínál található.

**Következő lépések**

Most már megadhat [kirakat részleteit, és határozza meg, hogy tegye közzé az Azure Marketplace-en vagy az appsource-ban](./cpp-consulting-service-storefront-details.md).
