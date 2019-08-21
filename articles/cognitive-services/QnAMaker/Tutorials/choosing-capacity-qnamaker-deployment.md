---
title: Üzembe helyezés – QnA Maker erőforrás-kapacitást
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatást hoz létre, mielőtt, meg kell határoznia, melyik szint a fenti szolgáltatások az Ön számára megfelelő.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b2879a1ebcf01a0433873e8da77c4ce55a078cd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647014"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>A QnA Maker telepítés kapacitás kiválasztása

A QnA Maker szolgáltatást függőséget vesz fel három Azure-erőforrások:
1.  App Service (a a modul)
2.  Azure Search (QnAs tárolásához és kereséséhez)
3.  Az App Insights (nem kötelező, csevegési naplók és a telemetriai adatok tárolása)

A QnA Maker szolgáltatást hoz létre, mielőtt, meg kell határoznia, melyik szint a fenti szolgáltatások az Ön számára megfelelő. 

Nincsenek általában három olyan paraméterek, figyelembe kell vennie:

1. **A szolgáltatás által igényelt átviteli sebesség**: Az igényeinek megfelelően válassza ki az App Service-hez megfelelő alkalmazáscsomag- [csomagot](https://azure.microsoft.com/pricing/details/app-service/plans/) . Is [vertikális felskálázás](https://docs.microsoft.com/azure/app-service/manage-scale-up) vagy le az alkalmazást. Ez is befolyásolja-e az Azure Search termékváltozat, további részletekért lásd: [Itt](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **A Tudásbázis mérete és száma**: Válassza ki a megfelelő [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) -t a forgatókönyvhöz. Egy adott szintre, ahol N az indexek maximális száma a csomagban engedélyezett N-1 tudásbázisok teheti közzé. A maximális méret és engedélyezett / csomag dokumentumok számát is ellenőrizheti.

    Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A tizenötödik index a szerzői műveletek és a tesztelés összes tudásbázisához használatos. 

1. **Dokumentumok száma forrásként**: A QnA Maker felügyeleti szolgáltatás ingyenes SKU-jának korlátozása a Portálon és az API-kon keresztül felügyelhető dokumentumok számát (egyenként 1 MB-os méret). A standard Termékváltozat már kezelheti a dokumentumok száma nincs korlátozva. További részletek [Itt](https://aka.ms/qnamaker-pricing).

Az alábbi táblázat néhány általános útmutatást biztosít.

|                        | A QnA Maker kezelése | App Service | Azure Search | Korlátozások                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Kísérletezés        | Ingyenes Termékváltozat             | Ingyenes szint   | Ingyenes szint    | Legfeljebb 2 Tudásbázis, 50 MB-os méret közzététele  |
| Fejlesztési-tesztelési környezet   | Standard termékváltozat         | Közös      | Alapszintű        | Akár 14 Tudásbázis, 2 GB méretű közzététele    |
| Éles környezetben | Standard termékváltozat         | Alapszintű       | Standard     | Akár 49 Tudásbázis, 25 GB méretű közzététele |

A QnA Maker stack frissítése, lásd: [frissítse a QnA Maker szolgáltatást](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker szolgáltatás frissítése](../How-To/upgrade-qnamaker-service.md)
