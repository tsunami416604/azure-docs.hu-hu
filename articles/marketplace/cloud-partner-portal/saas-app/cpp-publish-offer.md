---
title: Azure SaaS-alkalmazás ajánlat közzététele |} A Microsoft Docs
description: Közzétételi folyamat, és a lépései egy SaaS-alkalmazás nyújtanak az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 25c9ab0008e7f056789536d8cc737b69e83d6db5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197103"
---
# <a name="publish-a-saas-application-offer"></a>Egy SaaS-alkalmazás ajánlat közzététele

Az adatokat azáltal, hogy az új ajánlat létrehozása után a **új ajánlat** lapon teheti közzé az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

Az alábbi ábrán látható, a magas szintű lépéseket egy új SaaS-alkalmazás ajánlat közzétételéhez.

![Az ajánlat közzétételi lépéseket](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat ismerteti az egyes közzétételi lépésre, az egy becsült időtartam (maximális) egyes lépéseinek befejezéséhez.

|     **Lépés**       |     **idő**      |  **Leírás**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Tanúsítvány           |       2 hét            |          Az ajánlat az Azure hitelesítő csapata által elemzett. Ebben a lépésben elvégzi a vizsgálat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat. Azt is ellenőrzi, hogy ez az ajánlat megfelel-e az összes megfelelőségi feltételeket (lásd: [Előfeltételek](./cpp-prerequisites.md)). Ha problémát talál, visszajelzés érkezett.         |
|           Csomagolás         |       1 óra            |       Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az érdeklődő rendszerek vannak konfigurálva, és beállítása.            |
|        Közzétevő jelentkezzen ki            |         -          |        Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény. Válassza ki **élő** , az ajánlat helyezheti át a következő lépéssel.           |
|        Csomagolás            |        1 óra           |        Az ajánlat véglegesített marketplace éles rendszerekre és régióban replikálja a rendszer.           |
|        Élő            |       4 nap            |         Az ajánlat, amely a, a szükséges régiók replikálja, és nyilvánosan elérhetővé.          |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapon teszi lehetővé, és akkor szabadul fel, az ajánlat. Miután elvégezte a közzétételi folyamat, az SaaS-ajánlat jelenik meg a [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

[Egy létező ajánlat frissítése](./cpp-update-existing-offer.md)
