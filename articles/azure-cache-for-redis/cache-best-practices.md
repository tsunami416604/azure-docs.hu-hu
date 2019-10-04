---
title: Ajánlott eljárások az Azure cache Redis-hez
description: Az ajánlott eljárások követésével megtudhatja, hogyan használhatja hatékonyan az Azure cache-t a Redis.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: 6ac4722c1253f97bfb8c232202e24a923c027edf
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018825"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Ajánlott eljárások az Azure cache Redis-hez 
Az ajánlott eljárások követésével maximalizálhatja az Azure cache teljesítményének és költséghatékony felhasználásának hatékonyságát a Redis-példány esetében.

## <a name="configuration-and-concepts"></a>Konfigurálás és fogalmak
 * **Használjon standard vagy prémium szintű üzemi rendszereket.**  Az alapszintű csomag egyetlen csomópontos rendszer, amely nem rendelkezik adatreplikálással, és nem biztosít SLA-t. Emellett használjon legalább egy C1 gyorsítótárat.  A C0 cache-gyorsítótárak egyszerű fejlesztési és tesztelési forgatókönyvekhez készültek, mivel közös CPU-mag, kevés memória és a "zajos szomszédok" problémái vannak.

 * **Ne feledje, hogy a Redis egy memóriában tárolt adattároló.**  [Ez a cikk az](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) adatvesztést okozó egyes forgatókönyveket ismerteti.

 * **Fejlessze a rendszerét úgy, hogy képes legyen kezelni a kapcsolatok visszavert adatait** [javítás és feladatátvétel miatt](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Konfigurálja a [maxmemory fenntartott beállítást](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , hogy javítsa** a rendszerrugalmasságot a memória nyomási körülményei között.  Ez a beállítás különösen fontos a nagy írási szintű munkaterhelések esetén, vagy ha nagyobb értékeket (100 KB) tárol a Redis.  Azt javasoljuk, hogy a gyorsítótár méretének 10%-ában kezdjen el, majd növelje a nagy mennyiségű írási terhelést. [Bizonyos szempontokat](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) az értékek kiválasztásakor talál.

 * **A Redis kisebb értékekkel működik a legjobban**, ezért érdemes lehet nagyobb mennyiségű kulcsot feldarabolni több kulcsra.  [Ebben a Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)-vitában néhány szempontot figyelembe kell venni, hogy alaposan meg kell fontolnia.  Olvassa el [ezt a cikket](cache-how-to-troubleshoot.md#large-requestresponse-size) egy olyan problémával kapcsolatban, amelyet nagy értékek okozhatnak.

 * **Keresse meg a gyorsítótár-példányt és az alkalmazást ugyanabban a régióban.**  Egy másik régióban lévő gyorsítótárhoz való csatlakozás jelentősen növelheti a késést, és csökkentheti a megbízhatóságot.  Habár az Azure-on kívülről is csatlakozhat, nem ajánlott *különösen a Redis gyorsítótárként*való használatakor.  Ha a Redis-t csak kulcs/érték tárolóként használja, akkor a késés nem lehet az elsődleges szempont. 

 * **Kapcsolatok** újrafelhasználása – az új kapcsolatok létrehozása költséges, és növeli a késést, így a lehető legnagyobb mértékben újrahasznosíthatja a kapcsolatokat. Ha úgy dönt, hogy új kapcsolatokat hoz létre, győződjön meg róla, hogy lezárta a régi kapcsolatokat (még a felügyelt memória nyelvein is, például a .NET vagy a Java esetében).

 * **Konfigurálja az ügyféloldali függvénytárat úgy, hogy legalább 15 másodperces *csatlakozási* időkorlátot használjon**, így a rendszeridőt még nagyobb CPU-feltételek mellett is csatlakozhat.  A kis kapcsolat időtúllépési értéke nem garantálja, hogy a kapcsolat az adott időkereten belül van-e.  Ha valami probléma merül fel (magas szintű ügyfél-CPU, magas kiszolgálói processzor stb.), akkor egy rövid kapcsolat időtúllépési értéke miatt sikertelen lesz a kapcsolódási kísérlet. Ez a viselkedés gyakran rosszabb helyzetet tesz lehetővé.  A rövidebb időtúllépések támogatása helyett a rendszer arra kényszeríti a rendszert, hogy indítsa újra a kapcsolódási kísérlet folyamatát, ami egy *csatlakozási > sikertelen >* újrapróbálkozási hurokhoz vezethet. Általánosságban azt javasoljuk, hogy a kapcsolat időkorlátját 15 másodperc vagy annál nagyobb értékre hagyja. Jobb, ha a kapcsolódási kísérletet 15 vagy 20 másodperc után nem sikerül elérni, mint hogy az csak az újrapróbálkozást követően gyorsan meghiúsuljon. Egy ilyen újrapróbálkozási hurok miatt a leállás tovább tart, mint ha a rendszer már csak hosszabb ideig tart.  
     > [!NOTE]
     > Ez az útmutató a kapcsolódási *kísérletre* vonatkozik, és nem kapcsolódik ahhoz az időponthoz, ameddig várni szeretne egy műveletre, például a Get vagy a Complete értékre.
 

 * **Kerülje a költséges parancsokat** – bizonyos Redis műveletek, például a [Keys parancs](https://redis.io/commands/keys) *nagyon* drágák, és el kell kerülni őket.  További információ [: a költséges parancsok](cache-how-to-troubleshoot.md#expensive-commands) ismertetése


 
## <a name="memory-management"></a>Memória kezelése
A Redis Server-példányon belül számos olyan dolgot kell megfontolni, amelyet érdemes figyelembe venni.  Íme néhány:

 * **Válasszon ki [](https://redis.io/topics/lru-cache) egy kizárási szabályzatot, amely az alkalmazáshoz használható.**  Az Azure Redis alapértelmezett házirendje a *felejtő LRU*, ami azt jelenti, hogy csak a TTL-értékkel rendelkező kulcsok jogosultak a kizárásra.  Ha egyetlen kulcs sem rendelkezik TTL-értékkel, akkor a rendszer nem távolítja el a kulcsokat.  Ha azt szeretné, hogy a rendszer bármilyen kulcsot kizárjon a memória nyomása alatt, érdemes megfontolnia a *allkeys-LRU* szabályzat használatát.

 * **Állítsa be a lejárati értéket a kulcsokra.**  Így a kulcsok proaktív módon lesznek eltávolítva, és nem kell várnia a memória terhelésére.  Ha a kizárás a memória nyomása miatt nem indul el, a kiszolgáló további terhelést okozhat.  További információkért tekintse meg a lejárati [](https://redis.io/commands/expire) és [ExpireAt](https://redis.io/commands/expireat) parancsok dokumentációját.
 
## <a name="client-library-specific-guidance"></a>Ügyféloldali függvénytár-specifikus útmutató
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java – melyik ügyfelet kell használnia?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Saláta (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net munkamenet-szolgáltató](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Mikor biztonságos az Újrapróbálkozás?
Sajnos nincs egyszerű válasz.  Minden alkalmazásnak el kell döntenie, hogy milyen műveleteket lehet újrapróbálni, és nem.  Az egyes műveletek különböző követelményekkel és a kulcsok közötti függőségekkel rendelkeznek.  Íme néhány dolog, amit érdemes figyelembe vennie:

 * Az ügyféloldali hibák akkor is beszerezhetők, ha a Redis sikeresen futtatta a futtatni kívánt parancsot.  Példa:
     - Az időtúllépés egy ügyféloldali fogalom.  Ha a művelet elérte a kiszolgálót, a kiszolgáló akkor is futtatja a parancsot, ha az ügyfél várakozik.  
     - Ha a szoftvercsatorna-kapcsolaton hiba történik, nem lehet tudni, hogy a művelet ténylegesen futott-e a kiszolgálón.  A hiba például akkor fordulhat elő, ha a kiszolgáló feldolgozza a kérést, de az ügyfél nem kapja meg a választ.
 *  Hogyan reagál az alkalmazás, ha véletlenül kétszer ugyanazt a műveletet futtatom?  Mi a teendő, ha az egész számot kétszer szeretném növelni, csak egyszer?  Az alkalmazásom ugyanarra a kulcsra ír több helyről?  Mi a teendő, ha az újrapróbálkozási logika felülírja az alkalmazás egy másik része által beállított értéket?

Ha szeretné tesztelni, hogyan működik a kód a hibák között, érdemes lehet az [Újraindítás funkciót](cache-administration.md#reboot)használni. Ez lehetővé teszi, hogy megtekintse, hogyan befolyásolja a kapcsolódási visszavertség az alkalmazást.

## <a name="performance-testing"></a>Teljesítménytesztelés
 * **Kezdje a használatával `redis-benchmark.exe`**  , és tapasztalja meg a lehetséges átviteli sebességet/késést a saját Perf-tesztek írása előtt.  Redis – a teljesítményteszt dokumentációja [itt található](https://redis.io/topics/benchmarks).  Vegye figyelembe, hogy a Redis-benchmark nem támogatja az SSL használatát, ezért a teszt futtatása előtt [engedélyeznie kell a nem SSL-portot a portálon](cache-configure.md#access-ports) .  [Itt található a Redis-benchmark. exe Windows-kompatibilis verziója.](https://github.com/MSOpenTech/redis/releases)
 * A teszteléshez használt ügyfél virtuális gépnek **ugyanabban a régióban** kell lennie, mint a Redis cache-példánynak.
 * **Azt javasoljuk** , hogy a Dv2 virtuálisgép-sorozatokat az ügyfélhez hasonlóan használja, mivel ezek a hardverek jobbak, és a lehető legjobb eredményeket fogják biztosítani.
 * Győződjön meg arról, hogy az ügyfél által használt virtuális gép rendelkezik **legalább annyi számítási és sávszélességgel* , mint a tesztelt gyorsítótár. 
 * **Engedélyezze a VRSS** az ügyfélszámítógépen, ha Windows rendszeren van.  [Részletekért lásd itt](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Példa PowerShell-parancsfájlra:
     >PowerShell-ExecutionPolicy korlátozás nélküli engedélyezés-NetAdapterRSS-Name (Get-NetAdapter). név 
     
 * **Érdemes lehet prémium szintű Redis-példányokat használni**.  Ezek a gyorsítótár-méretek jobb hálózati késést és átviteli sebességet biztosítanak, mivel a processzor és a hálózat számára is jobb hardveren futnak.
 
     > [!NOTE]
     > A megfigyelt teljesítmény eredményei [itt jelennek](cache-faq.md#azure-cache-for-redis-performance) meg a referenciában.   Ügyeljen arra is, hogy az SSL/TLS felvesz némi terhelést, így eltérő késést és/vagy átviteli sebességet érhet el, ha átviteli titkosítást használ.
 
### <a name="redis-benchmark-examples"></a>Redis – teljesítményteszt-példák
**Tesztelés előtti beállítás**: Ezzel előkészíti a gyorsítótár-példányt az alább felsorolt késési és átviteli sebességű tesztelési parancsokhoz szükséges adatokkal.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**A késés tesztelése**: Ez egy 1k hasznos adat használatával teszteli a GET kérelmeket.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Az átviteli sebesség tesztelése:** Ez egy 1k-adattartalommal rendelkező, folyamatban lévő GET kérelmeket használ.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
