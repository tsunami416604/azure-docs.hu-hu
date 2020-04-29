---
title: Ajánlati beállítások a Power BI app ajánlathoz | Azure piactér
description: Adja meg az ajánlat beállításait egy Power BI alkalmazás-ajánlathoz a Microsoft AppSource Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143031"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI alkalmazások ajánlat beállításai lap

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a Power BI alkalmazás-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [Power bi alkalmazás-létrehozás áttekintése című](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) témakör utasításait.

Amikor megnyitja az **új ajánlat** lapot a Service apps szolgáltatáshoz, először az **ajánlat beállításai** lapot tekintheti meg. Ezen a lapon megadhatja az ajánlat elsődleges azonosítóit és nevét. Egy csillag (*) egy kötelező mezőt jelöl.

![Ajánlatbeállítási lap](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Ajánlat beállításai mezők 

Az **ajánlat beállításai** lapon meg kell adnia az adatokat a következő kötelező mezőkben. A kötelező mezőket csillag (*) alapján vádoljuk.

|  Mező        |  Leírás                                                               |
|---------------|----------------------------------------------------------------------------|
| **Ajánlat azonosítója\***  | Az ajánlathoz tartozó egyedi azonosító (közzétevői profilon belül). Ez az azonosító a termék URL-címeiben, Azure Resource Manager sablonokban és számlázási jelentésekben jelenik meg. A maximális hossz 50 karakter. Csak kisbetűs alfanumerikus karaktereket és kötőjelet (-) tartalmazhat. Nem végződhet kötőjeltel. Ez az azonosító nem módosítható, ha egy ajánlat élőben lép fel. Ha a contoso ajánlatot tesz közzé az ajánlat- `sample-SvcApp`azonosítóval, az ajánlat hozzá lesz rendelve `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`a AppSource URL-címhez.      |
| **Publisher\*** | A szervezet egyedi azonosítója a [AppSource](https://appsource.microsoft.com)-ben. Az összes ajánlathoz társítva kell lennie a közzétevő AZONOSÍTÓjának. Ez az érték nem módosítható az ajánlat mentése után.                         |
| **Name (Név)\***      | Az ajánlat megjelenítendő neve. Ez a név fog megjelenni a AppSource és a Cloud Partner Portal. A maximális hossz 50 karakter. Használjon a termékhez felismerhető márkanevet. Itt ne adja meg a szervezet nevét, kivéve, ha az alkalmazást az adott névvel forgalmazzák. Ha ezt az ajánlatot más webhelyekre és kiadványokra is megadja, használja ugyanazt a nevet az összes kiadványban.    <br/>Ha a Power BI-alkalmazások előzetes verziójának időtartama alatt ad ki ajánlatot, adja hozzá `(Preview)` az alkalmazás nevének végén található karakterláncot, a következőhöz hasonlóan `Sample Scv App (Preview)`:. |
|     |     |


## <a name="next-steps"></a>További lépések

A következő lapon megadhatja az ajánlathoz tartozó [műszaki adatokat](./cpp-technical-info-tab.md) .
