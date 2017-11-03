---
title: "Verizon jelentések alapvető |} Microsoft Docs"
description: "Megtekintheti használati minták a CDN a következő jelentések segítségével: sávszélesség, adatokat továbbít, a találatok, gyorsítótár állapotok, gyorsítótári találati aránya, IPV4/IPV6 adatokat továbbít."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9ee0041061296e313b3372dce13b5b86b2369c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="core-reports-from-verizon"></a>Verizon Core jelentések

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon Core jelentések segítségével a kezelés portálon Verizon profilok, tekintse meg a használati minták a CDN-t az alábbi jelentéseket:

* Sávszélesség
* Átvitt adatok
* A találatok
* Gyorsítótár-állapotok
* Gyorsítótár találati aránya
* Átvitt adatok IPv4/IPV6

## <a name="accessing-verizon-core-reports"></a>Verizon Core jelentések használata
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **Analytics** lapra, és vigye a **Core jelentések** menü.  Kattintson a kívánt jelentést, a menüben.
   
    ![CDN management portal - Core jelentések menü](./media/cdn-reports/cdn-core-reports.png)

## <a name="bandwidth"></a>Sávszélesség
A sávszélesség a jelentés egy grafikonon és adattáblát, amely jelzi a sávszélesség-használat a HTTP és HTTPS adott idő alatt áll. A sávszélesség-használat megtekintheti az összes CDN POP vagy egy adott CDN POP-ra. Ez a jelentés lehetővé teszi a forgalom teljesítményt és a terjesztési megtekintését CDN POP MB/s mértékegységben között.

* Minden peremhálózati csomópont összes csomópontjának hálózati forgalom, vagy válasszon egy adott régióban csomópont a legördülő listából válassza ki.
* Válassza ki a dátumtartományt megtekintése a mai ezen a héten/e havi adatokat stb. vagy adjon meg egyéni dátumok, majd kattintson **Ugrás** való győződjön meg arról, hogy a kiválasztott frissül.
* Exportálhatja és az adatok letöltése mellett található excel lap ikonra kattintva **Ugrás**.

A jelentés 5 percenként frissül.

