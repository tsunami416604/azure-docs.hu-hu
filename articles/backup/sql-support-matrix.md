---
title: Az SQL Server biztonsági mentés hez készült Azure Backup támogatási mátrixa az Azure virtuális gépein
description: A támogatási beállítások és korlátozások összefoglalása az SQL Server azure-beli virtuális gépeken való biztonsági mentésekor az Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409998"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Az SQL Server biztonsági másolat támogatási mátrixa az Azure virtuális gépein

Az Azure Backup segítségével biztonsági másolatot készíthet az SQL Server-adatbázisokról a Microsoft Azure felhőplatformon üzemeltetett Azure-beli virtuális gépeken. Ez a cikk összefoglalja az sql server biztonsági mentés forgatókönyvek és az Azure-beli virtuális gépek központi telepítések általános támogatási beállításait és korlátait.

## <a name="scenario-support"></a>Forgatókönyv támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott telepítések** | Az SQL Marketplace Azure virtuális gépek és a nem Marketplace-alapú (manuálisan telepített SQL Server) virtuális gépek támogatottak.
**Támogatott földrajzi adatok** | Ausztrália Délkelet (ASE), Kelet-Ausztrália (AE), Ausztrália Középső (AC), Ausztrália Középső 2 (AC) <br> Dél-Brazília (BRS)<br> Kanada Központi (CNC), Kelet-Kanada (CE)<br> Délkelet-Ázsia (SEA), Kelet-Ázsia (EA) <br> USA keleti régiója (EUS), USA keleti régiója 2 (EUS2), USA nyugati középső régiója (WCUS), USA nyugati régiója (WUS); US2 nyugati régió (WUS 2) USA északi középső (NCUS) usa középső (CUS) usa déli középső régiója (SCUS) <br> India Central (INC), India South (INS), India West <br> Kelet-Japán (JPE), Nyugat-Japán (JPW) <br> Korea Central (KRC), Dél-Korea (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli (UKS), Egyesült Királyság Nyugati (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Németország Észak, Németország Nyugat-Közép <br> Svájc Észak, Svájc Nyugati <br> Közép-Franciaország <br> Kína Kelet, Kína Kelet 2, Kína Észak, Kína Észak 2
**Támogatott operációs rendszerek** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> A Linux jelenleg nem támogatott.
**Az SQL Server támogatott verziói** | SQL Server 2019, SQL Server 2017 a [Termék életciklusának keresése lapján](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), az SQL Server 2016 és az SPs részletesen a [Termék életciklusának keresése lapján](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Támogatott .NET verziók** | A virtuális gépre telepített .

## <a name="feature-consideration-and-limitations"></a>A szolgáltatás szempontjai és korlátai

* Az SQL Server biztonsági mentése konfigurálható az Azure Portalon vagy a **PowerShellben.** Nem támogatjuk a CLI-t.
* A megoldás mindkét típusú [üzembe helyezésesetén](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) támogatott – Az Azure Resource Manager virtuális gépek és a klasszikus virtuális gépek.
* Az SQL Server t futtató virtuális gépnek internetkapcsolatra van szüksége az Azure nyilvános IP-címeinek eléréséhez.
* Az SQL Server **feladatátvételi fürtpéldánya (FCI)** nem támogatott.
* A tüköradatbázisok és az adatbázis-pillanatképek biztonsági mentése és visszaállítása nem támogatott.
* Ha egynél több biztonsági mentési megoldást használ az önálló SQL Server-példány vagy az SQL Always a rendelkezésre állási csoporton történő biztonsági mentéséhez, az biztonsági mentési hibákhoz vezethet; tartózkodjanak ettől.
* Biztonsági mentés egy rendelkezésre állási csoport két csomópontkülönben azonos vagy különböző megoldásokkal, biztonsági mentési hiba is vezethet.
* Az Azure Backup csak a teljes és a csak másolásra alkalmas teljes biztonsági mentési típusokat támogatja **az írásvédett** adatbázisokhoz
* A nagy számú fájlt tartalmazó adatbázisok nem védhetők. A támogatott fájlok maximális száma **~1000**.  
* Akár **~2000** SQL Server-adatbázist is biztonsági másolatot lehet tenni egy tárolóban. Több tárolót is létrehozhat abban az esetben, ha nagyobb számú adatbázissal rendelkezik.
* Egyszerre legfeljebb **50** adatbázis biztonsági másolatát konfigurálhatja; ez a korlátozás segít optimalizálni a biztonsági mentés terhelését.
* Akár **2 TB** méretű adatbázisokat is támogatunk; nagyobb méretekben, mint a teljesítményproblémák.
* Ha meg szeretné tudni, hogy kiszolgálónként hány adatbázis védhető meg, vegye figyelembe az olyan tényezőket, mint a sávszélesség, a virtuális gép mérete, a biztonsági mentés gyakorisága, az adatbázis mérete és így tovább. [Töltse le](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) az erőforrás-tervezőt a kiszolgálónként i adatbázis-alapú adatbázisok hozzávetőleges számának kiszámításához a virtuális gép erőforrásai és a biztonsági mentési házirend alapján.
* A rendelkezésre állási csoportok konfigurálásakor a biztonsági mentések a különböző csomópontokból néhány tényező alapján kerülnek ki. A rendelkezésre állási csoport biztonsági mentési viselkedését az alábbiakban foglalunk össze.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Biztonsági másolatot a viselkedésről a Mindig a rendelkezésre állási csoportokon

Azt javasoljuk, hogy a biztonsági mentés egy rendelkezésre állási csoport (AG) egyetlen csomópontján van konfigurálva. A biztonsági mentést mindig ugyanabban a régióban konfigurálja, mint az elsődleges csomópontot. Más szóval mindig szükség van az elsődleges csomópont jelen a régióban, ahol a biztonsági mentés konfigurálása. Ha az AG összes csomópontja ugyanabban a régióban van, ahol a biztonsági mentés konfigurálva van, nincs semmilyen probléma.

#### <a name="for-cross-region-ag"></a>Régióközi AG esetében

* A biztonsági mentési beállításoktól függetlenül a biztonsági mentések csak azokból a csomópontokból futnak, amelyek ugyanabban a régióban találhatók, ahol a biztonsági másolat konfigurálva van. Ennek az az oka, hogy a régiók közötti biztonsági mentések nem támogatottak. Ha csak két csomóponttal rendelkezik, és a másodlagos csomópont a másik régióban található, a biztonsági mentések továbbra is az elsődleges csomópontról futnak (kivéve, ha a biztonsági mentési beállítás "csak másodlagos").
* Ha egy csomópont átadja a feladatátvételt egy olyan régiótól eltérő régióba, ahol a biztonsági mentés konfigurálva van, a biztonsági mentések sikertelenek lesznek a feladatátvételi régió csomópontjain.

A biztonsági mentési beállításoktól és a biztonsági mentések típusától függően (teljes/különbözeti/log/copy-only teljes), a biztonsági mentések egy adott csomópontról (elsődleges/másodlagos) származnak.

#### <a name="backup-preference-primary"></a>Biztonsági mentési beállítás: Elsődleges

**Biztonsági mentés típusa** | **Node**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Elsődleges
Csak másolás teljes |  Elsődleges

#### <a name="backup-preference-secondary-only"></a>Biztonsági mentés idék: Csak másodlagos

**Biztonsági mentés típusa** | **Node**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Csak másolás teljes |  Másodlagos

#### <a name="backup-preference-secondary"></a>Biztonsági mentési beállítás: Másodlagos

**Biztonsági mentés típusa** | **Node**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Csak másolás teljes |  Másodlagos

#### <a name="no-backup-preference"></a>Nincs biztonsági mentési beállítás

**Biztonsági mentés típusa** | **Node**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Csak másolás teljes |  Másodlagos

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [biztonsági másolatot lehet egy Azure-beli](backup-azure-sql-database.md) virtuális rendszeren futó SQL Server-adatbázisról.
