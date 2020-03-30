---
title: Recovery Manager a szegmenstérkép-problémák megoldásához
description: A RecoveryManager osztály használata a shard térképek problémáinak megoldásához
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823856"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Horizontális skálázási térképek javítása a RecoveryManager osztállyal

A [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) osztály lehetővé teszi ADO.NET alkalmazások számára, hogy könnyen észlelje és javítsa ki a globális shard térkép (GSM) és a helyi shard térkép (LSM) közötti inkonzisztenciákat egy szilánkos adatbázis-környezetben.

A GSM és az LSM nyomon követi az egyes adatbázisok leképezését egy szilánkos környezetben. Alkalmanként szünet következik be a GSM és az LSM között. Ebben az esetben a RecoveryManager osztály segítségével észleli és kijavítja a törést.

A RecoveryManager osztály a [Rugalmas adatbázis ügyfélkódtár](sql-database-elastic-database-client-library.md)része.

![Shard térkép][1]

A kifejezésdefiníciókról az [Elastic Database tools szószedet című témakörben található.](sql-database-elastic-scale-glossary.md) Ha meg szeretné tudni, hogy a **ShardMapManager** hogyan kezeli az adatokat egy szilánkos megoldásban, olvassa el a [Shard térképkezelés című témakört.](sql-database-elastic-scale-shard-map-management.md)

## <a name="why-use-the-recovery-manager"></a>Miért érdemes használni a helyreállítási kezelőt?

Szilánkos adatbázis-környezetben adatbázisonként egy bérlő van, és kiszolgálónként több adatbázis is található. A környezetben számos kiszolgáló is lehet. Minden adatbázis le van képezve a shard térképen, így a hívások a megfelelő kiszolgálóra és adatbázisba irányíthatók. Az adatbázisokat a **kulcskőkulcs**szerint követi nyomon a függvény , és minden szegmenshez **kulcsértékek tartománya**van hozzárendelve. Egy szilánkos kulcs például a vevőneveket "D"-től "F"-ig ábrázolhatja. Az összes szegmens (más néven adatbázisok) és azok leképezési tartományainak leképezése a **globális shard térkép (GSM)** tartalmazza. Minden adatbázis tartalmaz egy térképet a shard-on található tartományokról, amelyet **a helyi shard térkép (LSM) néven ismerünk.** Amikor egy alkalmazás egy szegmenshez csatlakozik, a leképezés az alkalmazás gyorsítótárba kerül a gyors lekérés érdekében. Az LSM a gyorsítótárazott adatok érvényesítésére szolgál.

A GSM és az LSM a következő okok miatt nem lesz szinkronban:

1. A shard törlése, amelynek tartománya úgy vélik, hogy már nem használatos, vagy átnevezése egy shard. A szegmens törlése **árva szegmensleképezést**eredményez. Hasonlóképpen egy átnevezett adatbázis árva szegmensleképezést okozhat. Attól függően, hogy a szándék a változás, a shard lehet, hogy el kell távolítani, vagy a shard helyét frissíteni kell. Törölt adatbázis helyreállításáról a [Törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md).
2. Geo-feladatátvételi esemény következik be. A folytatáshoz frissíteni kell a kiszolgáló nevét, és az adatbázis nevét shard map manager az alkalmazásban, majd frissítse a szegmensek leképezési részleteit a shard térképen. Geo-feladatátvétel esetén az ilyen helyreállítási logikát a feladatátvételi munkafolyamaton belül kell automatikusan rendszereznie. A helyreállítási műveletek automatizálása zökkenőmentes kezelhetőséget tesz lehetővé a geoalapú adatbázisok számára, és elkerüli a manuális emberi műveleteket. Az adatbázis adatbázis-kimaradásesetén való helyreállításának lehetőségeiről az [Üzletmenet-folytonosság](sql-database-business-continuity.md) és [a vészhelyreállítás témakörben](sql-database-disaster-recovery.md)olvashat.
3. Egy shard vagy a ShardMapManager adatbázis visszaáll egy korábbi point-in idő. Ha többet szeretne tudni a biztonsági másolatok használatával történő időpont-helyreállításról, olvassa el a [Helyreállítási biztonsági másolatok használatával (Helyreállítás biztonsági másolatok használatával) témakört.](sql-database-recovery-using-backups.md)

Az Azure SQL Database Elastic Database eszközeiről, a georeplikációról és a visszaállításról az alábbi témakörben talál további információt:

