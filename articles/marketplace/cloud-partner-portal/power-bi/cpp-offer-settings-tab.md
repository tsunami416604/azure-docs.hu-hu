---
title: Ajánlat az ajánlat beállításait egy Power BI-alkalmazás – Azure Marketplace-en |} A Microsoft Docs
description: A Power BI alkalmazás ajánlatot a Microsoft AppSource piactérről az ajánlat beállításainak konfigurálása.
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
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819599"
---
# <a name="power-bi-apps-offer-settings-tab"></a>A Power BI alkalmazások ajánlat beállítások lap

Amikor megnyitja a **új ajánlat** lapon szolgáltatás alkalmazásokhoz, először tekintse meg a **ajánlat beállítások** lapon. Ezen a lapon meg az elsődleges azonosítókat és az ajánlat nevét. Egy csillag (*) azt jelzi, hogy a kötelező kitölteni.

![Az ajánlat (beállítások) lapján](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Az ajánlat beállításainak mezők 

Az a **ajánlat beállítások** lapon meg kell adnia információkat a következő kötelező mezőket:

|  Mező        |  Leírás                                                               |
|---------------|----------------------------------------------------------------------------|
| **Ajánlat azonosítója**  | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító termék URL-címeket, az Azure Resource Manager-sablonok, látható lesz, és számlázási jelentések. A maximális hossz 50 karakter hosszú lehet. Csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) tartalmazhat. Nem végződhet kötőjellel. Ez az azonosító nem módosítható, miután egy ajánlatot élesíti. Ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója `sample-SvcApp`, az ajánlat az appsource-on URL-cím van hozzárendelve `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Közzétevő** | A szervezet egyedi azonosítóvá [AppSource](https://appsource.microsoft.com). Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, az ajánlat mentése után.                         |
| **Name (Név)**      | Az ajánlat megjelenített neve. Ez a név fog megjelenni, az appsource-on és a Cloud Partner portálra. A maximális hossz 50 karakter hosszú lehet. A termék felismerhető márkanév használja. Itt a szervezet neve ne tartalmazza, kivéve, ha az alkalmazás kereskedelmi forgalomba ilyen nevű. Ha ez az ajánlat más webhelyek és kiadványok már megadta, használja ugyanazt a nevet az összes olyan kiadvány esetén.    <br/>Ha felold egy ajánlatot az előzetes verzió ideje alatt a Power BI-alkalmazások, vegye fel a karakterlánc `(Preview)` végén található az alkalmazás nevét, ehhez hasonló: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>További lépések

A következő lapon adja meg [technikai információ](./cpp-technical-info-tab.md) az ajánlatban.
