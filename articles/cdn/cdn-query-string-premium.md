---
title: "Szabályozhatja az Azure CDN a lekérdezési karakterláncok - prémium gyorsítótárazásának |} Microsoft Docs"
description: "Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának hogyan fájljai gyorsítótárazni a lekérdezési karakterláncokat tartalmazó vezérlő."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>Vezérlő Azure CDN a lekérdezési karakterláncok - prémium gyorsítótárazásának
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon Azure CDN Premium](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Áttekintés
Lekérdezési karakterláncok gyorsítótárazásának hogyan fájljai gyorsítótárazni a lekérdezési karakterláncokat tartalmazó vezérlő.

> [!IMPORTANT]
> A Standard és prémium szintű CDN-termékek, adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület eltér.  Ez a dokumentum ismerteti a felület **verizon Azure CDN Premium**.  A lekérdezési karakterláncok gyorsítótárazása a **Azure CDN Standard Akamai** és **Azure CDN Standard verizon**, lásd: [lekérdezési karakterláncokat tartalmazó kérelmek gyorsítótárazási CDN viselkedésének vezérlése](cdn-query-string.md).
> 
> 

Három módszer áll rendelkezésre:

* **Standard-gyorsítótár**: Ez az az alapértelmezett mód.  A CDN élcsomópont fogja továbbítani a lekérdezési karakterláncot a kérelmezőnek az eredeti az első kérelem és a gyorsítótár az eszköz.  Az összes további kérelmet, hogy az eszköz az élcsomópont szolgáltatott eléréséig a gyorsítótárazott eszköz figyelmen kívül hagyja a lekérdezési karakterlánc.
* **no-cache**: Ebben a módban a lekérdezési karakterláncot tartalmazó kérelmek nem gyorsítótárazzák a CDN a peremhálózati csomóponton.  Élcsomópont olvassa be az objektumot közvetlenül a forrásból, és minden egyes kérelemmel a kérelmező számára továbbítja azokat.
* **egyedi gyorsítótár**: Ebben a módban a lekérdezési karakterlánc az egyes kérelmek kezeli a saját gyorsítótárával egyedi eszközként.  Például az eredeti adatforrást kérelmet kapott válasz *foo.ashx?q=bar* ehhez az élcsomóponthoz gyorsítótárazott és későbbi gyorsítótárak az, hogy ugyanazt a lekérdezési karakterláncot adott vissza.  A kérelem *foo.ashx?q=somethingelse* külön eszközként saját, amikor élő volna gyorsítótárazható.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának prémium szintű CDN-profil beállításainak módosítása
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** menü.  Kattintson a **lekérdezési karakterlánc-gyorsítótár**.
   
    Lekérdezési karakterlánc gyorsítótárazási beállítások jelennek meg.
   
    ![A CDN a lekérdezési karakterláncban a gyorsítótár](./media/cdn-query-string-premium/cdn-query-string.png)
3. Miután választott, kattintson a **frissítés** gombra.

> [!IMPORTANT]
> A beállítások módosításait nem lehet azonnal láthatóak, mivel némi időre van a CDN propagálásához a regisztráció.  A <b>Verizon Azure CDN</b> típusú profilok propagálása általában 90 percen belül befejeződik, ám egyes esetekben több időt is igénybe vehet.
> 
> 

