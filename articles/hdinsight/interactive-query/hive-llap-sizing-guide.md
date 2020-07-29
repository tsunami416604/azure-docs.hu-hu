---
title: HDInsight interaktív lekérdezési fürt (LLAP) méretezési útmutatója
description: LLAP méretezési útmutató
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83664962"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Az Azure HDInsight interaktív lekérdezési fürt (kaptár LLAP) méretezési útmutatója

Ez a dokumentum ismerteti a HDInsight interaktív lekérdezési fürt (kaptár LLAP-fürt) méretezését egy tipikus számítási feladathoz, amely ésszerű teljesítmény elérését biztosítja. Vegye figyelembe, hogy az ebben a dokumentumban szereplő javaslatok általános irányelvek, és konkrét számítási feladatok szükségesek a   hangoláshoz.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Az Azure alapértelmezett virtuálisgép-típusai a HDInsight interaktív lekérdezési fürthöz (LLAP)**

| Csomópont típusa      | Példány | Méret     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB RAM, 400 GB SSD   |
| Feldolgozói   | **D14 v2**        | **16 vCPU, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB RAM, 40 GB SSD       |

***Megjegyzés: az összes ajánlott konfigurációs érték a D14 v2 típusú feldolgozó csomóponton alapul.***  

### <a name="configuration"></a>**Configuration**    
| Konfigurációs kulcs      | Javasolt érték  | Description |
| :---        |    :----:   | :---     |
| fonal. nodemanager. Resource. Memory – MB | 102400 (MB) | Teljes memória (MB-ban megadva) a csomóponton lévő összes fonal-tárolóhoz | 
| fonal. Scheduler. maximális kiosztása – MB | 102400 (MB) | Az RM-on lévő összes Container-kérelem maximális kiosztása (MB). Az ennél az értéknél nagyobb memória-kérelmek nem lépnek érvénybe |
| fonal. Scheduler. maximális – foglalás – virtuális mag | 12 |A CPU-magok maximális száma a Resource Manager összes tároló-kérelméhez. Az ennél az értéknél nagyobb kérelmek nem lépnek érvénybe. |
| fonal. nodemanager. Resource. CPU – virtuális mag | 12 | A tárolók számára kiosztható CPU-magok száma NodeManager. |
| fonál. Scheduler. Capacity. root. llap. Capacity | 80 (%) | A llap-várólista fonal-kapacitásának kiosztása  |
| Tez. am. Resource. Memory. MB | 4096 (MB) | A TEZ AppMaster által használandó memória mérete (MB) |
| kaptár. Kiszolgáló2. TEZ. Sessions. per. default. üzenetsor | <number_of_worker_nodes> |A kaptár. Kiszolgáló2. TEZ. default. Queues nevű üzenetsor-munkamenetek száma. Ez a szám a lekérdezési koordinátorok számának felel meg (TEZ AMs) |
| kaptár. TEZ. Container. size | 4096 (MB) | Megadott TEZ-tároló mérete (MB) |
| hive.llap.daemon.num.executors | 12 | Végrehajtók száma LLAP démonban | 
| kaptár. llap. IO. szálkészlet munkaszála belépett. size | 12 | Végrehajtók szál-készletének mérete |
| kaptár. llap. Daemon. fonal. Container. MB | 77824 (MB) | Az egyes LLAP-démonok által használt teljes memória (MB) (memória/démon)
| kaptár. llap. IO. Memory. size | 235520 (MB) | A gyorsítótár mérete (MB/LLAP) démon által megadott SSD-gyorsítótár engedélyezve |
| kaptár. Auto. convert. JOIN. noconditionaltask. size | 2048 (MB) | a memória mérete MB-ban a Térkép csatlakoztatásához |

### <a name="llap-daemon-size-estimations"></a>**LLAP démonok méretének becslése:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. a teljes fonal-memória kiosztásának meghatározása a csomóponton lévő összes tárolóhoz**    
Konfiguráció: ***fonal. nodemanager. Resource. Memory-MB***  

