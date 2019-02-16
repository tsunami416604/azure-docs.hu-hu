---
title: Tervezzenek és valósítsanak meg az Oracle-adatbázishoz az Azure-ban |} A Microsoft Docs
description: Tervezze meg és Oracle-adatbázis megvalósítása az Azure-környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 8241dc0303b7e60f9ce1e04e56d152c9a0b3906c
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327510"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Oracle-adatbázis tervezése és megvalósítása az Azure-ban

## <a name="assumptions"></a>Előfeltételek

- Tervezi az Oracle-adatbázis migrálása a helyszínről Azure-bA.
- Oracle AWR jelentések van a különböző metrikák megismerése.
- Egy alkalmazás teljesítmény- és használati platform alapvető ismeretekkel rendelkezik.

## <a name="goals"></a>Célok

- Megtudhatja, hogyan a az Azure-ban Oracle-fejlesztés optimalizálásához.
- Ismerje meg a teljesítmény-finomhangolási Oracle-adatbázis, Azure-környezet lehetőségei.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>A helyszíni közötti különbségeket és az Azure végrehajtása 

Az alábbiakban néhány fontos dolgot tartsa szem előtt való migráláshoz az Azure-bA a helyszíni alkalmazások. 

Egy fontos különbség az, hogy az Azure implementációja, erőforrások, például a virtuális gépek, a lemezek és virtuális hálózatok megosztott többek között a más ügyfelekkel. Emellett erőforrások szabályozható a követelmények alapján. Helyett elkerülését (MTBF) sikertelen, az Azure több összpontosít újraindulnak a még működő a hiba (MTTR).

Az alábbi táblázat néhány egy helyszíni megvalósítás és a egy Oracle-adatbázis Azure megvalósítását közötti különbségeket.

