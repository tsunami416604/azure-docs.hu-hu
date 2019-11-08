---
title: Egyidejűség-vezérlés | Azure piactér
description: A Cloud Partner Portal közzétételi API-k Egyidejűség-vezérlési stratégiái.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819747"
---
# <a name="concurrency-control"></a>Egyidejűség-vezérlés

A Cloud Partner Portal közzétételi API-k minden hívásának explicit módon meg kell határoznia, hogy melyik Egyidejűség-vezérlési stratégiát kell használni. Az **IF-Match** fejléc megadása sikertelen lesz a http 400-es hiba miatt. Két stratégiát biztosítunk a Egyidejűség-vezérléshez.

-   **Optimista** – a frissítést végrehajtó ügyfél ellenőrzi, hogy az adatok módosultak-e az adatok utolsó beolvasása óta.
-   **Utolsó egy WINS** – az ügyfél közvetlenül frissíti az adatmennyiséget, függetlenül attól, hogy egy másik alkalmazás módosította-e a legutóbbi olvasási idő óta.

<a name="optimistic-concurrency-workflow"></a>Optimista egyidejűségi munkafolyamat
-------------------------------

Javasoljuk, hogy az optimista egyidejűségi stratégiát a következő munkafolyamattal használva garantálni lehessen, hogy az erőforrásokon nem történt váratlan módosítás.

1.  Entitás beolvasása az API-k használatával. A válasz tartalmaz egy ETag értéket, amely az entitás jelenleg tárolt verzióját azonosítja (a válasz időpontjában).
2.  A frissítés időpontjában ugyanazt a ETag értéket adja meg a kötelező **IF-Match** kérelem fejlécében.
3.  Az API összehasonlítja a kérelemben kapott ETag értéket egy atomi tranzakcióban lévő entitás aktuális ETag értékével.
    *   Ha a ETag értékei eltérnek, az API egy `412 Precondition Failed` HTTP-választ ad vissza. Ez a hiba azt jelzi, hogy egy másik folyamat frissítette az entitást, mivel az ügyfél utoljára lekérte, vagy hogy a kérelemben megadott ETag érték helytelen.
    *  Ha a ETag értékei megegyeznek, vagy az **IF-Match** fejléc tartalmazza a helyettesítő karakteres csillag karaktert (`*`), az API végrehajtja a kért műveletet. Az API-művelet az entitás tárolt ETag értékét is frissíti.


> [!NOTE]
> A helyettesítő karakter (*) megadása a **IF-Match** fejlécben az API-t az utolsó-egy WINS egyidejűségi stratégia használatával eredményezi. Ebben az esetben a ETag összehasonlítása nem történik meg, és az erőforrás ellenőrzés nélkül frissül. 
