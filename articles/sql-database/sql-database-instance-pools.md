---
title: Azure SQL Database példány-készletek (előzetes verzió)
description: Ez a cikk Azure SQL Database példányok készleteit (előzetes verzió) ismerteti.
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
ms.openlocfilehash: 7d8c316d5c78cfe09bcf134b5a5c513e1c007d74
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689764"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Mik azok a SQL Database példány-készletek (előzetes verzió)?

A példány-készletek a Azure SQL Database egy új erőforrása, amely kényelmes és költséghatékony megoldást kínál a kisebb SQL-példányok felhőbe való átméretezésére.

A példánykészletek lehetővé teszik a számítási erőforrások migrálási követelményeknek megfelelő, előzetes létrehozását. Ezt követően több egyedi felügyelt példányt is üzembe helyezhet, az előzetesen megadott számítási szintig. Ha például előre kiépít 8 virtuális mag, két virtuális mag és egy 4 virtuális mag példányt telepíthet, majd áttelepítheti az adatbázisokat ezekben a példányokban. A példányok elérhetővé válása előtt a kisebb és kisebb számítási igényű munkaterheléseket gyakran egy nagyobb felügyelt példányba kell összevonni a felhőbe való Migrálás során. Az adatbázis-csoportok nagy példányra való áttelepítéséhez általában körültekintő kapacitás-tervezésre és erőforrás-szabályozásra, további biztonsági megfontolásokra és néhány további adatösszesítésre van szükség a példány szintjén.

A példányok emellett támogatják a natív VNet-integrációt is, így több példány-készletet és több példányt is telepíthet ugyanabban az alhálózatban.


## <a name="key-capabilities-of-instance-pools"></a>Példány-készletek főbb képességei

A példány-készletek a következő előnyöket nyújtják:

1. 2 virtuális mag-példány üzemeltetésének képessége. *csak a példányokhoz tartozó példányok esetében\** .
2. Kiszámítható és gyors példány üzembe helyezési ideje (legfeljebb 5 perc).
3. Minimális IP-cím kiosztása.

A következő ábra egy olyan példány-készletet ábrázol, amely egy virtuális hálózati alhálózaton belül több példányt is üzembe helyez.

![példány-készlet több példánnyal](./media/sql-database-instance-pools/instance-pools1.png)

A példány-készletek lehetővé teszik több példány üzembe helyezését ugyanazon a virtuális gépen, ahol a virtuális gép számítási mérete a készlethez lefoglalt virtuális mag teljes száma alapján történik. Ez az architektúra lehetővé teszi a virtuális gép több példányra történő *particionálását* , ami bármilyen támogatott méret lehet, beleértve a 2 virtuális mag (2 virtuális mag példány csak a készletekben).

A készlet példányain lévő felügyeleti műveletek sokkal gyorsabbak, ha a készletet először telepítették. Ezek a műveletek gyorsabbak, mert a [virtuális fürtök](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) üzembe helyezése vagy kiterjesztése (a virtuális gépek dedikált készlete) nem része a felügyelt példány kiépítésének.

Mivel a készlet összes példánya ugyanazt a virtuális gépet használja, a teljes IP-foglalás nem függ az üzembe helyezett példányok számától, ami kényelmes a keskeny IP-tartománnyal rendelkező alhálózatokban való üzembe helyezéshez.

