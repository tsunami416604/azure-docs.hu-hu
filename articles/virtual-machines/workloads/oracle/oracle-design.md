---
title: Oracle-adatbázis tervezése és megvalósítása az Azure-ban | Microsoft dokumentumok
description: Oracle-adatbázis tervezése és megvalósítása azure-környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: ad446180b3bd864c5b6df808e6e4efac7d6c1c65
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687540"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Oracle-adatbázis tervezése és megvalósítása az Azure-ban

## <a name="assumptions"></a>Feltételezések

- Azt tervezi, hogy áttelepíti az Oracle-adatbázist a helyszíni Azure-ból.
- Az áttelepíteni kívánt Oracle-adatbázis [diagnosztikai csomagjával rendelkezik](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm)
- Az Oracle AWR-jelentésekben ismeri a különböző mutatókat.
- Az alkalmazás teljesítményének és a platformkihasználtságnak az alapszintű ismerete.

## <a name="goals"></a>Célok

- Ismerje meg, hogyan optimalizálhatja az Oracle üzembe helyezését az Azure-ban.
- Fedezze fel az Oracle-adatbázisok teljesítményhangolási lehetőségeit egy Azure-környezetben.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>A helyszíni és az Azure-megvalósítás közötti különbségek 

Az alábbiakban néhány fontos dolgot kell szem előtt tartania, amikor helyszíni alkalmazásokat telepít át az Azure-ba. 

Az egyik fontos különbség az, hogy egy Azure-implementációban az erőforrások, például a virtuális gépek, a lemezek és a virtuális hálózatok meg vannak osztva más ügyfelek között. Emellett az erőforrások a követelmények alapján szabályozhatók. Ahelyett, hogy a sikertelenség elkerülésére összpontosítana, az Azure inkább a hiba (MTTR) túlélésére összpontosít.

Az alábbi táblázat néhány különbséget sorol fel a helyszíni megvalósítás és az Oracle-adatbázis Azure-megvalósítása között.

