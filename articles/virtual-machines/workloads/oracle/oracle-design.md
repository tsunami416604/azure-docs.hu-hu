---
title: Oracle-adatbázis tervezése és megvalósítása az Azure-ban | Microsoft Docs
description: Egy Oracle-adatbázis megtervezése és megvalósítása az Azure-környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rgardler
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogardle
ms.openlocfilehash: b553256d3e6a498e36e8b5c98d90c6c14b10df75
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224570"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Oracle-adatbázis tervezése és implementálása az Azure-ban

## <a name="assumptions"></a>Feltételezések

- Egy Oracle-adatbázist szeretne áttelepíteni a helyszínről az Azure-ba.
- Az áttelepíteni kívánt Oracle Databasehoz tartozó [diagnosztikai csomaggal](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) rendelkezik
- Megértette az Oracle AWR jelentéseinek különböző mérőszámait.
- Az alkalmazások teljesítményének és a platform kihasználtságának alapvető ismerete.

## <a name="goals"></a>Célok

- Ismerje meg, hogyan optimalizálhatja Oracle-alapú üzembe helyezését az Azure-ban.
- Ismerje meg az Oracle-adatbázisok teljesítmény-hangolási beállításait egy Azure-környezetben.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>A helyszíni és az Azure-implementáció közötti különbségek 

Az alábbiakban néhány fontos dolgot figyelembe kell venni, amikor helyszíni alkalmazásokat telepít át az Azure-ba. 

Az egyik fontos különbség az, hogy egy Azure-implementációban az erőforrások, például a virtuális gépek, a lemezek és a virtuális hálózatok megoszthatók a többi ügyfél között. Emellett az erőforrások a követelmények alapján is szabályozható. Ahelyett, hogy a sikertelenség elkerülésére (MTBF) koncentráljon, az Azure sokkal inkább a sikertelenség (MTTR) túlélésére koncentrál.

Az alábbi táblázat a helyszíni megvalósítás és az Oracle-adatbázisok Azure-beli megvalósítása közötti különbségeket ismerteti.

