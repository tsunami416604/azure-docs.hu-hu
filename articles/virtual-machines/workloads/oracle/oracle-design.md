---
title: Tervezési és valósítsa meg az Oracle-adatbázishoz a Azure |} Microsoft Docs
description: Tervezése és megvalósítása az Oracle-adatbázishoz az Azure környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.openlocfilehash: 2661c386ea747fc75b67df9a67c7e62ac8c4fea4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658138"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Oracle-adatbázis tervezése és megvalósítása az Azure-ban

## <a name="assumptions"></a>Előfeltételek

- Arra készül, Oracle-adatbázishoz a helyszíni Azure telepíthetők át.
- A különböző metrikák ismeretét van Oracle AWR jelentésekben.
- Rendelkezik egy alapkonfiguráció ismeretekkel az alkalmazások teljesítményének és a platform felhasználását.

## <a name="goals"></a>Célok

- Megtudhatja, hogyan optimalizálható az Oracle-telepítés az Azure-ban.
- Fedezze fel teljesítményének hangolása beállításait az Azure környezetben Oracle-adatbázishoz.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Egy helyszíni közötti különbségek és Azure végrehajtása 

Az alábbiakban néhány fontos dolgot tartsa szem előtt, ha a helyszíni alkalmazások az Azure-ba, amelybe migrálna. 

Egy fontos különbség az, hogy az Azure-megvalósításban erőforrások, például a virtuális gépek, a lemezek és a virtuális hálózatok megosztott egymás között. Ezenkívül erőforrásokat is lehet szabályozva a követelmények alapján. Helyett összpontosító elkerülése sikertelenek lesznek (MTBF), Azure van, ahol inkább a még működő a hiba (MTTR).

Az alábbi táblázat egy helyszíni megvalósítás és az Oracle-adatbázishoz Azure megvalósítását közötti különbségek egy részét.

