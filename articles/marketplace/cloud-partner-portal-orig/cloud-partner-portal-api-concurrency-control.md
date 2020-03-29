---
title: Egyidejűség-ellenőrzés | Azure Piactér
description: Egyidejűség-ellenőrzési stratégiák a Cloud Partner Portal közzétételi API-k.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d0f035d77e74f157b793b9edf3ab5d3494096d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288648"
---
# <a name="concurrency-control"></a>Egyidejűség-vezérlés

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
