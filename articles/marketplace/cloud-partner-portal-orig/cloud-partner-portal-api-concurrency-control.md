---
title: Egyidejűség-ellenőrzés | Azure Piactér
description: Egyidejűség-ellenőrzési stratégiák a Cloud Partner Portal közzétételi API-k.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256399"
---
# <a name="concurrency-control"></a>Egyidejűség-vezérlés

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

A Cloud Partner Portal közzétételi API-kra irányuló minden hívásnak kifejezetten meg kell határoznia, hogy melyik egyidejűség-ellenőrzési stratégiát használja. Ha nem adja meg az **If-Match** fejlécet, az HTTP 400-as hibaválaszt eredményez. Két stratégiát kínálunk az egyidejűség szabályozására.

-   **Optimista** – A frissítést végző ügyfél ellenőrzi, hogy az adatok megváltoztak-e az adatok legutóbbi olvasása óta.
-   **Az utolsó nyer** – Az ügyfél közvetlenül frissíti az adatokat, függetlenül attól, hogy egy másik alkalmazás módosította-e azt az utolsó olvasási idő óta.

<a name="optimistic-concurrency-workflow"></a>Optimista egyidejűségi munkafolyamat
-------------------------------

Azt javasoljuk, hogy az optimista egyidejűségi stratégia, a következő munkafolyamat, annak biztosítása érdekében, hogy nem váratlan szerkesztések az erőforrásokat.

1.  Entitás lekérése az API-k használatával. A válasz tartalmaz egy ETag-értéket, amely azonosítja az entitás jelenleg tárolt verzióját (a válasz időpontjában).
2.  A frissítés időpontjában adja meg ugyanezt az ETag értéket a kötelező **If-Match** kérelem fejlécében.
3.  Az API összehasonlítja a kérelemben kapott ETag értéket az entitás aktuális ETag értékével egy atomi tranzakcióban.
    *   Ha az ETag értékek eltérőek, `412 Precondition Failed` az API http-választ ad vissza. Ez a hiba azt jelzi, hogy vagy egy másik folyamat frissítette az entitást, mióta az ügyfél utoljára lekérte, vagy hogy a kérelemben megadott ETag érték helytelen.
    *  Ha az ETag értékek megegyeznek, vagy az **If-Match** fejléc tartalmazza`*`a helyettesítő csillag karaktert ( ), az API végrehajtja a kért műveletet. Az API-művelet az entitás tárolt ETag értékét is frissíti.


> [!NOTE]
> A helyettesítő karakter (*) megadása az If-Match fejlécben az **API-ban** az Utolsó egy-nyer egyidejűségi stratégia használatával történik. Ebben az esetben az ETag-összehasonlítás nem történik meg, és az erőforrás ellenőrzés nélkül frissül. 
