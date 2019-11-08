---
title: Ajánlati beállítások a Power BI app ajánlathoz | Azure piactér
description: Adja meg az ajánlat beállításait egy Power BI alkalmazás-ajánlathoz a Microsoft AppSource Marketplace-en.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822681"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI alkalmazások ajánlat beállításai lap

Amikor megnyitja az **új ajánlat** lapot a Service apps szolgáltatáshoz, először az **ajánlat beállításai** lapot tekintheti meg. Ezen a lapon megadhatja az ajánlat elsődleges azonosítóit és nevét. Egy csillag (*) egy kötelező mezőt jelöl.

![Ajánlatbeállítási lap](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Ajánlat beállításai mezők 

Az **ajánlat beállításai** lapon meg kell adnia az adatokat a következő kötelező mezőkben. A kötelező mezőket csillag (*) alapján vádoljuk.

|  Mező        |  Leírás                                                               |
|---------------|----------------------------------------------------------------------------|
| **Ajánlat azonosítója\***  | Az ajánlathoz tartozó egyedi azonosító (közzétevői profilon belül). Ez az azonosító a termék URL-címeiben, Azure Resource Manager sablonokban és számlázási jelentésekben jelenik meg. A maximális hossz 50 karakter. Csak kisbetűs alfanumerikus karaktereket és kötőjelet (-) tartalmazhat. Nem végződhet kötőjeltel. Ez az azonosító nem módosítható, ha egy ajánlat élőben lép fel. Ha a contoso `sample-SvcApp`ajánlat-AZONOSÍTÓval rendelkező ajánlatot tesz közzé, az ajánlat a AppSource URL-címét rendeli hozzá `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`hoz.      |
| **Közzétevő\*** | A szervezet egyedi azonosítója a [AppSource](https://appsource.microsoft.com)-ben. Az összes ajánlathoz társítva kell lennie a közzétevő AZONOSÍTÓjának. Ez az érték nem módosítható az ajánlat mentése után.                         |
| **Név\***      | Az ajánlat megjelenítendő neve. Ez a név fog megjelenni a AppSource és a Cloud Partner Portal. A maximális hossz 50 karakter. Használjon a termékhez felismerhető márkanevet. Itt ne adja meg a szervezet nevét, kivéve, ha az alkalmazást az adott névvel forgalmazzák. Ha ezt az ajánlatot más webhelyekre és kiadványokra is megadja, használja ugyanazt a nevet az összes kiadványban.    <br/>Ha Power BI alkalmazások esetében az előzetes verzió ideje alatt ad ki ajánlatot, adja hozzá az alkalmazás nevének végén `(Preview)` karakterláncot, a következőhöz hasonlóan: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>További lépések

A következő lapon megadhatja az ajánlathoz tartozó [műszaki adatokat](./cpp-technical-info-tab.md) .
