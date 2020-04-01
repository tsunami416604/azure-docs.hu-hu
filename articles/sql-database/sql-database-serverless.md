---
title: Kiszolgáló nélküli
description: Ez a cikk ismerteti az új kiszolgáló nélküli számítási réteget, és összehasonlítja azt a meglévő kiépített számítási szinttel
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 8a72d3dc7f7b3fddf66df8111b1e92116a62883a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474028"
---
# <a name="azure-sql-database-serverless"></a>Az Azure SQL Database kiszolgáló nélküli

Az Azure SQL Database kiszolgáló nélküli egy számítási réteg egyetlen adatbázisok, amely automatikusan skálázódik számítási terhelés i terhelésés számlák a másodpercenként felhasznált számítási mennyiség alapján. A kiszolgáló nélküli számítási szint is automatikusan szünetelteti az adatbázisokat inaktív időszakokban, amikor csak a tárolás számlázása, és automatikusan folytatja az adatbázisokat, amikor a tevékenység visszatér.

## <a name="serverless-compute-tier"></a>Kiszolgáló nélküli számítási szint

Egyetlen adatbázis kiszolgáló nélküli számítási szintjét egy számítási automatikus skálázási tartomány és egy automatikus szünetkésleltetés paraméterezi.  Ezeknek a paramétereknek a konfigurációja alakítja az adatbázis teljesítményélményét és a számítási költséget.