> 
> |  | **Helyszíni megvalósítás** | **Az Azure végrehajtása** |
> | --- | --- | --- |
> | **Hálózat** |LAN/WAN  |SDN-alapú (szoftveresen meghatározott Hálózatkezelés)|
> | **Biztonsági csoport** |IP-port korlátozás eszközök |[Hálózati biztonsági csoport (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Rugalmasságának biztosításával** |MTBF (hibák közötti átlagos idő) |MTTR (helyreállítási időt)|
> | **Tervezett karbantartás** |Javítás/frissítése|[A rendelkezésre állási csoportok](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (javítás/frissítése az Azure által felügyelt) |
> | **Erőforrás** |Dedikált  |Más ügyfelek megosztva|
> | **Régiók** |Adatközpontok |[Régiópárok](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Tárolás** |A SAN-ről/fizikai lemezek |[Azure által felügyelt tárolási](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Méretezés** |Függőleges méretezés |Horizontális skálázhatóság|


### <a name="requirements"></a>Követelmények

- Az adatbázis méretére és növekedésére sebesség határozza meg.
- Meghatározza az IOPS követelményeket, amely meg tudja becsülni a Oracle AWR jelentések és az egyéb figyelési eszközök alapján.

## <a name="configuration-options"></a>Beállítási lehetőségek

Nincsenek Azure-környezet a teljesítmény javítása hangolhassa négy lehetséges területeket:

- Virtuális gép mérete
- Hálózati átviteli sebessége
- Lemeztípusok és konfigurációk
- Lemez gyorsítótár beállításai

### <a name="generate-an-awr-report"></a>Egy AWR jelentés készítése

Ha rendelkezik egy meglévő, Oracle-adatbázishoz, és az Azure-bA migrálni kívánt, számos lehetősége van. Az Oracle AWR jelentés lekérése a metrikák (IOPS, MB/s, GiBs és így tovább) futtathatja. Ezután válassza ki a virtuális gép az összegyűjtött metrikák alapján. Vagy az infrastruktúra-csapat hasonló információkat lekérni.

Érdemes lehet a AWR jelentés futtatása rendszeres és a kiugró kihasználtsággal számítási feladatok során, hogy össze lehessen hasonlítani. Ezek a jelentések alapján, az átlagos számítási feladat vagy a maximális munkaterhelést-alapú virtuális gépek méretét.

Következő a következő példa bemutatja, hogyan AWR-jelentés létrehozásához:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Alapvető metrikák

Az alábbiakban, hogy a AWR jelentés szerezhetők be:

- Magok teljes száma
- Processzor órajel
- Teljes memória GB-ban
- Processzorkihasználtság
- Maximális adatátviteli sebesség
- I/o-módosítások (olvasás/írás)
- Ismételje meg a napló sebesség (MB/s)
- Hálózati átviteli sebessége
- Hálózati késés sebessége (alsó vagy felső)
- Adatbázis méretét GB-ban
- SQL-n keresztül fogadott bájtok száma * nettó Azure blobból vagy az ügyfél

### <a name="virtual-machine-size"></a>Virtuális gép mérete

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Becslés Virtuálisgép-méretet a AWR jelentésből CPU, memória és I/O használat alapján

Előfordulhat, hogy megtekinti egy dolog, amelyek jelzik, ha a rendszer szűk keresztmetszetei vannak a felső öt időzített előtér eseményeket.

Ha például az alábbi ábrán a log file sync van tetején. Azt jelzi, hogy vár, mielőtt a LGWR ír a napló puffer az ismétlés naplófájl szükséges számát. Ezekkel az eredményekkel azt jelzik, hogy jobb teljesítményű tárolási vagy lemezek szükségesek. Emellett az ábrán is látható a Processzor (magok) és a memória mennyiségét.

![Képernyőfelvétel a AWR-jelentés oldalról](./media/oracle-design/cpu_memory_info.png)

Az alábbi ábrán látható a teljes i/o-olvasási és írási. 59 GB, olvassa el és a jelentés az idő során írt 247.3 GB volt.

![Képernyőfelvétel a AWR-jelentés oldalról](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Válassza ki a virtuális gép

A AWR jelentésből összegyűjtött információk alapján, a következő lépés az válassza ki az igényeinek megfelelő hasonló méretű virtuális Gépet. Rendelkezésre álló virtuális gépek listájának található cikk [memóriahasználatra optimalizált](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Az ACU-k alapján hasonló VM-sorozatba a virtuális gép méretezési finomhangolása

Miután kiválasztotta a virtuális Gépet, az ACU figyelmet fordítania a virtuális gép számára. Egy másik virtuális gép igényeinek jobban megfelelő ACU értéke alapján választhatja azt is. További információkért lásd: [Azure számítási egység](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Az ACU egységek oldalát bemutató képernyőkép](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Hálózati átviteli sebessége

Az alábbi ábrán látható, az adatátviteli sebessége és IOPS közötti kapcsolat:

![Képernyőkép az átviteli sebesség](./media/oracle-design/throughput.png)

A teljes hálózati átviteli sebesség becsült alapján a következő információkat:
- SQL*Net traffic
- MB/s x (például az Oracle Data Guard kimenő folyam) kiszolgálók száma
- Egyéb tényezők, például az alkalmazás replikálása

![Képernyőkép az SQL * hálózati átviteli sebesség](./media/oracle-design/sqlnet_info.png)

A hálózati sávszélesség-követelmények alapján, közül választhat a különböző átjáró típusa van. Ezek közé tartozik a basic, VpnGw, és az Azure ExpressRoute. További információkért lásd: a [VPN-átjáró díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Javaslatok**

- Hálózati késés magasabb össze egy helyszíni üzemelő. Hálózati round lelassítja is jelentősen csökkenti a teljesítmény javítása.
- Üzenetváltások csökkentése érdekében az alkalmazásokat, ugyanahhoz a virtuális géphez magas tranzakciók vagy a "forgalmas" alkalmazások egyesíthetők.

### <a name="disk-types-and-configurations"></a>Lemeztípusok és konfigurációk

- *Alapértelmezett operációsrendszer-lemezek*: Ezek a lemeztípusok a perzisztens adatok és a gyorsítótárazás kínálnak. Az operációs rendszer hozzáférés indításkor vannak optimalizálva, és nem tervezett vagy tranzakciós vagy az adatraktár (analitikai) számítási feladatokat.

- *Nem felügyelt lemezek*: A következő lemeztípusok kezelése a storage-fiókok, amelyek a virtuális merevlemez (VHD) fájlok, amelyek megfelelnek a Virtuálisgép-lemezek tárolására. VHD-fájlokat az Azure storage-fiókok lap blobként vannak tárolva.

- *A Managed disks*: Az Azure kezeli a virtuális gépek lemezeihez használt tárfiókok. A lemez típusát (prémium vagy standard) és a szükséges a lemez kívánt méretét kell megadni. Az Azure létrehozza és felügyeli a lemezt Ön helyett.

- *Prémium szintű tárolólemezeket*: Ezek a lemeztípusok legalkalmasabbak, a termelési számítási feladatokhoz. A Premium storage támogatja a Virtuálisgép-lemezek, amelyek adott méret-sorozat virtuális gépei, például a DS, DSv2, GS és az F sorozatú virtuális gépek csatolhatók. A prémium szintű lemez a különböző méretű, és 4096 GB-os és a 32 GB-os lemezek közül választhat. Minden lemez méretét a saját teljesítmény specifikációi. Az alkalmazás követelményeitől függően egy vagy több lemez is csatlakoztatható a virtuális géphez.

Amikor egy új felügyelt lemezt hoz létre a portálról, választhat a **fiók típusa** a használni kívánt lemez típusa. Ne feledje, hogy nem minden rendelkezésre álló lemezek jelennek meg a legördülő menüből. Ha úgy dönt, hogy egy adott Virtuálisgép-méretet, a menü mutatja csak az elérhető a premium storage Termékváltozatai, amelyeket a virtuális gép méretén alapulnak.

![A felügyelt lemez oldalát bemutató képernyőkép](./media/oracle-design/premium_disk01.png)

Miután konfigurálta a tároló egy virtuális gépen, előfordulhat, hogy kívánja betölteni a lemezek tesztelje az adatbázis létrehozása előtt. Az i/o sebességét, teljesítmény és a késés szempontjából ismerete segíthet a határozza meg, ha a virtuális gépek a várt teljesítményről, a késés célértéke támogatja.

Nincsenek számos olyan eszközt, az alkalmazás terheléses tesztelés, például az Oracle Orion Sysbench és Fio.

A terhelési teszt futtassa újra az Oracle-adatbázis telepítése után. Indítsa el a kiugró kihasználtsággal, és rendszeres számítási feladatokat, és az eredmények megjelenítése a környezet az alaptervhez.

Érdemes lehet több fontos, hogy a tároló mérete helyett IOPS sebessége alapján a tárhely mérete. Például ha a szükséges IOPS 5000, de csak akkor kell 200 GB-ot, előfordulhat, hogy továbbra is megjelenik az osztály P30 prémium szintű lemezes annak ellenére, hogy együttműködik a több mint 200 GB tárhelyet.

Az IOPS arány a AWR jelentésből szerezhető be. Azt határozza meg a visszaállítási napló, fizikai olvasások és írási műveletek sebessége.

![Képernyőfelvétel a AWR-jelentés oldalról](./media/oracle-design/awr_report.png)

Az ismétlés mérete például 12,200,000 bájt / másodpercben, amely egyenlő 11.63 MB/s.
Az IOPS érték 12,200,000 / 2,358 = 5,174.

Miután egy világos képet az i/o-követelményeket, kiválaszthatja a meghajtó, amely a leginkább kihasználni erőforrásigények kielégítéséhez kombinációját.

**Javaslatok**

- Adatok táblaterületre, helyezkednek el az I/O munkaterheléstől lemezek számos felügyelt storage vagy az Oracle ASM használatával.
- Ahogy az i/o-blokkméret olvasásigényű és nagy írási műveletekhez növekszik, adjon hozzá további adatlemezeket.
- Növelje a blokkméret a nagy szekvenciális folyamatokhoz.
- Az adattömörítés segítségével csökkentse az i/o (az adatok és indexek).
- Ismétlés naplók, a rendszer és a temps külön, és vonja vissza a TS külön lemezeken.
- Ne helyezzen minden olyan alkalmazás fájljait az alapértelmezett operációsrendszer-lemezek (/ dev/sda). Ezek a lemezek nem optimalizált gyors virtuális gép rendszerindítási ideje, és előfordulhat, hogy nem jó teljesítményt biztosítanak az alkalmazás.

### <a name="disk-cache-settings"></a>Lemez gyorsítótár beállításai

Állomás-gyorsítótárazás a három lehetőség áll rendelkezésre:

- *Csak olvasható*: Összes kérelem a jövőbeli olvasási gyorsítótárban. Az összes írási művelet közvetlenül az Azure Blob storage tárolja.

- *Olvasási és írási*: Ez a "előreolvasási" algoritmus. Az olvasási és írási a jövőbeli olvasási gyorsítótárban. Nem-visszaírási írási műveletek először a helyi gyorsítótárba megmaradnak. Írás az SQL Server esetében megmaradnak az Azure Storage mert visszaírási használ. Azt is biztosít a legalacsonyabb lemez késése kisebb számítási feladatokhoz.

- *Nincs* (letiltva): Ez a beállítás használatával, elkerülheti a gyorsítótárban. Minden az adatok átkerülnek a lemez és az Azure Storage tárolja. Ez a módszer biztosítja a legmagasabb i/o-forgalom i/o-igényű számítási feladatokhoz. Is kell a "tranzakciós költség" figyelembe kell venni.

**Javaslatok**

Az átviteli sebesség maximalizálása azt javasoljuk, hogy először a **nincs** állomás-gyorsítótárazás számára. A Premium Storage, vegye figyelembe, hogy le kell tiltania a "korlátok" Ha a fájlrendszer csatlakoztatja a **ReadOnly** vagy **nincs** beállítások. Frissítse a /etc/fstab fájlt a lemezekre az UUID azonosítójú.

![A felügyelt lemez oldalát bemutató képernyőkép](./media/oracle-design/premium_disk02.png)

- Operációsrendszer-lemezek esetén használja az alapértelmezett **olvasási/írási** gyorsítótárazását.
- Használja a rendszer, a TEMP és a Visszavonás **nincs** gyorsítótárazáshoz.
- ADATOK használata **nincs** gyorsítótárazáshoz. De ha az adatbázis csak olvasható vagy olvasásigényű, **csak olvasható** gyorsítótárazását.

Az adatok lemezre beállításainak mentése után nem módosítható a gazdagép az ügyfélgyorsítótár beállítása, kivéve ha válassza le a meghajtót az operációs rendszer szintjén, majd csatlakoztassa újra, a módosítás elvégzése után.


## <a name="security"></a>Biztonság

Miután beállítása és konfigurálása az Azure-környezetben, a következő lépés az a hálózat védelme érdekében. Az alábbiakban néhány javaslat:

- *NSG-t a házirend*: NSG-t egy alhálózathoz vagy hálózati adapterre definiálni Való hozzáférés biztonsági kényszerített útválasztási például tűzfalak és az alhálózat szintjén is egyszerűbb legyen.

- *Jumpbox*: A biztonságosabb hozzáférés érdekében a rendszergazdák kell nem közvetlenül kapcsolódni az alkalmazásszolgáltatás vagy az adatbázis. A jumpbox a rendszergazda-gép és az Azure-erőforrások közötti adathordozó használatban van.
![A Jumpbox topológia oldalát bemutató képernyőkép](./media/oracle-design/jumpbox.png)

    A rendszergazda-gép IP-korlátozott hozzáférés csak a jumpbox kínáljon. A jumpbox rendelkezzenek hozzáféréssel az alkalmazás- és adatbázis.

- *Magánhálózat* (alhálózatok): Azt javasoljuk, hogy a alkalmazás service és az adatbázis külön alhálózatokon, így hatékonyabban szabályozhatja is létrehozhatnak az NSG-házirendet.


## <a name="additional-reading"></a>További olvasnivaló

- [Oracle ASM konfigurálása](configure-oracle-asm.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Oracle Golden kapu beállítása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [Ismerje meg a virtuális gép üzembe helyezés az Azure CLI-minták](../../linux/cli-samples.md)
