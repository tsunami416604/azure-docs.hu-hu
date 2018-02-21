---
title: "Az Azure Migrate bemutatása | Microsoft Docs"
description: "A cikk áttekintést nyújt az Azure Migrate szolgáltatásról."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 01/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3269b865c4ef3c11a674d7b755faab2bbf5970e3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Az Azure Migrate szolgáltatás a helyszíni számítási feladatokat értékeli ki az Azure-ra történő migráláshoz. A szolgáltatás felméri a migrálásra való alkalmasságot és a teljesítményalapú méretezést, illetve költségbecslést ad a helyi számítógépek Azure-ral történő futtatásával kapcsolatban. Ha átemeléses migráláson gondolkodik, vagy épp a migrálási szakaszok felmérésének elején jár, hasznát veheti ennek a szolgáltatásnak. Az értékelés után olyan szolgáltatásokat használhat a számítógépek Azure-ra történő migrálásához, mint az Azure Site Recovery vagy az Azure Database Migration.

> [!NOTE]
> Az Azure Migrate jelenleg előzetes verzióban érhető el, és támogatja az éles környezetben futó számítási feladatokat.

## <a name="why-use-azure-migrate"></a>Miért javasolt az Azure Migrate használata?

Az Azure Migrate az alábbiakban nyújt segítséget:

- **Az Azure használatához szükséges állapot felmérése**: segít megállapítani, hogy a helyszíni számítógépek alkalmasak-e az Azure-ban való futtatásra. 
- **Javaslatok a méretekkel kapcsolatban**: méretezési javaslatokat kaphat az Azure-beli virtuális gépekhez, a helyszíni virtuális gépek korábbi teljesítménye alapján. 
- **Becsült havi költség**: megkaphatja a helyszíni gépek Azure-ban való futtatásának költségbecslését.  
- **Megbízható migrálás**: megjelenítheti a helyszíni virtuális gépek függőségeit olyan gépcsoportok létrehozásához, amelyeket együtt fog értékelni és migrálni. Pontosan megtekintheti az adott számítógép vagy a csoport összes gépének függőségeit.

## <a name="current-limitations"></a>Aktuális korlátozások

- Jelenleg értékelheti a helyszíni VMware virtuális gépeket az Azure-beli virtuális gépekre való migráláshoz.

> [!NOTE]
> A Hyper-V támogatása az ütemterv részét képezi, és hamarosan engedélyezve lesz. Addig is az [Azure Site Recovery Deployment Planner](http://aka.ms/asr-dp-hyperv-doc) használatát javasoljuk a Hyper-V-alapú számítási feladatok migrálásának megtervezéséhez. 

- Egyetlen felderítéssel akár 1000 virtuális gépet, egyetlen projekt részeként pedig akár 1500 virtuális gépet is felderíthet. Ezenkívül egyetlen értékeléssel akár 400 virtuális gépet is értékelhet. Ha ennél többet kell felderítenie vagy értékelnie, növelheti a felderítések vagy az értékelések számát. [További információk](how-to-scale-assessment.md).
- Az értékelni kívánt virtuális gépet egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú vCenter Servernek kell felügyelnie.
- Azure Migrate-projektet csak az USA középnyugati régiójában lehet létrehozni. Ez azonban nem befolyásolja a migrálás megtervezését egy másik Azure-beli célhelyre. A migrálási projekt helyét a rendszer csak a helyszíni környezetből felderített metaadatok tárolására használja.
- Az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.

## <a name="what-do-i-need-to-pay-for"></a>Mi az, amiért fizetnem kell?

További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/) talál.


## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

