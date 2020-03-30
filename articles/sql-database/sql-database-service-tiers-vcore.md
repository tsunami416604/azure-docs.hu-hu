---
title: Virtuálismag-alapú modell áttekintése
description: A virtuálismag-vásárlási modell lehetővé teszi a számítási és tárolási erőforrások független méretezését, a helyszíni teljesítmény egyeztetését és az ár optimalizálását.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481082"
---
# <a name="vcore-model-overview"></a>Virtuálismag-alapú modell áttekintése

A virtuális mag (vCore) modell számos előnnyel jár:

- Magasabb számítási, memória-, i/o-és tárolási korlátok.
- A hardvergenerálás vezérlése a számítási feladatok számítási és memóriakövetelményeinek jobb anamtalán.
- Díjszabási kedvezmények az [Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) és [a fenntartott példány (RI)](sql-database-reserved-capacity.md)számára.
- Nagyobb átláthatóság a hardver részleteiben, amelyek a számítást működtetik; megkönnyíti a helyszíni telepítésekről történő áttelepítések tervezését.

## <a name="service-tiers"></a>Szolgáltatásszintek

A virtuálismag-modell szolgáltatásiszint-beállításai közé tartozik az általános cél, az üzleti legkritikusabb és a nagy kapacitású. A szolgáltatási szint általában meghatározza a tárolási architektúra, a hely és az Io korlátok, valamint az üzletmenet folytonossági lehetőségek a rendelkezésre állás és a vész-helyreállítási kapcsolatos.

