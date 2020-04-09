---
title: A Power BI Alkalmazásajánlat ajánlatbeállításai | Azure Piactér
description: Konfigurálja a Microsoft AppSource piactér Hez a Power BI Alkalmazásajánlat ajánlatbeállításait.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 1bf8abb2d8ac6c1c9d2e03ef5e4f1edce530cbdb
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985762"
---
# <a name="power-bi-apps-offer-settings-tab"></a>A Power BI-alkalmazások ajánlatbeállításai lap

>[!Important]
>2020. április 13-tól megkezdjük a Power BI-alkalmazásajánlatok felügyeletének áthelyezését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse a [Power BI-alkalmazások létrehozásának áttekintése](https://aka.ms/AzureCreatePBIServiceApp) című témakör utasításait.

Amikor megnyitja a szolgáltatásalkalmazások **Új ajánlat** lapját, először megjelenik az **Ajánlat beállításai** lap. Ezen a lapon adja meg az ajánlat elsődleges azonosítóit és nevét. A csillag (*) egy kötelező mezőt jelöl.

![Ajánlatbeállítási lap](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Ajánlatbeállításai mezők 

Az **Ajánlat beállításai** lapon a következő kötelező mezőkben kell megadnia az adatokat. A kötelező mezőket csillag (*) vádolja.

|  Mező        |  Leírás                                                               |
|---------------|----------------------------------------------------------------------------|
| **Ajánlat azonosítója\***  | Az ajánlat egyedi azonosítója (közzétevői profilon belül). Ez az azonosító látható lesz a termék URL-címek, az Azure Resource Manager-sablonok és a számlázási jelentésekben. A maximális hossz 50 karakter. Csak kisalfanumerikus karaktereket és kötőjeleket (-) tartalmazhat. Nem végződhet kötőjellel. Ez az azonosító nem módosítható, miután az ajánlat éles. Ha a Contoso ajánlatazonosítóval `sample-SvcApp`tesz közzé ajánlatot, az `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`ajánlat az AppSource URL-címéhez van rendelve.      |
| **Publisher\*** | A szervezet egyedi azonosítója az [AppSource alkalmazásban.](https://appsource.microsoft.com) Minden ajánlatának a közzétevő azonosítójához kell kapcsolódnia. Ez az érték nem módosítható az ajánlat mentése után.                         |
| **név\***      | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az AppSource-on és a Cloud Partner Portalon. A maximális hossz 50 karakter. Használjon olyan márkanevet, amely felismerhető a termékén. Ne adja meg a szervezet nevét itt, kivéve, ha az alkalmazás ezzel a névvel van forgalmazva. Ha ezt az ajánlatot más webhelyeken és kiadványokban nyújtja, minden kiadványban ugyanazt a nevet használja.    <br/>Ha a Power BI-alkalmazások előzetes verzióiban ad ki egy ajánlatot, adja hozzá a karakterláncot `(Preview)` az alkalmazás nevének végén, a következőket: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>További lépések

A következő lapon meg kell adnia az ajánlat [technikai adatait.](./cpp-technical-info-tab.md)
