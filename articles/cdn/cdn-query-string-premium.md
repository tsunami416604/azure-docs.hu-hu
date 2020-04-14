---
title: Az Azure CDN gyorsítótárazási viselkedésének szabályozása lekérdezési karakterláncokkal – prémium szint
description: Az Azure CDN-lekérdezési karakterlánc-gyorsítótárazás szabályozza, hogyan fájlok gyorsítótárazott, ha egy webes kérelem tartalmaz egy lekérdezési karakterláncot. Ez a cikk a Microsoft-termék Azure CDN Premium szolgáltatásában található lekérdezési karakterlánc-gyorsítótárazást ismerteti.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 94949a31db5321929a3440281cebd01712c79bb8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260139"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Az Azure CDN gyorsítótárazási viselkedésének szabályozása lekérdezési karakterláncokkal – prémium szint
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN) segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazzata a fájlokat egy lekérdezési karakterláncot tartalmazó webes kérelemhez. A lekérdezési karakterláncot tartalmazó webes kérelemben a lekérdezési karakterlánc a kérelemnek az a része, amely egy kérdőjel (?) után következik be. A lekérdezési karakterlánc egy vagy több kulcs-érték párt tartalmazhat, amelyekben a mező nevét és értékét egyenlőségjel (=) választja el egymástól. Minden kulcs-érték párt egy -mampersand (&) választ el egymástól. Például http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár van, a sorrendjük nem számít. 

> [!IMPORTANT]
> A standard és prémium szintű CDN-termékek ugyanazt a lekérdezési karakterlánc-gyorsítótárazási funkciót biztosítják, de a felhasználói felület eltérő. Ez a cikk a **Verizon Azure CDN Premium felületét**ismerteti. Az Azure CDN-szabványtermékekkel való lekérdezési karakterlánc-gyorsítótárazásról az [Azure CDN-gyorsítótárazási viselkedés vezérlése lekérdezési karakterláncokkal – szabványos szint.](cdn-query-string.md)
>


Három lekérdezési karakterlánc mód áll rendelkezésre:

- **standard-cache**: Alapértelmezett mód. Ebben az üzemmódban a CDN-pont (POP) csomópont átadja a lekérdezési karakterláncokat a kérelmezőnek az első kérésen az eredeti kiszolgálónak, és gyorsítótárazza az eszközt. A POP-kiszolgálóról kiszolgált eszköz minden további kérése figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.

    >[!IMPORTANT] 
    > Ha a jogkivonat-engedélyezés engedélyezve van a fiók bármely elérési útvonalához, a szabványos gyorsítótár-üzemmód az egyetlen használható mód. 

- **nincs gyorsítótár:** Ebben a módban a lekérdezési karakterláncokat azonosító kérelmek nem kerülnek a CDN POP-csomópontba. A POP-csomópont az eszközt közvetlenül az eredeti kiszolgálóról olvassa le, és minden kéréssel átadja a kérelmezőnek.

- **egyedi gyorsítótár:** Ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot is, egyedi eszközként lesz kezelve, saját gyorsítótárral. Például a válasz az eredeti kiszolgáló egy kérelmet például.ashx?q=test1 gyorsítótárba a POP-csomópont, és visszaadja a későbbi gyorsítótárak ugyanazzal a lekérdezési karakterlánccal. A kérelem például.ashx?q=test2 gyorsítótárazott, mint egy külön eszköz saját time-to-live beállítást.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc olyan paramétereket tartalmaz, amelyek minden kérésnél megváltoznak, például egy munkamenet-azonosítóval vagy egy felhasználónévvel, mert az alacsony gyorsítótár-találati arányt eredményez.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>A prémium szintű CDN-profilok lekérdezési karakterlánc-gyorsítótárazási beállításainak módosítása
1. Nyisson meg egy CDN-profilt, majd kattintson **a Kezelés gombra.**
   
    ![CDN-profil kezelés gombja](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Mutasson a **NAGY HTTP-fülre,** majd mutasson a **Gyorsítótár beállításai** úszó panel menüre. Kattintson **a Lekérdezés-karakterlánc-gyorsítótárazás gombra.**
   
    Megjelennek a lekérdezési karakterlánc-gyorsítótárazási beállítások.
   
    ![CDN-lekérdezési karakterlánc-gyorsítótárazási beállítások](./media/cdn-query-string-premium/cdn-query-string.png)
3. Jelöljön ki egy lekérdezési karakterlánc módot, majd kattintson **a Frissítés gombra.**

> [!IMPORTANT]
> Mivel a regisztráció cdn-en keresztül történő propagálása időbe telik, előfordulhat, hogy a gyorsítótár karakterlánc-beállításainak módosításai nem jelennek meg azonnal. A szaporítás általában 10 perc alatt befejeződik.
 

