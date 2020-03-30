---
title: Példánykészletek (előzetes verzió)
description: Ez a cikk ismerteti az Azure SQL Database példánykészletek (előzetes verzió).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256210"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Mik azok az SQL Database-példánykészletek (előzetes verzió)?

A példánykészletek az Azure SQL Database új erőforrásai, amely kényelmes és költséghatékony módot biztosít a kisebb SQL-példányok nagy méretű felhőbe való áttelepítésére.

A példánykészletek lehetővé teszik a számítási erőforrások migrálási követelményeknek megfelelő, előzetes létrehozását. Ezt követően több egyedi felügyelt példányt is üzembe helyezhet, az előzetesen megadott számítási szintig. Ha például előre kiépít 8 virtuális magot, telepíthet két 2 virtuálismagos és egy 4 virtuálismagos példányt, majd áttelepítheti az adatbázisokat ezekbe a példányokba. Mielőtt a példánykészletek elérhetővé, kisebb és kevésbé nagy számítási igényű számítási feladatok gyakran kell konszolidálni egy nagyobb felügyelt példány, amikor a felhőbe való áttelepítés. Az adatbáziscsoportok nagy példányra való áttelepítésének szükségessége általában gondos kapacitástervezést és erőforrás-irányítást, további biztonsági szempontokat és néhány további adatkonszolidációs munkát igényel a példány szintjén.

Emellett a példánykészletek támogatják a natív virtuális hálózat integrációját, így több példánykészletet és több egyetlen példányt telepíthet ugyanabban az alhálózatban.


## <a name="key-capabilities-of-instance-pools"></a>A példánykészletek főbb képességei

A példánykészletek a következő előnyöket biztosítják:

1. 2 virtuálismagos példány okainak üzemeltetésére való képesség. *Csak például készletek példányaiesetén. \**
2. Kiszámítható és gyors példánytelepítési idő (legfeljebb 5 perc).
3. Minimális IP-címkiosztás.

Az alábbi ábra egy olyan példánykészletet mutat be, amelyegy virtuális hálózati alhálózaton belül telepített több példányt is bemutat.

![példánykészlet több példánysal](./media/sql-database-instance-pools/instance-pools1.png)

A példánykészletek lehetővé teszik több példány üzembe helyezését ugyanazon a virtuális gépen, ahol a virtuális gép számítási mérete a készlethez lefoglalt virtuális magok teljes számán alapul. Ez az architektúra lehetővé teszi a virtuális gép *particionálását* több példányra, amely bármilyen támogatott méretű lehet, beleértve a 2 virtuális magot (2 virtuálismagpéldány csak a készletek példányai számára érhető el).

