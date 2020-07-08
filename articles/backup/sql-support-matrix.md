---
title: Azure Backup támogatási mátrix az Azure-beli virtuális gépeken futó SQL Server biztonsági mentéshez
description: Összefoglalja a támogatási beállításokat és korlátozásokat, amikor Azure-beli virtuális gépeken SQL Server biztonsági mentést készít a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: f9594b9157f84a0536ffd4b62f792fd86fb1c243
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234250"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Az Azure-beli virtuális gépek SQL Server biztonsági mentésének támogatási mátrixa

A Azure Backup használatával biztonsági mentést készíthet a Microsoft Azure Cloud platformon üzemeltetett Azure-beli virtuális gépeken futó SQL Server-adatbázisokról. Ez a cikk az Azure-beli virtuális gépek SQL Server biztonsági mentésének forgatókönyveit és üzembe helyezéseit érintő általános támogatási beállításokat és korlátozásokat foglalja össze.

## <a name="scenario-support"></a>Forgatókönyvek támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | Az SQL Marketplace Azure-beli virtuális gépek és a nem piactér (SQL Server manuálisan telepített) virtuális gépek támogatottak.
**Támogatott régiók** | Dél-Ausztrália, Kelet-Ausztrália (AE), Ausztrália középső régiója (AC), Ausztrália középső régiója (AC) <br> Dél-Brazília (BRS)<br> Közép-Kanada (CNC), Kelet-Kanada (CE)<br> Dél-Kelet-Ázsia (tenger), Kelet-Ázsia (EA) <br> USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), USA nyugati középső régiója (WCUS), USA nyugati régiója (WUS); USA 2. nyugati régiója (WUS 2), USA északi középső régiója (NCUS), USA középső régiója (ke), USA déli középső régiója (SCUS) <br> India Central (INC), Dél-India (INS), Nyugat-India <br> Kelet-Japán (JPE), Nyugat-Japán (JPW) <br> Korea középső régiója (KRC), Dél-Korea (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli régiója (UKS), Egyesült Királyság nyugati régiója (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD – középső régió, US DoD – keleti régió <br> Észak-Németország, Középnyugat-Németország <br> Észak-Svájc, Nyugat-Svájc <br> Közép-Franciaország <br> Kelet-Kína, Kelet-Kína 2, Észak-Kína, Észak-Kína 2
**Támogatott operációs rendszerek** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> A Linux jelenleg nem támogatott.
**Támogatott SQL Server verziók** | SQL Server 2019, SQL Server 2017 a [termék életciklusának keresése lapon](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 és az SPS a [termék életciklusa lapon](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, standard, web, Developer, Express.
**Támogatott .NET-verziók** | .NET-keretrendszer 4.5.2-es vagy újabb verziója telepítve a virtuális gépen

## <a name="feature-consideration-and-limitations"></a>Funkciókkal kapcsolatos szempontok és korlátozások

* SQL Server biztonsági mentés konfigurálható a Azure Portal vagy a **PowerShellben**. Nem támogatjuk a CLI-t.
* A megoldás mindkét típusú [üzembe helyezést](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) támogatja – Azure Resource Manager virtuális gépeket és klasszikus virtuális gépeket.
* A SQL Server rendszert futtató virtuális gépek internetkapcsolatot igényelnek az Azure nyilvános IP-címeinek eléréséhez.
* SQL Server **feladatátvevő fürt példánya (** nem támogatott).
* A tükrözési adatbázisok és az adatbázis-Pillanatképek biztonsági mentésére és visszaállítására vonatkozó műveletek nem támogatottak.
* Ha több biztonsági mentési megoldást használ a különálló SQL Server példány vagy az SQL always on rendelkezésre állási csoport biztonsági mentéséhez, a biztonsági mentés sikertelen lehet; ne tegye meg.
* A rendelkezésre állási csoport két csomópontjának különálló, azonos vagy eltérő megoldásokkal történő biztonsági mentése a biztonsági mentés sikertelenségét is eredményezheti.
* A Azure Backup csak a teljes és csak a csak **olvasható** adatbázisok teljes biztonsági mentési típusait támogatja
* A nagy mennyiségű fájllal rendelkező adatbázisok védelme nem biztosítható. A támogatott fájlok maximális száma **~ 1000**.  
* Egy tárolóban akár **2000** SQL Server adatbázisra is készíthet biztonsági mentést. Több tárolót is létrehozhat abban az esetben, ha nagyobb számú adatbázisra van szüksége.
* A biztonsági mentést akár **50** adatbázisra is konfigurálhatja egy menet közben. Ez a korlátozás segít optimalizálni a biztonsági mentési terheléseket.
* Az adatbázisok akár **2 TB** -os méretekben is támogatottak; a teljesítménnyel kapcsolatos problémáknál nagyobb méreteket eredményezhet.
* Ha szeretné, hogy a kiszolgálók hány adatbázis védelmét tudják védeni, vegye figyelembe az olyan tényezőket, mint a sávszélesség, a virtuális gép mérete, a biztonsági mentés gyakorisága, az adatbázis mérete stb. [Töltse le](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) az erőforrás-tervezőt, hogy a virtuális gépek erőforrásai és a biztonsági mentési szabályzat alapján számítsa ki a kiszolgálónként használható adatbázisok hozzávetőleges számát.
* A rendelkezésre állási csoportok konfigurálásakor a biztonsági mentések a különböző csomópontokból származnak, néhány tényező alapján. A rendelkezésre állási csoport biztonsági mentési viselkedését az alábbiakban összegzi.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Biztonsági mentés működése az Always On rendelkezésreállási csoportok esetén

Azt javasoljuk, hogy a biztonsági mentés a rendelkezésre állási csoport (AG) csak egy csomópontjára legyen konfigurálva. Mindig konfigurálja a biztonsági mentést ugyanabban a régióban, mint az elsődleges csomópontot. Más szóval mindig szüksége lesz az elsődleges csomópontra abban a régióban, ahol a biztonsági mentést konfigurálja. Ha az AG összes csomópontja ugyanabban a régióban van, ahol a biztonsági mentés be van állítva, nincs semmi gond.

#### <a name="for-cross-region-ag"></a>Régiók közötti AG

* A biztonsági mentéstől függetlenül a biztonsági mentések csak azokon a csomópontokon futnak, amelyek ugyanabban a régióban találhatók, ahol a biztonsági mentés konfigurálva van. Ennek az az oka, hogy a régiók közötti biztonsági másolatok nem támogatottak. Ha csak két csomóponttal rendelkezik, és a másodlagos csomópont a másik régióban található, akkor a biztonsági mentések továbbra is az elsődleges csomópontról fognak futni (kivéve, ha a biztonsági mentési beállítások "másodlagosak").
* Ha egy csomópont feladatátvételt hajt végre egy olyan régióba, amely eltér a biztonsági mentés konfigurálásának helyétől, a biztonsági mentések sikertelenek lesznek a feladatátvételi régió csomópontjain.

A biztonsági mentési beállítások és a biztonsági másolatok típusaitól függően (teljes/különbözeti/napló/csak másolás) a biztonsági másolatok egy adott csomópontból (elsődleges/másodlagos) származnak.

#### <a name="backup-preference-primary"></a>Biztonsági mentési beállítások: elsődleges

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Elsődleges
Csak másolás – teljes |  Elsődleges

#### <a name="backup-preference-secondary-only"></a>Biztonsági mentési beállítás: csak másodlagos

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Csak másolás – teljes |  Másodlagos

#### <a name="backup-preference-secondary"></a>Biztonsági mentési beállítások: másodlagos

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Csak másolás – teljes |  Másodlagos

#### <a name="no-backup-preference"></a>Nincs biztonsági mentési beállítás

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Csak másolás – teljes |  Másodlagos

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [készíthet biztonsági mentést egy](backup-azure-sql-database.md) Azure-beli virtuális gépen futó SQL Server-adatbázisról.
