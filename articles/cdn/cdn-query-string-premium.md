---
title: Szabályozhatja az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal – prémium szintű |} A Microsoft Docs
description: Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának szabályozza, hogyan kerül a gyorsítótárba Ha webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk ismerteti az Azure CDN Premiumhoz a Verizontól termék gyorsítótárazása lekérdezési karakterlánc.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2f0a361d53489e22ccc8e41406e5b86b423ea2f6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091402"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal – prémium szintű vezérlése
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN), szabályozhatja, hogyan kerül a gyorsítótárba webes egy lekérdezési karakterláncot tartalmazó kérelmek. Egy lekérdezési karakterláncot tartalmazó webes kérés esetén a lekérdezési karakterláncban része, amely a kérelemhez, amely egy kérdőjelet (?) után kerül sor. A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amelyek a mező nevének és értékének egyenlőségjelnek (=) elválasztva. Minden kulcs-érték pár elválasztott ampersand (&). Ha például a http:\//www.contoso.com/content.mov?field1=value1 & mező2 = value2. Ha egynél több kulcs-érték pár egy kérelem egy lekérdezési karakterláncban, a sorrendjük nem számít. 

> [!IMPORTANT]
> A standard és prémium szintű CDN-termékek, adja meg az azonos lekérdezési karakterlánc gyorsítótárazási funkció, de a felhasználói felület különböző. Ez a cikk ismerteti a felület **verizon Azure CDN Premium**. Lekérdezési karakterlánc gyorsítótárazást, ha standard szintű Azure CDN-termékek, lásd: [Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal – standard szintű vezérlése](cdn-query-string.md).
>


Lekérdezési karakterlánc három mód érhetők el:

- **Standard – gyorsítótár**: alapértelmezett mód. Ebben a módban a CDN jelenléti pontok (POP) csomópontot adja át a lekérdezési karakterláncok a kérelmezőnek az eredeti kiszolgálóra első kérésére, és gyorsítótárba helyezi az eszközt. Az összes további kérelmet az eszköz a kiszolgáló POP fájlnévkiterjesztései a lekérdezési karakterláncok figyelmen kívül, amíg le nem jár a gyorsítótárazott objektumhoz.

    >[!IMPORTANT] 
    > Token használata engedélyezve van a bármilyen útvonalat ehhez a fiókhoz, standard-gyorsítótáras üzemmód-e a mód, amely használható. 

- **no-cache**: Ebben a módban lekérdezési karakterláncot tartalmazó kérelmek nincsenek gyorsítótárazva a CDN összes jelenléti Pontjára csomópontban. A POP-csomópont átveszi az eszköz közvetlenül a forráskiszolgálóról, és átadja azokat a kérelmezőnek minden egyes kérelemmel.

- **egyedi gyorsítótár**: Ebben a módban egy egyedi URL-címet, a lekérdezési karakterlánc, beleértve az egyes kérelmek egyedi saját gyorsítótár-eszközként kezelni. Például example.ashx?q=test1 a kérelmek származási kiszolgálótól kapott válasz van a POP-csomópont is gyorsítótárazza, és az ezt követő gyorsítótárakhoz, az ugyanabban a lekérdezési karakterláncban adja vissza. Example.ashx?q=test2 kérelmet a saját time-to-live-beállítással külön eszközként van gyorsítótárazva.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc paraméterek, amely minden egyes kérelemmel, például a munkamenet-azonosító vagy a felhasználó neve, változást tartalmaz, mert egy alacsony gyorsítótár találati aránya eredményez.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának prémium szintű CDN-profilokban használható beállítások módosítása
1. Nyisson meg egy CDN-profilt, majd kattintson a **kezelés**.
   
    ![A CDN-profil felügyelet gombra](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **HTTP nagy** lapfülre, majd mutasson a **gyorsítótár beállításainak** úszó menüjét. Kattintson a **lekérdezési karakterlánc gyorsítótárazási**.
   
    Lekérdezési karakterlánc gyorsítótárazási beállítások jelennek meg.
   
    ![A CDN-lekérdezési karakterlánc gyorsítótárazási beállítások](./media/cdn-query-string-premium/cdn-query-string.png)
3. Válasszon ki egy lekérdezési karakterlánc módot, majd kattintson a **frissítés**.

> [!IMPORTANT]
> Mivel némi időre a regisztráció propagálásához a CDN-en keresztül, gyorsítótár karakterlánc módosításai nem jelennek meg azonnal. A propagálás általában 10 perc múlva befejeződik.
 