> 
> |  | **Helyszíni megvalósítás** | **Az Azure végrehajtása** |
> | --- | --- | --- |
> | **Hálózat** |LAN VAGY WAN  |SDN-alapú (szoftveresen meghatározott Hálózatkezelés)|
> | **Biztonsági csoport** |IP-/ port korlátozást eszközök |[Hálózati biztonsági csoport (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Rugalmasság** |MTBF (középidő hibák között) |MTTR (helyreállítási átlagos idő)|
> | **Tervezett karbantartás** |Javítás vagy frissítés|[Rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (javítás/frissítések Azure által kezelt) |
> | **Erőforrás** |Dedikált  |A más ügyfelekkel megosztott|
> | **Régiók** |Adatközpontok |[A régióban párok](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |SAN vagy fizikai lemezek |[Azure által kezelt tárolási](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Méretezés** |Függőleges méretezés |Horizontális skálázhatóság|


### <a name="requirements"></a>Követelmények

- Határozza meg az adatbázis méretére és növekedésére sebességét.
- Az IOPS követelmények, amelyek Oracle AWR jelentések vagy más hálózati eszközök figyelése alapján megbecsülheti meghatározása.

## <a name="configuration-options"></a>Konfigurációs beállítások

Nincsenek négy lehetséges területet észlelheti a környezetet az Azure a teljesítmény javítása érdekében:

- Virtuális gép mérete
- Hálózati teljesítmény
- Lemez legyen kijelölve, és konfigurációk
- Gyorsítótár-beállítások

### <a name="generate-an-awr-report"></a>Egy AWR jelentés készítése

Ha egy meglévő Oracle-adatbázishoz és tervez áttérni az Azure-ba, több lehetőség közül választhat. Futtathatja a Oracle AWR jelentést a metrikák (IOPS, MB/s, GiBs és így tovább). Válassza ki a virtuális gép az összegyűjtött metrikák alapján. Vagy felveheti a kapcsolatot a infrastruktúra csapatát hasonló információkat lekérni.

Érdemes lehet a AWR jelentés futtatása során rendszeres és a csúcsidőre munkaterhelések esetén, összehasonlítás. Ezek a jelentések alapján, a munkaterhelés átlagos vagy a maximális munkaterhelést alapuló virtuális gépek méretét.

Az alábbiakban látható egy példa bemutatja, hogyan egy AWR jelentést:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Alapvető metrikákat

Az alábbiakban a metrikák a AWR jelentés szerezhetők be:

- Magok száma összesen
- Órajelű Processzor
- Teljes memória GB-ban
- Processzorkihasználtság
- Maximális adatátviteli sebesség
- I/o-módosításokat (olvasás/írás) aránya
- Végezze el újra napló gyakorisága (MB/s)
- Hálózati teljesítmény
- Hálózati késés gyakorisága (alsó vagy felső)
- Adatbázis méretét GB-ban
- SQL keresztül fogadott bájtok * nettó vagy ügyfél

### <a name="virtual-machine-size"></a>Virtuális gép mérete

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Becsült Virtuálisgép-méretet a AWR jelentésből Processzor, memória és i/o-használata alapján

Egy dolog, előfordulhat, hogy megnézi a felső öt időzített előtér események, amelyek jelzik, amelyben a rendszer szűk keresztmetszetek is.

Például az alábbi ábra a napló fájlszinkronizálás szerepel a szabálylista. Azt jelzi, hogy vár, amelyek szükségesek ahhoz a LGWR a visszaállítási napló fájlba ír, a napló puffer száma. Ezekkel az eredményekkel jelzi, hogy jobban teljesítő tároló vagy lemezek szükségesek. Emellett az ábrán is látható a Processzor (magok) és a memória mennyiségét.

![A AWR jelentés oldalát bemutató képernyőkép](./media/oracle-design/cpu_memory_info.png)

Az alábbi ábrán látható az összes i/o-olvasási és írási. Nem voltak 59 GB olvasása és írása az időre, amíg a jelentés 247.3 GB.

![A AWR jelentés oldalát bemutató képernyőkép](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Válassza ki a virtuális gépek

A AWR jelentésből összegyűjtött információk alapján a következő lépés, hogy válassza körültekintően a vonatkozó követelményeknek megfelelő hasonló méretű virtuális gép. A cikk a rendelkezésre álló virtuális gépek listáját találja [Memóriaoptimalizált](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. A Virtuálisgép-méretezési Finomhangolja a ACU alapján hasonló virtuális gép több

Miután választott, hogy a virtuális Gépet, a ACU figyelmet fordítania a virtuális gép számára. Akkor célszerű használni egy másik virtuális gép igényeinek jobban megfelelő ACU értéke alapján. További információkért lásd: [Azure számítási egység](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![A ACU egységek oldalát bemutató képernyőkép](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Hálózati teljesítmény

Az alábbi ábrán látható, az átviteli sebesség és IOPS közötti kapcsolat:

![Képernyőkép a átviteli sebesség](./media/oracle-design/throughput.png)

Az összes hálózati átviteli becsült az alábbi információk alapján:
- Az SQL * forgalom Net
- MB/s x (például Oracle Data Guard kimenő adatfolyam) kiszolgálók száma
- Más tényezőktől, például az alkalmazás-replikáció

![Képernyőfelvétel az SQL * nettó átviteli sebesség](./media/oracle-design/sqlnet_info.png)

A hálózati sávszélesség követelmények alapján kiválaszthatja a különböző átjáró típusa van. Ezek közé tartozik a basic, VpnGw, és az Azure ExpressRoute. További információkért lásd: a [árképzést ismertető oldalra VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Javaslatok**

- Hálózati késés nagyobb összeveti egy helyszíni telepítéshez. Hálózati round való adatváltások számát is nagy mértékben csökkenti a teljesítmény javításához.
- Üzenetváltások számának csökkentése érdekében az alkalmazásokat, amelyek magas tranzakciók, illetve "chatty" alkalmazásokat az azonos virtuális gépen lévő egyesíteni.

### <a name="disk-types-and-configurations"></a>Lemez legyen kijelölve, és konfigurációk

- *Az operációs rendszer lemezek alapértelmezett*: ezek lemeztípusokkal ajánlat állandó adatok és a gyorsítótár. Az operációs rendszer hozzáférés indításkor vannak optimalizálva, és nem terveztek vagy tranzakciós vagy az adatraktár (analitikai) munkaterhelések.

- *Nem felügyelt lemezek*: az lemez ilyen tárolására a virtuális merevlemez (VHD), hogy a virtuális gépek lemezei storage-fiókok kezelése. VHD-fájlokat, az Azure storage-fiókok lapblobokat tárolódnak.

- *Által kezelt lemezeken*: Azure kezeli a storage-fiókok, amelyek a virtuális gép lemezeit használja. Megadhatja, hogy a lemez típusát (prémium és standard) és a mérete a lemez, amelyekre szüksége van. Azure hoz létre, és az Ön kezeli a lemezt.

- *Prémium szintű storage lemezek*: A lemez típusok a következők termelési számítási feladatokhoz ideális. Prémium szintű storage támogatja az adott mérete sorozatú virtuális gépekhez, például a DS, DSv2, GS és F adatsorozat virtuális gépek csatolt virtuális gépek lemezei. A prémium szintű lemez mérete eltér a, és választhat lemezeket beállításnak 32 GB és 4096 GB között. Minden lemez mérete a saját teljesítmény specifikációi. Attól függően, hogy az alkalmazás követelményeinek csatolhat egy vagy több lemezt a virtuális Gépet.

A portál egy új kezelt lemezt hoz létre, amikor a **fiók típusa** a használni kívánt lemez típusa. Ne feledje, hogy nem minden rendelkezésre álló lemezek jelennek meg a legördülő menüből. Után úgy dönt, hogy egy adott virtuális gép méretét, a menü csak a rendelkezésre álló prémium szintű storage, hogy a Virtuálisgép-méretet alapuló termékváltozatok jeleníti meg.

![A felügyelt lemezes oldalát bemutató képernyőkép](./media/oracle-design/premium_disk01.png)

További információkért lásd: [prémium szintű Storage nagy teljesítményű és a virtuális gépek felügyelt lemezek](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Miután a virtuális gép tárhelyét konfigurált, előfordulhat, hogy betölteni kívánt adatbázis létrehozása előtt a lemezek teszteléséhez. Az i/o sebességét késés és a teljesítmény tekintetében ismerete segíthet a határozza meg, hogy a virtuális gépek a várt átvitelét a késés célok támogatja.

Számos alkalmazás terhelés tesztelési, például Oracle Orion Sysbench vagy Fio eszközök.

A terheléstesztet futtassa újból az Oracle-adatbázis telepítése után. Indítsa el a szabályos és csúcsidőre munkaterhelések, és az eredmények megjelenítése a környezet az alapkonfigurációt.

Több, fontos lehet a következő méretre a tárolási IOPS sebessége helyett a tárméret alapján. Például, ha a szükséges IOPS-érték 5000, de csak akkor kell 200 GB-os, előfordulhat, hogy továbbra is megjelenik a P30 osztály prémium szintű lemez annak ellenére, hogy a több mint 200 GB tárhelyet származik.

Az IOPS sebessége a AWR jelentés lehet lekérni. Azt határozza meg a visszaállítási napló, fizikai olvasási és írási műveletek sebessége.

![A AWR jelentés oldalát bemutató képernyőkép](./media/oracle-design/awr_report.png)

A visszaállítási mérete például másodpercenként, amely megegyezik a 11.63 MBPs 12,200,000 bájt.
Az IOPS-érték 12,200,000 / 2,358 = 5,174.

Miután az i/o-követelményeinek világossá, dönthet úgy Erőforrásigények kielégítéséhez legmegfelelőbb meghajtók kombinációja.

**Javaslatok**

- Az adatok táblaterületen, elosztva a i/o-munkaterhelés lemezek számát felügyelt tároló- és Oracle ASM használatával.
- Ha az i/o-blokkméret olvasásigényű és írási-igényes műveletek növekszik, adja hozzá a további adatlemezt.
- Növelje a blokkméret nagy szekvenciális folyamatok.
- I/o csökkentése (az adatok és indexek) az adatok tömörítésének segítségével.
- Ismét: naplókat, a rendszer, és a temps külön, és külön adatlemezek a TS visszavonása.
- Ne helyezze el az alapértelmezett operációsrendszer-lemezek (/ dev/sda) alkalmazás fájljai. Ezek a lemezek nem optimalizált gyors virtuális gép rendszerindítási ideje, és előfordulhat, hogy nem adja meg az alkalmazás a megfelelő teljesítmény.

### <a name="disk-cache-settings"></a>Gyorsítótár-beállítások

Az állomás gyorsítótárazását három lehetőség áll rendelkezésre:

- *Csak olvasható*: összes kérelem későbbi olvasása gyorsítótárba kerüljenek-e. Minden közvetlenül az Azure Blob storage megmaradnak.

- *Olvasási és írási*: Ez a "előreolvasási" algoritmus. Az olvasási és írási jövőbeli olvasása lettek gyorsítótárazva. Nem-író írások először a helyi gyorsítótárba megmaradnak. Írás az SQL Server megmaradnak Azure Storage mert író használ. A legkisebb mértékű késleltetést lemez könnyű munkaterhelések is tartalmazza.

- *Nincs* (letiltva): A ezt a beállítást, a gyorsítótár mellőzhetik. Az adatok lemezre át és Azure Storage maradnak. Ez a módszer a legmagasabb i/o-sebesség i/o-igényes munkaterhelések esetében adja meg. Szükség "tranzakciós költségek" figyelembe kell venni.

**Javaslatok**

Az átviteli sebesség maximalizálása érdekében azt javasoljuk, hogy a kiindulási pont **nincs** állomás gyorsítótárazását. A prémium szintű Storage, vegye figyelembe, hogy le kell tiltania a "korlátok", ha a fájlrendszer csatlakoztatja a **ReadOnly** vagy **nincs** beállítások. A lemezek UUID frissítse a /etc/fstab fájlt.

További információkért lásd: [prémium szintű Storage Linux virtuális gépek](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![A felügyelt lemezes oldalát bemutató képernyőkép](./media/oracle-design/premium_disk02.png)

- Az operációs rendszer lemezek, használja az alapértelmezett **olvasási/írási** gyorsítótárazását.
- A rendszer, a TEMP és a visszavonás használja **nincs** a gyorsítótárazáshoz.
- Az adatokat, használja **nincs** a gyorsítótárazáshoz. De ha az adatbázis csak olvasható vagy olvasásigényű, **írásvédett** gyorsítótárazását.

Az adatok lemezre beállításainak mentése után nem módosítható a gazdagép gyorsítótár-beállítást, kivéve, ha a meghajtó az operációs rendszer szintjén leválasztása és majd a a módosítás elvégzése után csatlakoztassa újra.


## <a name="security"></a>Biztonság

Miután beállítása és az Azure környezetben konfigurálva, a következő lépés megvédi a hálózatot. Az alábbiakban néhány javaslatot:

- *NSG házirend*: NSG definiálni egy alhálózatot vagy egy hálózati adaptert. Elérés az alhálózatok, mind a biztonság és a kényszerített útválasztást többek között a tűzfalak egyszerűbb.

- *Jumpbox*: A biztonságosabb hozzáférés érdekében a rendszergazdák kell közvetlenül csatlakozik a alkalmazás service vagy az adatbázis. Egy media a rendszergazda számítógép és az Azure-erőforrások egy jumpbox lesz.
![A Jumpbox topológia oldalát bemutató képernyőkép](./media/oracle-design/jumpbox.png)

    A rendszergazda gép IP-korlátozott hozzáférést csak a jumpbox kínálja. A jumpbox kell rendelkeznie az alkalmazás és az adatbázis elérésére.

- *Magánhálózati* (alhálózatok): javasoljuk, hogy rendelkezik a alkalmazás service és az adatbázis külön alhálózatokon, így jobb vezérlő NSG házirend állítható be.


## <a name="additional-reading"></a>További olvasnivaló

- [Oracle ASM konfigurálása](configure-oracle-asm.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Oracle Golden kapu beállítása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre magas rendelkezésre állású virtuális gépek](../../linux/create-cli-complete.md)
- [Virtuális gép telepítése az Azure parancssori felület minták felfedezése](../../linux/cli-samples.md)
