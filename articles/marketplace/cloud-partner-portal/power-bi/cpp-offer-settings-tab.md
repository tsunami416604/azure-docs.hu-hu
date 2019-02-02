---
title: Ajánlat az ajánlat beállításait egy Power BI-alkalmazás – Azure Marketplace-en |} A Microsoft Docs
description: A Microsoft AppSource Marketplace egy Power BI alkalmazás ajánlat az ajánlat beállításainak konfigurálása.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666785"
---
# <a name="power-bi-apps-offer-settings-tab"></a>A Power BI alkalmazások ajánlat beállítások lap

A **új ajánlat** szolgáltatásalkalmazások megnyílik az első lapon nevű **ajánlat beállítások**.  Az elsődleges azonosítókat és az ezen a lapon az ajánlat nevét adja meg lesz.  A hozzáfűzött csillag (*) az a mező nevét jelzi, hogy szükséges.

![Az ajánlat (beállítások) lapján](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Az ajánlat beállításainak mezők 

Az a **ajánlat beállítások** lapon meg kell adnia a következő kötelező mezőket.

|  Mező        |  Leírás                                                               |
|---------------|----------------------------------------------------------------------------|
| **Ajánlat azonosítója**  | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító meg fognak jelenni a termék URL-címek, Resource Manager-sablonok, és a számlázás jelentések. Azt a maximális hossza legfeljebb 50 karakter hosszúságú, csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) áll lehet, de nem végződhet kötőjellel. Ez a mező nem módosítható, miután ajánlat élesíti. Például, ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója `sample-SvcApp`, az appsource-on URL-cím van hozzárendelve `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Közzétevő** | A szervezet egyedi azonosítóvá [AppSource](https://appsource.microsoft.com). Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, miután a rendszer menti az ajánlatot.                         |
| **Name (Név)**      | Az ajánlat megjelenített neve. Az appsource-ban, és a Cloud Partner portálra fog megjelenni. Legfeljebb 50 karakterből állhat. Itt útmutatást, hogy a termék felismerhető márkanév tartalmazza. Itt a szervezet neve ne tartalmazza, kivéve, ha ezt az alkalmazást hogyan kereskedelmi forgalomba. Ha ez az ajánlat egyéb webhelyeken és kiadványok vannak marketing, ellenőrizze, hogy a neve azonos között az összes olyan.    <br/>Ha felold egy ajánlatot az előzetes verzió időszakában a Power BI alkalmazások, az előnézeti módot, Hozzáfűzés karakterlánc `(Preview)` , például az alkalmazás neve `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>További lépések

A következő lapon megadásához [technikai információ](./cpp-technical-info-tab.md) az ajánlatban.
