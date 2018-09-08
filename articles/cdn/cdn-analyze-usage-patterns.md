---
title: Alapvető jelentések a Verizontól |} A Microsoft Docs
description: 'Az alábbi jelentések használatával a CDN használati mintáinak is megtekintheti: a sávszélesség, átvitt adatok, a találatok, gyorsítótárak állapota, gyorsítótár találati aránya, IPV4/IPV6 átvitt adatok.'
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
ms.openlocfilehash: d10a40d03f0f76676e70afdec94e9adfaa0dd09f
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162070"
---
# <a name="core-reports-from-verizon"></a>Alapvető jelentések a Verizontól

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A Verizon alapvető jelentések a kezelés portálján a Verizon profilok, a CDN az alábbi jelentések a használati minták láthatja:

* Bandwidth
* Átvitt adatok
* Találatok
* Gyorsítótárak Allapota
* Gyorsítótárbeli találatok arányát
* Átvitt adatok IPv4/IPV6

## <a name="accessing-verizon-core-reports"></a>Verizon alapvető jelentések elérése
1. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![A CDN-profil felügyelet gombra](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **Analytics** lapfülre, majd mutasson a **alapvető jelentések** úszó menü. Kattintson a jelentés menüjében.
   
    ![CDN felügyeleti portálját – alapvető jelentések menüben](./media/cdn-reports/cdn-core-reports.png)

3. Az egyes jelentések esetében a dátumtartományok kijelölésével a **dátumtartomány** listája. Kiválaszthatja egy előre meghatározott dátumtartományt, mint például **Ma** vagy **ezen a héten**, vagy választhat **egyéni** és manuálisan adjon meg egy dátumtartományt naptár ikonokra kattintva. 

4. Miután kiválasztotta, hogy egy adott időtartományban, kattintson a **lépjen** a jelentés létrehozásához. 

4. Ha azt szeretné, exportálhatja az adatokat az Excel-formátumban, kattintson a fenti Excelben ikonra a **Go** gombra.

## <a name="bandwidth"></a>Bandwidth
A sávszélesség-jelentést a graph és az adatok táblázatként, amely azt jelzi, hogy a CDN sávszélesség-használat a HTTP és HTTPS egy adott idő alatt (Mbps) áll. A sávszélesség-használat összes POP vagy egy adott POP tekintheti meg. Ez a jelentés lehetővé teszi a forgalomnövekedések és a POP-disztribúciója megtekintését.

Az a **Élcsomópontok** listáról válassza ki **összes Élcsomópontok** , tekintse meg a forgalmat az összes csomóponton, vagy válasszon ki egy adott régióban.

A jelentés öt percenként frissül.

![A sávszélesség-jelentés](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Átvitt adatok
Ez a jelentés áll graph és az adatok táblázatként, amely azt jelzi, hogy a CDN-adatforgalom használati HTTP és HTTPS adott idő alatt, GB-ban. A forgalom használati összes POP vagy egy adott POP tekintheti meg. Ez a jelentés lehetővé teszi a forgalomnövekedések és a terjesztési megtekintését a POP-k között.

Az a **Élcsomópontok** listáról válassza ki **összes Élcsomópontok** , tekintse meg a forgalmat az összes csomóponton, vagy válasszon ki egy adott régióban.

A jelentés öt percenként frissül.

![A jelentés átvitt adatok](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Találatok száma (állapotkódok)
Ez a jelentés-tartalomra vonatkozó kérés állapotkódok ismerteti. Minden tartalom érkező HTTP-állapotkód állít elő. Az állapotkód: azt ismerteti, a kérelem Perembeli kezelésének módját. Például a 2xx állapotkódot azt jelzi, hogy a kérés sikeresen kiszolgálása ügyfélnek, amíg a 4xx típusú állapotkódot azt jelzi, hogy hiba történt. HTTP-állapotkódok kapcsolatos további információkért lásd: [lista a HTTP-állapotkódok](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![A találatok jelentés](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Gyorsítótárak allapota
Ez a jelentés találatot eredményező gyorsítótárbeli kereséseinek és az ügyféli kérelmek részére gyorsítótárbeli ismerteti. A leggyorsabb teljesítmény találatot eredményező gyorsítótárbeli kereséseinek eredménye, mert adatok kézbesítési megbízhatóbbak csökkenthetik a lejárt gyorsítótárbeli találatok és a gyorsítótárbeli optimalizálható. 

Gyorsítótárbeli csökkentése érdekében adja meg a forráskiszolgáló minimalizálása érdekében a következő használatát: 
 * `no-cache` Válaszfejlécek
 * Lekérdezési karakterlánc gyorsítótárazási, hacsak nem feltétlenül szükséges  
 * A nem gyorsítótárazható válaszkódok

Lejárt gyorsítótárbeli találatok csökkentése érdekében állítsa be az eszköz `max-age` a hosszabb ideig minimalizálása érdekében az eredeti kiszolgálóra vonatkozó kérelmek száma.

![Gyorsítótár-állapotok jelentés](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Fő gyorsítótárak állapota a következők:
* TCP_HIT: A peremhálózati kiszolgáló szolgáltatja. Az objektum a gyorsítótárban volt, és nem lépte túl a max-age.
* TCP_MISS: Szolgálja ki a forráskiszolgálóról. Az objektum nem volt a gyorsítótárban, és a válasz vissza a forrás.
* TCP_EXPIRED _MISS: forráskiszolgálóról után a forrás újraérvényesítésre szolgálja ki. Az objektum a gyorsítótárban volt, de a max-age nagyobb volt. A forrás egy újraérvényesítésre váltja fel egy új válasz forrásból gyorsítótár-objektumát eredményezett.
* TCP_EXPIRED _HIT: Edge után a forrás újraérvényesítésre szolgálja ki. Az objektum a gyorsítótárban, de a max-age nagyobb volt. Egy együtt a forráskiszolgáló újbóli ellenőrzése alatt álló kívánja módosítani a gyorsítótár-objektumát eredményezett.

### <a name="full-list-of-cache-statuses"></a>Gyorsítótárak allapota teljes listája
* TCP_HIT – amikor egy kérelem szolgáltatja közvetlenül a jelenléti pont az ügyfél ezt az állapotot jelentett. Az eszköz azonnal akkor szolgáltatja POP, ha a jelenléti pont az ügyfél legközelebb a rendszer gyorsítótárazza, és a egy érvényes idő-az-élettartam (TTL) rendelkezik. A következő válaszfejlécek TTL határozza meg:
  
  * A Cache-Control: s-maxage
  * A Cache-Control: max-age
  * Lejárat
* TCP_MISS: Ez az állapot azt jelzi, hogy a gyorsítótárazott verziót, a kért objektum nem található a legközelebbi az ügyfélnek a jelenléti pont. Az eszköz az eredeti kiszolgálóra vagy-forráskiszolgáló pajzs kérik. Ha a forráskiszolgáló és a forráskiszolgáló pajzs ad vissza egy eszköz, az ügyfélprogram és az ügyfél és a peremhálózati kiszolgáló gyorsítótárazza. Ellenkező esetben nem – 200-as állapotkódot (például 403 Tiltott vagy 404-es nem található) adja vissza.
* TCP_EXPIRED_HIT: Ezt az állapotot jelentett egy kérelmet, amely az eszköz egy lejárt TTL-célozza az ügyfél közvetlenül a jelenléti pontra Irányíthatja a kiszolgálása során. Például, ha az eszköz által a max-age lejárt. 
  
   Egy lejárt kérelem általában eredményez újraérvényesítésre kérelmet az eredeti kiszolgálóra. Egy TCP_EXPIRED_HIT állapot következik be a forráskiszolgáló jeleznie kell, hogy egy újabb verzióra, az eszköz nem létezik. Ez a helyzet általában egy frissítést, az eszköz Cache-Control és Expires fejlécet eredményez.
* TCP_EXPIRED_MISS: Ez az állapot rendszer jelenti, amikor egy lejárt gyorsítótárazott objektumot egy újabb verziója rendereléséhez a jelenléti pontra Irányíthatja a szükséges az ügyfél. Ez az állapot akkor jelentkezik, ha egy gyorsítótárazott objektum Élettartama lejárt (például lejárt max-age) és a forráskiszolgáló visszaküldi az adott eszköz egy újabb verzióra. Ez az eszköz új verziója helyett a gyorsítótárazott verziót az ügyfél biztosítja. Ezenkívül gyorsítótárazza a peremhálózati kiszolgáló és az ügyfélen.
* CONFIG_NOCACHE: Ez az állapot azt jelzi, hogy egy ügyfél-specifikus konfigurációs a jelenléti pont peremhálózati. Ebben az esetben az eszköz a gyorsítótárba.
* Nincs – Ez azt jelzi, hogy egy gyorsítótár-tartalom frissessége ellenőrzést nem lett végrehajtva.
* TCP_CLIENT_REFRESH_MISS: Ez az állapot jelent, ha egy HTTP-ügyfelet, például a böngésző arra kényszeríti az edge beolvasni egy elavult eszköz új verziója a forráskiszolgálóról a jelenléti pontra Irányíthatja. Alapértelmezés szerint a kiszolgálók megakadályozása egy HTTP-ügyfél kényszerítése a peremhálózati kiszolgálókon a forráskiszolgáló beolvasni az eszköz új verziója.
* TCP_PARTIAL_HIT: Ez az állapot a küldött bájt tartomány kérelem részlegesen gyorsítótárazott objektumhoz találat eredményez. A kért bájttartomány az ügyfél azonnal szolgáltatja a jelenléti pont.
* UNCACHEABLE: Ez az állapot jelent, ha egy eszköz `Cache-Control` és `Expires` fejlécek azt jelzik, hogy azt nem gyorsítótárazza a POP- vagy HTTP-ügyfél által. Az ilyen típusú kérelmeket szolgáltatás szolgálja ki a forráskiszolgálóról.

## <a name="cache-hit-ratio"></a>Gyorsítótárbeli találatok arányát
Ez a jelentés gyorsítótárazott, amely közvetlenül a gyorsítótárból is kiszolgált kérések arányát jelzi.

A jelentés a következő részleteket biztosítja:

* A kért tartalom gyorsítótárazva lett a legközelebb a kérelmező jelenléti pontra Irányíthatja.
* A kérés kiszolgálása közvetlenül a a hálózat peremén.
* A kérelem nem volt szükség újraérvényesítésre a forrás-kiszolgálóval.

A jelentés nem tartalmazza:

* Ország szűrési beállítások miatt elutasított kérelmek.
* Eszközök fejlécekhez azt jelzik, hogy azok nem gyorsítótárazza a kérelmeket. Ha például `Cache-Control: private`, `Cache-Control: no-cache`, vagy `Pragma: no-cache` fejlécek egy eszköz megakadályozza a gyorsítótárba.
* Bájt tartomány kérelmek részlegesen gyorsítótárazott tartalom.

A képlet a következő: (TCP_ TALÁLATI / (TCP_ TALÁLAT + TCP_MISS)) * 100

![Találati arány a jelentés gyorsítótárazása](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Átvitt adatok IPv4/IPV6
A jelentés a használati forgalomelosztás IPV4 és IPv6-alapú jeleníti meg.

![Átvitt adatok IPv4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az elmúlt 18 hónapban hozható létre.