A készlet kezdeti üzembe helyezése után a készlet példányaiin végzett felügyeleti műveletek sokkal gyorsabbak. Ezek a műveletek gyorsabbak, mert egy [virtuális fürt](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (dedikált virtuális gépek) telepítése vagy bővítése nem része a felügyelt példány kiépítésének.

Mivel egy készlet összes példánya ugyanazt a virtuális gépet tartalmazza, a teljes IP-foglalás nem függ a telepített példányok számától, ami kényelmes a keskeny IP-hettel rendelkező alhálózatokban történő üzembe helyezéshez.

Minden készlet csak kilenc IP-cím rögzített IP-címfoglalással rendelkezik (az alhálózatban lévő öt, a saját igényeinek fenntartott IP-cím nélkül). További információt az [egyes példányok alhálózati méretkövetelményei című témakörben talál.](sql-database-managed-instance-determine-size-vnet-subnet.md)

## <a name="application-scenarios-for-instance-pools"></a>Alkalmazási forgatókönyvek példánykészletekhez

Az alábbi lista azokat a főfelhasználási eseteket tartalmazza, amelyekben a példánykészleteket figyelembe kell venni:

- *Sql-példányok egy csoportjának áttelepítése* egyszerre, ahol a többség kisebb méretű (például 2 vagy 4 virtuális mag).
- Forgatókönyvek, ahol *kiszámítható és rövid példány létrehozása vagy méretezése* fontos. Például egy új bérlő üzembe helyezése egy több-bérlős SaaS-alkalmazáskörnyezetben, amely példányszintű képességeket igényel.
- Olyan esetek, amikor fontos, hogy *fix költség-* vagy *költségkorláttal rendelkezik.Scenarios* where having a fixed cost or spending limit is important. Például a megosztott fejlesztési-teszt vagy bemutató környezetek egy rögzített (vagy ritkán változó) méretű, ahol rendszeresen telepíteni felügyelt példányok, ha szükséges.
- Olyan esetekben, ahol a virtuális hálózat alhálózatában *fontos a minimális IP-címfoglalás.* A készlet minden példánya egy virtuális gépen osztozik, így a lefoglalt IP-címek száma alacsonyabb, mint egyetlen példány esetén.


## <a name="architecture-of-instance-pools"></a>A példánygyűjtő medencék architektúrája

A példánykészletek architektúrája hasonló a rendszeres felügyelt példányokhoz *(egyetlen példány).* Az [Azure virtuális hálózatokon (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) belüli telepítések támogatásához, valamint az elkülönítés és a biztonság biztosításához a példánykészletek virtuális fürtökre is [támaszkodnak.](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) A virtuális fürtök az ügyfél virtuális hálózati alhálózatán belül telepített elkülönített virtuális gépek dedikált készletét képviselik.

A fő különbség a két központi telepítési modell között az, hogy a példánykészletek lehetővé teszik több SQL Server-folyamat központi telepítését ugyanazon a virtuálisgép-csomóponton, amelyek a [Windows feladatobjektumok](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)által szabályozott erőforrások, míg az egyes példányok mindig egyedül vannak egy virtuálisgép-csomóponton.

Az alábbi ábrán egy példánykészlet és két különálló példány látható ugyanabban az alhálózatban, és mindkét telepítési modell fő architekturális részleteit mutatja be:

![mintakészlet és két egyedi példány](./media/sql-database-instance-pools/instance-pools2.png)

Minden példánykészlet külön virtuális fürtöt hoz létre alatta. Egy készleten belüli példányok és az ugyanazon alhálózatban üzembe helyezett egyetlen példány nem osztja meg az SQL Server-folyamatokhoz és az átjáró-összetevőkhöz rendelt számítási erőforrásokat, ez biztosítja a teljesítmény kiszámíthatóságát.

## <a name="instance-pools-resource-limitations"></a>Példánykészletek erőforrás-korlátai

A példánykészletekre és a készletekben lévő példányokra többféle erőforrás-korlátozás is vonatkozik:

- A példánykészletek csak a Gen5 hardveren érhetők el.
- A készleten belüli példányok dedikált PROCESSZOR- és RAM-mal rendelkeznek, így a virtuális magok összesített számának az összes példányban kisebbnek vagy egyenlőnek kell lennie a készlethez rendelt virtuális magok számával.
- Minden [példányszint-korlát](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) a készleten belül létrehozott példányokra vonatkozik.
- A példányszintű korlátok mellett két korlát is van *érvényben a példánykészlet szintjén:*
  - Teljes tárhelykészletenként (8 TB).
  - Az adatbázisok száma készletenként összesen (100).

A teljes tárhelyfoglalásnak és az adatbázisok számának az összes példányban alacsonyabbnak vagy egyenlőnek kell lennie a példánykészletek által elérhetőkorlátokkal.

- A példánykészletek 8, 16, 24, 32, 40, 64 és 80 virtuális magot támogatnak.
- A készleteken belüli felügyelt példányok támogatják a 2, 4, 8, 16, 24, 32, 40, 64 és 80 virtuális magokat.
- A készleteken belüli felügyelt példányok 32 GB és 8 TB közötti tárolóméretet támogatnak, kivéve:
  - 2 virtuálismagos példány 32 GB és 640 GB közötti méreteket támogat
  - 4 virtuálismagos példány 32 GB és 2 TB közötti méreteket támogat

A [service tier tulajdonság](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) a példánykészlet erőforráshoz van társítva, így a készlet összes példányának meg kell egyeznie a készlet szolgáltatási szintjével. Jelenleg csak az Általános célú szolgáltatási szint érhető el (lásd a következő szakaszt az aktuális előzetes verzió korlátozásáról).

### <a name="public-preview-limitations"></a>A nyilvános előzetes verzió korlátozásai

A nyilvános előzetes verzió a következő korlátozásokkal rendelkezik:

- Jelenleg csak az Általános célú szolgáltatásszint érhető el.
- A példánykészletek nem méretezhetők a nyilvános előzetes verzióban, ezért fontos az üzembe helyezés előtt a gondos kapacitástervezés.
- Az Azure Portal támogatása a példánykészlet létrehozása és konfigurációja még nem érhető el. A példánykészletek en végzett összes művelet csak a PowerShell által támogatott. Az előre létrehozott készletben a kezdeti példányüzembe helyezés t csak a PowerShell támogatja. Miután üzembe helyezett egy készletben, felügyelt példányok frissíthetők az Azure Portalon keresztül.
- A készleten kívül létrehozott felügyelt példányok nem helyezhetők át meglévő készletbe, és a készleten belül létrehozott példányok nem helyezhetők át egyetlen példányként vagy egy másik készletbe.
- Fenntartott példány díjszabása (licenc tartalmazza, vagy az Azure Hybrid Benefit) nem érhető el.

## <a name="sql-features-supported"></a>Támogatott SQL-szolgáltatások

A készletekben létrehozott példányok ugyanazokat a [kompatibilitási szinteket és szolgáltatásokat támogatják, amelyeket az egykezelt példányok támogatnak.](sql-database-managed-instance.md#sql-features-supported)

A készletben telepített minden felügyelt példány rendelkezik az SQL-ügynök külön példányával.

Választható funkciók vagy szolgáltatások, amelyek megkövetelik, hogy válasszon bizonyos értékeket (például a példányszintű rendezés, időzóna, nyilvános végpont adatforgalom, feladatátvételi csoportok) vannak konfigurálva példányszinten, és eltérő lehet az egyes példányok egy készletben.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Bár a készleteken belüli felügyelt példányok dedikált virtuális maggal és RAM-mal rendelkeznek, megosztják a helyi lemezt (tempdb használat esetén) és hálózati erőforrásokat. Nem valószínű, de a *zajos szomszéd* hatás, ha több példány a készletben magas erőforrás-felhasználás sal egy időben. Ha ezt a viselkedést észleli, fontolja meg ezeket a példányokat egy nagyobb készletbe vagy egyetlen példányra.

## <a name="security-considerations"></a>Biztonsági szempontok

Mivel a készletben telepített példányok ugyanazt a virtuális gépet rendelkeznek, érdemes megfontolni a magasabb biztonsági kockázatot okozó szolgáltatások letiltását, vagy a hozzáférési engedélyek határozott szabályozását ezekhez a szolgáltatásokhoz. Például, CLR integráció, natív biztonsági mentés és visszaállítás, adatbázis e-mail, stb.

## <a name="instance-pool-support-requests"></a>Példánykészlet támogatási kérelmei

Hozzon létre és kezeljen támogatási kérelmeket az [Azure Portalon.](https://portal.azure.com)

Ha a példánykészlet telepítésével (létrehozással vagy törléssel) kapcsolatos problémákat tapasztal, győződjön meg arról, hogy a **Probléma altípus** mezőben meghatározta a **példánykészleteket.**

![példánykészletek támogatási kérelme](./media/sql-database-instance-pools/support-request.png)

Ha egy készleten belüli egyetlen példányokkal vagy adatbázisokkal kapcsolatos problémákat tapasztal, hozzon létre egy rendszeres támogatási jegyet az Azure SQL Database felügyelt példányaiszámára.

Nagyobb felügyelt példányok központi telepítésének létrehozásához (példánykészletekkel vagy anélkül), előfordulhat, hogy nagyobb regionális kvótát kell beszereznie. További információ: [Request kvóta emelése az Azure SQL Database.](quota-increase-request.md) Vegye figyelembe, hogy ha példánykészleteket használ, a központi telepítési logika összehasonlítja *a készlet szintjén a teljes* virtuálismag-felhasználást a kvótával annak meghatározásához, hogy a kvóta további növelése nélkül hozhat-e létre új erőforrásokat.

## <a name="instance-pool-billing"></a>Példánykészlet számlázása

A példánykészletek lehetővé teszik a számítási és tárolási egymástól független méretezést. Az ügyfelek fizetnek a készlet erőforrás virtuális magokban mért számítási, és a kapcsolódó tároló minden példány gigabájtban mért (az első 32 GB-os ingyenes minden példány).

egy készlet virtuálismag-árát attól függetlenül, hogy hány példány van telepítve az adott készletben.

A számítási ár (virtuális magokban mérve) két árképzési lehetőség áll rendelkezésre:

  1. *Licenc tartalmazza:* Az ÁR AZ SQL licencek tartalmazza. Ez azoknak az ügyfeleknek szól, akik úgy döntenek, hogy nem alkalmazzák a meglévő SQL Server-licenceket a Frissítési Garanciával.
  2. *Azure Hybrid Benefit:* Az Azure Hybrid Benefit for SQL Server csökkentett ár. Az ügyfelek ezt az árat a frissítési garanciával rendelkező meglévő SQL Server licenceik használatával választhatják. A jogosultságról és egyéb részletekről az [Azure Hybrid Benefit (Azure Hybrid Benefit) oldalon](https://azure.microsoft.com/pricing/hybrid-benefit/)talál.

A készlet egyes példányai nem választhatók különböző árképzési beállításokhoz. A szülőkészletben lévő összes példánynak licenccel járó vagy Azure Hybrid Benefit áron kell lennie. A készlet licencmodellje a készlet létrehozása után módosítható.

> [!IMPORTANT]
> Ha olyan licencmodellt ad meg a példányhoz, amely eltér a készletben, a készlet árát használja a rendszer, és figyelmen kívül hagyja a példány szintértékét.

Ha a [fejlesztési-teszt juttatásra jogosult előfizetéseken](https://azure.microsoft.com/pricing/dev-test/)hoz létre példánykészleteket, automatikusan akár 55%-os kedvezményes díjakat kap az Azure SQL által felügyelt példányon.

A példánykészlet díjszabásának részletes adatait a *példánykészletek* szakasza a [felügyelt példány árképzési lapján találja.](https://azure.microsoft.com/pricing/details/sql-database/managed/)

## <a name="next-steps"></a>További lépések

- A példánykészletek első lépései az [SQL Database példánykészletei útmutatójában.](sql-database-instance-pools-how-to.md)
- Az első felügyelt példány létrehozásáról a Rövid útmutató című [témakörben olvashat.](sql-database-managed-instance-get-started.md)
- A szolgáltatások és az összehasonlító lista az SQL általános szolgáltatásai című témakörben [található.](sql-database-features.md)
- A virtuális hálózat konfigurációjáról további információt a [felügyelt példány virtuális hálózatának konfigurálása](sql-database-managed-instance-connectivity-architecture.md)című témakörben talál.
- Felügyelt példányt létrehozó és biztonsági másolatból adatbázist visszaállító rövidútmutatóról a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)című témakörben található.
- Az Azure Database Migration Service (DMS) áttelepítési szolgáltatását használó oktatóanyagról a [Felügyelt példányáttelepítés DMS használatával című](../dms/tutorial-sql-server-to-managed-instance.md)témakörben található.
- A felügyelt példányok adatbázis-teljesítményének speciális figyelése beépített hibaelhárítási intelligenciával az [Azure SQL Database monitorozása az Azure SQL Analytics használatával című](../azure-monitor/insights/azure-sql.md)témakörben található.
- A díjszabásról az [SQL Database által felügyelt példányok díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/)című témakörben talál.
