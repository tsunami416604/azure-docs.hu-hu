---
title: Virtuálismag-alapú modell áttekintése
description: A virtuális mag beszerzési modellje lehetővé teszi a számítási és tárolási erőforrások egymástól független méretezését, a helyszíni teljesítmény egyeztetését és az árak optimalizálását.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: d57f1e87c503a86a522fdb3004b021fbcb5c6ff1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351404"
---
# <a name="vcore-model-overview"></a>Virtuálismag-alapú modell áttekintése

A Virtual Core (virtuális mag) modell számos előnyt kínál:

- Nagyobb számítási, memória-, IO-és tárolási korlátok.
- Szabályozhatja a hardver generációját, hogy jobban megfeleljen a számítási és memória-követelményeknek.
- A [Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) és a [fenntartott példány (ri)](sql-database-reserved-capacity.md)díjszabási kedvezményei.
- A hardver részletes adatainak nagyobb átláthatósága a számítási teljesítmény érdekében megkönnyíti a helyszíni telepítések áttelepítésének megtervezését.

## <a name="service-tiers"></a>Szolgáltatásszintek

A virtuális mag modellben található szolgáltatási rétegek beállításai közé tartozik a általános célú, a üzletileg kritikus és a nagy kapacitású. A szolgáltatási szinten általában a tárolási architektúra, a tárhely és az IO-korlátok, valamint a rendelkezésre állással és a vész-helyreállítással kapcsolatos üzletmenet-folytonossági lehetőségek vannak meghatározva.

