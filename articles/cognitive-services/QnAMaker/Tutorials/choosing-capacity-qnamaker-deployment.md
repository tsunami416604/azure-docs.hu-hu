---
title: Erőforrás-kapacitás az üzembe helyezéshez – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatás létrehozása előtt el kell döntenie, hogy a fenti szolgáltatások melyik szintje legyen megfelelő az Ön számára.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793950"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>A QnA Maker üzemelő példány kapacitásának kiválasztása

A QnA Maker szolgáltatás három Azure-erőforrás függőségét veszi igénybe:
1.  App Service (a futtatókörnyezethez)
2.  Azure Cognitive Search (QnAs tárolásához és kereséséhez)
3.  Alkalmazás-elemzések (nem kötelező, csevegési naplók és telemetria tárolásához)

A QnA Maker szolgáltatás létrehozása előtt el kell döntenie, hogy a fenti szolgáltatások melyik szintje legyen megfelelő az Ön számára. 

Általában három paramétert kell figyelembe vennie:

1. **A szolgáltatás által igényelt átviteli sebesség**: válassza ki a [megfelelő alkalmazáscsomag az App](https://azure.microsoft.com/pricing/details/app-service/plans/) Service-hez az igényei alapján. Az alkalmazás vertikális fel-és [leskálázásra](https://docs.microsoft.com/azure/app-service/manage-scale-up) is használható. Ez az Azure Cognitive Search SKU kiválasztását is befolyásolhatja, további részletek [itt](https://docs.microsoft.com/azure/search/search-sku-tier)találhatók.

1. A **Tudásbázis mérete és száma**: válassza ki a megfelelő [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) -t a forgatókönyvhöz. Az N-1 tudásbázist egy adott rétegben teheti közzé, ahol N a rétegben engedélyezett maximális indexek. Tekintse meg a maximális méretet és a másodpercenként engedélyezett dokumentumok számát is.

    Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A tizenötödik index a szerzői műveletek és a tesztelés összes tudásbázisához használatos. 

1. **Dokumentumok száma forrásként**: a QnA Maker felügyeleti szolgáltatás ingyenes SKU-jának a Portálon és az API-kon keresztül kezelhető dokumentumok számát korlátozza (egyenként 1 MB méretűre). A standard SKU nem korlátozza a felügyelhető dokumentumok számát. További részletek [itt](https://aka.ms/qnamaker-pricing)találhatók.

A következő táblázat néhány magas szintű útmutatást nyújt.

|                        | QnA Maker-kezelés | App Service | Azure Cognitive Search | Korlátozások                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Kísérletezés        | Ingyenes SKU             | Ingyenes szint   | Ingyenes szint    | Legfeljebb 2 Tudásbázis közzététele, 50 MB méretű  |
| Fejlesztési/tesztelési környezet   | Standard termékváltozat         | Megosztott      | Basic        | Legfeljebb 14 Tudásbázis közzététele, 2 GB méretű méret    |
| Éles környezet | Standard termékváltozat         | Basic       | Standard     | Közzététel akár 49 Tudásbázis, 25 GB méretű |

A QnA Maker stack frissítését lásd: [a QnA Maker szolgáltatás frissítése](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker szolgáltatás frissítése](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