![kiszolgáló nélküli számlázás](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Teljesítmény konfigurációja

- A **minimális virtuális magok** és a maximális virtuális **magok** konfigurálható paraméterek, amelyek meghatározzák az adatbázis számára rendelkezésre álló számítási kapacitás tartományát. A memória- és I/O-korlátok arányosak a megadott virtuálismag-tartománysal.  
- Az **automatikus szüneteltetési késleltetés** egy konfigurálható paraméter, amely meghatározza azt az időtartamot, amerre az adatbázisnak inaktívnak kell lennie az automatikus szüneteltetés előtt. Az adatbázis automatikusan folytatódik, amikor a következő bejelentkezés vagy más tevékenység történik.  Másik lehetőségként az autopausing letiltható.

### <a name="cost"></a>Költségek

- A kiszolgáló nélküli adatbázis költsége a számítási költség és a tárolási költség összegzése.
- Ha a számítási használat a beállított min és max korlátok között van, a számítási költség a virtuális magon és a használt memórián alapul.
- Ha a számítási használat a beállított min-korlátok alatt van, a számítási költség a min virtuális magok és a min memória konfigurált.
- Az adatbázis szüneteltetésekén a számítási költség nulla, és csak a tárolási költségek merülnek fel.
- A tárolási költség ugyanúgy van meghatározva, mint a kiépített számítási rétegben.

További költségrészletek: [Számlázás](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Forgatókönyvek

A kiszolgáló nélküli ár-teljesítmény az egyes adatbázisokhoz optimalizált, időszakos, előre nem látható használati mintákkal, amelyek megengedhetik maguknak a számítási bemelegítés némi késleltetését az alapjárati használati időszakok után. Ezzel szemben a kiosztott számítási szint az ár-teljesítmény optimalizált egyetlen adatbázisok vagy több adatbázisok rugalmas készletek nagyobb átlagos használat, amely nem engedheti meg magának a számítási bemelegítés.

### <a name="scenarios-well-suited-for-serverless-compute"></a>A kiszolgáló nélküli számításhoz jól illeszkedő forgatókönyvek

- Egyetlen adatbázisok időszakos, előre nem látható használati minták tarkított időszakok inaktivitás és az alacsonyabb átlagos számítási kihasználtság idővel.
- A kiépített számítási rétegben lévő, gyakran átskálázott és a számítási átskálázást a szolgáltatásra delegált ügyfelek.
- Új, egyetlen adatbázisok használati előzmények nélkül, ahol a számítási méretezés nehéz vagy nem lehet megbecsülni az SQL Database-ben való üzembe helyezés előtt.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Kiépített számítási célokra jól illeszkedő forgatókönyvek

- Egyetlen adatbázisok több rendszeres, kiszámítható használati minták és a magasabb átlagos számítási kihasználtság idővel.
- Olyan adatbázisok, amelyek nem tolerálják a gyakoribb memóriavágásból vagy a szüneteltetett állapotból történő automatikus újraszövegezés késleltetéséből eredő teljesítménykompromisszumokat.
- Több adatbázis időszakos, előre nem látható használati mintákkal, amelyek rugalmas készletekbe konszolidálhatók a jobb ár-teljesítmény optimalizálás érdekében.

## <a name="comparison-with-provisioned-compute-tier"></a>Összehasonlítás a kiépített számítási szinttel

Az alábbi táblázat a kiszolgáló nélküli számítási szint és a kiépített számítási szint közötti különbségeket foglalja össze:

| | **Kiszolgáló nélküli számítástechnika** | **Kiépített számítás** |
|:---|:---|:---|
|**Adatbázis használati mintája**| Időszakos, előre nem látható használat alacsonyabb átlagos számítási kihasználtsítással az idő múlásával. |  Több rendszeres használati minták magasabb átlagos számítási kihasználtság idővel, vagy több adatbázisok rugalmas készletek használatával.|
| **Teljesítménymenedzsment-erőfeszítések** |Lower|Magasabb|
|**Számítási méretezés**|Automatikus|Kézi|
|**Számítási válaszidő**|Alacsonyabb inaktív időszakok után|Azonnali|
|**Számlázási részletesség**|Másodpercenként|Óránként|

## <a name="purchasing-model-and-service-tier"></a>Beszerzési modell és szolgáltatási szint

Az SQL Database kiszolgáló nélküli jelenleg csak a virtuálismag-vásárlási modell 5.

## <a name="autoscaling"></a>Automatikus skálázás

### <a name="scaling-responsiveness"></a>Válaszkészség méretezése

A kiszolgáló nélküli adatbázisok általában olyan gépen futnak, amely elegendő kapacitással rendelkezik ahhoz, hogy megszakítás nélkül kielégítse az erőforrás-igényt a maximális virtuális magok értéke által meghatározott korlátokon belül kért számítási mennyiségben. Alkalmanként a terheléselosztás automatikusan bekövetkezik, ha a gép néhány percen belül nem tudja kielégíteni az erőforrás-igényt. Ha például az erőforrás-igény 4 virtuális mag, de csak 2 virtuális mag érhető el, akkor akár néhány percig is eltarthat, amíg az elosztás 4 virtuális mag rendelkezésre áll. Az adatbázis online állapotban marad a terheléselosztás során, kivéve a kapcsolatok eldobásakor a művelet végén egy rövid ideig.

### <a name="memory-management"></a>Memóriakezelés

A kiszolgáló nélküli adatbázisok memóriája gyakrabban kerül visszanyerésre, mint a kiépített számítási adatbázisokhoz. Ez a viselkedés fontos a kiszolgáló nélküli költségek szabályozásához, és hatással lehet a teljesítményre.

#### <a name="cache-reclamation"></a>Gyorsítótár-visszanyerés

A kiépített számítási adatbázisoktól eltérően az SQL-gyorsítótárból származó memóriát a rendszer visszanyeri egy kiszolgáló nélküli adatbázisból, ha a processzor vagy a gyorsítótár kihasználtsága alacsony.

- A gyorsítótár-kihasználtság akkor tekinthető alacsonynak, ha a legutóbb használt gyorsítótár-bejegyzések teljes mérete egy adott időszakra egy küszöbérték alá esik.
- Gyorsítótár-visszanyerés aktiválásakor a célgyorsítótár mérete a korábbi méret töredékére csökken, és a visszaigénylés csak akkor folytatódik, ha a használat alacsony marad.
- Gyorsítótár-visszanyerés esetén a kizárásra kerülő gyorsítótár-bejegyzések kiválasztására vonatkozó házirend megegyezik a kiosztott számítási adatbázisok kijelölési házirendjével, ha a memórianyomás magas.
- A gyorsítótár mérete soha nem csökken a min virtuális magok által meghatározott min memóriakorlát alá, amely konfigurálható.

A kiszolgáló nélküli és a kiépített számítási adatbázisokban a gyorsítótár-bejegyzések kilakoltathatók, ha az összes rendelkezésre álló memóriát használja.

#### <a name="cache-hydration"></a>Gyorsítótár-hidratálás

Az SQL-gyorsítótár növekszik, ahogy az adatok beolvasása a lemezről ugyanolyan módon és ugyanolyan sebességgel, mint a kiépített adatbázisok. Ha az adatbázis foglalt, a gyorsítótár korlátlanul növekedhet a maximális memóriakorlátig.

## <a name="autopausing-and-autoresuming"></a>Automatikus újrafelhasználás és automatikus újraismétlés

### <a name="autopausing"></a>Automatikus használat

Az automatikus használat akkor aktiválódik, ha az autopause késleltetés idejére az alábbi feltételek mindegyike teljesül:

- Munkamenetek száma = 0
- CPU = 0 a felhasználói készletben futó felhasználói munkaterheléshez

Az automatikus használat letiltására szükség esetén lehetőség is rendelkezésre áll.

A következő szolgáltatások nem támogatják az automatikus használatot.  Ez azt, hogy ha az alábbi szolgáltatások bármelyikét használja, akkor az adatbázis az adatbázis inaktivitásának időtartamától függetlenül online marad:

- Georeplikáció (aktív georeplikációs és automatikus feladatátvételi csoportok).
- Hosszú távú biztonsági mentés megőrzése (LTR).
- Az SQL-adatszinkronizálásban használt szinkronizálási adatbázis.  A szinkronizálási adatbázisokkal ellentétben a hub- és tagadatbázisok támogatják az automatikus használatot.
- A rugalmas feladatokban használt feladat-adatbázis.

Az automatikus használat ideiglenesen megakadályozza egyes szolgáltatásfrissítések telepítését, amelyek megkövetelik, hogy az adatbázis online állapotban legyen.  Ilyen esetekben az automatikus használat ismét engedélyezettlesz, amint a szolgáltatásfrissítés befejeződik.

### <a name="autoresuming"></a>Automatikus újraszövegezés

Az automatikus újraszövegezés akkor aktiválódik, ha az alábbi feltételek bármelyike bármikor teljesül:

|Szolgáltatás|Automatikus folytatás iszaktiválása|
|---|---|
|Hitelesítés és engedélyezés|Bejelentkezés|
|Fenyegetések észlelése|Fenyegetésészlelési beállítások engedélyezése/letiltása adatbázis vagy kiszolgáló szintjén.<br>Fenyegetésészlelési beállítások módosítása adatbázis vagy kiszolgáló szintjén.|
|Adatfelderítés és besorolás|Érzékenységi címkék hozzáadása, módosítása, törlése vagy megtekintése|
|Naplózás|Naplózási rekordok megtekintése.<br>A naplózási házirend frissítése vagy megtekintése.|
|Adatmaszkolás|Adatmaszkolási szabályok hozzáadása, módosítása, törlése vagy megtekintése|
|Transzparens adattitkosítás|Átlátszó adattitkosítás állapotának vagy állapotának megtekintése|
|Lekérdezési (teljesítmény) adattár|Lekérdezéstároló beállításainak módosítása vagy megtekintése|
|Autotuning|Az automatikus hangolási javaslatok, például az automatikus indexelés alkalmazása és ellenőrzése|
|Adatbázis másolása|Hozzon létre adatbázist másolatként.<br>Exportálás BACPAC-fájlba.|
|SQL-adatok szinkronizálása|Konfigurálható ütemezés szerint futó vagy manuálisan végrehajtott hub- és tagadatbázisok közötti szinkronizálás|
|Adatbázis bizonyos metaadatainak módosítása|Új adatbáziscímkék hozzáadása.<br>Max virtuális magok, min virtuális magok vagy automatikus szüneteltetés módosítása.|
|SQL Server Management Studio (SSMS)|A 18.1-nél korábbi SSMS-verziók használata és a kiszolgáló bármely adatbázisának új lekérdezési ablakának megnyitása minden automatikusan szüneteltetett adatbázist folytat ugyanazon a kiszolgálón. Ez a jelenség nem fordul elő, ha az SSMS 18.1-es vagy újabb verzióját használja.|

Az automatikus újratelepítés egyes szolgáltatásfrissítések telepítése során is aktiválódik, amelyek megkövetelik, hogy az adatbázis online állapotban legyen.

### <a name="connectivity"></a>Kapcsolatok

Ha egy kiszolgáló nélküli adatbázis szünetel, akkor az első bejelentkezés folytatja az adatbázist, és hibát ad vissza, amely szerint az adatbázis nem érhető el a 40613-as hibakóddal. Az adatbázis folytatása után a bejelentkezést újra meg kell próbálni a kapcsolat létrehozásához. A kapcsolat-újrapróbálkozási logikával rendelkező adatbázis-ügyfeleket nem kell módosítani.

### <a name="latency"></a>Késés

A kiszolgáló nélküli adatbázis automatikus folytatásának és automatikus szüneteltetésének késése általában 1 perc automatikus folytatás, 1–10 perc automatikus szüneteltetése.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Ügyfél által kezelt transzparens adattitkosítás (BYOK)

Ha [az ügyfél által felügyelt transzparens adattitkosítás](transparent-data-encryption-byok-azure-sql.md) (BYOK) használatával, és a kiszolgáló nélküli adatbázis automatikusan szünetel a kulcs törlésekor vagy visszavonásokor, akkor az adatbázis automatikusan szüneteltetve marad.  Ebben az esetben, ha az adatbázis következő folytatása, az adatbázis online állapotú marad, amíg az állapota átmenetek elérhetetlenné körülbelül 10 perc vagy annál kevesebb után.  Amint az adatbázis elérhetetlenné válik, a helyreállítási folyamat ugyanaz, mint a kiépített számítási adatbázisok.  Ha a kiszolgáló nélküli adatbázis online állapotban van a kulcs törlése vagy visszavonása esetén, akkor az adatbázis is elérhetetlenné válik körülbelül 10 perc vagy annál kevesebb után, ugyanúgy, mint a kiépített számítási adatbázisok.

## <a name="onboarding-into-serverless-compute-tier"></a>Bevezetés kiszolgáló nélküli számítási rétegbe

Új adatbázis létrehozása vagy egy meglévő adatbázis áthelyezése egy kiszolgáló nélküli számítási rétegbe ugyanazt a mintát követi, mint egy új adatbázis létrehozása a kiépített számítási rétegben, és a következő két lépést foglalja magában.

1. Adja meg a szolgáltatáscél nevét. A szolgáltatási cél előírja a szolgáltatási szint, a hardver generálása és a maximális virtuális magok. Az alábbi táblázat a szolgáltatáscél-beállításokat mutatja be:

   |Szolgáltatási cél neve|Szolgáltatásszint|Hardver generálása|Maximális virtuális magok|
   |---|---|---|---|
   |GP_S_Gen5_1|Általános célú|Gen5 (1998)|1|
   |GP_S_Gen5_2|Általános célú|Gen5 (1998)|2|
   |GP_S_Gen5_4|Általános célú|Gen5 (1998)|4|
   |GP_S_Gen5_6|Általános célú|Gen5 (1998)|6|
   |GP_S_Gen5_8|Általános célú|Gen5 (1998)|8|
   |GP_S_Gen5_10|Általános célú|Gen5 (1998)|10|
   |GP_S_Gen5_12|Általános célú|Gen5 (1998)|12|
   |GP_S_Gen5_14|Általános célú|Gen5 (1998)|14|
   |GP_S_Gen5_16|Általános célú|Gen5 (1998)|16|

2. Tetszés szerint adja meg a min virtuális magokat, és az alapértelmezett értékek módosításához módosítsa az automatikus szünetelési késleltetést. Az alábbi táblázat a paraméterekhez rendelkezésre álló értékeket mutatja be.

   |Paraméter|Értékválasztási lehetőségek|Alapértelmezett érték|
   |---|---|---|---|
   |Min virtuális magok|A beállított virtuális magok maximális értékétől függ - lásd [az erőforráskorlátokat.](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)|0,5 virtuális magok|
   |Automatikus szünetelés késleltetése|Minimum: 60 perc (1 óra)<br>Maximum: 10080 perc (7 nap)<br>Lépések: 60 perc<br>Automatikus szünet letiltása: -1|60 perc|


### <a name="create-new-database-in-serverless-compute-tier"></a>Új adatbázis létrehozása kiszolgáló nélküli számítási rétegben 

A következő példák egy új adatbázist hoznak létre a kiszolgáló nélküli számítási rétegben. A példák kifejezetten meghatározzák a min virtuális magokat, a maximális virtuális magokat és az automatikus szüneteléskésleltetést.

#### <a name="use-azure-portal"></a>Az Azure Portal használata

Lásd: [Rövid útmutató: Hozzon létre egyetlen adatbázist az Azure SQL Database-ben az Azure Portal használatával.](sql-database-single-database-get-started.md)


#### <a name="use-powershell"></a>A PowerShell használata

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) használata

A következő példa új adatbázist hoz létre a kiszolgáló nélküli számítási rétegben.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

További információt az ADATBÁZIS LÉTREHOZÁSA című [témakörben](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)talál.  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Adatbázis áthelyezése a kiépített számítási rétegből kiszolgáló nélküli számítási szintre

A következő példák áthelyezik az adatbázist a kiépített számítási rétegből a kiszolgáló nélküli számítási szintre. A példák kifejezetten meghatározzák a min virtuális magokat, a maximális virtuális magokat és az automatikus szüneteléskésleltetést.

#### <a name="use-powershell"></a>A PowerShell használata


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) használata