Ez az érték azt jelzi, hogy a memória maximális mérete MB-ban, amelyet a szálak tárolói használhatnak az egyes csomópontokon. A megadott értéknek kisebbnek kell lennie, mint a csomóponton lévő fizikai memória teljes mennyisége.   
Teljes memória a csomóponton lévő összes fonal-tárolónál = [teljes fizikai memória] – [memória az operációs rendszer + más szolgáltatások esetében]  
Állítsa ezt az értéket az elérhető RAM-méret ~ 90%-ában.  
A D14 v2 esetében az ajánlott érték **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. a memória maximális mennyiségének meghatározása a FONALas tárolóra vonatkozó kérelem esetében**  
Konfiguráció: ***fonal. Scheduler. maximális kiosztása – MB***

Ez az érték azt jelzi, hogy a Resource Manager összes tároló-kérelmének maximális kiosztása MB-ban. A megadott értéknél nagyobb memória-kérelmek nem lépnek érvénybe. A Resource Manager memóriában tárolhatja a tárolókat a *fonalak. Scheduler. minimum-kiosztási MB* -onként, és nem lépheti túl a fonalak által megadott méretet. *ütemező maximális kiosztása (MB*). A megadott érték nem lehet nagyobb, mint a *szálak. nodemanager. Resource. Memory-MB-* ban megadott csomópont összes tárolójának teljes megadott memóriája.    
A D14 v2 munkavégző csomópontok esetében az ajánlott érték **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. a virtuális mag maximális mennyiségének meghatározása a FONALas tárolóra vonatkozó kérelem esetében**  
Konfiguráció: ***fonal. Scheduler. maximális – foglalás – virtuális mag***  

Ez az érték jelzi a virtuális CPU-magok maximális számát a Resource Manager összes tárolóra vonatkozó kérelme esetében. Ennél az értéknél nagyobb virtuális mag kérése nem lép érvénybe. Ez a fonal-ütemező globális tulajdonsága. A LLAP Daemon-tároló esetében ez az érték az összes rendelkezésre álló virtuális mag 75%-ában állítható be. A fennmaradó 25%-ot le kell foglalni a munkavégző csomópontokon futó NodeManager, DataNode és más szolgáltatásokhoz.  
A LLAP Daemon-tárolóban 16 virtuális mag van a D14 v2 virtuális gépeken, és a 16 virtuális mag 75%-a is használható.  
A D14 v2 esetében az ajánlott érték **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. egyidejű lekérdezések száma**  
Konfiguráció: ***kaptár. Kiszolgáló2. TEZ. Sessions. per. default. üzenetsor***

Ez a konfigurációs érték határozza meg a párhuzamosan indítható TEZ-munkamenetek számát. Ezek a TEZ-munkamenetek a "kaptár. Kiszolgáló2. TEZ. default. Queues" által meghatározott várólistákhoz lesznek elindítva. A TEZ AMs (lekérdezési koordinátorok) számának felel meg. A munkavégző csomópontok számának meg kell egyeznie. Az AMs TEZ száma nagyobb lehet, mint a LLAP démon-csomópontok száma. A TEZ elsődleges feladata a lekérdezés végrehajtásának koordinálása és a lekérdezési terv töredékek kiosztása a megfelelő LLAP-démonokhoz a végrehajtáshoz. Ezt az értéket több LLAP Daemon-csomópont többszörösére is megtarthatja a nagyobb átviteli sebesség elérése érdekében.  

Az alapértelmezett HDInsight-fürt négy LLAP-démont futtat négy munkavégző csomóponton, ezért az ajánlott érték **4**.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. TEZ-tároló és TEZ-alkalmazás fő mérete**    
Konfiguráció: ***TEZ. am. Resource. Memory. MB, kaptár. TEZ. Container. size***  

*TEZ. am. Resource. Memory. MB* – meghatározza a TEZ alkalmazás fő méretét.  
Az ajánlott érték **4096 MB**.
   
