---
title: Ajánlott eljárások az Azure Cache redis
description: Ismerje meg, hogyan használható az Azure Cache redis hatékonyan által ajánlott eljárások követésével.
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
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452461"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Ajánlott eljárások az Azure Cache redis 
Ajánlott eljárások követésével elősegítheti teljesítménnyel és költséghatékonysággal használhatja az Azure Cache a Redis-példányt.

## <a name="configuration-and-concepts"></a>Konfigurációs és fogalmak
 * **Éles rendszerek esetén. használja a Standard vagy prémium csomagra.**  Az alapszintű csomag egyetlen csomópont rendszer nincs adatreplikáció és nem biztosítunk szolgáltatói szerződést. Ezenkívül legalább C1 gyorsítótárak használatát.  C0 csomag gyorsítótárak egyszerű fejlesztési és tesztelési célra van kialakítva, mivel egy megosztott processzormaggal, kevés memóriával rendelkezik, és a során gyakran fordul elő, a "zajos szomszédok" problémák.

 * **Ne feledje, hogy a Redis egy memórián belüli adattár.**  [Ez a cikk](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) néhány forgatókönyvet, ahol előfordulhat adatvesztés ismerteti.

 * **A rendszer fejlesztése, hogy a kapcsolat jelekből képes kezelni** [javítása és a feladatátvétel miatt](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Konfigurálja a [maxmemory fenntartott beállítás](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) növelni a válaszkészséget rendszer** memória nyomás körülmények között.  Ez a beállítás különösen fontos az írási számítási feladatok, vagy ha redis nagyobb értékek (100 KB vagy több) tárolja.  E lenne javasoljuk, a gyorsítótár méretének 10 %-át, majd a növelése, ha írási terheléseket. Lásd: [néhány szempontot](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) kiválasztásakor az értéket.

 * **A kisebb értékek legjobb redis Cache működik**, ezért érdemes a nagyobb méretű adatok több kulcsokból darabolás.  A [Redis vitafórumban](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), néhány szempontot, hogy alaposan gondolja felsorolt.  Olvasási [Ez a cikk](cache-how-to-troubleshoot.md#large-requestresponse-size) egy példa a probléma, amely a nagy értékek okozhatja.

 * **Keresse meg a cache-példányt és az alkalmazás ugyanabban a régióban.**  Egy másik régióban lévő cache szolgáltatáshoz való csatlakozáskor is jelentősen növelheti a késést, és csökkentheti a megbízhatóságot.  Csatlakozhat Azure-on kívül, bár ez nem ajánlott *különösen akkor, ha a Redis gyorsítótár használata*.  Ha csak egy kulcs/érték tároló, a Redis használata esetén késés nem lehet az elsődleges szempont. 

 * **Újból felhasználhatja a kapcsolatok** – új kapcsolatok létrehozásának költséges, és növeli a késés, így újból felhasználhatja a lehető legnagyobb mértékben kapcsolatok. Ha úgy dönt, hogy az új kapcsolatok létrehozása, ügyeljen arra, hogy viselkednek őket (még az felügyelt memória nyelvet .NET vagy Java), zárja be a régi kapcsolatokat.

 * **Konfigurálja az ügyféloldali kódtár használatával egy *kapcsolat időtúllépése* legalább 15 másodperc**, így a rendszer pontos ideje szerinti való csatlakozáshoz még magasabb CPU-körülmények között.  Egy kis kapcsolódási időtúllépés értékét nem garantálja, hogy a kapcsolat létrejött-e a megadott idő alatt.  Ha valami probléma goes helytelen (ügyfél magas CPU, magas kiszolgáló Processzorát, és így tovább), majd rövid kapcsolat időtúllépési érték miatt a csatlakozási kísérlet meghiúsul. Ez a jelenség gyakran egy rossz helyzet még rosszabb teszi.  Ahelyett, hogy rövidebb időtúllépések kezdenie a folyamatot, próbálja újból csatlakozni, a rendszer kényszeríti a problémát, ami annak egy *kapcsolódás sikertelen -> újrapróbálkozási ->* ciklus. Általában javasoljuk, hogy hagyja-e 15 másodperc vagy annál újabb a kapcsolat időkorlátja. Jobb lehetővé teszik a kapcsolódási kísérlet, 15 vagy 20 másodperc múlva sikeres legyen, mint a, hogy gyorsan sikertelen csak az újrapróbálkozáshoz. Ilyen újrapróbálkozási hurokba került, mint ha hagyja, hogy a rendszer csak már kezdetben is hosszabb ideig a szolgáltatáskimaradás okozhatja.  
     > [!NOTE]
     > Ez az útmutató csak a *kapcsolódási kísérlet* , és nem hajlandó Várjon, amíg az időhöz kapcsolódó egy *művelet* például GET vagy a SET végrehajtásához.
 

 * **Elkerülheti a költséges parancsok** -néhány redis műveletek, például a [kulcsok parancsot](https://redis.io/commands/keys), vannak *nagyon* költséges és el kell kerülni.  További információkért lásd: [néhány szempontot körül igényesebb parancsokat](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Memória-kezelés
Néhány dolgot a Redis-kiszolgálópéldányra, érdemes lehet megfontolni memória-felhasználásról kapcsolatos.  Az alábbiakban néhány példa:

 * **Válasszon egy [kiürítési szabályzatot](https://redis.io/topics/lru-cache) , amely az alkalmazás működik.**  Az alapértelmezett házirend az Azure redis Cache *ideiglenes-lru*, ami azt jelenti, hogy csak azok a kulcsokat, amelyek Élettartamot érték beállítása kiürítési jogosultak lesznek.  Ha nincsenek kulcsok a TTL-érték, a rendszer nem zárja ki minden olyan kulcsokat.  Ha azt szeretné, hogy ha a rendelkezésre álló memória mennyisége kimaradásához bármelyik billentyűt a rendszer, akkor érdemes lehet megfontolni a *allkeys-lru* házirend.

 * **A lejárati idő értéke beállítani a kulcsokat.**  Ez a művelet eltávolítja a kulcsok proaktív módon megvárnia addig, amíg nincs rendelkezésre álló memória mennyisége.  A kizárási memóriaprobléma miatt jelentkezik, ha további terhelést okozhat a kiszolgálón.  További információkért lásd a dokumentációban a [adathalmazok elévülése](https://redis.io/commands/expire) és [ExpireAt](https://redis.io/commands/expireat) parancsokat.
 
## <a name="client-library-specific-guidance"></a>Ügyféloldali kódtár konkrét útmutatást
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - melyik ügyfél használjam?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Saláta (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net-munkamenetállapot-szolgáltatója](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Ha van, biztonságos az újrapróbálkozás?
Sajnos nincs egyszerű válasz.  Döntse el, milyen műveletek végrehajtásával lehet újrapróbálkozni, és amelyeket nem kell minden alkalmazáshoz.  Minden művelet eltérő követelmények és a kulcs közötti függőségek rendelkezik.  Az alábbiakban néhány dolgot, érdemes lehet:

 * Az ügyféloldali kérheti le annak ellenére, hogy a Redis sikeresen futtatta-e a parancs futtatását kéri.  Példa:
     - Időtúllépések egy ügyféloldali fogalom.  A művelet elérte a kiszolgálón, ha a kiszolgálón futtassa a parancsot, még akkor is, ha az ügyfél ad várakozási fel.  
     - Ha hiba lép fel a szoftvercsatorna-kapcsolatot, akkor sem lehet tudni, hogy ha a művelet ténylegesen a kiszolgálón futott.  Például a kapcsolódási hiba fordulhat elő a kiszolgálón, de mielőtt a válasz érkezik, az ügyfél által a kérelem feldolgozása után.
 *  Hogyan reagál a alkalmazásom a, ha ugyanazt a műveletet kétszer véletlenül futott?  Például mi történik, ha szeretnék növelni kétszer, helyett csak egyszer egész?  Az alkalmazásom írja az ugyanazzal a kulccsal több helyről?  Mi történik, ha a saját újrapróbálkozási logikát felülírja a többi része az alkalmazás által megadott értékre?

Ha szeretné tesztelni, hogyan működik a kódját hibaállapotok, fontolja meg a [újraindítás funkció](cache-administration.md#reboot). Ez lehetővé teszi, hogy milyen hatással vannak a kapcsolat jelekből az alkalmazás megtekintéséhez.

## <a name="performance-testing"></a>Teljesítménytesztelés
 * **Indítsa el a `redis-benchmark.exe`**  lehetséges átviteli sebesség/késésének betekintést nyerhet saját teljesítményoptimalizált írása előtt ellenőrzi.  A redis-teljesítményteszt dokumentáció lehet [itt található](http://redis.io/topics/benchmarks).  Vegye figyelembe, hogy a redis-teljesítményteszt nem támogatja az SSL, úgy kell [a portálon keresztül a nem SSL port engedélyezéséhez](cache-configure.md#access-ports) a teszt futtatása előtt.  [A redis-benchmark.exe windows kompatibilis verziója itt található](https://github.com/MSOpenTech/redis/releases)
 * A teszteléshez használt virtuális gép ügyfelét **ugyanabban a régióban** , a Redis cache-példányhoz.
 * **Javasoljuk a Virtuálisgép-sorozatok Dv2** az ügyfél jobb hardveres és a legjobb eredményt ad.
 * Ellenőrizze, hogy az ügyfél használata virtuális gép rendelkezik **legalább mennyi számítási és a sávszélesség* , a tesztelt gyorsítótár. 
 * **Engedélyezi a VRSS** az ügyfélszámítógépen, a Windows.  [Részleteket itt talál](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Példa a powershell parancsprogramra:
     >PowerShell - ExecutionPolicy Unrestricted Enable-NetAdapterRSS-név (Get-NetAdapter). név 
     
 * **Fontolja meg Premium szintű Redis-példány**.  A gyorsítótár-méretek lesz teljesítmény és jobban hálózati késés, mivel a Processzor és a hálózati jobb hardvereken futnak.
 
     > [!NOTE]
     > A megfigyelt teljesítményeredmények [itt közzétett](cache-faq.md#azure-cache-for-redis-performance) referenciaként.   Továbbá vegye figyelembe, hogy az SSL/TLS hozzáad némi többletterhelést okoz, így előfordulhat, hogy különböző késéseket és/vagy átviteli átviteltitkosítást használata.
 
### <a name="redis-benchmark-examples"></a>Példák a redis-teljesítményteszt
**Tesztelje a telepítő**: Ez készíti elő a gyorsítótárpéldány késésével és átviteli sebesség tesztelése az alábbi parancsokat a szükséges adatokkal.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Késés teszteléséhez**: Ez teszteli egy 1 KB hasznos GET-kérések.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Átviteli sebesség tesztelése:** Ez a vagyok GET kérelmeket 1 k hasznos adattal használja.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
