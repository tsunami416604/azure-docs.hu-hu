---
title: Szabályozhatja az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal – standard szintű |} A Microsoft Docs
description: Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának szabályozza, hogyan kerül a gyorsítótárba Ha webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk ismerteti a lekérdezési karakterláncok gyorsítótárazásának a standard szintű Azure CDN-termékek.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: f0dab3dc81c626e3e7f8c79b4142e5eb4f2a1276
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093799"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal – standard szintű vezérlése
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN), szabályozhatja, hogyan kerül a gyorsítótárba webes egy lekérdezési karakterláncot tartalmazó kérelmek. Egy lekérdezési karakterláncot tartalmazó webes kérés esetén a lekérdezési karakterláncban része, amely a kérelemhez, amely egy kérdőjelet (?) után kerül sor. A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amelyek a mező nevének és értékének egyenlőségjelnek (=) elválasztva. Minden kulcs-érték pár elválasztott ampersand (&). Ha például a http:\//www.contoso.com/content.mov?field1=value1 & mező2 = value2. Ha egynél több kulcs-érték pár egy kérelem egy lekérdezési karakterláncban, a sorrendjük nem számít. 

> [!IMPORTANT]
> Az Azure CDN standard és prémium szintű termékek biztosítanak a azonos lekérdezési karakterlánc gyorsítótárazási funkció, de a felhasználói felület nem egyezik. Ez a cikk ismerteti a felület **Azure CDN Standard a Microsoft**, **Azure CDN Akamai Standard** és **Azure CDN Standard verizon**. A lekérdezési karakterláncok gyorsítótárazása az **verizon Azure CDN Premium**, lásd: [Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal – prémium szintű vezérlése](cdn-query-string-premium.md).

Lekérdezési karakterlánc három mód érhetők el:

- **Lekérdezési karakterláncok kihagyása**: alapértelmezett mód. Ebben a módban a CDN jelenléti pontok (POP) csomópontot adja át a lekérdezési karakterláncok a kérelmezőnek az eredeti kiszolgálóra első kérésére, és gyorsítótárba helyezi az eszközt. Az összes további kérelmet az adott eszközre, a jelenléti pont fájlnévkiterjesztései a lekérdezési karakterláncok figyelmen kívül, amíg le nem jár a gyorsítótárazott objektumhoz.

- **Lekérdezési karakterláncok gyorsítótárazásának megkerülése**: Ebben a módban lekérdezési karakterláncot tartalmazó kérelmek nincsenek gyorsítótárazva a CDN összes jelenléti Pontjára csomópontban. A POP-csomópont átveszi az eszköz közvetlenül a forráskiszolgálóról, és átadja azokat a kérelmezőnek minden egyes kérelemmel.

- **Minden egyedi URL gyorsítótárazása**: Ebben a módban egy egyedi URL-címet, a lekérdezési karakterlánc, beleértve az egyes kérelmek egyedi saját gyorsítótár-eszközként kezelni. Például example.ashx?q=test1 a kérelmek származási kiszolgálótól kapott válasz van a POP-csomópont is gyorsítótárazza, és az ezt követő gyorsítótárakhoz, az ugyanabban a lekérdezési karakterláncban adja vissza. Example.ashx?q=test2 kérelmet a saját time-to-live-beállítással külön eszközként van gyorsítótárazva.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc paraméterek, amely minden egyes kérelemmel, például a munkamenet-azonosító vagy a felhasználó neve, változást tartalmaz, mert egy alacsony gyorsítótár találati aránya eredményez.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának standard CDN-profilokban használható beállítások módosítása
1. Nyissa meg a CDN-profilt, majd válassza ki a kezelni kívánt CDN-végpont.
   
   ![Profil CDN-végpontok](./media/cdn-query-string/cdn-endpoints.png)
   
2. Kattintson a bal oldali ablaktáblán a beállítások, **gyorsítótár-szabályokkal**.
   
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Az a **lekérdezési karakterláncok gyorsítótárazásának működése** , válasszon ki egy lekérdezési karakterlánc módot, majd kattintson **mentése**.
   
   ![A CDN-lekérdezési karakterlánc gyorsítótárazási beállítások](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Mivel némi időre a regisztráció propagálásához az Azure CDN, a gyorsítótár karakterlánc módosításai nem érdemes azonnal látható:
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 