![Sávszélesség-jelentés](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Átvitt adatok
Ez a jelentés egy grafikon és adattáblát, a forgalom használati feltüntetett HTTP és HTTPS adott idő alatt áll. A forgalom használata az összes CDN POP vagy egy adott POP keresztül tekintheti meg. Ez a jelentés lehetővé teszi a forgalom teljesítményt és a terjesztési megtekintését között CDN POP GB-ban.

* Válassza ki az összes jegyzetet hálózati forgalom, vagy a legördülő listából válassza ki a régió/csomópont csomópontjaihoz peremhálózati.
* Válassza ki a dátumtartományt megtekintése a mai ezen a héten/e havi adatokat stb. vagy adjon meg egyéni dátumok, majd kattintson **Ugrás** való győződjön meg arról, hogy a kiválasztott frissül.
* Exportálhatja és az adatok letöltése mellett található excel lap ikonra kattintva **Ugrás**.

A jelentés 5 percenként frissül.

![Az átvitt adatmennyiség jelentés](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Találatok (állapotkód esetében)
Ez a jelentés a kérelem állapotkódok a tartalom terjesztési ismerteti. Minden kérelemnél tartalom HTTP-állapotkódot állít elő. Az állapotkód: ismerteti, hogyan történik a peremhálózati POP a kérelem kezelése. Például egy 2xx állapotkód: azt jelzi, hogy a kérés sikeresen kiszolgálásának ügyfélnek, amíg egy 4xx állapotkód: azt jelzi, hogy hiba történt. További információ a HTTP-állapotkódok: [állapotkódok](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

* Válassza ki a dátumtartományt megtekintése a mai ezen a héten/e havi adatokat stb. vagy adjon meg egyéni dátumok, majd kattintson **Ugrás** való győződjön meg arról, hogy a kiválasztott frissül.
* Exportálhatja, és töltse le az adatok az excel-táblában található melletti **Ugrás**.

![A találatok jelentés](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Gyorsítótárak allapota
Ez a jelentés a terjesztési találatot eredményező gyorsítótárbeli kereséseinek és az ügyfél kérésében gyorsítótárbeli ismerteti. A leggyorsabb teljesítmény találatot eredményező gyorsítótárbeli kereséseinek származik, mert adatok kézbesítési sebességű minimalizálja a gyorsítótárbeli és lejárt találatok is optimalizálhatja. A forrás kiszolgálón, hogy ne rendelje hozzá a "no-cache" válaszfejlécek konfigurálásával, lehetőleg ne a lekérdezési karakterlánc gyorsítótárazás kivéve, ha feltétlenül szükséges és nem gyorsítótárazható válaszkódot elkerülésével gyorsítótárbeli csökkentheti. Lejárt találatok azáltal, hogy az eszköz maximális-kor, amíg az eredeti kiszolgálóra kérések száma minimalizálása érdekében a lehető elkerülése érdekében.

![Gyorsítótár-állapotok jelentés](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Fő gyorsítótár állapotok a következők:
* TCP_HIT: Kiszolgált szélétől. Az objektum a gyorsítótárban, és nem túllépte a maximális életkora.
* TCP_MISS: Kiszolgált a forrásból. Az objektum nem a gyorsítótárban, és a válasz vissza az eredeti volt.
* TCP_EXPIRED _MISS: kiszolgált forrásból eredete ismételt érvényesítése után. Az objektum a gyorsítótárban, de nagyobb a maximális életkora volt. A forrás egy ismételt érvényesítése a felváltja a forrásból új választ gyorsítótár-objektumának eredményezett.
* TCP_EXPIRED _HIT: kiszolgált szélétől eredete ismételt érvényesítése után. Az objektum a gyorsítótárban volt, de nagyobb a maximális életkora volt. A forrás-kiszolgálóval a ismételt érvényesítése a módosítani kívánt gyorsítótár-objektumának eredményezett.
* Válassza ki a dátumtartományt megtekintése a mai ezen a héten/e havi adatokat stb. vagy adjon meg egyéni dátumok, majd kattintson **Ugrás** való győződjön meg arról, hogy a kiválasztott frissül.
* Exportálhatja és az adatok letöltése mellett található excel lap ikonra kattintva **Ugrás**.

### <a name="full-list-of-cache-statuses"></a>Gyorsítótár-állapotok teljes listája
* TCP_HIT – amikor egy kérelem kiszolgált a POP-ről az ügyfél ezt az állapotot jelentett. Ha a POP legközelebb az ügyfél gyorsítótárában van, és egy érvényes idő-live (TTL) rendelkezik egy eszköz azonnal kiszolgált a POP. A következő válaszfejlécek TTL határozza meg:
  
  * A Cache-Control: s-maxage
  * A Cache-Control: maximális-kor
  * Lejár
* TCP_MISS - azt jelzi, hogy a kért eszköz gyorsítótárazott verzió nem található meg az ügyfél legközelebb POP. Az eszköz az eredeti kiszolgálóra vagy egy eredeti pajzs kiszolgálóra van szükség. Ha az eredeti kiszolgálóra vagy a forráskiszolgáló pajzs adja vissza egy eszköz, az ügyfélprogram és az ügyfél és a peremhálózati kiszolgáló gyorsítótárazza. Ellenkező esetben nem 200 állapotkódot (például 403 Tiltott vagy 404-es nem található) adja vissza.
* TCP_EXPIRED _HIT – Ha egy meglévő eszközt egy lejárt TTL megcélzó kérés kiszolgálásának a POP-ről, az ügyfél ezt az állapotot jelentett. Például, ha az eszköz tartozó maximális-életkora érvényessége lejárt. 
  
    Lejárt kérelmet általában annak az eredménye ismételt érvényesítése kérelemben az eredeti kiszolgálóra. A TCP_EXPIRED _HIT, hogy mi történjen a forrás kiszolgálónak jeleznie kell, hogy az eszköz egy újabb verziója nem létezik. Ez a helyzet általában az eszköz a Cache-Control és Expires fejléc frissítése eredményez.
* TCP_EXPIRED _MISS – amikor egy lejárt gyorsítótárazott eszköz egy újabb verziója az ügyfélnek a POP-ra a kiszolgált ezt az állapotot jelentett. Ez az állapot akkor fordul elő, ha egy gyorsítótárazott eszköz Élettartama lejárt (például lejárt maximális-életkora) és az eredeti kiszolgálóra adja vissza egy adott eszközre újabb verziója. Az eszköz ezen új verziójával kiszolgált az ügyfélnek a gyorsítótárazott verzió helyett. Emellett gyorsítótárazza a peremhálózati kiszolgáló és az ügyfélen.
* CONFIG_NOCACHE - azt jelzi, hogy egy ügyfél-konfigurációs az oldal POP miatt nem sikerült az eszköz a gyorsítótárba helyezésből.
* Nincs – Ez az állapot azt jelzi, hogy a tartalom frissesség ellenőrzése nem történt meg.
* TCP_ CLIENT_REFRESH _MISS – amikor egy HTTP-ügyfél, a böngészőbe, például arra kényszeríti a forráskiszolgálóról egy új verziója elavult eszköz beolvasandó POP él ezt az állapotot jelentett.
  
    Alapértelmezés szerint a kiszolgálóink megakadályozhatják, hogy egy HTTP-ügyfél kényszerítése a peremhálózati kiszolgálóinak beolvasása az eszköz új verziójának a forráskiszolgálóról.
* TCP_ PARTIAL_HIT - Ez az állapot jelent, ha a bájttartománykéréseket eredményezi a részlegesen gyorsítótárazott eszköz találat. A kért bájttartomány azonnal kiszolgált a a csatlakozási pont az ügyfélhez.
* UNCACHEABLE – Ha egy eszköz a Cache-Control és Expires fejléc jelzi, hogy azt nem gyorsítótárazza a POP- vagy HTTP-ügyfél által jelentett ezt az állapotot. Az ilyen típusú kérelmek szolgáltatott a forráskiszolgálóról.

## <a name="cache-hit-ratio"></a>Gyorsítótár találati aránya
Ez a jelentés azt jelzi, hogy közvetlenül a gyorsítótárból volt szolgáltatott gyorsítótárazott kérelem azon százaléka.

A jelentés tartalmazza a következő adatokat:

* A kért tartalom a legközelebbi igénylő POP gyorsítótárazva lett.
* A kérés kiszolgálásának közvetlenül a hálózati szélétől.
* A kérelem nem volt szükség a forrás-kiszolgálóval ismételt érvényesítése.

A jelentés nem tartalmaz:

* Ország szűrési beállítások miatt elutasított kérelmek.
* Eszközök fejlécekhez jelzi, hogy azok nem gyorsítótárazza a kérelmeket. Például a Cache-Control: saját, a Cache-Control: no-cache vagy Pragma: no-cache fejlécek egy eszköz megakadályozza a gyorsítótárba.
* Tartomány kérelmek bájt részlegesen gyorsítótárazott tartalom.

A képlet: (TCP_ TALÁLATI / (TCP_ TALÁLAT + TCP_MISS)) * 100

* Válassza ki a dátumtartományt megtekintése a mai ezen a héten/e havi adatokat stb. vagy adjon meg egyéni dátumok, majd kattintson **Ugrás** való győződjön meg arról, hogy a kiválasztott frissül.
* Exportálhatja és az adatok letöltése mellett található excel lap ikonra kattintva **Ugrás**.

![Gyorsítótár találati arány jelentés](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4-/ IPV6 esetén az átvitt adatmennyiség
Ez a jelentés az IPV4 és IPv6-alapú forgalom használati eloszlását mutatja.

![IPv4-/ IPV6 esetén az átvitt adatmennyiség](./media/cdn-reports/cdn-ipv4-ipv6.png)

* Jelölje ki a mai ezen a héten/e havi adatokat stb megtekintéséhez, vagy adjon meg egyéni dátumok dátumtartományt.
* Kattintson a **lépjen** való győződjön meg arról, hogy a kiválasztott frissül.

## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az utolsó 18 hónapon belül hozható létre.

