---
title: Helyreállítás-kezelő segítségével szilánkleképezési problémák javítása |} A Microsoft Docs
description: A RecoveryManager osztály használatával kapcsolatos problémák megoldásához a szegmenstérképet
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: f6c289c87f4f58fdad8950bdf61fa68016fe8d3e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042070"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Horizontális skálázási térképek javítása a RecoveryManager osztállyal

A [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) osztály teszi lehetővé az ADO.Net-alkalmazások könnyen észlelheti és javíthatja a a globális szegmenstérkép (GSM) és a egy szilánkokra osztott adatbázis-környezetet a helyi szegmenstérkép (LSM) közötti inkonzisztenciákat.

A GSM és LSM nyomon követheti a leképezést a horizontálisan skálázott környezetben az egyes adatbázisok. Alkalmanként szünet következik be, a GSM és a LSM között. Ebben az esetben a RecoveryManager osztály használatával észleli és megjavítja a szünet.

A RecoveryManager osztály része a [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md).

![Horizontális skálázási térképet][1]

Kifejezés-definíciókat, lásd: [rugalmas adatbáziseszközökkel](sql-database-elastic-scale-glossary.md). Tudni, hogyan a **ShardMapManager** szolgál a horizontálisan skálázott megoldások adatok kezelésére, olvassa el [Szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Miért érdemes használni a helyreállítás-kezelő

Szilánkokra osztott adatbázis környezetben nincs adatbázisonkénti egyetlen bérlőt, és számos adatbázis kiszolgálónként. Emellett lehetnek kiszolgálók számát a környezetben. Minden adatbázis le van képezve a horizontális skálázási térképet, így hívásai átirányíthatók a megfelelő kiszolgálóhoz és adatbázishoz. Adatbázisok nyomon követi a következők szerint egy **horizontális skálázási kulcs**, és minden egyes szegmens hozzá van rendelve egy **értékek tartományán**. Például olyan szegmenskulcsot tüntetheti ügyfél nevét a "D", "F" Található minden szegmensre (más néven adatbázisok) és a hozzárendelés tartományok leképezése a **globális szegmenstérkép (GSM)**. Minden adatbázis is tartalmaz a tartományokat, a szegmens, amely az úgynevezett által tartalmazott térképet a **helyi szegmenstérkép (LSM)**. Amikor egy alkalmazás szegmensek csatlakozik, a hozzárendelés gyorsítótárazza gyors lekérésére az alkalmazással. A LSM gyorsítótárazott adatok ellenőrzésére szolgál.

A GSM és LSM válhat nincs szinkronban a következő okok miatt:

1. Egy szegmens, amely a címtartomány feltételezhető, hogy már nem használja, vagy a szegmensek átnevezése törlését. Szegmensek törlése eredményezi egy **szegmens leképezés árva**. Ehhez hasonlóan átnevezve adatbázis okozhat egy árva szilánkleképezés-hozzárendelést. Függően a módosítást előfordulhat, hogy a szegmenshez kell őket távolítani, vagy frissíteni kell helyét a szegmensek közt. Tekintse meg a törölt adatbázis helyreállítása [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md).
2. A földrajzi feladatátvételt esemény következik be. A folytatáshoz egy kell frissíteni, a kiszolgáló nevét, és a szilánkleképezés-kezelővel, az alkalmazás nevét, és frissítse minden szegmensre horizontálispartíció-térkép a szegmens hozzárendelés részletei. Ha földrajzi feladatátvételt, az ilyen helyreállítási logikai kell automatikus a feladatátvétel munkafolyamaton belül. A helyreállítási műveletek automatizálása lehetővé teszi a zökkenőmentes kezelhetőségét geo-kompatibilis adatbázisokat, és elkerülhető az emberi manuális műveleteket. Adatbázis helyreállítása, ha a tartós adatközponti üzemkimaradások lehetőségekkel kapcsolatos tudnivalókért lásd: [üzletmenet-folytonossági](sql-database-business-continuity.md) és [vész-helyreállítási](sql-database-disaster-recovery.md).
3. Szegmensek vagy a ShardMapManager adatbázis egy korábbi pont – az adott visszaállítja. Pont az idő helyreállítás biztonsági másolatokkal kapcsolatos további információkért lásd: [helyreállítás biztonsági másolatokkal](sql-database-recovery-using-backups.md).

Az Azure SQL Database Elastic Database-eszközök, a georeplikáció és a visszaállítási kapcsolatos további információkért tekintse meg a következőket:

* [Áttekintés: A felhő üzleti folytonossági és az adatbázis vész-helyreállítási az SQL Database szolgáltatással](sql-database-business-continuity.md)
* [Ismerkedés az elastic database-eszközökkel](sql-database-elastic-scale-get-started.md)  
* [ShardMap kezelése](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Egy ShardMapManager RecoveryManager lekérése

Az első lépéseként RecoveryManager példányt hoz létre. A [GetRecoveryManager metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) adja vissza a helyreállítás-kezelő a jelenlegi [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) példány. Oldja meg a szegmenstérkép mindennemű inkonzisztenciáját, először le kell kérnie a RecoveryManager az adott horizontális skálázási térképet.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Ebben a példában a RecoveryManager a ShardMapManager van inicializálva. Az egy ShardMap tartalmazó ShardMapManager is már inicializálva van.

Az alkalmazás kódjában a szegmenstérkép maga kezeli, mivel a gyári metódus (az előző példában smmConnectionString) használt hitelesítő adatokat kell lennie a GSM-adatbázis, a kapcsolat által hivatkozott olvasási és írási engedéllyel rendelkező hitelesítő adatokat karakterlánc. Ezeket a hitelesítő adatokat általában eltérnek a Adatfüggő útválasztás kapcsolat megnyitásához használt hitelesítő adatokat. További információkért lásd: [a rugalmas adatbázis-ügyfél hitelesítő adatok használatával](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>A szegmensek eltávolítása a ShardMap szegmensek törlése után

A [DetachShard metódus](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) leválasztja az adott szegmens a horizontális skálázási térképet, és törli a szegmenshez tartozó leképezések.  

* A hely paraméter helyét a szegmensek közt, kifejezetten a kiszolgáló nevét és az adatbázis nevét, a szegmensek leválasztása folyamatban.
* ShardMapName paraméter a szilánkleképezés-leképezés neve. Ez a tulajdonság csak akkor szükséges, ha több szegmenstérképet az azonos szilánkleképezés-kezelő kezel. Választható.

> [!IMPORTANT]
> Ezt a módszert használja, csak ha biztos abban, hogy a frissített leképezése tartománya üres. A fenti módszerek nem ellenőrzi a tartomány áthelyezése esetén az adatok, így érdemes ellenőrzéseket tartalmazzák a kódban.

Ebben a példában a szegmenstérkép szegmensek távolít el.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

A horizontális skálázási térképet tükrözi a a GSM-ben a szegmensek a törlés előtt helyét a szegmensek közt. A szegmens törölve lett, mert feltételezzük ez szándékos volt, és a horizontális skálázási kulcs tartomány már használatban van. Ha nem, a helyreállítási pont az adott hajthat végre. a szegmens helyreállítása egy korábbi-időponthoz. (Ebben az esetben ellenőrizze az alábbi szakaszt szegmens inkonzisztenciát észleléséhez.) Szeretné használni, lásd: [az idő a helyreállítási pont](sql-database-recovery-using-backups.md).

Feltételezzük, hogy az adatbázis-törlési szándékos volt, mert az utolsó felügyeleti karbantartási művelet törli a bejegyzést a szegmenshez a szilánkleképezés-kezelővel a. Ez megakadályozza, hogy az alkalmazás olyan tartományt, amely nem várt adatok véletlenül írásakor.

## <a name="to-detect-mapping-differences"></a>Leképezés eltérések észlelését

A [DetectMappingDifferences metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) kiválasztása és hiteles forrásaként a szegmenstérképet (helyi vagy globális) egyikét adja vissza, és összehangolja ezzel leképezése mindkét szegmenstérképet (GSM és LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* A *hely* adja meg a kiszolgáló nevét és az adatbázis nevét.
* A *shardMapName* paraméter értéke a szilánkleképezés-leképezés neve. Ez csak akkor szükséges, ha több szegmenstérképet az azonos szilánkleképezés-kezelő kezel. Választható.

## <a name="to-resolve-mapping-differences"></a>A leképezés különbségek feloldása

A [ResolveMappingDifferences metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) kiválaszt közülük egyet a szegmenstérképet (helyi vagy globális) hiteles forrásaként, és összehangolja leképezése mindkét szegmenstérképet (GSM és LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* A *RecoveryToken* paraméter enumerálása a leképezéseket a GSM és a LSM számára az adott szegmens közötti különbségeket.
* A [MappingDifferenceResolution enumerálás](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) jelzi az a különbség a szilánkleképezés-leképezések megoldásának módszere.
* **MappingDifferenceResolution.KeepShardMapping** ajánlott, ha a LSM tartalmaz a pontos leképezést, és ezért a szegmensben leképezést kell használni. Ez általában az az eset, ha feladatátvitel történik: a szegmens már található egy új kiszolgálón. A szegmens először el kell távolítani a GSM (RecoveryManager.DetachShard mód használatával), mert a leképezés már nem létezik, a GSM a. Ezért a LSM ismételt létrehozása a szilánkleképezés-leképezést kell használható.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Szilánk csatolása a ShardMap szegmensek visszaállítását követően

A [AttachShard metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) csatolja az adott szegmens horizontális skálázási térképet. Ezután minden szegmensben térkép észlel, és frissíti az f:\ megfelelően a szegmens a szilánkleképezés-visszaállítás ponton kell megadni. Azt feltételezzük, hogy az adatbázis is átnevezve, hogy az eredeti adatbázis nevét (mielőtt a szegmens vissza lett állítva), mivel az a pont – az adott visszaállítás az alapértelmezett hozzáfűzi az időbélyeg az új adatbázist.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* A *hely* paraméter a kiszolgáló nevét és az adatbázis neve, a szegmensek csatolni.
* A *shardMapName* paraméter értéke a szilánkleképezés-leképezés neve. Ez a tulajdonság csak akkor szükséges, ha több szegmenstérképet az azonos szilánkleképezés-kezelő kezel. Választható.

Ebben a példában egy szegmens hozzáadása a szegmenstérképet, amely mostanában helyreállt a pont a korábbi időpontra. A szegmens (azaz a leképezés a szegmens a LSM a) vissza lett állítva, mert ez nem a szilánkleképezés-bejegyzést a GSM potenciálisan konzisztens. Ebben a példában a kódban kívül a szegmens lett visszaállítva, és az eredeti nevére, az adatbázis neve. Vissza lett állítva, mivel feltételezzük a leképezést a LSM a rendszer a megbízható leképezés.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Szilánkleképezés-helyek frissítése után földrajzi feladatátvételt (helyreállíthatja) a szegmens

Ha földrajzi feladatátvételt, a másodlagos adatbázis írási elérhető lesz, és válik az új elsődleges adatbázis. Lehet, hogy a kiszolgáló és potenciálisan (a konfigurációtól függően) az adatbázis neve eltér az eredeti elsődleges. Ezért a leképezés bejegyzéseket a GSM és LSM a szegmenshez kell rögzíteni. Hasonlóképpen ha az adatbázis helyreállítása egy másik nevet vagy helyet, vagy egy korábbi időpontra az idő a szegmenstérképet Emiatt előfordulhat, hogy inkonzisztenciát. A Szilánkleképezés-kezelővel nyissa meg a megfelelő adatbázishoz való csatlakozás eloszlása kezeli. Terjesztési a szegmenstérkép és a horizontális skálázási kulcs, amelyek célja az alkalmazások kérés értékét az adatok alapján. Egy földrajzi feladatátvétel után ezek az információk frissíteni kell a pontos kiszolgáló neve, az adatbázis neve és a szegmens leképezés a helyreállított adatbázis.

## <a name="best-practices"></a>Ajánlott eljárások

Földrajzi feladatátvételt és helyreállítási műveletek általában kezeli a felhő rendszergazdájának az alkalmazás szándékosan használatával egy Azure SQL adatbázisok üzletmenet-folytonossági funkciókat, amelyek. Üzleti folytonosság tervezés szükséges folyamatok, eljárásokra és intézkedéseket annak biztosítása érdekében, hogy a üzleti műveletek továbbra is megszakítás nélkül. A következő módszerek, a RecoveryManager osztály része annak biztosítása érdekében a GSM és LSM mindig naprakészek, mert a munkahelyi flow-ban használandó alapján végrehajtott helyreállítási műveletet. Nincsenek megfelelően biztosítva a GSM és LSM tükrözik a feladatátadási esemény után a pontos információkat öt alapvető lépéseket. Hajtsa végre ezeket a lépéseket, az alkalmazás kódja integrálhatók meglévő eszközökkel és a munkafolyamat.

1. A RecoveryManager lekérni a ShardMapManager.
2. Válassza le a régi szegmens a horizontális skálázási térképet.
3. Az új szegmensen csatolása a szegmenstérkép, beleértve a szegmensek új helyét.
4. Képes észlelni a GSM és LSM közötti inkonzisztenciákat.
5. Oldja meg a GSM és a LSM a LSM megbízó közötti különbségeket.

Ebben a példában a következő lépéseket hajtja végre:

1. A szegmensek eltávolítása a Szegmenstérkép, amely a feladatátvétel megtörténte előtt szegmens helyek.
2. A szegmensek csatolja a Szegmenstérkép-hoz továbbítható az új szegmensek helyek (a "Configuration.SecondaryServer" paraméter az új kiszolgáló nevét, de az azonos adatbázis neve).
3. A GSM és a LSM mindegyik szegmenshez leképezés különbségeit észlelésével kérdezi le a helyreállítási jogkivonatokat.
4. Az inkonzisztenciák feloldása által az egyes szegmensek LSM leképezése megbízó.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png