||**Általános célú**|**Üzleti szempontból kritikus**|**Nagy kapacitású**|
|---|---|---|---|
|Mikor ajánljuk|A legtöbb üzleti számítási feladat. A szolgáltatás költségvetés-orientált, kiegyensúlyozott és méretezhető számítási és tárolási lehetőségeket kínál. |Több elkülönített replika használatával a lehető legnagyobb rugalmasságot nyújtja az üzleti alkalmazások számára, és az adatbázis-replikák esetében a legmagasabb I/O-teljesítményt biztosítja.|A legtöbb üzleti számítási feladat nagy mértékben méretezhető tárolási és olvasási méretezési követelményekkel.  Nagyobb rugalmasságot biztosít a hibákhoz azáltal, hogy lehetővé teszi több elkülönített adatbázis-replika konfigurációját. |
|Adattárolás|Távoli tárterületet használ.<br/>**Önálló adatbázis és rugalmas készlet kiépített számítási felszámítása**:<br/>5 GB – 4 TB<br/>**Kiszolgáló nélküli számítás**:<br/>5 GB – 3 TB<br/>**Felügyelt példány**: 32 GB – 8 TB |A helyi SSD-tárolót használ.<br/>**Önálló adatbázis és rugalmas készlet kiépített számítási felszámítása**:<br/>5 GB – 4 TB<br/>**Felügyelt példány**:<br/>32 GB – 4 TB |A tárterület rugalmas automatikus növekedése igény szerint. Akár 100 TB tárterületet is támogat. A helyi SSD-tárolót használ a helyi puffer-készlet gyorsítótárához és a helyi adattároláshoz. Az Azure-beli távoli tárterületet használja végső hosszú távú adattárként. |
|I/O-átviteli sebesség (hozzávetőleges)|**Önálló adatbázis és rugalmas készlet**: 500 IOPS/virtuális mag legfeljebb 40000 maximális IOPS.<br/>**Felügyelt példány**: a [fájl méretétől](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)függ.|5000 IOPS/virtuális mag legfeljebb 320 000 maximális IOPS|A nagy kapacitású egy többrétegű architektúra, több szinten történő gyorsítótárazással. A hatékony IOPs a munkaterheléstől függ.|
|Elérhetőség|1 replika, nincsenek olvasási méretezésű replikák|3 replika, 1 [olvasási léptékű replika](sql-database-read-scale-out.md),<br/>zóna – redundáns magas rendelkezésre állás (HA)|1 írható-olvasható replika, valamint 0-4 [-es olvasási léptékű replika](sql-database-read-scale-out.md)|
|Biztonsági másolatok|[Olvasási hozzáférés – geo-redundáns tárolás (ra-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 nap (alapértelmezés szerint 7 nap)|[Ra-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 nap (alapértelmezés szerint 7 nap)|Pillanatkép-alapú biztonsági másolatok az Azure-beli távoli tárolóban. A visszaállítja ezeket a pillanatképeket a gyors helyreállításhoz. A biztonsági másolatok azonnaliek, és nem befolyásolják a számítási I/O-teljesítményt. A visszaállítások gyorsak, és nem az adatmennyiség (óra vagy nap helyett percekben).|
|Memóriabeli|Nem támogatott|Támogatott|Nem támogatott|
|||


### <a name="choosing-a-service-tier"></a>Szolgáltatási szint kiválasztása

Az adott munkaterhelés szolgáltatási szintjeinek kiválasztásával kapcsolatos információkért tekintse meg a következő cikkeket:

- [Mikor válassza ki az általános célú szolgáltatási szintet](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Mikor válassza ki a üzletileg kritikus szolgáltatási szintet](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Mikor válassza ki a nagy kapacitású szolgáltatási szintet](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Számítási szintek

A virtuális mag modellben a számítási rétegek lehetőségei közé tartoznak a kiépített és a kiszolgáló nélküli számítási szintek.


### <a name="provisioned-compute"></a>Kiépített számítási teljesítmény

A kiépített számítási szintek meghatározott mennyiségű számítási erőforrást biztosítanak, amelyek a munkaterhelés-tevékenységektől függetlenül folyamatosan vannak kiépítve, és az óránként rögzített számítási kapacitás mennyiségét számlázzák.


### <a name="serverless-compute"></a>Kiszolgáló nélküli számítástechnika

A [kiszolgáló nélküli számítási réteg](sql-database-serverless.md) automatikusan méretezi a számítási erőforrásokat a munkaterhelés-tevékenységek alapján, és a másodpercenként felhasznált számítási kapacitás mennyiségét.



## <a name="hardware-generations"></a>Hardver generációi

A virtuális mag modell hardver-létrehozási lehetőségei közé tartozik a Gen 4/5, az M-Series (előzetes verzió) és a Fsv2 sorozat (előzetes verzió). A hardverek létrehozása általában meghatározza a számítási és a memóriabeli korlátokat, valamint azokat a jellemzőket, amelyek hatással vannak a munkaterhelés teljesítményére.

### <a name="gen4gen5"></a>Gen4/Gen5

- A Gen4/Gen5-hardver kiegyensúlyozott számítási és memória-erőforrásokat biztosít, és a legtöbb olyan adatbázis-számítási feladathoz alkalmas, amely nem rendelkezik nagyobb memóriával, magasabb virtuális mag vagy gyorsabb, virtuális mag követelményekkel, amelyet a Fsv2-sorozat vagy az M sorozat biztosít.

Azokon a régiókban, ahol a Gen4/Gen5 elérhető, tekintse meg a [Gen4/Gen5 rendelkezésre állását](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Fsv2 sorozat (előzetes verzió)

- A Fsv2 sorozat egy számítási optimalizált hardveres beállítás, amely alacsony CPU-késést és nagy órajelet biztosít a legtöbb CPU-igényű munkaterheléshez.
- A számítási feladattól függően a Fsv2 sorozat több CPU-teljesítményt biztosíthat virtuális mag, mint a Gen5, és az 72 virtuális mag mérete nagyobb CPU-teljesítményt biztosít, kevesebb, mint 80 virtuális mag a Gen5. 
- A Fsv2 kevesebb memóriát és tempdb biztosít, mint a többi hardver, így a korlátokra érzékeny munkaterhelések Ehelyett a Gen5 vagy az M sorozatokat is érdemes figyelembe venni.  

Azokon a régiókban, ahol elérhető a Fsv2 sorozat, tekintse meg a [Fsv2-sorozat elérhetőségét](#fsv2-series)ismertető témakört.


### <a name="m-seriespreview"></a>M-sorozat (előzetes verzió)

- Az M-sorozat egy memória-optimalizált hardveres beállítás, amely több memóriát és nagyobb számítási korlátot igényel, mint amennyit a Gen5 biztosít.
- Az M-sorozat 29 GB-ot biztosít virtuális mag és 128 virtuális mag, ami növeli a Gen5 viszonyított memória korlátját a 8x-tól közel 4 TB-ig.

Az M-sorozat hardverének az előfizetéshez és a régióhoz való engedélyezéséhez meg kell nyitni egy támogatási kérést. Ha a támogatási kérést jóváhagyják, az M sorozat kiválasztási és kiépítési tapasztalatai ugyanazt a mintát követik, mint az egyéb hardveres generációk esetében. Az m-sorozat rendelkezésre állását tartalmazó régiók esetében lásd: az [m-sorozat elérhetősége](#m-series).


### <a name="compute-and-memory-specifications"></a>Számítási és memória-specifikációk


|Hardver létrehozása  |Számítási szolgáltatások  |Memória  |
|:---------|:---------|:---------|
|Gen4     |-Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorok<br>-Akár 24 virtuális mag (1 virtuális mag = 1 fizikai mag)  |-7 GB/virtuális mag<br>– Akár 168 GB-nyi kiépítés|
|Gen5     |**Kiépített számítás**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) processzorok<br>– Akár 80 virtuális mag (1 virtuális mag = 1 Hyper-thread)<br><br>**Kiszolgáló nélküli számítás**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) processzorok<br>– Akár 16 virtuális mag automatikus méretezés (1 virtuális mag = 1 Hyper-thread)|**Kiépített számítás**<br>-5,1 GB/virtuális mag<br>– Akár 408 GB-nyi kiépítés<br><br>**Kiszolgáló nélküli számítás**<br>– Akár 24 GB-os automatikus méretezés virtuális mag<br>-Legfeljebb 48 GB-ig terjedő automatikus méretezés|
|Fsv2 sorozat     |-Intel Xeon Platinum 8168 (SkyLake) processzorok<br>– A 3,4 GHz-es és az összes Core Turbo órajel-sebesség, valamint a 3,7 GHz-es maximális, egyetlen Core Turbo órajel.<br>-Kiépítés 72 virtuális mag (1 virtuális mag = 1 Hyper-thread)|-1,9 GB/virtuális mag<br>– 136 GB kiépítése|
|M sorozat     |-Intel Xeon E7-8890 v3 2,5 GHz-es processzorok<br>-Kiépítés 128 virtuális mag (1 virtuális mag = 1 Hyper-thread)|– 29 GB/virtuális mag<br>– 3,7 TB kiépítése|


Az erőforrás-korlátokkal kapcsolatos további információkért lásd: [az önálló adatbázisok erőforrás-korlátai (virtuális mag)](sql-database-vcore-resource-limits-single-databases.md)vagy a [rugalmas készletek erőforrás-korlátai (virtuális mag)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Hardver-létrehozás kiválasztása

A Azure Portal a létrehozáskor kiválaszthatja egy SQL-adatbázis vagy-készlet hardver-generációját, vagy megváltoztathatja egy meglévő SQL-adatbázis vagy-készlet hardveres létrehozását is.

**Hardveres generáció kiválasztása SQL-adatbázis vagy-készlet létrehozásakor**

Részletes információkat az [SQL Database létrehozása](sql-database-single-database-get-started.md)című témakörben talál.

Az **alapvető** beállítások lapon válassza az **adatbázis konfigurálása** hivatkozást a **számítás + tárolás** szakaszban, majd válassza a **konfiguráció módosítása** hivatkozást:

  ![adatbázis konfigurálása](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Válassza ki a kívánt hardver-generálást:

  ![hardver kiválasztása](media/sql-database-service-tiers-vcore/select-hardware.png)


**Meglévő SQL-adatbázis vagy-készlet hardveres létrehozásának módosítása**

Adatbázis esetén az Áttekintés lapon válassza ki a **díjszabási szintet** mutató hivatkozást:

  ![hardver módosítása](media/sql-database-service-tiers-vcore/change-hardware.png)

Készlet esetén az Áttekintés lapon válassza a **Konfigurálás**lehetőséget.

Kövesse a konfiguráció módosításához szükséges lépéseket, és válassza ki a hardver generációját az előző lépésekben leírtak szerint.

**Hardveres generáció kiválasztása felügyelt példány létrehozásakor**

Részletes információkat a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)című témakörben talál.

Az **alapvető beállítások** lapon válassza az **adatbázis konfigurálása** hivatkozást a **számítás + tárolás** szakaszban, majd válassza a kívánt hardver-létrehozás lehetőséget:

  ![felügyelt példány konfigurálása](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Meglévő felügyelt példány hardveres létrehozásának módosítása**

Használja a következő PowerShell-parancsfájlt:

```powershell-interactive
$subscriptionId = "**************"
Select-AzSubscription -Subscription $subscriptionId

$instanceName = "********"
$resourceGroup = "****"

# THIS IS IMPORTANT PARAMETER:
$sku = @{name = "GP_Gen5" }

# NOTE: These properties are not necessary, but it would be good to set them to the current values:
# You might want to change vCores or storage with hardware generation
# $admin_login = "******"
# $admin_pass = "******"
# $location = "***** # for example: ""northeurope"
# $vCores = 8
# $maxStorage = 1024
# $license = "BasePrice"
# $subnetId = "/subscriptions/****/subnets/*******"

## NOTE: Uncomment some of the properties below if you have set them.
$properties = New-Object System.Object
# $properties | Add-Member -type NoteProperty -name subnetId -Value $subnetId
# $properties | Add-Member -type NoteProperty -name administratorLogin -Value $admin_login
# $properties | Add-Member -type NoteProperty -name administratorLoginPassword -Value $admin_pass
# $properties | Add-Member -type NoteProperty -name vCores -Value $vCores
# $properties | Add-Member -type NoteProperty -name storageSizeInGB -Value $maxStorage
# $properties | Add-Member -type NoteProperty -name licenseType -Value $license

Set-AzResource -Properties $properties -ResourceName $instanceName -ResourceType "Microsoft.SQL/managedInstances" -Sku $sku -ResourceGroupName $resourceGroup -Force -ApiVersion "2015-05-01-preview"
```

Ügyeljen rá, hogy megadja a felügyelt példány előfizetési azonosítóját, nevét és erőforrás-csoportját.

### <a name="hardware-availability"></a>Hardver rendelkezésre állása

#### <a name="gen4gen5-1"></a>Gen4/Gen5

Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában. 

A Gen5 a világ legtöbb régiójában elérhető.

#### <a name="fsv2-series"></a>Fsv2 sorozat

A Fsv2 sorozat a következő régiókban érhető el: Ausztrália középső régiója, Ausztrália középső régiója 2, Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Ázsia, USA keleti régiója, Közép-India, Közép-India, Nyugat-India, Közép-Korea, Dél-Korea, Észak Európa, Dél-Afrika északi régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA 2. nyugati régiója.


#### <a name="m-series"></a>M sorozat

Az M-sorozat a következő régiókban érhető el: USA keleti régiója, Észak-Európa, Nyugat-Európa, USA 2. nyugati régiója.
Az M-sorozat további régiókban is korlátozott rendelkezésre állással rendelkezhet. Az itt felsoroltakon kívül más régiót is igényelhet, de előfordulhat, hogy egy másik régióban való teljesítés nem lehetséges.

Az M-sorozat rendelkezésre állásának az előfizetésben való engedélyezéséhez [egy új támogatási kérelem bejelentésével](#create-a-support-request-to-enable-m-series)kell megkövetelni a hozzáférést.


##### <a name="create-a-support-request-to-enable-m-series"></a>Hozzon létre egy támogatási kérést az M-sorozat engedélyezéséhez: 

1. Válassza a **Súgó + támogatás** lehetőséget a portálon.
2. Válassza az **Új támogatási kérelem** lehetőséget.

Az **alapok** lapon adja meg a következőket:

1. A **probléma típusa**beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
2. **Előfizetés** = válassza ki az M-sorozat engedélyezéséhez szükséges előfizetést.
3. A **kvóta típusa**beállításnál válassza az **SQL Database**lehetőséget.
4. Kattintson a **tovább** gombra a **részletek** lapra való ugráshoz.

A **részletek** lapon adja meg a következőket:

5. A **probléma részletei** szakaszban válassza a **részletek megadása** hivatkozást. 
6. **SQL Database a kvóta típusa** lapon válassza az **M-sorozat**lehetőséget.
7. A **régió**területen válassza ki az M-sorozat engedélyezésének régióját.
    Az m-sorozat rendelkezésre állását tartalmazó régiók esetében lásd: az [m-sorozat elérhetősége](#m-series).

A jóváhagyott támogatási kérelmek általában 5 munkanapon belül teljesülnek.


## <a name="next-steps"></a>Következő lépések

- SQL-adatbázis létrehozásához tekintse meg [az SQL-adatbázis létrehozása a Azure Portal használatával](sql-database-single-database-get-started.md)című témakört.
- Az önálló adatbázisok számára elérhető számítási méretek és a tárolási méretek tekintetében lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok az önálló adatbázisokhoz](sql-database-vcore-resource-limits-single-databases.md).
- A rugalmas készletekhez rendelkezésre álló számítási méretek és a tárhelyek méretére vonatkozó választási lehetőségekért tekintse meg a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-elastic-pools.md).
- A díjszabással kapcsolatos részletekért tekintse meg a [Azure SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/single/).