||**Általános célú**|**Üzleti kritikus**|**Rugalmas skálázás**|
|---|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladatok. Költségvetés-orientált, kiegyensúlyozott és méretezhető számítási és tárolási lehetőségeket kínál. |Az üzleti alkalmazások számára a legnagyobb rugalmasságot biztosítja a hibákkal szemben több elkülönített kópia használatával, és adatbázis-replikánként a legmagasabb I/O-teljesítményt biztosítja.|A legtöbb üzleti számítási feladatok nagy mértékben méretezhető tárolási és olvasási méretezési követelmények.  Nagyobb rugalmasságot biztosít a hibákkal szemben, ha egynél több elszigetelt adatbázis-kópia konfigurációját engedélyezi. |
|Storage|Távtárolót használ.<br/>**Egyetlen adatbázisok és rugalmas készletek kiépített számítási:**<br/>5 GB – 4 TB<br/>**Kiszolgáló nélküli számítás:**<br/>5 GB - 3 TB<br/>**Felügyelt példány**: 32 GB – 8 TB |Helyi SSD-tárolót használ.<br/>**Egyetlen adatbázisok és rugalmas készletek kiépített számítási:**<br/>5 GB – 4 TB<br/>**Felügyelt példány**:<br/>32 GB - 4 TB |Rugalmas automatikus tárhely szükség szerint. Akár 100 TB tárhelyet is támogat. Helyi SSD-tárolót használ a helyi pufferkészlet gyorsítótárához és a helyi adattároláshoz. Az Azure távtárolóját használja végső hosszú távú adattárként. |
|IOPS és átviteli (hozzávetőleges)|**Egyetlen adatbázis és rugalmas készlet**: Az egyes [adatbázisokra](../sql-database/sql-database-vcore-resource-limits-single-databases.md) és [rugalmas készletekre](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)vonatkozó erőforráskorlátok megtekintése .<br/>**Felügyelt példány**: [Lásd: Áttekintés Az Azure SQL Database felügyelt példányerőforrás-korlátait.](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)|Tekintse meg az [egyes adatbázisok](../sql-database/sql-database-vcore-resource-limits-single-databases.md) és rugalmas készletek [erőforráskorlátait.](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)|A nagykapacitás ú többszintű architektúra több szinten gyorsítótárazásával. A hatékony IOPS és átviteli terhelés a számítási feladattól függ.|
|Rendelkezésre állás|1 replika, nincs olvasási méretű replika|3 replika, 1 [olvasási méretű replika](sql-database-read-scale-out.md),<br/>zónaredundáns, magas rendelkezésre állás (HA)|1 olvasási és írási replika, plusz 0-4 [olvasási méretű replika](sql-database-read-scale-out.md)|
|Biztonsági másolatok|[Olvasási hozzáférésű georedundáns tárolás (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7–35 nap (alapértelmezés szerint 7 nap)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 nap (alapértelmezés szerint 7 nap)|Pillanatképalapú biztonsági mentések az Azure távtárolójában. A visszaállítások ezeket a pillanatképeket használják a gyors helyreállításhoz. A biztonsági mentések azonnaliak, és nem befolyásolják a számítási I/O teljesítményét. A visszaállítások gyorsak, és nem adatméret-műveletek (órák vagy napok helyett perceket vesznek igénybe).|
|Memóriabeli|Nem támogatott|Támogatott|Nem támogatott|
|||


### <a name="choosing-a-service-tier"></a>Szolgáltatási szint kiválasztása

Az adott számítási feladathoz tartozó szolgáltatási szint kiválasztásáról az alábbi cikkekben talál további információt:

- [Mikor válassza ki az általános célú szolgáltatási szintet?](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Mikor válassza ki az üzleti legkritikusabb szolgáltatási szintet?](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Mikor válassza ki a nagy kapacitású szolgáltatási szintet?](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Számítási szintek

A virtuálismag-modell számítási rétegbeállításai közé tartoznak a kiépített és kiszolgáló nélküli számítási szintek.


### <a name="provisioned-compute"></a>Kiépített számítás

A kiépített számítási szint egy adott mennyiségű számítási erőforrásokat biztosít, amelyek a munkaterhelési tevékenységtől függetlenül folyamatosan ki vannak építve, és a rögzített óránkénti áron kiosztott számítási díj számláit.


### <a name="serverless-compute"></a>Kiszolgáló nélküli számítástechnika

A [kiszolgáló nélküli számítási szint](sql-database-serverless.md) automatikusan skálázza az erőforrásokszámítási tevékenység és a másodpercenként felhasznált számítási mennyiség alapján számított erőforrásokat.



## <a name="hardware-generations"></a>Hardvergenerációk

A virtuálismag-modell hardveres létrehozási lehetőségei közé tartozik a Gen 4/5, az M-sorozat (előzetes verzió) és az Fsv2 sorozat (előzetes verzió). A hardvergenerálás általában meghatározza a számítási és memóriakorlátokat és a számítási feladatok teljesítményét befolyásoló egyéb jellemzőket.

### <a name="gen4gen5"></a>Gen4/Gen5

- A Gen4/Gen5 hardver kiegyensúlyozott számítási és memória-erőforrásokat biztosít, és a legtöbb olyan adatbázis-számítási feladathoz alkalmas, amelyek nem rendelkeznek magasabb memóriával, magasabb virtuálismaggal vagy gyorsabb egyetlen virtuálismag-követelményekkel az Fsv2 sorozat vagy az M sorozat által biztosított követelmények szerint.

Azon régiók esetében, ahol a Gen4/Gen5 elérhető, olvassa el a [Gen4/Gen5 elérhetősége című témakört.](#gen4gen5-1)

### <a name="fsv2-seriespreview"></a>Fsv2 sorozat (előzetes verzió)

- Az Fsv2 sorozat egy számítási optimalizált hardveropció, amely alacsony CPU-késleltetést és nagy órasebességet biztosít a cpu-igényesebb munkaterhelésekhez.
- A munkaterheléstől függően az Fsv2 sorozat virtuális magonként több CPU-teljesítményt tud nyújtani, mint a Gen5, és a 72 virtuális mag mérete nagyobb processzorteljesítményt biztosít kevesebb, mint 80 virtuális magért a Gen5-ön. 
- Az Fsv2 kevesebb memóriát és tempdb-t biztosít virtuális magonként, mint más hardverek, így az ezekre a korlátokra érzékeny munkaterhelések inkább a Gen5 vagy az M sorozatot szeretnék figyelembe venni.  

Fsv2-sorozat csak az általános célú szinten támogatott.  Az Fsv2 sorozatú régiók esetében lásd: [Fsv2 sorozat elérhetősége.](#fsv2-series)


### <a name="m-seriespreview"></a>M-sorozat (előzetes verzió)

- Az M sorozat egy memóriaoptimalizált hardveropció olyan számítási feladatokhoz, amelyek több memóriát és magasabb számítási korlátokat igényelnek, mint a Gen5.
- Az M sorozat virtuális magonként 29 GB-ot és 128 virtuális magot biztosít, ami 8x-ről közel 4 TB-ra növeli a Gen5-höz viszonyított memóriakorlátot.

Az M sorozat csak az üzleti legkritikusabb rétegben támogatott, és nem támogatja a zónaredundanciát.

Az M sorozatú hardverek előfizetéshez és régióhoz való engedélyezéséhez támogatási kérelmet kell megnyitni. Az előfizetésnek fizetős ajánlattípusnak kell lennie, beleértve az alkalmazásalapú fizetés vagy a nagyvállalati szerződés (EA) típusát.  Ha a támogatási kérelmet jóváhagyják, majd az M sorozat kiválasztása és kiépítése ugyanazt a mintát követi, mint más hardvergenerációk esetében. Az M-sorozatú régiók esetében lásd: [M sorozat elérhetősége.](#m-series)


### <a name="compute-and-memory-specifications"></a>Számítási és memóriaspecifikációk


|Hardver generálása  |Compute  |Memory (Memória)  |
|:---------|:---------|:---------|
|Gen4 (1988)     |- Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorok<br>- Akár 24 virtuális mag kiépítése (1 virtuális mag = 1 fizikai mag)  |- 7 GB/vCore<br>- Akár 168 GB-os ellátás|
|Gen5 (1998)     |**Kiépített számítás**<br>- Intel E5-2673 v4 (Broadwell) 2,3 GHz-es és Intel SP-8160 (Skylake)* processzorok<br>- Kiépítése akár 80 virtuális mag (1 virtuális mag = 1 hyper-thread)<br><br>**Kiszolgáló nélküli számítástechnika**<br>- Intel E5-2673 v4 (Broadwell) 2,3 GHz-es és Intel SP-8160 (Skylake)* processzorok<br>- Automatikus skálázás akár 16 virtuális mag (1 virtuális mag = 1 hyper-thread)|**Kiépített számítás**<br>- 5,1 GB/virtuális mag<br>- Akár 408 GB-os ellátás<br><br>**Kiszolgáló nélküli számítástechnika**<br>- Automatikus méretezés akár 24 GB virtuális magonként<br>- Automatikus méretezés akár 48 GB max|
|Fsv2 sorozat     |- Intel Xeon Platinum 8168 (SkyLake) processzorok<br>- Felvázoló egy tartós minden mag turbó órajel 3,4 GHz-es és a maximális egymagos turbó órajel 3,7 GHz.<br>- Provision 72 virtuális magok (1 virtuális mag = 1 hyper-thread)|- 1,9 GB/virtuális mag<br>- 136 GB-os rendelkezés|
|M sorozat     |- Intel Xeon E7-8890 v3 2,5 GHz-es processzorok<br>- Provision 128 virtuális magok (1 virtuális mag = 1 hyper-thread)|- 29 GB/virtuális mag<br>- 3,7 TB rendelkezés|

\*A [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dinamikus felügyeleti nézetben az Intel SP-8160 (Skylake) processzorokat használó Gen5 adatbázisok hardvergenerálása Gen6 néven jelenik meg. Az összes Gen5-adatbázis erőforrás-korlátai a processzor típusától függetlenül megegyeznek (Broadwell vagy Skylake).

Az erőforráskorlátokról további információt az [Egyes adatbázisok (virtuális mag) erőforrás-korlátok vagy](sql-database-vcore-resource-limits-single-databases.md)rugalmas készletek [(virtuálismag) erőforrás-korlátai című témakörben talál.](sql-database-vcore-resource-limits-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Hardvergeneráció kiválasztása

Az Azure Portalon kiválaszthatja a hardver generálását egy SQL-adatbázis hoz vagy készlet létrehozásakor, vagy módosíthatja a hardver létrehozása egy meglévő SQL-adatbázis vagy-készlet.

**Hardvergenerálás kiválasztása SQL-adatbázis vagy -készlet létrehozásakor**

További információt az [SQL-adatbázis létrehozása](sql-database-single-database-get-started.md)című témakörben talál.

Az **Alapok lapon** válassza az **Adatbázis konfigurálása** hivatkozást a **Számítás + tárolás** szakaszban, majd kattintson a **Konfiguráció módosítása** hivatkozásra:

  ![adatbázis konfigurálása](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Válassza ki a kívánt hardvergenerálást:

  ![hardver kiválasztása](media/sql-database-service-tiers-vcore/select-hardware.png)


**Meglévő SQL-adatbázis vagy -készlet hardvergenerációjának módosítása**

Adatbázis esetén az Áttekintés lapon válassza a **Tarifacsomag hivatkozását:**

  ![hardver módosítása](media/sql-database-service-tiers-vcore/change-hardware.png)

Készlet esetén az Áttekintés lapon válassza a **Konfigurálás**lehetőséget.

Kövesse a konfiguráció módosításához szükséges lépéseket, és válassza ki a hardvergenerálást az előző lépésekben leírtak szerint.

**Hardvergeneráció kiválasztása felügyelt példány létrehozásakor**

Részletes információt a Felügyelt példány létrehozása című [témakörben talál.](sql-database-managed-instance-get-started.md)

Az **Alapok lapon** válassza az **Adatbázis konfigurálása** hivatkozást a **Számítás + tárolás** szakaszban, majd válassza ki a kívánt hardvergenerálást:

  ![felügyelt példány konfigurálása](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Meglévő felügyelt példány hardvergenerációjának módosítása**

# <a name="portal"></a>[Portál](#tab/azure-portal)

A felügyelt példány lapon válassza a Beállítások szakasz ban található **Tarifacsomag-hivatkozást.**

![felügyelt példány hardverének módosítása](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

A **Tarifacsomag** lapon módosíthatja a hardvergenerálást az előző lépésekben leírtak szerint.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Használja a következő PowerShell-parancsfájlt:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

További részletekért ellenőrizze [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) parancsot.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a következő CLI parancsot:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

További részletekért ellenőrizze [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) parancsot.

---

### <a name="hardware-availability"></a>Hardver elérhetősége

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

A Gen4 hardver [fokozatosan megszűnik,](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) és már nem érhető el az új telepítésekhez. Minden új adatbázist gen5 hardveren kell telepíteni.

A Gen5 a világ legtöbb régiójában elérhető.

#### <a name="fsv2-series"></a>Fsv2 sorozat

Az Fsv2 sorozat a következő régiókban érhető el: Ausztrália Közép-Ausztrália, Közép-Ausztrália, Ausztrália Délkeleti, Dél-Ausztrália, Kanada Közép-, Kelet-Ázsia, Kelet-Usa, Franciaország Közép-, India Közép-, India Nyugat-Korea, Korea Central, Korea South, North Európa, Dél-Afrika Észak-, Délkelet-Ázsia, Egyesült Királyság Déli, Egyesült Királyság Nyugat, Nyugat-Európa, Nyugat-UsA 2.


#### <a name="m-series"></a>M sorozat

Az M-sorozat a következő régiókban érhető el: USA keleti régiói, Észak-Európa, Nyugat-Európa, USA nyugati régiója 2.
Az M-sorozat további régiókban is korlátozott anameddig csak korlátozott anameddigcsak rendelkezésre áll. Az itt felsoroltaktól eltérő régiót kérhet, de előfordulhat, hogy egy másik régióban a teljesítés nem lehetséges.

Az M sorozatú előfizetések elérhetőségének engedélyezéséhez [új támogatási kérelem benyújtásával](#create-a-support-request-to-enable-m-series)kell hozzáférést kérni.


##### <a name="create-a-support-request-to-enable-m-series"></a>Hozzon létre támogatási kérelmet az M sorozat engedélyezéséhez: 

1. Válassza **a Súgó + támogatás lehetőséget** a portálon.
2. Válassza **az Új támogatási kérelem lehetőséget.**

Az **Alapok** lapon adja meg a következőket:

1. A **Probléma típusmezőben**válassza a **Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.
2. **Előfizetés** = válassza ki az előfizetést az M-sorozat engedélyezéséhez.
3. A **Kvótatípus hoz**válassza az **SQL-adatbázis**lehetőséget.
4. Válassza a **Tovább** gombot a **Részletek** lapra lépéshez.

A **Részletek** lapon adja meg a következőket:

1. A **PROBLÉMA RÉSZLETEI** szakaszban válassza a **Részletek megadásáhivatkozást.** 
2. Az **SQL Database kvótatípushoz** válassza az **M-sorozat**lehetőséget.
3. A **Régió**mezőben válassza ki az M sorozat engedélyezéséhez a régiót.
    Az M-sorozatú régiók esetében lásd: [M sorozat elérhetősége.](#m-series)

A jóváhagyott támogatási kérelmek általában 5 munkanapon belül teljesülnek.


## <a name="next-steps"></a>További lépések

- SQL-adatbázis létrehozásáról az [SQL-adatbázis létrehozása az Azure Portalhasználatával](sql-database-single-database-get-started.md)című témakörben található.
- Az egyes adatbázisokhoz rendelkezésre álló speciális számítási méreteket és tárolási méreteket az [SQL Database virtuálismag-alapú erőforráskorlátok az egyes adatbázisokhoz című témakörben található.](sql-database-vcore-resource-limits-single-databases.md)
- A rugalmas készletekhez rendelkezésre álló speciális számítási méreteket és tárolási méreteket lásd: [SQL Database virtuálismag-alapú erőforráskorlátok rugalmas készletekhez.](sql-database-vcore-resource-limits-elastic-pools.md)
- A díjszabásrészleteit az [Azure SQL Database díjszabási lapján találja.](https://azure.microsoft.com/pricing/details/sql-database/single/)