> 
> |  | **Helyszíni megvalósítás** | **Azure-implementáció** |
> | --- | --- | --- |
> | **Hálózat** |LAN/WAN  |SDN (szoftveresen definiált hálózat)|
> | **Biztonsági csoport** |IP-/portokra vonatkozó korlátozási eszközök |[Hálózati biztonsági csoport (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Rugalmasság** |MTBF (a hibák közötti átlagos idő) |MTTR (a helyreállítás átlagos ideje)|
> | **Tervezett karbantartás** |Javítások/frissítések|[Rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (az Azure által kezelt javítások/frissítések) |
> | **Erőforrás** |Dedikált  |Megosztva más ügyfelekkel|
> | **Régiók** |Adatközpontok |[Régiópárok](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Storage-fiók** |SAN/fizikai lemezek |[Azure által felügyelt tároló](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Méretezés** |Függőleges skála |Horizontális skálázhatóság|


### <a name="requirements"></a>Követelmények

- Határozza meg az adatbázis méretét és a növekedési arányt.
- Határozza meg a IOPS vonatkozó követelményeket, amelyeket az Oracle AWR jelentései vagy más hálózati figyelési eszközök alapján lehet megbecsülni.

## <a name="configuration-options"></a>Beállítási lehetőségek

Négy lehetséges terület közül választhat, amelyek segítségével javíthatja a teljesítményt egy Azure-környezetben:

- Virtuális gép mérete
- Hálózati átviteli sebesség
- Lemezek típusai és konfigurációi
- Lemezgyorsítótár beállításai

### <a name="generate-an-awr-report"></a>AWR-jelentés létrehozása

Ha már rendelkezik egy Oracle-adatbázissal, és azt tervezi, hogy áttelepíti az Azure-ba, több lehetősége is van. Ha rendelkezik az Oracle-példányok [diagnosztikai csomagjával](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) , futtathatja az Oracle AWR-jelentést a metrikák (IOPS, Mbps, GiBs stb.) lekéréséhez. Ezután válassza ki a virtuális gépet a gyűjtött mérőszámok alapján. Vagy kapcsolatba léphet az infrastruktúra-csapatával, hogy hasonló információkat kapjon.

Érdemes lehet a AWR-jelentést a normál és a maximális számítási feladatokhoz is futtatni, hogy össze lehessen hasonlítani. Ezen jelentések alapján a virtuális gépeket az átlagos munkaterhelés vagy a maximális munkaterhelés alapján lehet méretezni.

Az alábbi példa egy AWR-jelentés létrehozását mutatja be (AWR-jelentések létrehozása az Oracle Enterprise Manager használatával, ha a jelenlegi telepítése rendelkezik ilyennel):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Fő metrikák

A következő mérőszámok olvashatók be a AWR jelentésből:

- Magok száma összesen
- PROCESSZOR órajele
- Teljes memória GB-ban
- Processzorhasználat
- Maximális adatátviteli sebesség
- I/O-változások aránya (olvasás/írás)
- A naplózási sebesség (MB/s) visszaállítása
- Hálózati átviteli sebesség
- Hálózati késési arány (alacsony/magas)
- Adatbázis mérete GB-ban
- Az SQL * net-kiszolgálóról/ügyfélről fogadott bájtok száma

### <a name="virtual-machine-size"></a>Virtuális gép mérete

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. a virtuális gép méretének becslése a AWR-jelentés CPU-, memória-és I/O-használata alapján

Az egyik dolog, amit megnézhet, az első öt időzített esemény, amely a rendszer szűk keresztmetszetének helyét jelzi.

Az alábbi ábrán például a naplófájl szinkronizálása felül van. Azt jelzi, hogy hány várakozásra van szükség, mielőtt a LGWR beírja a log puffert az újra naplófájlba. Ezek az eredmények azt jelzik, hogy a tárolók vagy lemezek jobb teljesítményére van szükség. Emellett a diagram a CPU (magok) számát és a memória mennyiségét is megjeleníti.

![A AWR-jelentés oldalának képernyőképe](./media/oracle-design/cpu_memory_info.png)

Az alábbi ábrán az olvasási és írási műveletek teljes I/O-értéke látható. A jelentés ideje alatt 59 GB olvasható és 247,3 GB volt.

![A AWR-jelentés oldalának képernyőképe](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Válasszon egy virtuális gépet

A AWR jelentésből összegyűjtött információk alapján a következő lépés egy hasonló méretű virtuális gép kiválasztása, amely megfelel az igényeinek. A rendelkezésre álló virtuális gépek listáját a cikk a [memória optimalizált](../../linux/sizes-memory.md)verziójában találja.

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. a virtuális gép méretezése a ACU alapuló hasonló virtuálisgép-sorozattal

A virtuális gép kiválasztása után figyeljen a virtuális gép ACU. Választhat egy másik virtuális gépet is a ACU érték alapján, amely jobban megfelel a követelményeinek. További információ: [Azure számítási egység](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Képernyőfelvétel a ACU-egységek oldalról](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Hálózati átviteli sebesség

Az alábbi ábrán az átviteli sebesség és a IOPS közötti kapcsolat látható:

![Az átviteli sebesség képernyőképe](./media/oracle-design/throughput.png)

A teljes hálózati átviteli sebesség a következő információk alapján becsülhető meg:
- SQL * net-forgalom
- MBps x kiszolgálók száma (kimenő adatfolyam, például Oracle-adatőr)
- Egyéb tényezők, például az alkalmazás replikálása

![Az SQL * net átviteli sebességének képernyőképe](./media/oracle-design/sqlnet_info.png)

A hálózati sávszélességre vonatkozó követelmények alapján különböző típusú átjárók közül választhat. Ezek közé tartoznak az alapszintű, a VpnGw és az Azure ExpressRoute. További információt a [VPN Gateway díjszabási oldalán](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)talál.

**Javaslatok**

- A hálózati késés magasabb a helyszíni üzembe helyezéshez képest. A hálózati utak csökkentése jelentősen növelheti a teljesítményt.
- Az egyidejű váltás csökkentése érdekében a nagy tranzakciós vagy "Csevegő" alkalmazásokkal rendelkező alkalmazások összevonása ugyanazon a virtuális gépen.
- A gyorsabb hálózati teljesítmény érdekében a Virtual Machinest [gyorsított hálózatkezeléssel](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) használhatja.
- Bizonyos Linux-disztribúciók esetében érdemes lehet a [Trim/leképezésének megszüntetése támogatásának](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support)engedélyezése.
- Telepítse az [Oracle Enterprise Managert](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) egy különálló virtuális gépre.
- Alapértelmezés szerint a hatalmas lapok nincsenek engedélyezve a Linuxon. Érdemes lehet hatalmas lapokat engedélyezni és beállítani `use_large_pages = ONLY` a Oracle db. Ez segíthet a teljesítmény növelésében. További információt [itt](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)találhat.

### <a name="disk-types-and-configurations"></a>Lemezek típusai és konfigurációi

- *Alapértelmezett operációsrendszer-lemezek*: ezek a lemezek állandó adatmennyiséget és gyorsítótárazást biztosítanak. A rendszer indításkor optimalizált operációsrendszer-hozzáférésre, és nem a tranzakciós vagy adatraktár (analitikus) számítási feladatokhoz lett tervezve.

- Nem *felügyelt lemezek*: ilyen típusú lemezekkel kezelheti a virtuálisgép-lemezeknek megfelelő VHD-fájlokat tároló Storage-fiókokat. A VHD-fájlok blobként tárolódnak az Azure Storage-fiókokban.

- *Felügyelt lemezek*: az Azure kezeli a virtuálisgép-lemezekhez használt Storage-fiókokat. Adja meg a lemez típusát (prémium vagy standard) és a szükséges lemez méretét. Az Azure létrehozza és kezeli a lemezt.

- *Prémium szintű Storage-lemezek*: ezek a lemezek a legmegfelelőbbek az éles számítási feladatokhoz. A Premium Storage olyan virtuálisgép-lemezeket támogat, amelyek adott méretű virtuális gépekhez, például a DS, a DSv2, a GS és az F sorozatú virtuális gépekhez csatlakoztathatók. A prémium szintű lemez különböző méretekkel rendelkezik, és az 32 GB és 4 096 GB közötti hosszúságú lemezek közül választhat. Mindegyik lemez mérete saját teljesítményre vonatkozó előírásokkal rendelkezik. Az alkalmazás követelményeitől függően egy vagy több lemezt is csatolhat a virtuális géphez.

Amikor új felügyelt lemezt hoz létre a portálról, kiválaszthatja a **használni** kívánt lemez típusát. Ne feledje, hogy az összes rendelkezésre álló lemez nem jelenik meg a legördülő menüben. Egy adott virtuálisgép-méret kiválasztása után a menü csak a virtuális gép méretétől függően elérhető Premium Storage SKU-t jeleníti meg.

![A felügyelt lemez oldalának képernyőképe](./media/oracle-design/premium_disk01.png)

Miután konfigurálta a tárolót egy virtuális gépen, érdemes lehet betölteni a lemezek tesztelését az adatbázis létrehozása előtt. Az I/O-sebesség a késés és az átviteli sebesség tekintetében való ismerete segíthet megállapítani, hogy a virtuális gépek támogatják-e a várt átviteli sebességet a késési célokkal.

Számos eszköz használható az alkalmazások terhelésének tesztelésére, például az Oracle Orion, a Sysbench és a Fio.

Futtassa újra a terhelési tesztet, miután telepített egy Oracle-adatbázist. Indítsa el a normál és a legmagasabb munkaterhelést, és az eredmények megmutatják a környezet alapkonfigurációját.

Előfordulhat, hogy a tárolási méret helyett a IOPS arány alapján kell méreteznie a tárterületet. Ha például a szükséges IOPS értéke 5 000, de csak 200 GB szükséges, akkor is előfordulhat, hogy a P30 osztály prémium lemeze még akkor is elérhető, ha több mint 200 GB tárterületet tartalmaz.

A IOPS arány a AWR jelentésből szerezhető be. Ezt a rendszer a visszaállítási napló, a fizikai olvasások és az írási arány alapján határozza meg.

![A AWR-jelentés oldalának képernyőképe](./media/oracle-design/awr_report.png)

Például az ismétlés mérete 12 200 000 bájt/másodperc, ami egyenlő a 11,63 MBPs-val.
A IOPS a 12 200 000/2 358 = 5 174.

Az I/O-követelmények egyértelmű képe után kiválaszthatja a követelmények teljesítéséhez legmegfelelőbb meghajtók kombinációját.

**Javaslatok**

- Az adatkezelők esetében a felügyelt tároló vagy az Oracle ASM használatával vigye át az I/O-munkaterhelést több lemezre.
- Ahogy az I/O-blokk mérete növekszik az olvasási és az írási igényű műveleteknél, további adatlemezeket adhat hozzá.
- Növelje a nagyméretű szekvenciális folyamatok blokkjának méretét.
- Az adattömörítés használatával csökkentheti az I/O-t (az adatmennyiség és az indexek esetében egyaránt).
- Válassza el a naplók, a rendszer és a temps elválasztását, és vonja vissza a TS-t külön adatlemezeken.
- Ne helyezzen minden alkalmazásfájl alapértelmezett operációsrendszer-lemezre (/dev/sda). Ezek a lemezek nem optimalizálták a virtuális gép rendszerindítási idejét, és előfordulhat, hogy nem biztosítanak jó teljesítményt az alkalmazás számára.
- Ha az M sorozatú virtuális gépeket Premium Storage-on használja, engedélyezze a [írásgyorsítót](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) a naplók megismétlése lemezre.

### <a name="disk-cache-settings"></a>Lemezgyorsítótár beállításai

A gazdagépek gyorsítótárazásának három lehetősége van:

- *Readonly*: a rendszer az összes kérést gyorsítótárazza a későbbi olvasási műveletekhez. Az összes írás közvetlenül az Azure Blob Storage-ban marad.

- *READWRITE*: ez egy "olvasásra előre" algoritmus. Az olvasási és írási műveleteket a rendszer gyorsítótárazza a későbbi olvasási műveletekhez. A nem írható írási írások megmaradnak a helyi gyorsítótárban. Emellett a legkisebb lemezterületet is biztosítja a könnyű munkaterhelések esetében. Ha a ReadWrite cache-t olyan alkalmazással használja, amely nem kezeli a szükséges adatmegőrzést, adatvesztést okozhat, ha a virtuális gép összeomlik.

- *Nincs* (letiltva): ennek a lehetőségnek a használatával megkerülheti a gyorsítótárat. A rendszer az összes adatlemezt áthelyezi az Azure Storage szolgáltatásba. Ez a módszer a legmagasabb I/o-sebességet biztosítja az I/O-igényes számítási feladatokhoz. A tranzakciós költségeket is figyelembe kell vennie.

**Javaslatok**

Az átviteli sebesség maximalizálása érdekében javasoljuk, hogy a **Nincs beállítást** használja a gazdagépek gyorsítótárazásához. Premium Storage esetében ne feledje, hogy az "akadályokat" le kell tiltania, ha a fájlrendszert a **readonly** vagy a **none** lehetőséggel csatlakoztatja. Frissítse az/etc/fstab fájlt az UUID használatával a lemezekre.

![A felügyelt lemez oldalának képernyőképe](./media/oracle-design/premium_disk02.png)

- OPERÁCIÓSRENDSZER-lemezek esetén használja az alapértelmezett **írási/olvasási** gyorsítótárazást.
- A rendszer, a TEMP és a visszavonás a **Nincs beállítást** használja a gyorsítótárazáshoz.
- Az adattároláshoz a **Nincs beállítást** használja a gyorsítótárazáshoz. Ha azonban az adatbázis írásvédett vagy olvasási igényű, használja a **csak olvasható** gyorsítótárazást.

Az adatlemez-beállítás mentése után a gazdagép-gyorsítótár beállítása nem módosítható, ha leválasztja a meghajtót az operációs rendszer szintjén, majd újracsatlakoztatja azt a módosítás után.

## <a name="security"></a>Biztonság

Az Azure-környezet beállítása és konfigurálása után a következő lépés a hálózat védelme. Íme néhány javaslat:

- *NSG házirend*: a NSG alhálózat vagy hálózati adapter használatával lehet definiálni. Egyszerűbben szabályozható a hozzáférés az alhálózat szintjén, mind a biztonság, mind a kényszerített útválasztás, például az alkalmazási tűzfalak esetében.

- *Jumpbox*: a biztonságosabb hozzáférés érdekében a rendszergazdáknak nem kell közvetlenül kapcsolódniuk az alkalmazás-szolgáltatáshoz vagy-adatbázishoz. A Jumpbox a felügyeleti gép és az Azure-erőforrások közötti adathordozóként használják.
![A Jumpbox-topológia oldalának képernyőképe](./media/oracle-design/jumpbox.png)

    A rendszergazda számítógép csak a Jumpbox számára biztosít IP-hozzáférésre korlátozott hozzáférést. A Jumpbox hozzáféréssel kell rendelkeznie az alkalmazáshoz és az adatbázishoz.

- *Magánhálózat* (alhálózatok): javasoljuk, hogy az Application Service-t és az adatbázist külön alhálózatokon, így a NSG szabályzat által beállítható jobb szabályozás.


## <a name="additional-reading"></a>További olvasnivaló

- [Oracle ASM konfigurálása](configure-oracle-asm.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Az Oracle Golden Gate konfigurálása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](../../linux/cli-samples.md)
