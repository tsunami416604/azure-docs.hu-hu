---
title: Az Azure CDN gyorsítótárazási viselkedésének szabályozása lekérdezési karakterláncokkal – standard szint
description: Az Azure CDN-lekérdezési karakterlánc-gyorsítótárazás szabályozza, hogyan fájlok gyorsítótárazott, ha egy webes kérelem tartalmaz egy lekérdezési karakterláncot. Ez a cikk az Azure CDN standard termékek lekérdezési karakterlánc-gyorsítótárazását ismerteti.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 6471241527dd9b594eaaca20ebc75cacb27f8f72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083031"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Az Azure CDN gyorsítótárazási viselkedésének szabályozása lekérdezési karakterláncokkal – standard szint
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN) segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazzata a fájlokat egy lekérdezési karakterláncot tartalmazó webes kérelemhez. A lekérdezési karakterláncot tartalmazó webes kérelemben a lekérdezési karakterlánc a kérelemnek az a része, amely egy kérdőjel (?) után következik be. A lekérdezési karakterlánc egy vagy több kulcs-érték párt tartalmazhat, amelyekben a mező nevét és értékét egyenlőségjel (=) választja el egymástól. Minden kulcs-érték párt egy -mampersand (&) választ el egymástól. Például http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár van, a sorrendjük nem számít. 

> [!IMPORTANT]
> Az Azure CDN standard és prémium szintű termékek ugyanazt a lekérdezési karakterlánc-gyorsítótárazási funkciót biztosítják, de a felhasználói felület eltérő. Ez a cikk a **Microsoft Azure CDN Standard**felületét, az **Akamai Azure CDN Standard** és a Verizon Azure **CDN Standard felületét**ismerteti. A **Verizon Azure CDN Premium**szolgáltatással való lekérdezési karakterlánc-gyorsítótárazásáról az Azure [CDN-gyorsítótárazási viselkedése lekérdezési karakterláncokkal – prémium szint](cdn-query-string-premium.md).

Három lekérdezési karakterlánc mód áll rendelkezésre:

- **Lekérdezési karakterláncok figyelmen kívül hagyása**: Alapértelmezett mód. Ebben az üzemmódban a CDN-pont (POP) csomópont átadja a lekérdezési karakterláncokat a kérelmezőnek az első kérésen az eredeti kiszolgálónak, és gyorsítótárazza az eszközt. A POP-ból kiszolgált eszköz minden további kérése figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.

- **Lekérdezési karakterláncok gyorsítótárazásának megkerülése**: Ebben a módban a lekérdezési karakterláncokat azonosító kérelmek nem kerülnek a CDN POP-csomópontba. A POP-csomópont az eszközt közvetlenül az eredeti kiszolgálóról olvassa le, és minden kéréssel átadja a kérelmezőnek.

- **Minden egyedi URL gyorsítótárazása:** Ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot is, egyedi eszközként lesz kezelve, saját gyorsítótárral. Például a válasz az eredeti kiszolgáló egy kérelmet például.ashx?q=test1 gyorsítótárba a POP-csomópont, és visszaadja a későbbi gyorsítótárak ugyanazzal a lekérdezési karakterlánccal. A kérelem például.ashx?q=test2 gyorsítótárazott, mint egy külön eszköz saját time-to-live beállítást.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc olyan paramétereket tartalmaz, amelyek minden kérésnél megváltoznak, például egy munkamenet-azonosítóval vagy egy felhasználónévvel, mert az alacsony gyorsítótár-találati arányt eredményez.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>A szabványos CDN-profilok lekérdezési karakterlánc-gyorsítótárazási beállításainak módosítása
1. Nyisson meg egy CDN-profilt, majd jelölje ki a kezelni kívánt CDN-végpontot.
   
   ![CDN-profil végpontjai](./media/cdn-query-string/cdn-endpoints.png)
   
2. A bal oldali ablaktáblában a Beállítások csoportban kattintson a **Gyorsítótárazási szabályok**elemre.
   
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. A **Lekérdezési karakterlánc-gyorsítótárazásviselkedési** listájában jelöljön ki egy lekérdezési karakterlánc módot, majd kattintson a **Mentés gombra.**
   
   ![CDN-lekérdezési karakterlánc-gyorsítótárazási beállítások](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Mivel a regisztráció az Azure CDN-en keresztül történő propagálása időbe telik, előfordulhat, hogy a gyorsítótár karakterlánc-beállításainak módosításai nem jelennek meg azonnal:
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 



