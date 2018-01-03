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
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 2021b5b7602605a7c264e9cd575399077691da34
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Vezérlő Azure Content Delivery Network gyorsítótárazásának a lekérdezési karakterláncok - támogatás
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon Azure CDN Premium](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN), megadhatja a hogyan kerül a gyorsítótárba egy lekérdezési karakterláncot tartalmazó webes kérelem számára. A webes kérelem lekérdezési karakterláncot a lekérdezési karakterlánc része a kérelmet, amely után a kérdőjel (?). A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amely a mező nevét és az értéke egyenlőségjelnek (=) elválasztva. Minden kulcs-érték párt elválasztott ampersand (&). Például: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Ha a kérelem lekérdezési karakterláncként egynél több kulcs-érték pár, a sorrendjük nincs jelentősége. 

> [!IMPORTANT]
> A standard és prémium szintű CDN-termékek, adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület különböző.  Ez a cikk ismerteti a felület **verizon Azure CDN Premium**. A lekérdezési karakterláncok gyorsítótárazása a **Azure CDN Standard Akamai** és **Azure CDN Standard verizon**, lásd: [lekérdezési karakterláncokat tartalmazó kérelmek gyorsítótárazási CDN viselkedésének vezérlése](cdn-query-string.md).
>

Lekérdezés-karakterlánc három módot érhetők el:

- **Standard-gyorsítótár**: alapértelmezett mód. Ebben a módban CDN élcsomópont továbbítja a lekérdezési karakterláncok a kérelmezőnek az eredeti az első kérésre, és az eszköz gyorsítótárazza. Az összes további kérelmet az eszköz az élcsomópont szolgáltatott figyelmen kívül hagyja a lekérdezési karakterláncok eléréséig a gyorsítótárazott objektumhoz.
- **no-cache**: Ebben a módban a lekérdezési karakterláncot tartalmazó kérelmek nem gyorsítótárazzák a CDN a peremhálózati csomóponton. Élcsomópont olvassa be az objektumot közvetlenül a forrásból, és minden egyes kérelemmel a kérelmező számára továbbítja azokat.
- **egyedi gyorsítótár**: Ebben a módban egy egyedi URL-cím, beleértve a lekérdezési karakterlánc minden kérelmet egy egyedi objektum saját gyorsítótár számít. Például az eredeti adatforrást kérelmet kapott válasz `example.ashx?q=test1` élcsomópont gyorsítótárazott és későbbi gyorsítótárak az azonos lekérdezési karakterláncot adott vissza. A kérelem `example.ashx?q=test2` saját idő a működés közbeni beállítású külön eszközként gyorsítótárazza.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának prémium szintű CDN-profil beállításainak módosítása
1. Nyissa meg a CDN-profilt, majd kattintson a **kezelése**.
   
    ![CDN-profil Manage gomb](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** úszó menüje. Kattintson a **lekérdezési karakterlánc-gyorsítótár**.
   
    Lekérdezési karakterlánc gyorsítótárazási beállítások jelennek meg.
   
    ![A CDN a lekérdezési karakterláncban a gyorsítótár](./media/cdn-query-string-premium/cdn-query-string.png)
3. Válassza ki az lekérdezési karakterláncot, majd kattintson az **frissítés**.

> [!IMPORTANT]
> A regisztráció CDN propagálásához időt vesz igénybe, mert a gyorsítótár karakterlánc módosításait nem feltétlenül jelennek meg azonnal. A **verizon Azure CDN Premium** -profilok propagálása általában 90 percen belül befejeződik, de egyes esetekben több időt vesz igénybe.
 

