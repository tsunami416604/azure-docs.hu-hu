---
title: Ajánlott eljárások az Azure Cache for Redis használatához
description: Ismerje meg, hogyan használhatja az Azure-gyorsítótárat a Redis számára az alábbi gyakorlati tanácsok követésével.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478609"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Ajánlott eljárások az Azure Cache for Redis használatához 
Az ajánlott eljárások követésével maximalizálhatja az Azure-gyorsítótár a Redis-példány teljesítményét és költséghatékony használatát.

## <a name="configuration-and-concepts"></a>Konfiguráció és koncepciók
 * **Használja a standard vagy prémium szintű termelési rendszerek.**  Az alapszintű szint egy egycsomópontos rendszer, amelynek nincs adatreplikációja és SLA-ja. Ezen kívül használjon legalább C1 szintű gyorsítótárat.  A C0-gyorsítótárak egyszerű fejlesztési/tesztelési forgatókönyvekhez szolgálnak, mivel megosztott CPU-maggal, kevés memóriával rendelkeznek, és hajlamosak a "zajos szomszéd" problémákra.

 * **Ne feledje, hogy a Redis egy memórián belüli adattár.**  [Ez](cache-troubleshoot-data-loss.md) a cikk néhány olyan forgatókönyvet vázol fel, ahol adatvesztés fordulhat elő.

 * **Fejlessze a rendszert úgy, hogy a** [javítás és a feladatátvétel miatt](cache-failover.md)kezelni tudja a csatlakozási blips-et.

 * **Állítsa be a [maxmemory-fenntartott beállítást,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) hogy javítsa a rendszer válaszképességét** memórianyomás-körülmények között.  A megfelelő foglalási beállítás különösen fontos az írási nehéz számítási feladatok, vagy ha nagyobb értékeket (100 KB vagy több) a Redis tárolja. A gyorsítótár méretének 10%-ával kell kezdenie, és növelnie kell ezt a százalékot, ha írási terhelése van.

 * **A Redis kisebb értékekkel működik a legjobban,** ezért fontolja meg a nagyobb adatok több kulcsra történő feldarabolását.  Ebben [a Redis vita](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), néhány szempont szerepel, hogy meg kell fontolnia alaposan.  Olvassa el ezt a [cikket](cache-troubleshoot-client.md#large-request-or-response-size) egy példa probléma, amely által okozott nagy értékek.

 * **Keresse meg a gyorsítótár-példányt és az alkalmazást ugyanabban a régióban.**  Ha más régióban lévő gyorsítótárhoz csatlakozik, az jelentősen megnövelheti a késést, így a megoldás megbízhatósága is csökkenhet.  Bár az Azure-on kívülről is csatlakozhat, nem ajánlott, *különösen a Redis gyorsítótárként való használata esetén.*  Ha a Redis-t csak kulcs/értéktárolóként használja, előfordulhat, hogy nem a késés az elsődleges szempont. 

 * **Kapcsolatok újrafelhasználása.**  Az új kapcsolatok létrehozása költséges, és növeli a késést, ezért a lehető legnagyobb mértékben használja fel a kapcsolatokat. Ha új kapcsolatok at hoz létre, a feloldás előtt zárja be a régi kapcsolatokat (még a felügyelt memórianyelveken is, például .NET vagy Java nyelven).

 * **Állítsa be úgy az ügyfélműsortárat, hogy legalább 15 *másodperces csatlakozási időmeghosszabbítást* használjon,** így a rendszernek még magasabb cpu-körülmények között is lesz ideje csatlakozni.  Egy kis kapcsolat időtúllépési érték nem garantálja, hogy a kapcsolat ebben az időkeretben jön létre.  Ha valami elromlik (magas ügyfél-CPU, magas kiszolgálóprocesszor és így tovább), akkor egy rövid kapcsolati időtúllépési érték a csatlakozási kísérlet sikertelensítéséhez vezet. Ez a viselkedés gyakran teszi a rossz helyzet rosszabb.  Ahelyett, hogy segítenének, a rövidebb időmeghosszabbítások súlyosbítják a problémát azáltal, hogy a rendszert az újrakapcsolódási folyamat újraindítására kényszerítik, ami a *csatlakozás -> fail -> újrapróbálkozási* ciklushoz vezethet. Általában azt javasoljuk, hogy a kapcsolat időtúllépési idejét 15 másodpercvagy magasabb. Jobb, ha a csatlakozási kísérlet 15 vagy 20 másodperc után sikeres lesz, mint hogy gyorsan meghibásodjon, csak próbálja meg újra. Egy ilyen újrapróbálkozási ciklus a kimaradás hosszabb ideig tarthat, mint ha hagyja, hogy a rendszer kezdetben tovább tart.  
     > [!NOTE]
     > Ez az útmutató a *csatlakozási kísérletre* vonatkozik, és nem kapcsolódik ahhoz az időponthoz, amikor hajlandó várni egy *műveletre,* például a GET vagy a SET befejeződésre.
 
 * **Kerülje a költséges műveleteket** – Egyes Redis-műveletek, például a [KEYS](https://redis.io/commands/keys) *parancs, nagyon* drágák, és el kell kerülni.  További információt a [hosszú ideig futó parancsokkal](cache-troubleshoot-server.md#long-running-commands) kapcsolatos további tudnivalókban talál.

 * **TLS-titkosítás használata** – az Azure Cache for Redis alapértelmezés szerint TLS-titkosítást igényel.  A TLS 1.0-s, 1.1-es és 1.2-es verziója jelenleg támogatott.  A TLS 1.0 és az 1.1 azonban az egész iparágra kiterjedő eprecáció felé halad, ezért használja a TLS 1.2-t, ha egyáltalán lehetséges.  Ha az ügyféltár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése [az Azure Portalon](cache-configure.md#access-ports) vagy a felügyeleti [API-kon](https://docs.microsoft.com/rest/api/redis/redis/update)keresztül végezhető el.  Ilyen esetekben, amikor a titkosított kapcsolatok nem lehetséges, a gyorsítótár és az ügyfélalkalmazás virtuális hálózatba helyezése ajánlott lenne.  Arról, hogy mely portokat használja a rendszer a virtuális hálózati gyorsítótár-forgatókönyvben, tekintse meg ezt a [táblázatot.](cache-how-to-premium-vnet.md#outbound-port-requirements)
 
## <a name="memory-management"></a>Memóriakezelés
A Redis-kiszolgálópéldányon belüli memóriahasználattal kapcsolatban számos dolog rakható össze, amelyeket érdemes lehet figyelembe venni.  Íme néhány:

 * **Válasszon egy [kilakoltatási szabályzatot,](https://redis.io/topics/lru-cache) amely működik az alkalmazáshoz.**  Az Azure Redis alapértelmezett szabályzata *az illékony-lru,* ami azt jelenti, hogy csak a TTL-értékkészletű kulcsok jogosultak lesznek a kilakoltatásra.  Ha egyetlen kulcs sem rendelkezik TTL értékkel, akkor a rendszer nem oltja ki a kulcsokat.  Ha azt szeretné, hogy a rendszer lehetővé tegye a kulcs kilakoltatását, ha memórianyomás alatt van, akkor érdemes megfontolni az *allkeys-lru* házirendet.

 * **Állítson be egy lejárati értéket a kulcsokon.**  A lejárati adatok proaktív módon távolítják el a kulcsokat, ahelyett, hogy megvárnák a memórianyomásig.  Ha a kilakoltatás a memórianyomás miatt beindul, az további terhelést okozhat a kiszolgálón.  További információt a [LEJÁRT](https://redis.io/commands/expire) és [LEJÁRT ÉS LEJÁRT](https://redis.io/commands/expireat) PARANCSOK dokumentációjában talál.
 
## <a name="client-library-specific-guidance"></a>Ügyféltár-specifikus útmutatás
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Melyik ügyfelet használjam?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Saláta (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedik (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net munkamenet-állapotszolgáltató](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Mikor biztonságos újra próbálkozni?
Sajnos, nincs könnyű válasz.  Minden alkalmazásnak el kell döntenie, hogy mely műveletek kísérhetők meg újra, és melyek nem.  Minden művelet különböző követelményekkel és kulcsközi függőségekkel rendelkezik.  Íme néhány dolog, amit érdemes figyelembe venni:

 * Ügyféloldali hibákat kaphat annak ellenére, hogy a Redis sikeresen futtatta a parancsot, amelyet kért.  Példa:
     - Az időtúl-meghosszabbítás ügyféloldali fogalom.  Ha a művelet elérte a kiszolgálót, a kiszolgáló akkor is futtatja a parancsot, ha az ügyfél feladja a várakozást.  
     - Ha hiba történik a szoftvercsatorna-kapcsolaton, nem lehet tudni, hogy a művelet valóban futott-e a kiszolgálón.  A csatlakozási hiba például akkor fordulhat elő, ha a kiszolgáló feldolgozta a kérést, de még azelőtt, hogy az ügyfél megkapta volna a választ.
 *  Hogyan reagál az alkalmazásom, ha véletlenül kétszer futtatom ugyanazt a műveletet?  Például, mi van, ha egy egész szám kétszer növekszik, nem pedig egyszer?  Az alkalmazásom több helyről is ugyanarra a kulcsra ír?  Mi a teendő, ha az újrapróbálkozási logika felülír egy, az alkalmazás egy másik része által beállított értéket?

Ha szeretné tesztelni, hogyan működik a kód hibakörülmények között, fontolja meg az [Újraindítás funkciót.](cache-administration.md#reboot) Az újraindítás lehetővé teszi annak megtekintését, hogy a kapcsolati blips hogyan befolyásolja az alkalmazást.

## <a name="performance-testing"></a>Teljesítménytesztelés
 * **Kezdje azzal, `redis-benchmark.exe` ** hogy megérezte a lehetséges átviteli/késési, mielőtt megírja a saját perf tesztek.  Redis-benchmark dokumentáció [itt található](https://redis.io/topics/benchmarks).  Vegye figyelembe, hogy a redis-benchmark nem támogatja a TLS-t, ezért a teszt futtatása előtt engedélyeznie kell [a nem TLS-portot a portálon keresztül.](cache-configure.md#access-ports)  [A redis-benchmark.exe windows-kompatibilis verziója itt található.](https://github.com/MSOpenTech/redis/releases)
 * A teszteléshez használt ügyfél virtuális gépnek **ugyanabban a régióban** kell lennie, mint a Redis gyorsítótár-példánynak.
 * **Javasoljuk, hogy a Dv2 VM sorozat** az ügyfél, mivel azok jobb hardver, és a legjobb eredményt.
 * Győződjön meg arról, hogy a használt ügyfél virtuális gép **legalább annyi számítási és sávszélesség,* mint a vizsgált gyorsítótár. 
 * **Engedélyezze a VRSS-t** az ügyfélszámítógépen, ha Windows rendszert szeretne.  [Lásd itt a részleteket](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Példa powershell-parancsfájlra:
     >PowerShell -ExecutionPolicy Korlátlan enable-NetAdapterRSS -Name ( Get-NetAdapter). név 
     
 * **Fontolja meg a prémium szintű Redis-példányok használatát.**  Ezek a gyorsítótár-méretek jobb hálózati késést és átviteli csatornát, mert futnak a jobb hardver mind a CPU és a hálózat.
 
     > [!NOTE]
     > Megfigyelt teljesítményeredményeinket [itt tetszésünkre itt tesszük közzé.](cache-faq.md#azure-cache-for-redis-performance)   Azt is vegye figyelembe, hogy az SSL/TLS többletterhelést ad hozzá, így eltérő késéseket és/vagy átviteli értéket kaphat, ha átviteli titkosítást használ.
 
### <a name="redis-benchmark-examples"></a>Példák a Redis-Benchmark
**Teszt előtti beállítás:** Készítse elő a gyorsítótár-példányt az alábbiakban felsorolt késési és átviteli tesztelési parancsokhoz szükséges adatokkal.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**A késés tesztelése:** Tesztelje a GET-kérelmeket 1k hasznos adat használatával.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a-accesskey -t GET -d 1024 -P 50 -c 4

**Az átviteli eredmények tesztelése:** Futószalagos GET-kérelmek 1k hasznos adattal.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a-aAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