Az értékelés segítségével azonosíthatja a helyszíni virtuális gépek Azure-nak való megfelelőségét, valamint javaslatokat kaphat a megfelelő méretre, illetve költségbecsléseket a virtuális gépek Azure-ban történő futtatására vonatkozóan. Az értékelések az alábbi táblázatban összefoglalt tulajdonságokon alapulnak. Ezeket a tulajdonságokat az Azure Migrate portálon módosíthatja. 

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni. Az alapértelmezetten beállított hely az USA 2. nyugati régiója. 
**Tárhely-redundancia** | Az a tárolótípus, amelyet az Azure-beli virtuális gépek a migrálás után használni fognak. Az alapértelmezett típus az LRS.
**Díjszabások** | Az értékelés figyelembe veszi, hogy rendelkezik-e frissítési garanciával, és használhatja-e az [Azure Hybrid Use Benefitet](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Emellett az alkalmazandó Azure-ajánlatokat is figyelembe veszi, és lehetővé teszi, hogy előfizetés-specifikus kedvezményeket (%) adjon meg az ajánlaton felül. 
**Tarifacsomag** | Megadhatja az Azure-beli virtuális gépek [tarifacsomagját (alapszintű/standard)](../virtual-machines/windows/sizes-general.md). Ez segít abban, hogy megfelelő Azure-beli virtuálisgép-családba végezze a migrálást annak alapján, hogy éles környezetben van-e. Alapértelmezés szerint a rendszer a [standard](../virtual-machines/windows/sizes-general.md) csomagot használja.
**Teljesítményelőzmények** | Alapértelmezés szerint az Azure Migrate a helyszíni gépek teljesítményét egyhavi előzmények alapján, 95%-os százalékértékkel értékeli ki. Módosíthatja a beállítást.
**Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. Az alapértelmezett kényelmi beállítás 1.3x.


## <a name="how-does-azure-migrate-work"></a>Hogyan működik az Azure Migrate?

1.  Hozzon létre egy Azure Migrate projektet.
2.  Az Azure Migrate egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet használ a helyszíni gépek adatainak felderítésére. A berendezés létrehozásához töltse le a telepítőfájlt Open Virtualization Appliance (.ova) formátumban, majd importálja virtuális gépként a helyszíni vCenter Serverre.
3.  Csatlakozzon a virtuális géphez konzolkapcsolat használatával a vCenter Serverben, csatlakozás közben adjon meg új jelszót a virtuális géphez, majd a felderítés elindításához futtassa a gyűjtőalkalmazást a virtuális gépen.
4.  A gyűjtő a VMware PowerCLI-parancsmagok segítségével összegyűjti a virtuális gépek metaadatait. A felderítés ügynök nélkül történik, és nem telepít semmit a VMware-gazdagépekre vagy a virtuális gépekre. Az összegyűjtött metaadatok a virtuális gépek adatait is tartalmazzák (magok, memória, lemezek, lemezméretek és hálózati adapterek). A gyűjtő ezenkívül a virtuális gépek teljesítményadatait is gyűjti, például a processzor- és memóriahasználatot, a lemez IOPS-t, a lemezek átviteli sebességét (MB/s) és a hálózati kimenetet (MB/s).
5.  A rendszer továbbítja a metaadatokat az Azure Migrate projektnek. Ezeket az Azure Portalon tekintheti meg.
6.  Csoportokba rendezheti a felderített virtuális gépeket az értékeléshez. Például egy csoportba helyezheti az azonos alkalmazást futtató virtuális gépeket. A még pontosabb csoportosítás érdekében megtekintheti egy adott géphez vagy egy csoport gépeihez tartozó függőségeket, illetve a csoportok összes gépéhez tartozókat és pontosíthatja a csoportot.
7.  Miután létrejött a csoport, létrehozhat számára egy értékelést. 
8.  Az értékelés befejeződése után megtekintheti azt a portálon, vagy letöltheti Excel-formátumban.



  ![Azure Planner-architektúra](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Milyen követelmények vonatkoznak a portokra?

A táblázat összefoglalja az Azure Migrate kommunikációjához szükséges portokat.

|Összetevő          |Kommunikációs cél     |Szükséges port  |Ok   |
|-------------------|------------------------|---------------|---------|
|Gyűjtő          |Azure Migrate szolgáltatás   |443-as TCP        |A gyűjtő a 443-as SSL-porton keresztül csatlakozik a szolgáltatáshoz|
|Gyűjtő          |vCenter Server          |9443-as alapértelmezett   | Alapértelmezés szerint a gyűjtő a 9443-as porton csatlakozik a vCenter Serverhez. Ha a kiszolgáló egy másik porton figyel, azt kimenő portként kell konfigurálni a gyűjtő virtuális gépen. |
|Helyszíni virtuális gép     | Operations Management Suite (OMS) Workspace          |[443-as TCP](../log-analytics/log-analytics-windows-agent.md) |Az MMA-ügynök a 443-as TCP-portot használja a Log Analyticshez való csatlakozáshoz. Erre a portra csak akkor van szükség, ha a függőségmegjelenítési funkciót használja, és telepíti a Microsoft Monitoring Agent (MMA) ügynököt. |


  
## <a name="what-happens-after-assessment"></a>Mi történik az értékelés után?

Miután értékelte a helyszíni gépeket az Azure Migrate szolgáltatással történő migráláshoz, több eszközzel is végrehajthatja a migrálást:

- **Azure Site Recovery**: Az Azure Site Recovery segítségével a következőképpen végezhet migrálást az Azure-ba:
  - Készítse elő az Azure-erőforrásokat, köztük egy Azure-előfizetést, egy Azure Virtual Networköt és egy tárfiókot.
  - Készítse elő a helyszíni VMware-kiszolgálókat a migrálásra. Ellenőrizze a Site Recovery VMware-támogatási követelményeit, készítse elő a VMware-kiszolgálókat a felderítésre, és készüljön elő a Site Recovery mobilitási szolgáltatás telepítéséhez azokon a virtuális gépeken, amelyeket migrálni kíván. 
  - Állítsa be a migrálást. Ehhez állítson be egy Recovery Services-tárolót, konfigurálja a forrás és a cél migrálási beállításait, állítson be egy replikációs szabályzatot, majd engedélyezze a replikációt. Végrehajthat egy vészhelyreállítási próbát is annak ellenőrzéséhez, hogy megfelelően működik-e a virtuális gépek Azure-ba való migrálása.
  - Futtasson feladatátvételt a helyszíni gépek Azure-ba való migrálásához. 
  - [További információkat](../site-recovery/tutorial-migrate-on-premises-to-azure.md) a Site Recovery migrálási oktatóanyagában találhat.

- **Azure Database Migration**: Ha a helyszíni gépek adatbázist futtatnak (például SQL Servert, MySQL-t vagy Oracle-t), az adatbázis Azure-ba való migrálásához használhatja az Azure Database Migration Service-t. [További információk](https://azure.microsoft.com/campaigns/database-migration/).



## <a name="next-steps"></a>További lépések 
[Kövesse az oktatóanyag](tutorial-assessment-vmware.md) lépéseit értékelés létrehozásához egy helyszíni VMware virtuális gép számára.