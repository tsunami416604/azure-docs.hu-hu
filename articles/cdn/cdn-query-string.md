---
title: "Szabályozhatja az Azure CDN a lekérdezési karakterláncok gyorsítótárazásának |} Microsoft Docs"
description: "Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának hogyan fájljai gyorsítótárazni a lekérdezési karakterláncokat tartalmazó vezérlő."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8d79626fa8516f226a82d3dac693c2033904c91d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings"></a>Vezérlő Azure CDN a lekérdezési karakterláncok gyorsítótárazásának
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon Azure CDN Premium](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Áttekintés
Lekérdezési karakterláncok gyorsítótárazásának hogyan fájljai gyorsítótárazni a lekérdezési karakterláncokat tartalmazó vezérlő.

> [!IMPORTANT]
> A Standard és prémium szintű CDN-termékek, adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület eltér.  Ez a dokumentum ismerteti a felület **Azure CDN Standard Akamai** és **Azure CDN Standard verizon**.  A lekérdezési karakterláncok gyorsítótárazása a **verizon Azure CDN Premium**, lásd: [a lekérdezési karakterláncok - prémium szintű CDN gyorsítótárazási viselkedésének vezérlése kérelmek](cdn-query-string-premium.md).
> 
> 

Három módszer áll rendelkezésre:

* **Lekérdezési karakterláncok figyelmen kívül**: Ez az az alapértelmezett mód.  A CDN élcsomópont fogja továbbítani a lekérdezési karakterláncot a kérelmezőnek az eredeti az első kérelem és a gyorsítótár az eszköz.  Az összes további kérelmet, hogy az eszköz az élcsomópont szolgáltatott eléréséig a gyorsítótárazott eszköz figyelmen kívül hagyja a lekérdezési karakterlánc.
* **URL-címet a lekérdezési sztringek gyorsítótárazásának mellőzése**: Ebben a módban a lekérdezési karakterláncot tartalmazó kérelmek nem gyorsítótárazzák a CDN a peremhálózati csomóponton.  Élcsomópont olvassa be az objektumot közvetlenül a forrásból, és minden egyes kérelemmel a kérelmező számára továbbítja azokat.
* **Minden egyedi URL-cím gyorsítótárazása**: Ebben a módban a lekérdezési karakterlánc az egyes kérelmek kezeli a saját gyorsítótárával egyedi eszközként.  Például az eredeti adatforrást kérelmet kapott válasz *foo.ashx?q=bar* ehhez az élcsomóponthoz gyorsítótárazott és későbbi gyorsítótárak az, hogy ugyanazt a lekérdezési karakterláncot adott vissza.  A kérelem *foo.ashx?q=somethingelse* külön eszközként saját, amikor élő volna gyorsítótárazható.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának standard szintű CDN-profil beállításainak módosítása
1. A CDN-profil panelje kattintson a CDN-végpont felügyelni kíván.
   
    ![CDN-profil blade-végpontok](./media/cdn-query-string/cdn-endpoints.png)
   
    A CDN-végpont panelje megnyílik.
2. Kattintson a **konfigurálása** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-query-string/cdn-config-btn.png)
   
    Ekkor megnyílik a CDN konfigurációs panel.
3. A beállítás a **lekérdezési sztringek gyorsítótárazásának** legördülő menüből.
   
    ![A CDN a lekérdezési karakterláncban a gyorsítótár](./media/cdn-query-string/cdn-query-string.png)
4. Miután választott, kattintson a **mentése** gombra.

> [!IMPORTANT]
> A beállítások módosításait nem lehet azonnal láthatóak, mivel némi időre van a CDN propagálásához a regisztráció.  Az <b>Akamai Azure CDN</b> típusú profilok propagálása általában egy percen belül befejeződik.  A <b>Verizon Azure CDN</b> típusú profilok propagálása általában 90 percen belül befejeződik, ám egyes esetekben több időt is igénybe vehet.
> 
> 