* [Áttekintés: Felhőbeli üzletmenet-folytonosság és adatbázis-vészhelyreállítás az SQL Database segítségével](sql-database-business-continuity.md)
* [Első lépések a rugalmas adatbázis-eszközökkel](sql-database-elastic-scale-get-started.md)  
* [ShardMap-kezelés](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>RecoveryManager beolvasása ShardMapManager-ből

Az első lépés egy RecoveryManager példány létrehozása. A [GetRecoveryManager metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) az aktuális [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) példány helyreállítási kezelőjét adja vissza. A szegmenstérkép inkonzisztenciáinak kezeléséhez először be kell olvasnia a RecoveryManager-t az adott szegmenstérképhez.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Ebben a példában a RecoveryManager inicializálva a ShardMapManager. A ShardMap-et tartalmazó ShardMapManager is már inicializálva van.

Mivel ez az alkalmazáskód magát a szegmensleképezést manipulálja, a gyári metódusban használt hitelesítő adatoknak (az előző példában az smmConnectionString) olyan hitelesítő adatoknak kell lenniük, amelyek a kapcsolat által hivatkozott GSM-adatbázishoz írási-olvasási engedéllyel rendelkeznek. Karakterlánc. Ezek a hitelesítő adatok általában eltérnek az adatfüggő útválasztás hoz a kapcsolatok megnyitásához használt hitelesítő adatoktól. További információ: [A hitelesítő adatok használata a rugalmas adatbázis-ügyfélben](sql-database-elastic-scale-manage-credentials.md)című témakörben talál.

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Egy shard eltávolítása a ShardMap-ből egy shard törlése után

A [DetachShard metódus](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) leválasztja a megadott szegmenst a szegmenstérképről, és törli a szegmenshez társított leképezéseket.  

* A helyparaméter a shard helye, különösen a kiszolgáló nevét és az adatbázis nevét, a szegmens leválasztva.
* A shardMapName paraméter a shard térkép neve. Ez csak akkor szükséges, ha több shard térképek által kezelt ugyanaz a szegmens térképkezelő. Választható.

> [!IMPORTANT]
> Ezt a módszert csak akkor használja, ha biztos abban, hogy a frissített leképezés tartománya üres. A fenti módszerek nem ellenőrzik az áthelyezett tartomány adatait, ezért a legjobb, ha az ellenőrzéseket is belefoglalja a kódba.

Ebben a példában eltávolítja a szilánkok a shard térképről.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

A shard térkép tükrözi a shard helyét a GSM törlése előtt a shard. Mivel a szegmens törölve lett, feltételezzük, hogy szándékos volt, és a szilánkolási kulcstartomány már nincs használatban. Ha nem, akkor végrehajthatja a point-in idő-visszaállítást. a szegmens helyreállítása egy korábbi pont-in-time. (Ebben az esetben tekintse át a következő szakaszt a szegmensinkonzisztenciák észleléséhez.) A helyreállításhoz lásd: [Idő-helyreállítás](sql-database-recovery-using-backups.md).

Mivel feltételezhető, hogy az adatbázis törlése szándékos volt, a végső felügyeleti törlési művelet a shard bejegyzéstörlése a shard térképkezelőben. Ez megakadályozza, hogy az alkalmazás véletlenül olyan tartományba írjon információkat, amely nem várt.

## <a name="to-detect-mapping-differences"></a>Leképezési különbségek észlelése

A [DetectMappingDifferences metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) kiválasztja és visszaadja az egyik shard leképezést (helyi vagy globális) az igazság forrásaként, és egyezteti a leképezéseket a szegmenstérképeken (GSM és LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* A *hely* a kiszolgáló nevét és az adatbázis nevét adja meg.
* A *shardMapName* paraméter a shard térkép neve. Ez csak akkor szükséges, ha több shard térképek által kezelt ugyanaz a szegmens térképkezelő. Választható.

## <a name="to-resolve-mapping-differences"></a>Leképezési különbségek megoldása

A [ResolveMappingDifferences metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) kiválasztja az egyik shard leképezések (helyi vagy globális) az igazság forrásaként, és egyezteti leképezések mindkét szegmens leképezések (GSM és LSM) leképezések.

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* A *RecoveryToken* paraméter számba veszi a GSM és az LSM közötti különbségeket az adott szegmenshez.
* A [MappingDifferenceResolution enumerálás](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) a szegmensleképezések közötti különbség feloldásának metódusát jelzi.
* **MappingDifferenceResolution.KeepShardMapping** ajánlott, hogy ha az LSM tartalmazza a pontos leképezés, és ezért a leképezés a shard kell használni. Ez általában akkor áll fenn, ha feladatátvétel történik: a szegmens most egy új kiszolgálón található. Mivel a szegmenst először el kell távolítani a GSM-ből (a RecoveryManager.DetachShard metódus használatával), a GSM-en már nem létezik leképezés. Ezért az LSM-et kell használni a szegmensleképezés visszaállításához.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Shard csatolása a ShardMap-hez a szegmens visszaállítása után

Az [AttachShard metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) csatolja a megadott shard a shard térképhez. Ezután észleli a szegmenstérkép-inkonzisztenciákat, és frissíti a leképezéseket, hogy megfeleljenek a szegmens visszaállításának. Feltételezzük, hogy az adatbázis is átnevezése, hogy tükrözze az eredeti adatbázis nevét (mielőtt a szegmens visszaállítása), mivel a pont-időpont helyreállítása alapértelmezés szerint egy új adatbázis hozzáfűzve az időbélyeg.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* A *helyparaméter* a kiszolgáló neve és az adatbázis neve, a hozzácsatolt szegmens.
* A *shardMapName* paraméter a shard térkép neve. Ez csak akkor szükséges, ha több shard térképek által kezelt ugyanaz a szegmens térképkezelő. Választható.

Ez a példa egy shard-ot ad hozzá a shard térképhez, amely nemrég lett visszaállítva egy korábbi időpontból. Mivel a shard (azaz a shard az LSM) helyreállt, potenciálisan nem egyeztethető össze a shard bejegyzést a GSM.Since the shard (azaz a shard in the LSM) has ad at a, it is potentially inconsistent with the shard entry in the GSM. Ezen a példakódon kívül a szegmens visszalett állítva, és az adatbázis eredeti nevére lett átnevezve. Mivel visszaállították, a rendszer feltételezi, hogy az LSM-ben lévő leképezés a megbízható leképezés.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>A szegmensek földrajzi feladatátvétele (visszaállítása) után a szegmensek frissítése

Geo-feladatátvétel esetén a másodlagos adatbázis írási elérhetővé válik, és az új elsődleges adatbázis lesz. A kiszolgáló neve és potenciálisan az adatbázis (a konfigurációtól függően) eltérhet az eredeti elsődlegestől. Ezért a GSM-ben és az LSM-ben a szegmens leképezési bejegyzéseit rögzíteni kell. Hasonlóképpen, ha az adatbázis vissza áll egy másik nevet vagy helyet, vagy egy korábbi időpontban, ez inkonzisztenciákat okozhat a shard térképek. A Shard Map Manager kezeli a nyitott kapcsolatok elosztását a megfelelő adatbázisba. A terjesztési adatok on the shard map and the value of the sharding key that is the target of the applications request. A geo-feladatátvétel után ezt az információt frissíteni kell a helyreállított adatbázis pontos kiszolgálónevével, adatbázisnevével és szegmensleképezésével.

## <a name="best-practices"></a>Ajánlott eljárások

A geo-feladatátvétel és a helyreállítás olyan műveletek, amelyeket általában az alkalmazás felhőrendszergazdája kezel, és amelyek az Azure SQL-adatbázisok egyik üzletmenet-folytonossági szolgáltatását szándékosan használja. Az üzletmenet folytonosságának megtervezése folyamatokat, eljárásokat és intézkedéseket igényel annak érdekében, hogy az üzleti műveletek megszakítás nélkül folytatódhassanak. A RecoveryManager osztály részeként elérhető módszereket ebben a munkafolyamatban kell használni annak biztosítására, hogy a GSM és az LSM naprakész legyen a helyreállítási művelet alapján. A GSM és az LSM öt alapvető lépéssel rendelkezik annak megfelelő biztosításához, hogy a feladatátvételi esemény után a pontos információkat tükrözze. A lépések végrehajtásához szükséges alkalmazáskód integrálható a meglévő eszközökbe és munkafolyamatokba.

1. A RecoveryManager beolvasása a ShardMapManager-ből.
2. Válassza le a régi szilánkot a shard térképről.
3. Csatlakoztassa az új shard a shard térkép, beleértve az új shard helyét.
4. A GSM és az LSM közötti leképezésini inkonzisztenciák észlelése.
5. A GSM és az LSM közötti különbségek feloldása az LSM-ben.

Ez a példa a következő lépéseket hajtja végre:

1. Eltávolítja a szilánkok a shard térkép, amely tükrözi a shard helyek a feladatátvételi esemény előtt.
2. Szilánkok a shard térkép, amely az új szegmens helyeken (a paraméter "Configuration.SecondaryServer" az új kiszolgáló nevét, de ugyanazt az adatbázis nevét).
3. Lekéri a helyreállítási jogkivonatokat a GSM és az LSM közötti leképezési különbségek észlelésével az egyes szegmensek esetében.
4. Az inkonzisztenciák megoldásához bízza meg az egyes szegmensek LSM-ből történő leképezést.

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