*kaptár. TEZ. Container. size* – a TEZ-tárolóhoz megadott memória mennyiségét határozza meg. Ezt az értéket meg kell adni a fonal minimális mérete (*fonál. ütemező. minimális kiosztási MB*) és a fonalak maximális mérete (*fonal. ütemező, maximális kiosztási MB*) között. A LLAP démon végrehajtói ezt az értéket használják a memóriahasználat korlátozására a végrehajtón.  
Az ajánlott érték **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. LLAP-várólista kapacitásának kiosztása**   
Konfiguráció: ***fonal. Scheduler. Capacity. root. llap. Capacity***  

Ez az érték a llap-várólista számára megadott kapacitás százalékos arányát jelzi. A kapacitás kiosztása eltérő értékekkel rendelkezhet a különböző számítási feladatokhoz, attól függően, hogy a rendszer hogyan konfigurálja a szál-várólistákat. Ha a munkaterhelés írásvédett művelet, akkor a kapacitás 90%-ában kell működnie. Ha azonban a számítási feladat a felügyelt táblákat használó frissítési/törlési/egyesítési műveletek keveréke, akkor azt javasoljuk, hogy a llap-várólista kapacitásának 80%-át adja meg. A fennmaradó 20%-os kapacitást más feladatok is felhasználhatják, például a tömörítést, stb. a tárolók alapértelmezett várólistából való lefoglalására. Az alapértelmezett várólistán lévő feladatok nem fogják megfossni a FONALak erőforrásait.    
A D14v2 Worker-csomópontok esetében a llap-várólista javasolt értéke **80**.   
(ReadOnly számítási feladatokhoz akár 90-ig is növelhető.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP Daemon-tároló mérete**    
Konfiguráció: ***kaptár. llap. Daemon. fonal. Container. MB***  
   
A LLAP démon minden munkavégző csomóponton szál-tárolóként fut. A LLAP Daemon-tároló teljes memóriájának mérete az alábbi tényezőktől függ:    
*  A FONALak tárolójának mérete (fonál. Scheduler. minimum-kiosztási MB, fonal. Scheduler. maximális kiosztási MB, fonal. nodemanager. Resource. Memory-MB)
*  A csomóponton található AMs TEZ száma
*  A csomópont összes tárolója és a LLAP-várólista kapacitása számára konfigurált teljes memória  

Az TEZ Application Masters (TEZ AM) által igényelt memória a következőképpen számítható ki:.  
A HDInsight interaktív fürtök esetében alapértelmezés szerint egy TEZ, amely lekérdezési koordinátorként működik. Az AMs TEZ száma az egyidejű lekérdezések alapján konfigurálható.
Ajánlott, hogy TEZ 4 GB memóriával rendelkezzen.

Tez AM memória/csomópont = [ceil (TEZ száma/LLAP-démonok száma)] x [TEZ AM Container size]  
A D14 v2 esetében az alapértelmezett konfiguráció négy TEZ AMs-t és négy LLAP démon-csomópontot tartalmaz.  
Tez AM memória/node = (ceil (4/4) x 4 GB) = 4 GB

A LLAP-várólista számára rendelkezésre álló teljes memória a következő módon számítható ki: ez az érték a csomóponton lévő összes fonal-tároló számára elérhető memória teljes mennyiségétől (*fonal. nodemanager. Resource. Memory-MB*) és a LLAP-várólista számára konfigurált kapacitás (*fonál. Scheduler. Capacity. root. LLAP. Capacity*) függ.  
A LLAP-várólista teljes memóriája a munkavégző csomóponton = teljes memória az összes fonal-tároló számára a LLAP-várólista kapacitása x százalékában.  
A D14 v2 esetében ez az érték [100 GB x 0,80] = 80 GB.

A LLAP Daemon-tároló mérete a következőképpen számítható ki:

**LLAP Daemon-tároló mérete = [az LLAP-várólista számára elérhető teljes memória] – [TEZ AM memória/csomópont]**  
    
A D14 v2 Worker csomópontnál a HDI 4,0 – az ajánlott érték (80 GB-4 GB)) = **76 GB**   
(A HDI 3,6 esetében az ajánlott érték a **74 GB** , mert a Slider am esetében további ~ 2 GB-ot kell lefoglalni.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. a végrehajtók számának meghatározása LLAP démon alapján**  
Konfiguráció: ***hive.llap.daemon.num.executors***, ***kaptár. llap. IO. szálkészlet munkaszála belépett. size***

***hive.llap.daemon.num.executors***:   
Ez a konfiguráció szabályozza azon végrehajtók számát, akik a feladatokat párhuzamosan futtathatják LLAP Daemon-ban. Ez az érték a virtuális mag számától, a végrehajtó által megadott memória mennyiségétől és a LLAP démon számára elérhető teljes memória mennyiségétől függ. Általában azt szeretnénk, hogy ez az érték minél közelebb legyen a virtuális mag számához.
A D14 v2 virtuális gépeken 16 virtuális mag van. Az összes virtuális mag azonban nem végezhető el, mert más szolgáltatások, például a NodeManager, a DataNode, a metrikák monitorozása stb. is szükség van a rendelkezésre álló virtuális mag. 

Ha módosítania kell a végrehajtók számát, azt javasoljuk, hogy a *kaptár. TEZ. Container. size* utasításban megadott végrehajtónál 4 GB memóriát vegyen figyelembe, és ügyeljen arra, hogy az összes végrehajtó számára szükséges teljes memória ne haladja meg a LLAP Daemon-tároló számára elérhető teljes memóriát.  
Ezt az értéket az adott csomóponton elérhető teljes virtuális mag 75%-ában lehet konfigurálni.  
A D14 v2 esetében a javasolt érték a következő: (. 75 X 16) = **12**

***kaptár. llap. IO. szálkészlet munkaszála belépett. size***:   
Ez az érték határozza meg a végrehajtók szál-készletének méretét. Mivel a végrehajtók a megadott módon vannak rögzítve, a végrehajtók száma a LLAP démonnál megegyező lesz.   
A D14 v2 esetében az ajánlott érték **12**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. a LLAP Daemon-gyorsítótár méretének meghatározása**  
Konfiguráció: ***kaptár. llap. IO. Memory. size***

A LLAP Daemon-tároló memóriája a következő összetevőkből áll:
*  Fő helyiség
*  A végrehajtók által használt halom memória (xmx)
*  Memóriában tárolt gyorsítótár (a terhelés nélküli memória mérete, nem alkalmazható, ha az SSD-gyorsítótár engedélyezve van)
*  Memóriában tárolt gyorsítótár metaadatainak mérete (csak akkor alkalmazható, ha engedélyezve van az SSD-gyorsítótár)

**Belmagasság mérete**: Ez a méret a Java virtuális gép terhelésének (metaspace, szálak verem, GC-adatstruktúrák stb.) használatos memóriában tárolt részét jelzi. Ez a terhelés általában a halom méretének (xmx) 6%-a. A biztonságosabb oldalon ez az érték a LLAP teljes memória-méretének 6%-ában számítható ki.  
A D14 v2 esetében az ajánlott érték a ceil (76 GB x 0,06) ~ = **5 GB**.  

**Halom mérete (xmx)**: az összes végrehajtó számára elérhető halom memória mennyisége.
Halom teljes mérete = a végrehajtók száma x 4 GB  
A D14 v2 esetében ez az érték 12 x 4 GB = **48 GB**  

Ha az SSD-gyorsítótár le van tiltva, a memórián belüli gyorsítótár azon memória mennyisége, amely a LLAP Daemon-tároló méretétől számítva a belmagasság méretének és a halom méretének megkezdése után marad.

A gyorsítótár méretének kiszámítása eltérő, ha engedélyezve van az SSD-gyorsítótár.
A *kaptár. llap. IO. lefoglalása. mmap* = True beállítása lehetővé teszi az SSD gyorsítótárazást.
Ha engedélyezve van az SSD-gyorsítótár, a rendszer a memória egy részét fogja használni az SSD-gyorsítótár metaadatainak tárolására. A metaadatok a memóriában tárolódnak, és várhatóan az SSD-gyorsítótár méretének 10%-a.   
SSD-gyorsítótár memóriabeli metaadatainak mérete = [LLAP Daemon-tároló mérete]-[Head szoba + halom mérete]  
A D14 v2 esetében, a HDI 4,0, az SSD gyorsítótár memóriában tárolt metaadatainak mérete = [76 GB]-[5 GB + 48 GB] = **23 GB**  
A D14 v2 esetében, a HDI 3,6, az SSD gyorsítótár memóriában tárolt metaadatainak mérete = [76 GB]-[5 GB + 48 GB + 2 GB csúszka] = **21 GB**

Az SSD gyorsítótár metaadatainak tárolásához rendelkezésre álló memória mérete miatt a támogatott SSD-gyorsítótár mérete kiszámítható.  
Memóriabeli metaadatok mérete az SSD-gyorsítótárban = az SSD-gyorsítótár méretének 10%-a       
SSD-gyorsítótár mérete = memóriabeli metaadatok mérete az SSD-gyorsítótárhoz x 10  

A D14 v2 és a HDI 4,0 esetében az ajánlott SSD-gyorsítótár mérete = 23 GB x 10 = **230 GB**  
A D14 v2 és a HDI 3,6 esetében az ajánlott SSD-gyorsítótár mérete = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. a térképhez való csatlakozás memóriájának módosítása**   
Konfiguráció: ***kaptár. Auto. convert. JOIN. noconditionaltask. size***

Győződjön meg arról, hogy a (z) *struktúra. Auto. convert. JOIN. noconditionaltask* engedélyezve van a paraméter érvénybe léptetéséhez.
Ez a konfiguráció lehetővé teszi, hogy a felhasználó megadhatja a memóriában elférő táblázatok méretét a csatlakoztatáshoz. Ha az n-1 táblák vagy partíciók mérete nem haladja meg az n-Way illesztést, akkor a térképhez való csatlakozás nem lesz kiválasztva. Az LLAP-végrehajtó memória méretének kiszámításához a csatlakozás leképezéséhez az autoconvert küszöbértékét kell használni.
Az egyes végrehajtók 4 GB-nál több halomból álló méretet feltételeznek, de nem mindegyik lesz elérhető a csatlakoztatáshoz. A rendszer bizonyos halom memóriát használ a rendezési pufferek, a véletlenszerű pufferek, a kivonatoló táblák stb. más műveletek esetén is. Így a csatlakoztatáshoz a 4 GB-os memória 50%-át is megadhatja.  
Megjegyzés: ennek az értéknek szüksége lehet a számítási feladatokhoz megfelelő módosításokra. Az érték túl alacsonyra állítása nem használható az autoconvert funkció használata esetén. Ha túl magasra állítja a beállítást, a memóriához tartozó kivételek vagy a GC-felfüggesztések miatt nem lehet negatív teljesítményt okozni.  
A D14 v2 esetében 4 GB memóriával, a végrehajtónál javasolt az érték **2048 MB**-ra állítása.


#### <a name="next-steps"></a>**További lépések**
Ha ezek az értékek nem oldják meg a problémát, látogasson el az alábbiak egyikére...

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.  

* ##### <a name="other-references"></a>**Egyéb referenciák:**
  * [Egyéb LLAP tulajdonságainak konfigurálása](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [A kaptár-kiszolgáló halom méretének konfigurálása](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Csatlakoztatási memória méretezése a LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez-végrehajtó motor tulajdonságai](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [A kaptár LLAP](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
