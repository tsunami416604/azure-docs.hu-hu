---
title: Ajánlat beállításai a Power BI alkalmazás-ajánlat |} Az Azure Marketplace-en
description: A Power BI alkalmazás ajánlatot a Microsoft AppSource piactérről az ajánlat beállításainak konfigurálása.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943460"
---
# <a name="power-bi-apps-offer-settings-tab"></a>A Power BI alkalmazások ajánlat beállítások lap

Amikor megnyitja a **új ajánlat** lapon szolgáltatás alkalmazásokhoz, először tekintse meg a **ajánlat beállítások** lapon. Ezen a lapon meg az elsődleges azonosítókat és az ajánlat nevét. Egy csillag (*) azt jelzi, hogy a kötelező kitölteni.

![Ajánlatbeállítási lap](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Az ajánlat beállításainak mezők 

Az a **ajánlat beállítások** lapon meg kell adnia információkat a következő kötelező mezőket. Kötelező mezőt csillag (*) rendszer feltehetően.

|  Mező        |  Leírás                                                               |
|---------------|----------------------------------------------------------------------------|
| **Ajánlat azonosítója\***  | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító termék URL-címeket, az Azure Resource Manager-sablonok, látható lesz, és számlázási jelentések. A maximális hossz 50 karakter hosszú lehet. Csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) tartalmazhat. Nem végződhet kötőjellel. Ez az azonosító nem módosítható, miután egy ajánlatot élesíti. Ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója `sample-SvcApp`, az ajánlat az appsource-on URL-cím van hozzárendelve `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher\*** | A szervezet egyedi azonosítóvá [AppSource](https://appsource.microsoft.com). Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, az ajánlat mentése után.                         |
| **Név\***      | Az ajánlat megjelenített neve. Ez a név fog megjelenni, az appsource-on és a Cloud Partner portálra. A maximális hossz 50 karakter hosszú lehet. A termék felismerhető márkanév használja. Itt a szervezet neve ne tartalmazza, kivéve, ha az alkalmazás kereskedelmi forgalomba ilyen nevű. Ha ez az ajánlat más webhelyek és kiadványok már megadta, használja ugyanazt a nevet az összes olyan kiadvány esetén.    <br/>Ha felold egy ajánlatot az előzetes verzió ideje alatt a Power BI-alkalmazások, vegye fel a karakterlánc `(Preview)` végén található az alkalmazás nevét, ehhez hasonló: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>További lépések

A következő lapon adja meg [technikai információ](./cpp-technical-info-tab.md) az ajánlatban.
