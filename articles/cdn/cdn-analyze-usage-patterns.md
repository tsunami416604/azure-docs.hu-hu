---
title: Verizon jelentések alapvető |} Microsoft Docs
description: 'Megtekintheti használati minták a CDN a következő jelentések segítségével: sávszélesség, adatokat továbbít, a találatok, gyorsítótár állapotok, gyorsítótári találati aránya, IPV4/IPV6 adatokat továbbít.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: fa828bfa736d677fb4881e5cc2628c0e03eb8749
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
ms.locfileid: "26345247"
---
# <a name="core-reports-from-verizon"></a>Verizon Core jelentések

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon Core jelentések segítségével a kezelés portálon Verizon profilok, tekintse meg a használati minták a CDN-t az alábbi jelentéseket:

* Sávszélesség
* Átvitt adatok
* Találatok
* Gyorsítótár-állapotok
* Gyorsítótár találati aránya
* Átvitt adatok IPv4/IPV6

## <a name="accessing-verizon-core-reports"></a>Verizon Core jelentések használata
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil Manage gomb](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **Analytics** lapra, és vigye a **Core jelentések** menü. Kattintson a jelentés a menüben.
   
    ![CDN management portal - Core jelentések menü](./media/cdn-reports/cdn-core-reports.png)

3. Az egyes jelentések, válassza ki a dátumtartományt a **dátumtartomány** listája. Kiválaszthatja egy előre definiált dátumtartományt, például a **Ma** vagy **ezen a héten**, vagy választhat **egyéni** és manuálisan a naptár ikonok kattintva adjon meg egy dátumtartományt. 

4. Miután kiválasztotta a dátumtartományt, kattintson a **lépjen** jelentést. 

4. Ha az adatok Excel formátumú exportálni szeretné, kattintson a fenti Excel ikonra a **lépjen** gombra.

## <a name="bandwidth"></a>Sávszélesség
A sávszélesség jelentés áll egy grafikonon és adattáblát, amely jelzi a CDN sávszélesség-használat a HTTP és HTTPS adott idő alatt, a MB/s. A sávszélesség-használat összes POP vagy egy adott POP tekintheti meg. Ez a jelentés lehetővé teszi a forgalom teljesítményt és a csatlakozási pontok terjesztési megtekintését.

Az a **peremhálózati csomópontok** listáról válassza ki **csomópontjaihoz peremhálózati** összes csomópontjának hálózati forgalom, vagy válasszon ki egy meghatározott régiót.

A jelentés 5 percenként frissül.

![Sávszélesség-jelentés](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Átvitt adatok
Ez a jelentés tartalmaz egy grafikonon és adattáblát, amely jelzi a CDN-adatforgalom használati HTTP és HTTPS adott idő alatt, GB-ban. A forgalom használati összes POP vagy egy adott POP tekintheti meg. Ez a jelentés lehetővé teszi a forgalom teljesítményt és a terjesztési megtekintését kapcsolódási pontok között.

Az a **peremhálózati csomópontok** listáról válassza ki **csomópontjaihoz peremhálózati** összes csomópontjának hálózati forgalom, vagy válasszon ki egy meghatározott régiót.

A jelentés 5 percenként frissül.

![Az átvitt adatmennyiség jelentés](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Találatok (állapotkód esetében)
Ez a jelentés a kérelem állapotkódok a tartalom terjesztési ismerteti. Minden kérelemnél tartalom HTTP-állapotkódot állít elő. Az állapotkód: ismerteti, hogyan történik a peremhálózati POP a kérelem kezelése. Például egy 2xx állapotkód: azt jelzi, hogy a kérés sikeresen kiszolgálásának ügyfélnek, amíg egy 4xx állapotkód: azt jelzi, hogy hiba történt. További információ a HTTP-állapotkódok: [lista a HTTP-állapotkódok](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![A találatok jelentés](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Gyorsítótárak allapota
Ez a jelentés a terjesztési találatot eredményező gyorsítótárbeli kereséseinek és az ügyféli kérelmek részére gyorsítótárbeli ismerteti. A leggyorsabb teljesítmény találatot eredményező gyorsítótárbeli kereséseinek az eredménye, mert optimalizálhatja adatok kézbesítési sebességű gyorsítótárbeli és lejárt találatok csökkentésével. 

Gyorsítótárbeli csökkentése érdekében konfigurálja a forrás-kiszolgálót, minimalizálása érdekében a következő használatát: 
 * `no-cache`válaszfejlécek
 * Lekérdezés-karakterlánc gyorsítótárazás, kivéve, ha feltétlenül szükséges  
 * Nem gyorsítótárazható válaszkódok

Lejárt találatok csökkentése érdekében állítsa be az eszköz `max-age` hosszú időszakra minimalizálása érdekében az eredeti kiszolgálóra kérelmek számát jelenti.

![Gyorsítótár-állapotok jelentés](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Fő gyorsítótár állapotok a következők:
* TCP_HIT: A peremhálózati kiszolgáló kiszolgálása között. Az objektum a gyorsítótárban, és nem túllépte a maximális életkora.
* TCP_MISS: Kiszolgált a forráskiszolgálóról. Az objektum nem a gyorsítótárban, és a válasz vissza az eredeti volt.
* TCP_EXPIRED _MISS: kiszolgált forráskiszolgálóról eredete ismételt érvényesítése után. Az objektum a gyorsítótárban, de nagyobb a maximális életkora volt. A forrás egy ismételt érvényesítése a felváltja a forrásból új választ gyorsítótár-objektumának eredményezett.
* TCP_EXPIRED _HIT: kiszolgált szélétől eredete ismételt érvényesítése után. Az objektum a gyorsítótárban volt, de nagyobb a maximális életkora volt. A forrás-kiszolgálóval a ismételt érvényesítése a módosítani kívánt gyorsítótár-objektumának eredményezett.

### <a name="full-list-of-cache-statuses"></a>Gyorsítótár-állapotok teljes listája
* TCP_HIT – amikor egy kérelem kiszolgált a POP-ről az ügyfél ezt az állapotot jelentett. Ha a POP legközelebb az ügyfél gyorsítótárában van, és egy érvényes idő-live (TTL) rendelkezik egy eszköz azonnal kiszolgált a POP. A következő válaszfejlécek TTL határozza meg:
  
  * A Cache-Control: s-maxage
  * A Cache-Control: maximális-kor
  * Elévül
* TCP_MISS: Ez az állapot azt jelzi, hogy a kért eszköz gyorsítótárazott verzió nem található meg az ügyfél legközelebb POP. Az eszköz az eredeti kiszolgálóra vagy egy eredeti pajzs kiszolgálóra van szükség. Ha az eredeti kiszolgálóra vagy a forráskiszolgáló pajzs adja vissza egy eszköz, az ügyfélprogram és az ügyfél és a peremhálózati kiszolgáló gyorsítótárazza. Ellenkező esetben nem 200 állapotkódot (például 403 Tiltott vagy 404-es nem található) adja vissza.
* TCP_EXPIRED_HIT: Ez az állapot jelent, ha egy meglévő eszközt egy lejárt TTL megcélzó kérés kiszolgálásának a POP-ről, az ügyfélnek. Például, ha az eszköz tartozó maximális-életkora érvényessége lejárt. 
  
   Lejárt kérelmet általában annak az eredménye ismételt érvényesítése kérelemben az eredeti kiszolgálóra. Egy TCP_EXPIRED_HIT állapot fordulhat elő a forrás kiszolgálónak jeleznie kell, hogy az eszköz egy újabb verziója nem létezik. Ez a helyzet általában az eszköz a Cache-Control és Expires fejléc frissítése eredményez.
* TCP_EXPIRED_MISS: Amikor egy lejárt gyorsítótárazott eszköz egy újabb verziója az ügyfélnek a POP-ra a kiszolgált Ez az állapot az elvártnak. Ez az állapot akkor fordul elő, ha egy gyorsítótárazott eszköz Élettartama lejárt (például lejárt maximális-életkora) és az eredeti kiszolgálóra adja vissza egy adott eszközre újabb verziója. Az eszköz ezen új verziójával kiszolgált az ügyfélnek a gyorsítótárazott verzió helyett. Emellett gyorsítótárazza a peremhálózati kiszolgáló és az ügyfélen.
* CONFIG_NOCACHE: Ez az állapot azt jelzi, hogy egy ügyfél-konfigurációs a peremhálózaton POP miatt nem sikerült az eszköz a gyorsítótárba helyezésből.
* Nincs – Ez az állapot azt jelzi, hogy a tartalom frissesség ellenőrzése nem történt meg.
* TCP_CLIENT_REFRESH_MISS: Ez az állapot jelent, ha HTTP-ügyfelek, például a böngésző kényszeríti a forráskiszolgálóról egy új verziója elavult eszköz beolvasandó POP él. Alapértelmezés szerint a kiszolgálók megakadályozhatják, hogy egy HTTP-ügyfél kényszerítése a peremhálózati kiszolgálóinak beolvasása az eszköz új verziójának a forráskiszolgálóról.
* TCP_PARTIAL_HIT: A bájttartománykérés eredményez a részlegesen gyorsítótárazott eszköz találat esetén ez az állapot van küldött. A kért bájttartomány azonnal kiszolgált a a csatlakozási pont az ügyfélhez.
* UNCACHEABLE: Ez az állapot jelent, ha egy eszköz `Cache-Control` és `Expires` fejlécek jelzi, hogy azt nem gyorsítótárazza a POP vagy a HTTP-ügyfél. Az ilyen típusú kérelmek szolgáltatott a forráskiszolgálóról.

## <a name="cache-hit-ratio"></a>Gyorsítótár találati aránya
Ez a jelentés azt jelzi, hogy közvetlenül a gyorsítótárból volt szolgáltatott gyorsítótárazott kérelem azon százaléka.

A jelentés tartalmazza a következő adatokat:

* A kért tartalom a legközelebbi igénylő POP gyorsítótárazva lett.
* A kérés kiszolgálásának közvetlenül a hálózati szélétől.
* A kérelem nem volt szükség a forrás-kiszolgálóval ismételt érvényesítése.

A jelentés nem tartalmaz:

* Ország szűrési beállítások miatt elutasított kérelmek.
* Eszközök fejlécekhez jelzi, hogy azok nem gyorsítótárazza a kérelmeket. Például `Cache-Control: private`, `Cache-Control: no-cache`, vagy `Pragma: no-cache` fejlécek egy eszköz megakadályozza a gyorsítótárba.
* Tartomány kérelmek bájt részlegesen gyorsítótárazott tartalom.

A képlet: (TCP_ TALÁLATI / (TCP_ TALÁLAT + TCP_MISS)) * 100

![Gyorsítótár találati arány jelentés](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4-/ IPV6 esetén az átvitt adatmennyiség
Ez a jelentés az IPV4 és IPv6-alapú forgalom használati eloszlását mutatja.

![IPv4-/ IPV6 esetén az átvitt adatmennyiség](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az utolsó 18 hónapon belül hozható létre.