> 
> |  | **Helyszíni megvalósítás** | **Azure-implementáció** |
> | --- | --- | --- |
> | **Hálózat** |LAN/WAN  |SDN (szoftveresen definiált hálózat)|
> | **Biztonsági csoport** |IP/port korlátozó eszközök |[Hálózati biztonsági csoport (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Rugalmasság** |MTBF (a hibák közötti átlagos idő) |MTTR (átlagos felépülési idő)|
> | **Tervezett karbantartás** |Javítások/frissítések|[Rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (az Azure által kezelt javítások/frissítések) |
> | **Erőforrás** |Dedikált  |Más ügyfelekkel megosztva|
> | **Régiók** |Adatközpontok |[Régiópárok](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Storage** |SAN/fizikai lemezek |[Azure által felügyelt tárhely](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Méretezés** |Függőleges skála |Horizontális skálázhatóság|


### <a name="requirements"></a>Követelmények

- Határozza meg az adatbázis méretét és növekedési ütemét.
- Határozza meg az IOPS-követelményeket, amelyeket az Oracle AWR-jelentések vagy más hálózatfigyelő eszközök alapján megbecsülhet.

## <a name="configuration-options"></a>Beállítási lehetőségek

Négy lehetséges terület van, amelyeket az Azure-környezetben a teljesítmény javítása érdekében finomíthat:

- Virtuális gép mérete
- Hálózati átviteli-átmenő
- Lemeztípusok és -konfigurációk
- Lemezgyorsítótár beállításai

### <a name="generate-an-awr-report"></a>AWR-jelentés készítése

Ha rendelkezik egy Oracle-adatbázissal, és az Azure-ba való átutazást tervezi, számos lehetősége van. Ha rendelkezik az Oracle-példányok [diagnosztikai csomagjával,](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) futtathatja az Oracle AWR-jelentést a metrikák (IOPS, Mbps, GiBs és így tovább) lekérni. Ezután válassza ki a virtuális gép a mérőszámok alapján, hogy a begyűjtött. Vagy felveheti a kapcsolatot az infrastruktúra-csapat, hogy hasonló információkat.

Érdemes lehet az AWR-jelentést rendszeres és csúcsterhelések során is futtatni, hogy összehasonlíthassa. Ezek a jelentések alapján az átlagos munkaterhelés vagy a maximális munkaterhelés alapján méretezheti a virtuális gépeket.

Az alábbi példa bemutatja, hogyan hozhat létre AWR-jelentést (Az AWR-jelentések készítése az Oracle Enterprise Manager segítségével, ha az aktuális telepítésrendelkezik ilyenel):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Fő bb mérőszámok

Az AWR-jelentésből az alábbi mutatókat láthatja el:

- Magok teljes száma
- CPU órajel
- Teljes memória GB-ban
- Processzorhasználat
- Maximális adatátviteli sebesség
- Az I/O-változások mértéke (olvasás/írás)
- Naplósebesség ismétlése (MPP)
- Hálózati átviteli-átmenő
- Hálózati késési sebesség (alacsony/magas)
- Adatbázis mérete GB-ban
- SQL*Net-en keresztül fogadott bájtok az ügyféltől/ügyfélnek

### <a name="virtual-machine-size"></a>Virtuális gép mérete

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. A virtuális gép méretének becslése a CPU, a memória és az I/O-használat alapján az AWR-jelentésből

Egy dolog, amit lehet nézni az első öt időzített előtér események, amelyek jelzik, ahol a rendszer szűk keresztmetszetek vannak.

Az alábbi ábrán például a naplófájl szinkronizálása van felül. Azt jelzi, hogy hány várakozásra van szükség ahhoz, hogy az LGWR a naplópuffert a naplófájlba írja. Ezek az eredmények azt jelzik, hogy jobban teljesítő tárolóra vagy lemezekre van szükség. Ezenkívül a diagram a PROCESSZOR (magok) számát és a memória mennyiségét is mutatja.

![Képernyőkép az AWR jelentésoldaláról](./media/oracle-design/cpu_memory_info.png)

Az alábbi ábra az olvasás és írás teljes I/O-ját mutatja be. A jelentés ideje alatt 59 GB olvasásés 247,3 GB volt.

![Képernyőkép az AWR jelentésoldaláról](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Válasszon virtuális gép

Az AWR-jelentésből gyűjtött információk alapján a következő lépés egy hasonló méretű virtuális gép kiválasztása, amely megfelel az Ön igényeinek. Az elérhető virtuális gépek listáját a [Memória optimalizált](../../linux/sizes-memory.md)című cikkben találja.

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Finomítsa a virtuális gép méretezését egy hasonló vm sorozatalapján ACU

Miután kiválasztotta a virtuális gép, figyeljen a virtuális gép ACU.After you've chosen the VM, figyelni, hogy az ACU a virtuális gép. Előfordulhat, hogy egy másik virtuális gép alapján az ACU érték, amely jobban megfelel az igényeinek. További információ: [Azure compute unit](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Képernyőkép az ACU egységek lapról](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Hálózati átviteli-átmenő

Az alábbi ábra az átviteli és az IOPS közötti kapcsolatot mutatja be:

![Képernyőkép az átviteli hangról](./media/oracle-design/throughput.png)

A teljes hálózati átviteli átmenő t a következő információk alapján becsülik meg:
- SQL*Net forgalom
- Mb/s x kiszolgálók száma (kimenő adatfolyam, például Oracle Data Guard)
- Egyéb tényezők, például az alkalmazások replikációja

![Képernyőkép az SQL*Net átviteli fókarendszeréről](./media/oracle-design/sqlnet_info.png)

A hálózati sávszélesség-követelmények alapján különböző átjárótípusok közül választhat. Ezek közé tartozik az alapszintű, a VpnGw és az Azure ExpressRoute. További információt a [VPN-átjáró díjszabási lapján](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)talál.

**Javaslatok**

- A hálózati késés magasabb, mint a helyszíni központi telepítés. A hálózati oda-vissza utak csökkentése nagyban javíthatja a teljesítményt.
- Az oda-vissza utak csökkentése érdekében konszolidálja azokat az alkalmazásokat, amelyek nagy tranzakciókkal vagy "beszédes" alkalmazásokkal rendelkeznek ugyanazon a virtuális gépen.
- A jobb hálózati teljesítmény érdekében [használjon gyorsított hálózattal](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) rendelkező virtuális gépeket.
- Bizonyos Linux-disztibutiók esetében fontolja meg a [TRIM/UNMAP támogatás engedélyezését.](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support)
- Telepítse az [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) alkalmazást egy külön virtuális gépre.
- Hatalmas oldalak alapértelmezés szerint nincsenek engedélyezve linuxon. Fontolja meg a hatalmas `use_large_pages = ONLY` oldalak engedélyezését, és állítsa be az Oracle DB-t. Ez segíthet a teljesítmény növelésében. További információ [itt](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)található .

### <a name="disk-types-and-configurations"></a>Lemeztípusok és -konfigurációk

- *Alapértelmezett operációsrendszer-lemezek:* Ezek a lemeztípusok állandó adatokat és gyorsítótárazást kínálnak. Az operációs rendszer indításkori elérésére vannak optimalizálva, és nem tranzakciós vagy adattárház (analitikus) számítási feladatokhoz készültek.

- *Nem felügyelt lemezek:* Ezekkel a lemeztípusokkal kezelheti a virtuális merevlemez-fájlokat (VHD) tartalmazó tárfiókokat, amelyek megfelelnek a virtuális gép lemezeinek. VHD-fájlok at tárolják, mint a lap blobok az Azure storage-fiókokban.

- *Felügyelt lemezek:* Az Azure kezeli a virtuális gép lemezeihez használt tárfiókokat. Megadhatja a lemez típusát (prémium vagy normál) és a szükséges lemez méretét. Az Azure létrehozza és kezeli a lemezt.

- *Prémium szintű tárolólemezek:* Ezek a lemeztípusok a legalkalmasabbak az éles számítási feladatokhoz. A prémium szintű storage támogatja az adott méretsorozatú virtuális gépekhez , például a DS, a DSv2, a GS és az F sorozatú virtuális gépekhez csatlakoztatható virtuális gépeket. A prémium szintű lemez különböző méretű, és 32 GB és 4096 GB közötti lemezek közül választhat. Minden lemezméretnek saját teljesítményspecifikációja van. Az alkalmazás követelményeitől függően egy vagy több lemezt csatolhat a virtuális géphez.

Amikor új felügyelt lemezt hoz létre a portálról, kiválaszthatja a használni kívánt lemeztípus **fióktípusát.** Ne feledje, hogy nem minden elérhető lemez jelenik meg a legördülő menüben. Egy adott virtuális gép méretének kiválasztása után a menü csak a virtuális gép méretén alapuló rendelkezésre álló prémium szintű storage SK-kat jeleníti meg.

![Képernyőkép a felügyelt lemez lapról](./media/oracle-design/premium_disk01.png)

Miután konfigurálta a tárolót egy virtuális gépen, érdemes lehet betölteni a lemezeket az adatbázis létrehozása előtt. Az I/O-díj a késés és az átviteli sebesség szempontjából segíthet meghatározni, hogy a virtuális gépek támogatják-e a várt átviteli sebességet késési célokkal.

Számos eszköz az alkalmazás terhelésének teszteléséhez, például az Oracle Orion, a Sysbench és a Fio.

Futtassa újra a betöltési tesztet, miután telepítette az Oracle-adatbázist. Indítsa el a rendszeres és a csúcs számítási feladatok, és az eredmények azt mutatják, a környezet alapkonfigurációját.

Lehet, hogy fontosabb, hogy a tároló mérete alapján az IOPS-díj, nem pedig a tárolási méret. Például ha a szükséges IOPS 5000, de csak 200 GB-ra van szüksége, előfordulhat, hogy továbbra is megkapja a P30-osztályú prémium szintű lemezt, még akkor is, ha több mint 200 GB tárhellyel rendelkezik.

Az IOPS-ráta az AWR-jelentésből szerezhető be. Ezt az ismétlési napló, a fizikai olvasások és az írási arány határozza meg.

![Képernyőkép az AWR jelentésoldaláról](./media/oracle-design/awr_report.png)

Az ismétlés mérete például 12 200 000 bájt másodpercenként, ami 11,63 Mbps-nek felel meg.
Az IOPS 12 200 000 / 2358 = 5174.

Miután világos képet kap az I/O-követelményekről, kiválaszthatja az ezen követelményeknek leginkább megfelelő meghajtók kombinációját.

**Javaslatok**

- Az adattáblatér esetében az I/O-munkaterhelést több lemezre is elosztva felügyelt tároló vagy Oracle ASM használatával.
- Az I/O-blokk méretének növekedésével az olvasásigényes és írásigényes műveletek hez további adatlemezek et adhat hozzá.
- Növelje a blokkméretét a nagy szekvenciális folyamatokhoz.
- Az i/o-érték csökkentéséhez használjon adattömörítést (adat- és indexek esetén egyaránt).
- Külön ismétlési naplók, rendszer és hőmérséklet, és visszavonása TS külön adatlemezeken.
- Ne helyezzen alkalmazásfájlokat az alapértelmezett operációsrendszer-lemezekre (/dev/sda). Ezek a lemezek nincsenek optimalizálva a virtuális gép gyors rendszerindítási idejére, és előfordulhat, hogy nem nyújtanak jó teljesítményt az alkalmazás számára.
- Ha M-series virtuális gépeket használ prémium szintű tárhelyen, engedélyezze az [Írásgyorsítót](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) a naplózó lemez en.

### <a name="disk-cache-settings"></a>Lemezgyorsítótár beállításai

A gazdagép gyorsítótárazásának három lehetősége van:

- *Csak olvasás:* Minden kérelem gyorsítótárazva a jövőbeli olvasások. Az összes írási művelet közvetlenül az Azure Blob storage-ba van megőrződve.

- *ReadWrite*: Ez egy "read-ahead" algoritmus. Az olvasások és írások gyorsítótárba kerülnek a jövőbeli olvasásokhoz. A nem átíró írások először a helyi gyorsítótárban maradnak meg. Emellett a legalacsonyabb lemezkésést is biztosítja a könnyű munkaterhelések számára. ReadWrite cache használata egy olyan alkalmazással, amely nem kezeli a szükséges adatok at, adatvesztést okozhat, ha a virtuális gép összeomlik.

- *Nincs* (letiltva): Ezzel a beállítással megkerülheti a gyorsítótárat. Az összes adat lemezre kerül, és megmarad az Azure Storage-ba. Ez a módszer a legmagasabb I/O-díjat biztosítja az I/O-intenzív munkaterhelések esetén. A "tranzakciós költséget" is figyelembe kell vennie.

**Javaslatok**

Az átviteli szint maximalizálása érdekében azt javasoljuk, hogy kezdje a **Nincs** a gazdagép gyorsítótárazása. A prémium szintű tárolás esetén ne feledje, hogy le kell tiltania a "korlátokat", amikor a fájlrendszert a **ReadOnly** vagy a None beállítással **csatlakoztatja.** Frissítse az /etc/fstab fájlt az UUID azonosítóval a lemezekre.

![Képernyőkép a felügyelt lemez lapról](./media/oracle-design/premium_disk02.png)

- Operációs rendszer lemezei esetén használja az alapértelmezett **olvasási/írási** gyorsítótárazást.
- A SYSTEM, TEMP és UNDO esetében a **Nincs** a gyorsítótárazáshoz.
- Data esetén használja a **Nincs** parancsot a gyorsítótárazáshoz. Ha azonban az adatbázis írásvédett vagy írásigényes, csak **olvasható** gyorsítótárazást használjon.

Az adatlemez-beállítás mentése után csak akkor módosíthatja a gazdagyorsítótár-beállítást, ha leválassza a meghajtót az operációs rendszer szintjén, majd a módosítás után újra csatlakoztatja.

## <a name="security"></a>Biztonság

Miután beállította és konfigurálta az Azure-környezetet, a következő lépés a hálózat védelme. Íme néhány javaslat:

- *NSG-házirend*: Az NSG alhálózat vagy hálózati adapter definiálható. Egyszerűbb az alhálózati szinten szabályozni a hozzáférést, mind a biztonság, mind az alkalmazástűzfalak kényszerítése érdekében.

- *Jumpbox*: A biztonságosabb hozzáférés érdekében a rendszergazdák nem csatlakozhatnak közvetlenül az alkalmazásszolgáltatáshoz vagy adatbázishoz. A jumpbox a rendszergazdai gép és az Azure-erőforrások közötti adathordozóként használható.
![Képernyőkép a Jumpbox topológia lapról](./media/oracle-design/jumpbox.png)

    A rendszergazdai számítógép csak a jumpboxhoz biztosíthat korlátozott IP-hozzáférést. A jumpbox nak hozzáféréssel kell rendelkeznie az alkalmazáshoz és az adatbázishoz.

- *Magánhálózat* (alhálózatok): Azt javasoljuk, hogy az alkalmazásszolgáltatás és az adatbázis külön alhálózatokon, így az NSG-házirend jobb vezérlést állíthat be.


## <a name="additional-reading"></a>További olvasás

- [Oracle ASM konfigurálása](configure-oracle-asm.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Az Oracle Golden Gate konfigurálása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [Fedezze fel a virtuális gép üzembe helyezését az Azure CLI-mintákban](../../linux/cli-samples.md)
