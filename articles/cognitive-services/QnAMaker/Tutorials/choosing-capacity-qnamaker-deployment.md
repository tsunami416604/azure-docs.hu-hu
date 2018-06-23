---
title: A kérdések és válaszok készítő telepítéshez - Microsoft kognitív szolgáltatások kapacitás kiválasztása |} Microsoft Docs
titleSuffix: Azure
description: az a kérdés-válasz készítő telepítésre vonatkozó kapacitás kiválasztása való
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348659"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Az a kérdés-válasz készítő telepítésre vonatkozó kapacitás kiválasztása

A kérdések és válaszok készítő szolgáltatás függőség hajtja végre a három Azure-erőforrások:
1.  App Service (a a futtatókörnyezet)
2.  Az Azure Search (QnAs tárolásához)
3.  App Insights (nem kötelező, chatlogs és telemetriai adatokat tároló)

A kérdések és válaszok készítő szolgáltatás létrehozása előtt meg kell határoznia, melyik rétegek, a fenti szolgáltatások. 

Általában nincsenek figyelembe kell vennie három paramétert:
1. **A szolgáltatás szükséges az átviteli sebesség**: válassza ki a megfelelő [App terv](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) az App Service igényei szerint. Is [vertikális felskálázás](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) vagy az alkalmazás leállt. Ez is befolyásolja-e az Azure Search Termékváltozattal beállításának, lásd: több részletek [Itt](https://docs.microsoft.com/en-us/azure/search/search-sku-tier).

2. **Méretre és a Tudásbázis alapok**: válassza ki a megfelelő [Azure keresési SKU](https://azure.microsoft.com/en-in/pricing/details/search/) a forgatókönyvéhez. Egy adott réteg, ahol N az engedélyezett, a réteg maximális indexeinek N-1 Tudásbázis körrel teheti közzé. A maximális méret és a dokumentumok száma, engedélyezett-e a réteg is ellenőrizheti.

3. **A dokumentumok források száma**: a kérdések és válaszok készítő felügyeleti szolgáltatás szabad Termékváltozata korlátozza a dokumentumok száma, a portál és az API-k (az 1 MB méretű minden) 3 keresztül kezelheti. A standard Termékváltozat nem határoz meg a dokumentumok száma, kezelheti a rendelkezik. További részletek [Itt](https://aka.ms/qnamaker-pricing).

Az alábbi táblázat néhány általános útmutatást tartalmazza.

|                        | Kérdések és válaszok készítő kezelése | App Service | Azure Search | Korlátozások                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Kísérletezés        | Szabad Termékváltozat             | Ingyenes szint   | Ingyenes szint    | Legfeljebb 2 KB, 50 MB méretű közzététele  |
| Fejlesztési/tesztelési környezetben   | Standard termékváltozat         | Közös      | Alapszintű        | Legfeljebb 4 KB, 2 GB-os méret közzététele    |
| Éles környezetben | Standard termékváltozat         | Alapszintű       | Standard     | Legfeljebb 49 KB, 25 GB méretű közzététele |

A kérdések és válaszok készítő verem frissítése, lásd: [frissítse a kérdések és válaszok készítő szolgáltatást](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A kérdések és válaszok készítő service frissítése](../How-To/upgrade-qnamaker-service.md)
