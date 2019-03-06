---
title: 'Saas-alkalmazás: Számos Azure SQL-adatbázis teljesítményének figyelése |} A Microsoft Docs'
description: Az Azure SQL Database-adatbázisok és a készletek a több-bérlős SaaS-alkalmazás teljesítményének figyelése és kezelése
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: df54f9dd4047fffb578a1a95a2edc47cba711ba1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433518"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Az Azure SQL Database-adatbázisok és a készletek a több-bérlős SaaS-alkalmazás teljesítményének figyelése és kezelése

Ebben az oktatóanyagban használt SaaS-alkalmazásokban több fő felügyeleti forgatókönyvek teljesítménykezelési. Használja a terhelésgenerátor tevékenység szimulálása az összes bérlői adatbázison, a beépített figyelési és riasztási funkcióit az SQL Database és a rugalmas készletek találja meg.

A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás egy egybérlős adatmodellt, használ, ahol minden egyes helyszín (bérlő) saját adatbázissal rendelkezik. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. Ezen tipikus adatbázis-felhasználási minta kihasználásához a bérlői adatbázisok rugalmas készletekbe helyezik üzembe. A rugalmas készletek optimalizálják a megoldások költségeit azáltal, hogy számos adatbázis között osztják meg az erőforrásokat. Ennél a típusú mintánál fontos az adatbázis és a készleterőforrások felhasználásának figyelése annak biztosítása érdekében, hogy a terhelések egyenletesen oszoljanak meg a készletek közt. Emellett azt is biztosítani kell, hogy az egyes adatbázisok elengedő mennyiségű erőforrással rendelkezzenek, és hogy a készletek ne érjék el a maximális [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)-korlátot. Ez az oktatóanyag különböző módszereket ismertet az adatbázisok és készletek figyelésére és kezelésére, valamint a számítási feladatok változásaira adott korrekciós műveletek elvégzésére.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezés a Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás és megismerése](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Teljesítménykezelési minták SaaS bemutatása

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. Több bérlő tárolásához, a rugalmas készletek egy költséghatékony megoldás, és kiszámíthatatlan számítási feladatokkal adatbáziscsoportok erőforrásainak kezelése. Bizonyos számításifeladat-mintáknál már akár két S3-adatbázist is előnyös lehet készletben kezelni.

![Alkalmazásdiagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Készletek és a készletekben található adatbázisokat figyelendő teljesítmény teljesítményük elfogadható tartományon belül tartása. Finomhangolja a készlet konfigurációját az összesített számítási feladat összes adatbázis meggyőződött arról, hogy a készlet edtu-k a teljes számítási feladatnak megfelelő igényeinek. Állítsa be az adatbázisonkénti minimális és maximális eDTU-értékeket az alkalmazás specifikus igényei szerint.

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* Ne kelljen manuálisan a teljesítmény figyelésére, a leghatékonyabb **riasztások beállítását, melyek indítható el, ha az adatbázisok vagy készletek kilépnek az engedélyezett tartományból teljesítményfigyelésnél**.
* Rövidtávú ingadozásaira válaszolni az összesítésben egy készlet számítási mérete a **készletek eDTU-szintje vertikálisan fel-felfelé vagy lefelé**. Ha az ingadozás rendszeres vagy kiszámítható, akkor **a készlet beállítható úgy, hogy a skálázás automatikusan ütemezve legyen**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszabb távú ingadozásokra vagy az adatbázisok számának változására válaszul **az egyes adatbázisok áthelyezhetők másik készletekbe**.
* A rövid távú növekedése válaszolni *egyes* adatbázisok terhelésének **egyes adatbázisok kivehetők a készletből, és egy adott számítási méret hozzárendelt**. A terhelés csökkenésével az adatbázis visszahelyezhető a készletbe. Ha ez az előzetesen ismert, adatbázisok áthelyezhetők jegyértékesítésről, az adatbázis mindig rendelkezzen a szükséges erőforrásokat, és a készlet egyéb adatbázisainak gyakorolt hatás elkerülése érdekében. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. Az SQL Database esetében a figyelés és riasztás rendelkezésre áll az adatbázisokhoz és a készletekhez. A beépített figyelés és riasztás erőforrás-specifikus, így azt kényelmes megoldás az erőforrás használatára, de nem nagyon hasznos, sok erőforrás használatakor.

Nagy mennyiségű forgatókönyvekhez, ahol sok erőforrásokkal dolgozik, [naplózza az Azure Monitor](saas-dbpertenant-log-analytics.md) is használható. Ez az egy külön Azure-szolgáltatás, amely biztosít elemzési kibocsátott diagnosztikai naplók és a Log Analytics-munkaterületen gyűjtött telemetria. Az Azure Monitor naplóira számos szolgáltatásból képes telemetriai adatok gyűjtésére, és lekérdezheti, és riasztásokat állíthat be.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazásszkriptek beolvasása

A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Noha a készletek használata már két S3-adatbázis esetén is költséghatékony lehet, minél több adatbázis található egy készletben, a költséghatékonyság mértéke annál jobban nő. Annak érdekében, hogy a teljesítményfigyelés és -kezelés nagy léptékben való használatát kellőképpen szemléltetni lehessen, jelen oktatóanyaghoz legalább 20 üzembe helyezett adatbázis szükséges.

Ha egy korábbi oktatóanyagban már kiépített bérlők egy kötegét, ugorjon a [összes bérlői adatbázis használatának szimulálása](#simulate-usage-on-all-tenant-databases) szakaszban.

1. Az a **PowerShell ISE-ben**, nyissa meg... \\Tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Válassza a **$DemoScenario** = **1**, **Bérlők kötegelt kiépítése** lehetőséget.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A szkript kevesebb mint öt perc alatt 17 bérlőt helyez üzembe.

A *New-TenantBatch* a szkript egy beágyazott vagy hivatkozott készletét [Resource Manager](../azure-resource-manager/index.yml) sablonokat, hozzon létre egy batch-bérlőkkel, amely alapértelmezés szerint az adatbázis másolja **basetenantdb**a katalóguskiszolgálón adatbázisok létrehozására az új bérlőhöz, majd regisztrálja ezeket a katalógusban, és végül pedig inicializálja őket a bérlő nevével és a helyszíntípussal típusát. Ez az alkalmazás kiosztja az új bérlő módja összhangban. Végzett módosítások *basetenantdb* kiépített bérlőre érvényesek. Tekintse meg a [Sémakezeléssel foglalkozó oktatóanyag](saas-tenancy-schema-management.md) a séma módosításával kapcsolatos *meglévő* bérlői adatbázisok (beleértve a *basetenantdb* adatbázis).

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájl van, feltéve, hogy szimulálja egy számítási feladatot futtat az összes bérlői adatbázison. A terhelés a véleménypontszámok generálásáról az egyik rendelkezésre álló betöltése:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitású terhelés (hozzávetőlegesen 40 DTU) |
| 3 | Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel|
| 4 | (KB. 80 DTU) adatbázisonkénti magasabb DTU-löketekkel terhelés létrehozása|
| 5 | Hozzon létre egy normál terhelés, valamint magasabb terhelés létrehozása egyetlen bérlő (számára körülbelül 95 DTU)|
| 6 | Kiegyensúlyozatlan terhelés létrehozása több készlet számára|

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelések szintje (eDTU-ban mérve), időtartama és időköze minden adatbázis esetén más és más, ezzel szimulálva a kiszámíthatatlan bérlői aktivitást.

1. Az a **PowerShell ISE-ben**, nyissa meg... \\Tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Állítsa be **$DemoScenario** = **2**, *normál intenzitású terhelés*.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

A Wingtip Tickets SaaS adatbázis Per bérlő SaaS-alkalmazás, pedig a való életből vett terhelés egy SaaS-alkalmazás általában szórványosak és kiszámíthatatlanok. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Több perc szükséges a terhelés minta bontakozik ki, ezért futtassa a terhelésgenerátort 3 – 5 percet, mielőtt megkísérelné a következő szakaszok a terheléseket figyelni.

> [!IMPORTANT]
> A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben. Hagyja nyitva a *Demo-PerformanceMonitoringAndManagement.ps1* lapot! Ha bezárja a lapot vagy felfüggeszti a gép működését, a terhelésgenerátor leáll. A terhelésgenerátor marad egy *feladat meghívása* állapot, ahol minden olyan új bérlők számára, miután elindult a generátor felhasznált terhelése állít elő. Használat *Ctrl-C* meghívása új feladatok leállítása és lépjen ki a parancsprogramot. A terhelésgenerátor továbbra is futtatja, de csak a meglévő bérlők számára.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Az Azure portal használatával erőforrás-használat figyelése

A terhelések eredményei erőforrás-használat figyeléséhez nyissa meg a bérlői adatbázisokat tartalmazó készletet a portálon:

1. Nyissa meg a [az Azure portal](https://portal.azure.com) , és keresse meg a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló.
1. Görgessen lefelé, keresse meg a rugalmas készleteket, és kattintson a **Pool1** készletre. Ez a készlet tartalmazza az összes eddig létrehozott bérlői adatbázist.

Figyelje meg a **rugalmas készlet figyelése** és **rugalmas adatbázis figyelése** diagramok.

A készlet erőforrás-használat a készletben található adatbázisok összesített kihasználtságának. Az adatbázis diagram az 5 legtöbbet használt adatbázisokat mutatja:

![Adatbázis-diagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Mivel az első öt túl a készletben található további adatbázisok, a készlet kihasználtsági tevékenység, a felső diagramon öt adatbázis diagramjában nem jeleníti meg. További részletekért kattintson **Adabáziserőforrás-felhasználás**:

![adabáziserőforrás-felhasználás](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Teljesítményriasztások beállítása a készletben

A készlethez, amely elindítja riasztások beállítása \>75 %-os kihasználtságot, az alábbiak szerint:

1. Nyissa meg *Pool1* (a a *tenants1-dpt -\<felhasználói\>*  kiszolgáló) a a [az Azure portal](https://portal.azure.com).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrika = eDTU százalékos értéke**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**
   * **Időtartam = az elmúlt 30 percben**
1. Az e-mail cím hozzáadása a *további rendszergazdai email(s)* mezőbe, majd kattintson a **OK**.

   ![riasztás beállítása](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Foglalt készlet vertikális felskálázása

Ha egy készlet összesített terhelési szintje addig növekszik, hogy teljesen lefoglalja a készletet és 100%-os eDTU-használatot ér el, az hatással van az adatbázisok egyéni teljesítményére, és lelassíthatja a lekérdezések válaszidejét a készletben található összes adatbázisban.

**Rövid távú**, fontolja meg meg további erőforrások biztosítását a készlet vertikális felskálázásával, vagy adatbázisok eltávolítását a készletből (más készletekbe, vagy a készletből történő áthelyezése különálló szolgáltatásszintre).

**Hosszabb távon**, fontolja meg a lekérdezések optimalizálását vagy index használatát az adatbázis-teljesítmény javítása. Az alkalmazás teljesítményingadozásokra való érzékenységétől függően az ajánlott eljárás a készlet vertikális felskálázása még a 100%-os eDTU-használat elérése előtt. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Foglalt készletet a generátor által létrehozott terhelés növelésével szimulálhat. Az adatbázisok már, folyamatosan gyakrabban, valamint az okozza, növelje a készlet összesített terhelését az egyes adatbázisok követelményeinek megváltoztatása nélkül. A készlet vertikális felskálázása könnyedén elvégezhető a portálon vagy a PowerShellben. A gyakorlat során a Portalt használjuk.

1. Állítsa be a *$DemoScenario* = **3**, _Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel_ értéket, hogy megnövelje a készlet összesített terhelési intenzitását az egyes adatbázisok csúcsterhelési követelményeinek megváltoztatása nélkül.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

1. Lépjen a **Pool1** az Azure Portalon.

Figyelheti a készlet megnövekedett eDTU-használatát a felső diagramon. Az új magasabb terhelés, néhány percig tart, de gyorsan megtekintheti az a készlet elkezdi maximális kihasználtságát, és a terhelés stabilan be az új minta, ahogy azt beáll a készlet.

1. A készlet vertikális felskálázásához kattintson **készlet beállítása** felső részén a **Pool1** lapot.
1. Módosítsa a **készlet eDTU** beállítást **100**. A készlet eDTU-értékének módosítása nem módosítja az adatbázisonkénti beállításokat (ami továbbra is adatbázisonként legfeljebb 50 eDTU). Az adatbázis-beállítások a jobb oldalon látható a **készlet beállítása** lapot.
1. Kattintson a **mentése** a készlet méretezésére irányuló kérelem elküldéséhez.

Lépjen vissza a **Pool1** > **áttekintése** megtekintése a figyelési diagramjait. Figyelje meg, hogy a készlet több erőforrást (noha kevés adatbázis és véletlenszerű terhelés nem mindig könnyen von látható, amíg nem futtat egy kis ideig). A diagramok megtekintése közben vegye figyelembe, hogy a felső diagramon látható 100% most 100 eDTU-t jelent, míg az alsó diagramon látható 100% továbbra is 50 eDTU-t, mivel az adatbázisonkénti maximum változatlanul 50 eDTU.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Abban a pillanatban, hogy minden adatbázis készen áll a készlet új eDTU-értékével való engedélyezésre, minden aktív kapcsolat megszakad. Az alkalmazás kódját mindig úgy kell megírni, hogy újrapróbálja a megszakított kapcsolatokat, így újra csatlakozni fog az adatbázishoz a felskálázott készletben.

## <a name="load-balance-between-pools"></a>Készletek közötti terheléselosztás

A készlet felskálázása mellett másik lehetőségként létrehozhat egy második készletet és áthelyezhet abba adatbázisokat, hogy kiegyenlítse a két készlet terhelését. Ehhez az új készletet ugyanazon a kiszolgálón kell létrehozni, amelyen az első is megtalálható.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a **tenants1-dpt -&lt;felhasználói&gt;**  kiszolgáló.
1. Kattintson a **+ új készlet** -készlet létrehozása az aktuális kiszolgálón.
1. Az a **rugalmas készlet** sablon:

    1. Állítsa be **neve** való *Pool2*.
    1. A tarifacsomagnál hagyja meg a **Standard készlet** beállítást.
    1. Kattintson a **Készlet beállítása** elemre.
    1. Állítsa be **készlet eDTU** való *50 edtu méretű*.
    1. Kattintson a **adatbázisok hozzáadása** azon a kiszolgálón, lehet hozzáadni az adatbázisok listájának megtekintéséhez *Pool2*.
    1. Válassza ki, helyezze át ezeket az új készletbe, majd 10 adatbázisokhoz **kiválasztása**. Futtatja, a terhelésgenerátor, ha a szolgáltatás már tudja, hogy a teljesítmény profil nagyobb, mint az alapértelmezett 50 eDTU méretű készlet igényel, és javasolja, hogy egy 100 eDTU-beállítás kezdve.

    ![ajánlás](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Ebben az oktatóanyagban hagyja meg az alapértelmezett 50 edtu-t, és kattintson a **kiválasztása** újra.
    1. Válassza ki **OK** hoz létre az új készletet és áthelyezhet abba a kiválasztott adatbázisokat.

A készlet létrehozása és az adatbázisok áthelyezése néhány percet vesz igénybe. Adatbázisok kerülnek, online és teljes mértékben elérhetők maradjanak egészen az nagyon utolsó pillanatig, mivel ekkor minden megnyitott kapcsolatuk bezáródik. Mindaddig, amíg az egyes újrapróbálkozási algoritmussal rendelkezik, az ügyfelek ezután fognak kapcsolódni az adatbázis az új készletben.

Keresse meg a **Pool2** (a a *tenants1-dpt -\<felhasználói\>*  kiszolgáló), nyissa meg a készletet, és figyelemmel kísérni a teljesítményét. Ha nem látja, várja meg, végezze el az új készlet kiépítése.

A most látható, hogy az erőforrás-használat *Pool1* csökkent, és hogy *Pool2* hasonlóan most betöltve.

## <a name="manage-performance-of-an-individual-database"></a>Az egyes adatbázisok teljesítményének kezelése

Ha a készletben egy önálló adatbázis tartósan magas terhelés, a készlet beállításaitól függően, akkor előfordulhat, hogy általában uralja a készletben lévő erőforrásokat, és befolyásolja a többi adatbázis. Ha a tevékenység várhatóan huzamosabb ideig, az adatbázis ideiglenesen kivehető a készlet. Ez lehetővé teszi az adatbázis számára a további erőforrások van szüksége, és elszigetelődjön tőlük a más adatbázisok.

Ez a gyakorlat a Contoso Concert Hall magas terhelésének a hatását szimulálja, amikor megkezdődik a jegyek árusítása egy népszerű koncertre.

1. Az a **PowerShell ISE-ben**, nyissa meg a... \\ *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájlt.
1. Állítsa be **$DemoScenario = 5, normál terhelés létrehozása, valamint magasabb terhelés létrehozása egyetlen bérlő (KB. 95 DTU).**
1. Állítsa be a **$SingleTenantDatabaseName = contosoconcerthall**értéket.
1. Futtassa a szkriptet az **F5** billentyűvel.


1. Az a [az Azure portal](https://portal.azure.com), keresse meg az adatbázisok listája a *tenants1-dpt -\<felhasználói\>*  kiszolgáló. 
1. Kattintson a **contosoconcerthall** adatbázis.
1. Kattintson a készlet, amely **contosoconcerthall** szerepel. Keresse meg a készlet az a **rugalmas készlet** szakaszban.

1. Vizsgálja meg a **rugalmas készlet figyelése** diagram, és keresse meg a készlet megnövekedett eDTU kihasználtsága. Egy-két perc után jelentkezik a magas terhelés, aminek következtében a készlet eléri a 100%-os kihasználtságot.
2. Vizsgálja meg a **rugalmas adatbázis figyelése** megjeleníteni, amelyek az elmúlt órában legtöbbet használt adatbázisokat jeleníti meg. A *contosoconcerthall* adatbázis hamarosan megjelenik az 5 legtöbbet használt adatbázisokat egyikét.
3. **Kattintson a rugalmas adatbázis figyelése** **diagram** , megnyílik a **Adabáziserőforrás-felhasználás** oldal, ahol nyomon követheti az adatbázisok bármelyikét. Ez lehetővé teszi, hogy jelenítse meg a *contosoconcerthall* adatbázis.
4. Az adatbázisok listájában kattintson **contosoconcerthall**.
5. Kattintson a **Tarifacsomag (dtu)** megnyitásához a **teljesítmény konfigurálása** lap, amelyen beállíthatja az adatbázis egy különálló számítási méret.
6. Kattintson a **Standard** lapra a Standard csomag skálázási beállításainak megnyitásához.
7. Húzza az ujját a **DTU csúszkát** jobbra válassza ki a **100** dtu-k. Megjegyzés: Ez a szolgáltatási cél megfelel **S3**.
8. Kattintson a **alkalmaz** helyezze át az adatbázist a készletből, és lehetővé teszi egy *Standard S3 szintű* adatbázis.
9. Méretezés befejezése után a hatása a contosoconcerthall adatbázis és a rugalmas készlet és az adatbázis paneleket a Pool1 figyelése.

Ha a magas terhelés a contosoconcerthall adatbázis megszokottnál, helyezze vissza, a készletbe a költségek csökkentése érdekében. Ha nem egyértelmű, amikor, amely akkor történik meg, beállíthat egy riasztást az adatbázison, amely aktiválódik az adatbázisonkénti alá csökken a DTU-használatát a készletben a maximális. Egy adatbázis készletbe történő áthelyezésének menetét az 5. gyakorlat írja le.

## <a name="other-performance-management-patterns"></a>Egyéb teljesítménykezelési minták

**Preemptív méretezés** a gyakorlatban a fenti, ahol megismerte egy elkülönített adatbázis méretezését, melyik adatbázist kell keresni dolgozott. Ha a Contoso Concert hall felügyeleti kellett wingtips közelgő jegy értékesítés, az adatbázis sikerült átkerült a készletből jegyértékesítésről. Máskülönben valószínűleg egy riasztást kellett volna beállítani a készleten vagy az adatbázison ahhoz, hogy észre lehessen venni, mi történik. Nem jó, ha az ilyen eseményekről úgy szerez tudomást, hogy a készletben található többi bérlő csökkenő teljesítményről panaszkodik. Ha a bérlő meg tudja jósolni, hogy milyen hosszan lesz szüksége további erőforrásokra, beállítható egy Azure Automation-runbook, amely pontosan ütemezi az adatbázis kivételét, majd visszahelyezését a készletbe.

**Bérlők általi önkiszolgáló skálázás** Mivel a skálázási feladat könnyen meghívható a felügyeleti API-n keresztül, így a bérlői adatbázisok méretezésének lehetősége könnyen beépíthető a bérlőoldali alkalmazásba, és felkínálható az SaaS-szolgáltatás egy funkciójaként. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Egy készlet felfelé és lefelé skálázás használati mintákhoz ütemezés szerint**

Ha az összesített bérlői használat kiszámítható mintákat követ, az Azure Automationnel ütemezni tudja a készletek vertikális fel- és leskálázását. Például egy készletet hétköznapokon este 6 után leskálázhat, reggel 6 előtt pedig felskálázhat, ha tudja, hogy a két időpont között csökken az erőforrásigény.



## <a name="next-steps"></a>További lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére

[Egyetlen bérlő visszaállítása – oktatóanyag](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>További források

* További [a Wingtip Tickets SaaS adatbázis Per bérlői alkalmazástelepítés útmutatóra oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Rugalmas SQL-készletek](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Az Azure Monitor naplóira](saas-dbpertenant-log-analytics.md) - beállítás és használatát az Azure Monitor naplók oktatóanyag
