---
title: Üzembe helyezés – QnA Maker erőforrás-kapacitást
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatást hoz létre, mielőtt, meg kell határoznia, melyik szint a fenti szolgáltatások az Ön számára megfelelő.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a332d263526bb6507e7394c205caa1c4d1f9e3e6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873580"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>A QnA Maker telepítés kapacitás kiválasztása

A QnA Maker szolgáltatást függőséget vesz fel három Azure-erőforrások:
1.  App Service (a a modul)
2.  Az Azure Search (a QnA-tudásbázisok tárolása)
3.  Az App Insights (nem kötelező, csevegési naplók és a telemetriai adatok tárolása)

A QnA Maker szolgáltatást hoz létre, mielőtt, meg kell határoznia, melyik szint a fenti szolgáltatások az Ön számára megfelelő. 

Nincsenek általában három olyan paraméterek, figyelembe kell vennie:

1. **Az átviteli sebesség szükséges a szolgáltatás**: Válassza ki a megfelelő [App-csomag](https://azure.microsoft.com/pricing/details/app-service/plans/) az App Service igényei alapján. Is [vertikális felskálázás](https://docs.microsoft.com/azure/app-service/web-sites-scale) vagy le az alkalmazást. Ez is befolyásolja-e az Azure Search termékváltozat, további részletekért lásd: [Itt](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Méretét és a tudásbázisok**: Válassza ki a megfelelő [Azure keresés Termékváltozat](https://azure.microsoft.com/pricing/details/search/) a forgatókönyvhöz. Egy adott szintre, ahol N az indexek maximális száma a csomagban engedélyezett N-1 tudásbázisok teheti közzé. A maximális méret és engedélyezett / csomag dokumentumok számát is ellenőrizheti.

    Például ha a csomag esetében a 15 engedélyezett indexeket, közzéteheti 14 tudásbázisok (1 index / közzétett Tudásbázis). A tizenötödik index elkészítését és tesztelését tudásbázisok szolgál. 

1. **Forrásként dokumentumok száma**: Az ingyenes Termékváltozat a QnA Maker felügyeleti szolgáltatás segítségével kezelheti a portálon és az API-kat (az 1 MB méretű egyes) 3 dokumentumok számát korlátozza. A standard Termékváltozat már kezelheti a dokumentumok száma nincs korlátozva. További részletek [Itt](https://aka.ms/qnamaker-pricing).

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