A következő példa áthelyezi az adatbázist a kiépített számítási rétegből a kiszolgáló nélküli számítási szintre.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

További részletek az [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Adatbázis áthelyezése kiszolgáló nélküli számítási rétegből kiépített számítási szintre

A kiszolgáló nélküli adatbázis ugyanúgy áthelyezhető egy kiépített számítási rétegbe, mint egy kiépített számítási adatbázis kiszolgáló nélküli számítási szintre való áthelyezése.

## <a name="modifying-serverless-configuration"></a>Kiszolgáló nélküli konfiguráció módosítása

### <a name="use-powershell"></a>A PowerShell használata

A maximális vagy minimális virtuális magok módosítása és az automatikus szünetelés imázsa a `MaxVcore` `MinVcore`PowerShell `AutoPauseDelayInMinutes` [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsával történik a , és az argumentumok használatával.

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

A maximális vagy minimális virtuális magok módosítása és az automatikus szünetelés imázsa az `capacity` `min-capacity`az `auto-pause-delay` [sql db frissítési](/cli/azure/sql/db#az-sql-db-update) parancs használatával történik az Azure CLI használatával a , és az argumentumok használatával.


## <a name="monitoring"></a>Figyelés

### <a name="resources-used-and-billed"></a>Felhasznált és számlázott erőforrások

A kiszolgáló nélküli adatbázis erőforrásait alkalmazáscsomag, SQL-példány és felhasználói erőforráskészlet-entitások ágyazzák be.

#### <a name="app-package"></a>Alkalmazáscsomag

Az alkalmazáscsomag az adatbázis külső legtöbb erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten található-e. Az alkalmazáscsomag tartalmazza az SQL-példányt és a külső szolgáltatásokat, amelyek együttesen az SQL Database adatbázisa által használt összes felhasználói és rendszererőforrást tartalmazzák. A külső szolgáltatások közé tartozik például az R és a teljes szöveges keresés. Az SQL-példány általában uralja a teljes erőforrás-kihasználtságot az alkalmazáscsomagban.

#### <a name="user-resource-pool"></a>Felhasználói erőforráskészlet

A felhasználói erőforráskészlet az adatbázis legbelső erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten található-e. A felhasználói erőforráskészlet hatókörei a PROCESSZOR és az IO a DDL-lekérdezések , például a CREATE és alter és a DML lekérdezések ( SELECT, INSERT, UPDATE és DELETE) által generált felhasználói munkaterheléshez. Ezek a lekérdezések általában az alkalmazáscsomagon belüli használat legnagyobb hányadát képviselik.

### <a name="metrics"></a>Mérőszámok

Az alkalmazáscsomag és a kiszolgáló nélküli adatbázis felhasználói készlete erőforrás-használatának figyeléséhez használt metrikák az alábbi táblázatban találhatók:

|Entitás|Metrika|Leírás|Egység|
|---|---|---|---|
|Alkalmazáscsomag|app_cpu_percent|Az alkalmazás által használt virtuális magok százalékos aránya az alkalmazáshoz engedélyezett maximális virtuális magokhoz viszonyítva.|Százalék|
|Alkalmazáscsomag|app_cpu_billed|Az alkalmazásra a jelentéskészítési időszak alatt számlázott kiszámított számítás mennyisége. Az ebben az időszakban kifizetett összeg ennek a metrikának és a virtuális mag egységárának a szorzata. <br><br>Ennek a metrikának az értékeit úgy határozzuk meg, hogy idővel összesítjük a felhasznált processzor és a másodpercenként használt memória maximális értékét. Ha a felhasznált összeg kisebb, mint a minimális virtuális magok és a min memória által beállított minimális kiépített összeg, majd a kiosztott minimális összeget kell fizetni.Annak érdekében, hogy összehasonlítsa a CPU és a memória számlázási célokra, memória normalizálódik a virtuális magok egységei által átméretezésa a memória mennyisége GB-ban 3 GB/vCore.|virtuális mag másodperc|
|Alkalmazáscsomag|app_memory_percent|Az alkalmazás által használt memória százaléka az alkalmazás számára engedélyezett maximális memória hez viszonyítva.|Százalék|
|Felhasználói készlet|cpu_percent|A felhasználói munkaterhelés által használt virtuális magok százalékos aránya a felhasználói munkaterheléshez engedélyezett maximális virtuális magokhoz viszonyítva.|Százalék|
|Felhasználói készlet|data_IO_percent|A felhasználói munkaterhelés által használt adatok IOPS-ának százalékos aránya a felhasználói munkaterheléshez engedélyezett maximális iops-adatokhoz viszonyítva.|Százalék|
|Felhasználói készlet|log_IO_percent|A felhasználói munkaterhelés által használt naplóMB/s százaléka a felhasználói munkaterheléshez engedélyezett maximális napló MB/s értékhez viszonyítva.|Százalék|
|Felhasználói készlet|workers_percent|A felhasználói munkaterhelés által használt dolgozók százalékos aránya a felhasználói munkaterheléshez engedélyezett dolgozók maximális értékéhez viszonyítva.|Százalék|
|Felhasználói készlet|sessions_percent|A felhasználói munkaterhelés által használt munkamenetek százalékos aránya a felhasználói munkaterheléshez engedélyezett maximális munkamenetekhez viszonyítva.|Százalék|

### <a name="pause-and-resume-status"></a>Állapot szüneteltetése és folytatása

Az Azure Portalon az adatbázis állapota jelenik meg az áttekintő ablaktáblában a kiszolgáló, amely felsorolja a benne található adatbázisokat. Az adatbázis állapota az adatbázis áttekintő ablaktáblájában is megjelenik.

Az adatbázis szüneteltetési és folytatási állapotának lekérdezéséhez használja a következő parancsokat:

#### <a name="use-powershell"></a>A PowerShell használata

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Erőforráskorlátok

Az erőforráskorlátokról a [kiszolgáló nélküli számítási szint látható.](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)

## <a name="billing"></a>Számlázás

A számlázott számítási mennyiség a felhasznált processzor és a másodpercenként használt memória maximális mennyisége. Ha a felhasznált processzor és a felhasznált memória mennyisége kisebb, mint az egyes minimális kiépített összeg, majd a kiosztott összeg számlázása. Annak érdekében, hogy összehasonlítsa a CPU és a memória számlázási célokra, memória normalizálódik a virtuális magok egységei által átméretezésa a memória mennyisége GB-ban 3 GB/vCore.

- **Számlázott erőforrás**: CPU és memória
- **Számlázott összeg:** virtuális mag egységára * max (min virtuális magok, használt virtuális magok, min memória GB * 1/3, használt memória GB * 1/3) 
- **Számlázási gyakoriság**: Másodpercenként

A virtuális mag egységára a virtuális mag/másodperc költség. Tekintse meg az [Azure SQL Database díjszabási lap](https://azure.microsoft.com/pricing/details/sql-database/single/) adott egységárak egy adott régióban.

A számlázott számítási mennyiség a következő mérőszám szerint jelenik meg:

- **Mérőszám**: app_cpu_billed (virtuális magmásodperc)
- **Definíció:** max (min virtuális magok, használt virtuális magok, min memória GB * 1/3, használt memória GB * 1/3)
- **Jelentés gyakorisága**: Percenként

Ezt a mennyiséget másodpercenként számítják ki, és 1 perc alatt összesítik.

Fontolja meg egy kiszolgáló nélküli adatbázis konfigurálva 1 perc virtuális mag és 4 max virtuális magok.  Ez körülbelül 3 GB min memóriának és 12 GB max memóriának felel meg.  Tegyük fel, hogy az automatikus szüneteltetési késleltetés 6 óra, és az adatbázis munkaterhelése aktív egy 24 órás időszak első 2 órájában, és egyébként inaktív.    

Ebben az esetben az adatbázis számlázása számítási és tárolási az első 8 órában.  Annak ellenére, hogy az adatbázis inaktív a második óra után kezdődően, a következő 6 órában továbbra is kiszámlázva számítják ki a számítási adatbázis ban, miközben az adatbázis online állapotban van.  Csak a 24 órás időszak hátralévő részében kerül számlázásra a rendszer, amíg az adatbázis szünetel.

Pontosabban, a számítási számlát ebben a példában a következőképpen számítja ki:

|Időintervallum|minden másodpercben használt virtuális magok|GB minden másodpercben|Számlázott dimenzió számítása|időintervallumban számlázott virtuálismag-másodpercek|
|---|---|---|---|---|
|0:00-1:00|4|9|használt virtuális magok|4 virtuális mag * 3600 másodperc = 14400 virtuális mag másodperc|
|1:00-2:00|1|12|Használt memória|12 GB * 1/3 * 3600 másodperc = 14400 virtuális mag másodperc|
|2:00-8:00|0|0|Kiosztott min memória|3 GB * 1/3 * 21600 másodperc = 21600 virtuális mag másodperc|
|8:00-24:00|0|0|Szüneteltetett idő alatt nincs számlázott számítás|0 virtuális mag másodperc|
|24 óra alatt számlázott összes virtuálismag-másodperc||||50400 virtuális mag másodperc|

Tegyük fel, hogy a számítási egységár $0.000145/vCore/másodperc.  Ezután a 24 órás időszakra kiszámlázott számítási díj a kiszámlázott számítási egységár és a virtuálismag-másodpercek szorzata: $0.000145/vCore/second * 50400 virtuálismag másodperc ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit és fenntartott kapacitás

Az Azure Hybrid Benefit (AHB) és a fenntartott kapacitáskedvezmények nem vonatkoznak a kiszolgáló nélküli számítási szintre.

## <a name="available-regions"></a>Elérhető régiók

A kiszolgáló nélküli számítási szint világszerte elérhető, kivéve a következő régiókat: Kína Kelet, Kína Északi, Kína Közép-, Németország északkeleti része, az Egyesült Királyság északi része, az Egyesült Királyság déli 2.

## <a name="next-steps"></a>További lépések

- Első lépések: [Rövid útmutató: Egyetlen adatbázis létrehozása az Azure SQL Database-ben az Azure Portal használatával című témakörben.](sql-database-single-database-get-started.md)
- Az erőforráskorlátokról a [Kiszolgáló nélküli számítási réteg erőforráskorlátai](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
