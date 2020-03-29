---
title: A Verizon alapjelentései | Microsoft dokumentumok
description: 'A CDN használati mintáit a következő jelentések segítségével tekintheti meg: Sávszélesség, Átvitt adatátvitel, találatok, gyorsítótár-állapotok, Gyorsítótár találati aránya, IPV4/IPV6 átvitt adatok.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594161"
---
# <a name="core-reports-from-verizon"></a>Alapvető jelentések a Verizontól

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A Verizon Core Reports használatával a Verizon-profilok kezelése portálon keresztül a CDN használati mintáit a következő jelentésekkel tekintheti meg:

* Sávszélesség
* Átvitt adatok
* Találatok
* Gyorsítótár állapotai
* Gyorsítótár találati aránya
* IPV4/IPV6 átvitt adatok

## <a name="accessing-verizon-core-reports"></a>A Verizon Core jelentések elérése
1. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN-profil kezelés gombja](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Mutasson az **Analytics** fülre, majd mutasson a **Core Reports** úszó panelre. Kattintson egy jelentésre a menüben.
   
    ![CDN felügyeleti portál - Core Reports menü](./media/cdn-reports/cdn-core-reports.png)

3. Minden jelentéshez válasszon egy dátumtartományt a **Dátumtartomány** listából. Választhat egy előre definiált dátumtartományt, például **a Ma** vagy **a Hét**, vagy az **Egyéni** lehetőséget, és manuálisan megadhat egy dátumtartományt a naptár ikonokra kattintva. 

4. Miután kijelölt egy dátumtartományt, kattintson az **Ugrás** gombra a jelentés létrehozásához. 

4. Ha Excel formátumban szeretné exportálni az adatokat, kattintson az **Ugrás** gomb feletti Excel ikonra.

## <a name="bandwidth"></a>Sávszélesség
A sávszélesség-jelentés egy grafikonból és egy adattáblából áll, amely a HTTP és HTTPS CDN sávszélesség-használatát jelzi egy adott időszakban, Mbps-ben. Megtekintheti a sávszélesség-használatot az összes POP-ban vagy egy adott POP-ban. Ez a jelentés lehetővé teszi a pop-ok forgalmi csúcsok és elosztásmegtekintését.

Az **Edge csomópontok** listájában válassza az **Összes peremcsomópont lehetőséget** az összes csomópontból érkező forgalom megtekintéséhez, vagy válasszon ki egy adott területet.

A jelentést ötpercenként frissítik.

![Sávszélesség-jelentés](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Továbbított adatok
Ez a jelentés egy grafikonból és egy adattáblából áll, amely a HTTP és https CDN-forgalom használatát jelzi egy adott időszakban, GB-ban. Megtekintheti a forgalom használatát az összes POP-ban vagy egy adott POP-ban. Ez a jelentés lehetővé teszi a forgalmi csúcsok és a POP-ok közötti eloszlás megtekintését.

Az **Edge csomópontok** listájában válassza az **Összes peremcsomópont lehetőséget** az összes csomópontból érkező forgalom megtekintéséhez, vagy válasszon ki egy adott területet.

A jelentést ötpercenként frissítik.

![Továbbított adatokról szóló jelentés](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Találatok (állapotkódok)
Ez a jelentés a tartalom kérésállapot-kódjainak terjesztését ismerteti. Minden tartalomkérés HTTP-állapotkódot hoz létre. Az állapotkód azt írja le, hogy a szélelévő POP-ok hogyan kezelték a kérelmet. A 2xx állapotkód például azt jelzi, hogy a kérés sikeresen kézbesítve lett egy ügyfélnek, míg a 4xx állapotkód azt jelzi, hogy hiba történt. A HTTP-állapotkódokról a [HTTP-állapotkódok listája](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)című témakörben talál további információt.

![Találati jelentés](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Gyorsítótár állapotai
Ez a jelentés ismerteti a gyorsítótár-lekérések és az ügyfélkérelmek gyorsítótár-tévesztéseinek eloszlását. Mivel a gyorsítótár-találatok a leggyorsabb teljesítményt eredményezik, optimalizálhatja az adatkézbesítési sebességet a gyorsítótár-tévesztések és a lejárt gyorsítótár-találatok minimalizálásával. 

A gyorsítótár-tévesztések csökkentése érdekében konfigurálja úgy az eredeti kiszolgálót, hogy a lehető legkisebb rekedjen: 
 * `no-cache`válaszfejlécek
 * Lekérdezés-karakterlánc gyorsítótárazás, kivéve, ha feltétlenül szükséges  
 * Nem gyorsítótárazható válaszkódok

A lejárt gyorsítótár-lekérések csökkentéséhez `max-age` állítsa be az eszköz hosszú időszakra, hogy minimálisra csökkentse a származási kiszolgálóra irányuló kérelmek számát.

![Gyorsítótár állapotjelentése](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>A gyorsítótár fő állapotai a következők:
* TCP_HIT: Peremhálózati kiszolgálóról szolgálják ki. Az objektum a gyorsítótárban volt, és nem lépte túl a maximális életkorát.
* TCP_MISS: Az eredeti kiszolgálóról szolgálják ki. Az objektum nem volt a gyorsítótárban, és a válasz vissza állt az eredeti.
* TCP_EXPIRED _MISS: Az eredeti kiszolgálóról kiszolgálva, miután újraérvényesítést kapott az originnel. Az objektum a gyorsítótárban volt, de túllépte a maximális életkorát. Az eredeti vel rendelkező újraérvényesítés eredményeképpen a gyorsítótár-objektumot egy új, kezdő válasz váltotta fel.
* TCP_EXPIRED _HIT: Az edge-ből kiszolgálva, miután újraérvényesítést ért el az eredetivel. Az objektum a gyorsítótárban volt, de túllépte a maximális életkorát. Az eredeti kiszolgálóval való újraérvényesítés a gyorsítótár-objektum módosítását eredményezte.

### <a name="full-list-of-cache-statuses"></a>Gyorsítótár-állapotok teljes listája
* TCP_HIT – Ez az állapot akkor jelenik meg, ha a kérelmet közvetlenül a POP-ból kézbesítik az ügyfélnek. Egy eszköz azonnal kiszolgálják a POP, ha a gyorsítótárazott a POP legközelebb az ügyfélhez, és egy érvényes idő-to-live (TTL). A TTL-t a következő válaszfejlécek határozzák meg:
  
  * Gyorsítótár-vezérlés: s-maxage
  * Gyorsítótár-vezérlés: max-kor
  * Lejárat
* TCP_MISS: Ez az állapot azt jelzi, hogy a kért eszköz gyorsítótárazott verziója nem található az ügyfélhez legközelebb eső POP-on. Az eszközt az eredeti kiszolgálótól vagy az eredeti pajzskiszolgálótól kérik. Ha az eredeti kiszolgáló vagy az eredeti pajzskiszolgáló egy eszközt ad vissza, a rendszer az ügyfélnek kézbesíti, és az ügyfélen és a peremhálózati kiszolgálón is gyorsítótárba helyezi. Ellenkező esetben egy nem 200-as állapotkódot (például 403 Tiltott vagy 404 nem található) ad vissza.
* TCP_EXPIRED_HIT: Ez az állapot akkor jelenik meg, ha egy lejárt TTL-lel rendelkező eszközt megcélző kérést közvetlenül a POP-ból kézbesített az ügyfélnek. Például, ha az eszköz maximális életkora lejárt. 
  
   A lejárt kérelem általában újraérvényesítési kérelmet eredményez a forráskiszolgálón. Ahhoz, hogy egy TCP_EXPIRED_HIT állapot bekövetkezzen, az eredeti kiszolgálónak jeleznie kell, hogy az eszköz újabb verziója nem létezik. Ez a helyzet általában az eszköz gyorsítótár-vezérlési és lejárati fejléceinek frissítését eredményezi.
* TCP_EXPIRED_MISS: Ez az állapot akkor jelenik meg, ha egy lejárt gyorsítótárazott eszköz újabb verzióját szolgálják ki a POP-ból az ügyfélnek. Ez az állapot akkor fordul elő, ha egy gyorsítótárazott eszköz TTL-je lejárt (például lejárt a maximális életkor), és az eredeti kiszolgáló az eszköz újabb verzióját adja vissza. Az eszköz ezen új verziója a gyorsítótárazott verzió helyett az ügyfélnek kerül kiszolgálásra. Emellett a peremhálózati kiszolgálón és az ügyfélen is gyorsítótárazva van.
* CONFIG_NOCACHE: Ez az állapot azt jelzi, hogy egy ügyfél-specifikus konfiguráció a peremhálózati POP megakadályozta az eszköz gyorsítótárazását.
* NONE – Ez az állapot azt jelzi, hogy a gyorsítótár tartalmának frissesség-ellenőrzése nem történt meg.
* TCP_CLIENT_REFRESH_MISS: Ez az állapot akkor jelenik meg, ha egy HTTP-ügyfél, például egy böngésző, kényszeríti a peremhálózati POP-ot, hogy lekérje az elavult eszköz új verzióját az eredeti kiszolgálóról. Alapértelmezés szerint a kiszolgálók megakadályozzák, hogy egy HTTP-ügyfél arra kényszerítse a peremhálózati kiszolgálókat, hogy az eszköz új verzióját kérjék le az eredeti kiszolgálóról.
* TCP_PARTIAL_HIT: Ez az állapot akkor jelenik meg, ha egy bájttartomány-kérelem egy részlegesen gyorsítótárazott eszköz lekérését eredményezi. A kért bájttartomány tüstént kiszolgálja a POP az ügyfélnek.
* Nem gyorsítótárazható: Ez az állapot `Cache-Control` akkor `Expires` jelenik meg, ha egy eszköz és a fejlécek azt jelzik, hogy nem kell gyorsítótárazza a POP vagy a HTTP-ügyfél. Az ilyen típusú kérelmek et az eredeti kiszolgálóról szolgálják ki.

## <a name="cache-hit-ratio"></a>Gyorsítótár találati aránya
Ez a jelentés a gyorsítótárazott kérelmek százalékos arányát jelzi, amelyeket közvetlenül a gyorsítótárból kézbesített.

A jelentés a következő részleteket tartalmazza:

* A kért tartalom gyorsítótárazva lett a kérelmezőhöz legközelebb eső POP-on.
* A kérést közvetlenül a hálózatunk széléről kézbesítettük.
* A kérelem nem igényelt újraérvényesítést az eredeti kiszolgálóval.

A jelentés nem tartalmazza a következőket:

* Az ország-/régiószűrési beállítások miatt elutasított kérelmek.
* Olyan eszközökre vonatkozó kérelmek, amelyek fejlécei azt jelzik, hogy azokat nem szabad gyorsítótárba tanuk. Például `Cache-Control: private`, `Cache-Control: no-cache`vagy `Pragma: no-cache` a fejlécek megakadályozzák egy eszköz gyorsítótárazását.
* A részben gyorsítótárazott tartalom bájttartomány-kérelmei.

A képlet: (TCP_ HIT/(TCP_ HIT+TCP_MISS)*100

![Gyorsítótár találati arányának jelentése](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPV4/IPV6 átvitt adatok
Ez a jelentés az IPV4 és az IPV6 forgalomhasználati eloszlását jeleníti meg.

![IPV4/IPV6 átvitt adatok](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az elmúlt 18 hónapban készíthetők.