Az egyes készletek csak kilenc IP-címet foglalnak magukban (az alhálózat öt IP-címével együtt, amely a saját igényeihez van lefoglalva). Részletekért lásd: [az alhálózati méretre vonatkozó követelmények az egyes példányok esetében](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>A példányok használati forgatókönyvei

Az alábbi lista azokat a fő felhasználási eseteket tartalmazza, amelyekben a példány-készleteket figyelembe kell venni:

- SQL- *példányok egy csoportjának* áttelepítése egy időben, ahol a többség kisebb méretű (például 2 vagy 4 virtuális mag).
- Olyan forgatókönyvek, amelyekben a *kiszámítható és a rövid példányok létrehozása vagy skálázása* fontos. Például egy új bérlő üzembe helyezése egy több-bérlős SaaS-alkalmazás környezetében, amely a példány szintű képességeket igényli.
- Olyan forgatókönyvek, amelyek esetében fontos a *rögzített költségek* vagy a költségkeretek *korlátozása* . Például egy rögzített (vagy ritkán változó) méretű megosztott fejlesztési-tesztelési vagy bemutató környezetek futtatásához, ahol szükség esetén rendszeresen telepítheti a felügyelt példányokat.
- Olyan forgatókönyvek esetén, ahol a VNet-alhálózatok *minimális IP-címeinek kiosztása* fontos. A készlet összes példánya egy virtuális gépet oszt meg, így a lefoglalt IP-címek száma alacsonyabb, mint az egyes példányok esetében.


## <a name="architecture-of-instance-pools"></a>Példány-készletek architektúrája

A példány-készletek hasonló architektúrával rendelkeznek a normál felügyelt példányokhoz (*egyetlen példány*). Az [Azure Virtual Networks (virtuális hálózatok) szolgáltatáson belüli központi telepítések](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) támogatásához , valamint az ügyfelek elkülönítésének és biztonságának biztosításához a példányok készletei is a [virtuális fürtökre](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)támaszkodnak. A virtuális fürtök az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét jelölik.

A két üzemi modell közötti fő különbség az, hogy a példányok több SQL Server folyamat telepítését teszik lehetővé ugyanazon a virtuálisgép-csomóponton, amely a [Windows-feladatok objektumaira](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)érvényes erőforrás, míg az önálló példányok mindig a egy virtuálisgép-csomópont.

A következő ábra egy példány-készletet és két, ugyanabban az alhálózatban üzembe helyezett példányt mutat be, és bemutatja a telepítési modellek fő architektúrájának részleteit:

![példány-készlet és két különálló példány](./media/sql-database-instance-pools/instance-pools2.png)

Minden példány-készlet létrehoz egy különálló virtuális fürtöt a alatt. A készleten belüli példányok és az ugyanabban az alhálózatban telepített önálló példányok nem osztják meg SQL Server folyamatokhoz és átjáró-összetevőkhöz lefoglalt számítási erőforrásokat, így biztosítható a teljesítmény kiszámíthatósága.

## <a name="instance-pools-resource-limitations"></a>Példány-készletek erőforrás-korlátai

A példánykészletekre és a készletekben lévő példányokra többféle erőforrás-korlátozás is vonatkozik:

- A példány-készletek csak Gen5 hardveren érhetők el.
- A készleten belüli példányok dedikált PROCESSZORral és RAM-mal rendelkeznek, így az összes példány virtuális mag összesített száma nem lehet kisebb a készlethez lefoglalt virtuális mag számával.
- Az összes [példányi szintű korlát](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) a készleten belül létrehozott példányokra vonatkozik.
- A példányok szintjének korlátain kívül *a példányok készletének szintjén*is két korlátot kell megszabni:
  - A tárterület teljes mérete (8 TB).
  - A készletben lévő adatbázisok teljes száma (100).

A tárolók teljes kiosztása és az adatbázisok száma az összes példánynál kisebbnek vagy egyenlőnek kell lennie a példány-készletek által elérhető korlátokkal.

- A példány-készletek 8, 16, 24, 32, 40, 64 és 80 virtuális mag támogatnak.
- A készletekben található felügyelt példányok támogatják a 2, 4, 8, 16, 24, 32, 40, 64 és 80 virtuális mag.
- A készletekben lévő felügyelt példányok támogatják a 32 GB és 8 TB közötti tárolási méreteket, a következők kivételével:
  - 2 a virtuális mag-példányok a 32 GB és 640 GB közötti méreteket támogatják
  - 4 a virtuális mag-példányok a 32 GB és 2 TB közötti méreteket támogatják

A [szolgáltatási szintek tulajdonság](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) a példány-készlet erőforrásához van társítva, így a készletben lévő összes példánynak a készlet szolgáltatási szintjével megegyező szolgáltatási szintet kell használnia. Jelenleg csak a általános célú szolgáltatási szinten érhető el (az aktuális előzetes verzió korlátozásait lásd a következő szakaszban).

### <a name="public-preview-limitations"></a>A nyilvános előzetes verzió korlátozásai

A nyilvános előzetes verzió a következő korlátozásokkal rendelkezik:

- Jelenleg csak a általános célú szolgáltatási szintje érhető el.
- A példányok készletei nem méretezhetők a nyilvános előzetes verzióban, így a központi telepítés szempontjából fontos a kapacitás megtervezése.
- A példány-készlet létrehozásához és konfigurálásához Azure Portal támogatása még nem érhető el. A példányok összes művelete csak a PowerShell használatával támogatott. Egy előre létrehozott készletben a kezdeti példány üzembe helyezését csak a PowerShell támogatja. A készletbe való üzembe helyezést követően a felügyelt példányok a Azure Portal használatával frissíthetők.
- A készleten kívül létrehozott felügyelt példányok nem helyezhetők át egy meglévő készletbe, és a készleten belül létrehozott példányok nem helyezhetők át egyetlen példányon vagy egy másik készleten kívülre.
- A fenntartott példányok díjszabása (vagy Azure Hybrid Benefit) nem érhető el.

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

A készletekben létrehozott példányok ugyanazt a [kompatibilitási szintet és funkciót támogatják, mint az egyetlen felügyelt példányban](sql-database-managed-instance.md#sql-features-supported).

A készletben üzembe helyezett összes felügyelt példány külön SQL Agent-példánnyal rendelkezik.

Azok a választható funkciók vagy szolgáltatások, amelyekhez szükség van bizonyos értékek kiválasztására (például a példány szintű rendezés, az időzóna, a nyilvános végpont az adatforgalomhoz, a feladatátvételi csoportok) a példány szintjén vannak konfigurálva, és a készlet minden példánya esetében eltérőek lehetnek.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Bár a készletekben lévő felügyelt példányok dedikált virtuális mag és RAM-mal rendelkeznek, a helyi lemez (tempdb-használat) és a hálózati erőforrások megoszthatók. Ez nem valószínű, de lehetséges a *zajos szomszéd* hatása, ha a készletben több példány is magas erőforrás-felhasználással rendelkezik. Ha ezt a viselkedést észleli, érdemes lehet ezeket a példányokat egy nagyobb készletbe vagy egyetlen példányként telepíteni.

## <a name="security-considerations"></a>Biztonsági szempontok

Mivel a készletben üzembe helyezett példányok ugyanazt a virtuális gépet használják, érdemes lehet letiltani a magasabb biztonsági kockázatokat bevezető szolgáltatásokat, vagy a hozzáférési engedélyeket határozottan vezérelni ezekhez a funkciókhoz. Például: CLR-integráció, natív biztonsági mentés és visszaállítás, adatbázis-levelezés stb.

## <a name="instance-pool-support-requests"></a>Példány-készlet támogatási kérelmek

A [Azure Portal](https://portal.azure.com)lévő példány-készletek támogatási kérelmeit hozhatja létre és kezelheti.

Ha a példány-készlet üzembe helyezésével kapcsolatos problémákat tapasztal (létrehozás vagy törlés), akkor a **probléma altípusa** mezőben adja meg a **példányok készleteit** .

![példány-készletek támogatási kérelem](./media/sql-database-instance-pools/support-request.png)

Ha egy készleten belül egy példányhoz vagy adatbázishoz kapcsolódó problémákat tapasztal, hozzon létre egy rendszeres támogatási jegyet Azure SQL Database felügyelt példányokhoz.

Ha nagyobb felügyelt példányokat kíván létrehozni (példány-készletekkel vagy anélkül), előfordulhat, hogy nagyobb regionális kvótát kell megszereznie. Használja a [szabványos felügyelt példányok eljárását nagyobb kvóta kéréséhez](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance), de vegye figyelembe, hogy ha példány-készleteket használ, a telepítési logika összehasonlítja a *készlet szintjének* teljes virtuális mag-felhasználását a kvóta alapján annak megállapításához, hogy Ön engedélyezett új erőforrások létrehozása a kvóta további növelése nélkül.

## <a name="instance-pool-billing"></a>Példány-készlet számlázása

A példány-készletek a számítás és a tárolás egymástól független méretezését teszik lehetővé. Az ügyfelek a virtuális mag-ben mért készlet-erőforrással kapcsolatos számítási feladatokért és a gigabájtban mért minden egyes példányhoz társított tárterületért fizetnek (az első 32 GB díjmentes minden példány esetében).

a készlet virtuális mag díja attól függetlenül történik, hogy hány példányt telepítettek a készletbe.

A számítási díj (virtuális mag mérve) esetében két díjszabási lehetőség érhető el:

  1. *Licenc mellékelve*: meglévő SQL Server licencek alkalmazása frissítési garanciával.
  2. *Azure Hybrid Benefit*: kedvezményes ár, amely SQL Server Azure Hybrid Benefit tartalmaz. Az ügyfelek a frissítési garanciával rendelkező meglévő SQL Server-licencek használatával dönthetik el ezt az árat. A jogosultsági és egyéb részleteket lásd: [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

A különböző díjszabási lehetőségek beállítása nem lehetséges a készletben lévő egyes példányok esetében. A szülő készletben lévő összes példánynak licenccel ellátott árat vagy Azure Hybrid Benefit árat kell tartalmaznia. A készlethez tartozó licencelési modell a készlet létrehozása után módosítható.

> [!IMPORTANT]
> Ha olyan licencet ad meg a példányhoz, amely eltér a készlettől, a rendszer a készlet árát fogja használni, és a példány szintjének értékét figyelmen kívül hagyja.

Ha a [fejlesztési és tesztelési juttatásra jogosult előfizetésekhez](https://azure.microsoft.com/pricing/dev-test/)hoz létre példány-készleteket, az Azure SQL felügyelt példányain a rendszer akár 55%-os kedvezményt is igénybe vehet.

A példányok készletének díjszabásával kapcsolatos részletes információkért tekintse meg a [felügyelt példány díjszabása lap](https://azure.microsoft.com/pricing/details/sql-database/managed/) *példány-készletek* szakaszát.

## <a name="next-steps"></a>További lépések

- A példányok készletének megkezdéséhez tekintse meg a [SQL Database példány-készletek útmutatója](sql-database-instance-pools-how-to.md)című témakört.
- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](sql-database-managed-instance-get-started.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- A VNet-konfigurációval kapcsolatos további információkért lásd: [felügyelt példányok VNet konfigurációja](sql-database-managed-instance-connectivity-architecture.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- A Azure Database Migration Service (DMS) áttelepítésre való használatát ismertető oktatóanyagért lásd: a [felügyelt példányok áttelepítése a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).
- A felügyelt példányok adatbázisának teljesítményének speciális figyelése a beépített hibaelhárítási intelligenciával: [Azure SQL Database figyelése Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért lásd: [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).