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
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231937"
---
# <a name="offer-settings-tab"></a>Az ajánlat (beállítások) lapján

Az a **új ajánlat** képernyő, az első lépése, hogy az ajánlat identitás létrehozása. Az ajánlat identitás három részből áll: **Ajánlat azonosítója**, **közzétevő-azonosító**, és **neve**. A következő szakaszok ezeket a részeket mindegyike foglalkozik.

![Hozzon létre egy új, tanácsadási szolgáltatási ajánlat - ajánlat (beállítások) lapján](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>Ajánlat azonosítója

Ezt az azonosítót hoz létre, amikor először küld az ajánlat egyedi neve. Csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak, kell állnia. A **Ajánlatazonosító** látható az URL-címet, és hatással van a keresőmotorok találatai között. Például *yourcompanyname_exampleservice*.

A példában látható módon a **Ajánlatazonosító** rendszer hozzáfűzi a Gyártóazonosítóval hozhat létre egy egyedi azonosítója. Ez az egyedi azonosító, amely képes terhére és indexelik a keresőprogramok állandó kapcsolat üzemállapottal érhető el.

>[!Note]
>Miután egy ajánlatot az élő, annak azonosítója nem lehet frissíteni.

### <a name="publisher-id"></a>Közzétevő azonosítója

Ez az azonosító van a fiókjával kapcsolatban. Miután a szervezeti fiókkal jelentkezik be a **közzétevő-azonosító** megjelenik-e a legördülő menüből.

### <a name="name"></a>Name (Név)

Ez a karakterlánc az appsource-on vagy az Azure piactéren az ajánlat nevét jeleníti meg. A **neve** mező el legfeljebb 50 karakter hosszúságú lehet. A felülvizsgáló szükség lehet a cím fűzze hozzá a időtartama és írja be az ajánlat neve a szerkesztése.

Az alábbi példa bemutatja, hogyan van-e összeállítva ajánlat nevét. 

![Hozzon létre egy új, tanácsadási szolgáltatási ajánlat](media/cppsampleconsultingoffer.png)

Az ajánlat neve négy részből áll:

-   **Időtartam:** A meghatározott a **kirakat részletek** a szerkesztő lapján. Időtartam órában, napban vagy hétben a jelöl.
-   **Szolgáltatás típusa:** A meghatározott a **kirakat részletek** a szerkesztő lapján. A szolgáltatások típusok a következők `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, és `Workshop`.
-   **Preposition:** Szúrja be a felülvizsgáló.
-   **név:** A meghatározott a **ajánlat beállítások** lapot.

>[!Note]
>A **neve** mező el legfeljebb 50 karakter hosszúságú lehet. A felülvizsgáló szükség lehet a cím fűzze hozzá a időtartama és írja be az ajánlat neve a szerkesztése.

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

Után töltse ki a **ajánlat beállítások** lapon mentse a beküldött elemet. Az ajánlat neve megjelenik a szerkesztő felett, és annak a **összes kínál**.

## <a name="next-steps"></a>További lépések

Most már megadhat [kirakat részleteit, és határozza meg, hogy tegye közzé az Azure Marketplace-en vagy az appsource-on](./cpp-consulting-service-storefront-details.md).
