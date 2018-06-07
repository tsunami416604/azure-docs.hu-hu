---
title: Helyreállítás-kezelő használatával shard térkép problémák elhárításának |} Microsoft Docs
description: A RecoveryManager osztály használatával shard maps kapcsolatos problémák megoldásához
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 6257edbb567be3ebb3151724e7e50ca81905ad40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646235"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Horizontális skálázási térképek javítása a RecoveryManager osztállyal
A [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) osztály teszi lehetővé az ADO.Net alkalmazások felismerését és javítsa ki a globális shard térkép (GSM) és a helyi shard térkép (LSM) szilánkos adatbázis környezetben közötti inkonzisztenciákat. 

A globális szolgáltatásfigyelő szolgáltatás és a LSM nyomon követheti az egyes adatbázisok szilánkos környezetben leképezése. Alkalmanként szünet a globális szolgáltatásfigyelő szolgáltatás és a LSM között történik. Ebben az esetben használja a RecoveryManager osztály észlelésére, és javítsa ki a szünet.

A RecoveryManager osztály része a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md). 

![A shard térkép][1]

Kifejezés-definíciók, lásd: [rugalmas adatbázis eszközök szószedet](sql-database-elastic-scale-glossary.md). Tudni, hogyan a **ShardMapManager** szolgál a szilánkos megoldás adatok kezelésére, olvassa el [Shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Miért érdemes használni a helyreállítás-kezelő?
Horizontálisan skálázott adatbázis környezetben nincs adatbázisonként egy bérlői, és számos más adatbázis kiszolgálónként. Lehetnek is kiszolgálók számát a környezetben. Minden egyes adatbázis hozzá van rendelve a shard térkép hívások irányíthassa megfelelő kiszolgálót és adatbázist. Adatbázisok a következők szerint követi nyomon a rendszer egy **horizontális kulcs**, és minden shard hozzá van rendelve egy **értékek tartományán**. "D" az ügyfélnevek a horizontális kulcs képviselhet például, "F" Minden szilánkok (más néven adatbázisok) és a leképezés tartományok leképezése tartalmazza a **globális shard térkép (GSM)**. Az egyes adatbázisok is található a szilánkcímtárban szerint tartományok térképét tartalmazza a **helyi shard térkép (LSM)**. Amikor egy alkalmazás egy shard csatlakozik, a hozzárendelés gyorsítótárazza, és az alkalmazás a gyors beolvasásához. A LSM gyorsítótárazott adatok ellenőrzésére szolgál. 

A globális szolgáltatásfigyelő szolgáltatás és a LSM válhat nincs szinkronban a következők lehetnek az okai:

1. A shard, amelynek tartomány feltételezhető, hogy már nem használja, vagy átnevezésének a shard törlését. Egy shard törlése eredményezi egy **árván maradt a szilánkok leképezése**. Hasonlóképpen a átnevezett adatbázis egy árva szilánkok leképezése okozhat. Attól függően, hogy a módosítás célját a shard esetleg el kell távolítani, vagy frissíteni kell a shard helyét. A törölt adatbázisok helyreállításához, lásd: [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md).
2. A földrajzi-feladatátadási esemény következik be. A folytatáshoz egy kell frissíteni, a kiszolgáló nevét, és a shard térkép Manager az alkalmazás nevét, és frissítse a shard térképen az összes szilánkok szilánkok leképezése részleteit. Földrajzi-feladatátvétel esetén az ilyen helyreállítási logika belül a feladatátvételi munkafolyamat érdemes automatizálni. Helyreállítási műveletek automatizálása lehetővé teszi, hogy a frictionless kezelhetőségi adatbázisok földrajzi engedélyezve van, és ezzel elkerülheti a manuális emberi műveletek. Adatbázis helyreállítása, ha egy adatközpont-meghibásodás után beállításokkal kapcsolatos további tudnivalókért lásd: [az üzletmenet folytonossága](sql-database-business-continuity.md) és [vész-helyreállítási](sql-database-disaster-recovery.md).
3. A szilánkok vagy a ShardMapManager adatbázis egy korábbi pont az időpontra állítottak vissza. Időpontra történő visszaállítás biztonsági mentések használatával kapcsolatos további tudnivalókért lásd: [biztonsági másolatokból helyreállítási](sql-database-recovery-using-backups.md).

Azure SQL Database rugalmas adatbáziseszközöket, georeplikáció és visszaállításával kapcsolatban további információkért tekintse meg a következőket: 

* [Áttekintése: A felhő üzleti folytonossági és az adatbázis katasztrófa utáni helyreállítás az SQL Database](sql-database-business-continuity.md) 
* [Ismerkedés a rugalmas adatbázis-eszközök](sql-database-elastic-scale-get-started.md)  
* [ShardMap kezelése](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Egy ShardMapManager RecoveryManager lekérése
Az első lépés, ha a RecoveryManager példánya. A [GetRecoveryManager metódus](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) adja vissza az aktuális helyreállítás-kezelő [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) példány. Kezelje a shard térkép inkonzisztenciákat, az adott shard térkép RecoveryManager először be kell olvasni. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

Az ebben a példában a RecoveryManager a ShardMapManager az inicializálása. Egy ShardMap tartalmazó ShardMapManager is már inicializálva van. 

Az alkalmazás kódjában a shard térkép maga kezeli, mivel a gyári metódusban (a fenti példában smmConnectionString) használt hitelesítő adatok olvasási és írási jogosultságokkal rendelkezik a kapcsolati karakterlánc által hivatkozott GSM adatbázishoz megadott hitelesítő adatokat kell lennie. Ezek a hitelesítő adatok általában eltérnek a nyitott kapcsolatot az adatok függő továbbításához használt hitelesítő adatokat. További információkért lásd: [hitelesítő adatokkal a rugalmas adatbázis-ügyfélben](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>A szilánkok eltávolítása a ShardMap egy shard törlése után
A [DetachShard metódus](https://msdn.microsoft.com/library/azure/dn842083.aspx) szervezőről a megadott shard shard leképezés, és törli a shard társított leképezéseket.  

* A hely paraméter shard helyét, kifejezetten a kiszolgálónév és a adatbázis nevét, a shard leválasztás alatt áll. 
* A shardMapName paraméter a shard leképezés neve. Ez csak akkor kötelező, ha több shard maps a ugyanazt a shard térkép manager kezeli. Választható. 


> [!IMPORTANT]
> Ez a módszer akkor használja, csak ha biztos abban, hogy a frissített leképezése tartománya üres. A fenti módszerek nem ellenőrzi adatokat át, a tartományhoz, így legjobb ellenőrzések szerepeljenek a kódot.
>

Ebben a példában a shard térkép szilánkok eltávolítja. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

A szilánkok térkép tükrözi a shard törlése előtt GSM shard helyét. A szilánkok törölve lett, mert van feltételezi, hogy ez szándékos volt, és a horizontális kulcs tartomány már nem használja. Ha nem, a visszaállítás egy korábbi időpontra pont a hajthat végre. a szilánkok helyreállítása egy korábbi-időpontban. (Ebben az esetben ellenőrizze az alábbi szakaszt észleléséhez shard inkonzisztenciákat.) Kíván helyreállítani, lásd: [időpontra történő visszaállítás](sql-database-recovery-using-backups.md).

Feltételezzük, hogy az adatbázisok törlése szándékos volt, mert a végső felügyeleti törlési művelete a shard térkép kezelő shard a bejegyzést törölni. Ez megakadályozza, hogy az alkalmazás véletlenül olyan tartománnyal, amely nem várt adatok írásakor.

## <a name="to-detect-mapping-differences"></a>Leképezési különbségek észlelése
A [DetectMappingDifferences metódus](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) kiválasztása és igazság forrásaként a shard maps (helyi vagy globális) egyikét adja vissza, és mindkét shard maps (GSM és LSM) leképezése rendezheti.

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* A *hely* határozza meg a kiszolgáló és adatbázis nevét. 
* A *shardMapName* paramétere a shard leképezés neve. Erre csak akkor van szükség, ha több shard maps a ugyanazt a shard térkép manager kezeli. Választható. 

## <a name="to-resolve-mapping-differences"></a>Leképezési különbségek megoldásához
A [ResolveMappingDifferences metódus](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) igazság forrásaként kiválaszt egy a shard maps (helyi vagy globális), és mindkét shard maps (GSM és LSM) leképezése összehangolja.

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* A *RecoveryToken* paraméter enumerálása a leképezéseket a globális szolgáltatásfigyelő szolgáltatás és az adott shard LSM közötti különbségeket. 
* A [MappingDifferenceResolution számbavételi](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) jelzi az a különbség a shard leképezések megoldásának módszere. 
* **MappingDifferenceResolution.KeepShardMapping** ajánlott, ha a LSM tartalmazza a pontos, ezért a a szilánkok leképezése használható. Ez általában az az eset, feladatátvétel esetén: a shard mostantól egy új kiszolgálón található. A szilánkok először el kell távolítani a GSM (a RecoveryManager.DetachShard módszer alkalmazásával), mert a leképezés már nem létezik, a GSM a. Ezért a LSM hozhatók létre újra a szilánkok leképezése.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>A szilánkok csatolása a ShardMap egy shard történt visszaállítása után
A [AttachShard metódus](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) az adott shard csatolja a shard leképezés. Ezután shard térkép inkonzisztenciákat észlelt, és frissíti a shard visszaállítását helyén shard kereséséhez. Feltételezzük, hogy az adatbázis is átnevezi az eredeti adatbázis név (előtt a shard vissza lett állítva), mivel az a pont a idő visszaállítás az alapértelmezett hozzáfűzi a Timestamp értékkel rendelkező új adatbázist. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* A *hely* paraméter a kiszolgáló nevét és adatbázis nevét, az éppen csatolt szilánkcímtárban. 
* A *shardMapName* paramétere a shard leképezés neve. Ez csak akkor kötelező, ha több shard maps a ugyanazt a shard térkép manager kezeli. Választható. 

Ebben a példában a shard hozzáadja a shard leképezés, amely egy korábbi pont óta nemrég helyreállt. A szilánkok (azaz a leképezést a LSM a szilánkok) vissza lett állítva, mert nem a shard bejegyzése a GSM potenciálisan következetes. A példakódot kívül a shard visszaállítani, és az eredeti nevére, az adatbázis neve. Vissza lett állítva, mivel feltételezzük leképezése a LSM a nem megbízható leképezése. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Egy földrajzi-feladatátvételt követően (visszaállítás) a a szilánkok shard helyek frissítése
A földrajzi feladatátvétel esetén a másodlagos adatbázis írási elérhető lesz, és válik az új elsődleges adatbázis. Lehet, hogy a kiszolgáló, és potenciálisan (konfigurációtól függően), az adatbázis neve eltér az eredeti elsődleges. Ezért a leképezési bejegyzéseket a GSM és LSM shard javítani kell. Hasonló módon ha az adatbázis helyreállítása egy másik nevet vagy helyet, vagy egy korábbi időpontbeli időben Ez okozhatja inkonzisztenciát a shard leképezésekben. A szilánkok térkép Manager nyitott kapcsolatot annak a megfelelő adatbázis terjesztési kezeli. Terjesztési a shard és az alkalmazások kérelem céljaként a horizontális kulcsnak az értéke az adatokon alapul. Egy földrajzi-feladatátvétel után ezek az információk frissíteni kell a pontos kiszolgáló neve, az adatbázisnév és a helyreállított adatbázis szilánkok leképezése. 

## <a name="best-practices"></a>Ajánlott eljárások
Földrajzi-feladatátvételi és helyreállítási műveletek általában kezeli a felhő rendszergazdájának az alkalmazás szándékosan okhoz egy Azure SQL-adatbázisok üzleti folytonosságot biztosító szolgáltatásokat, amelyek. Üzleti folytonossági tervezési folyamatok, eljárások és intézkedéseket, győződjön meg arról, hogy üzleti műveletek megszakítás nélkül továbbra is igényel. A módszer áll rendelkezésre, része a RecoveryManager osztálynak kell használni a munkafolyamat győződjön meg arról, a GSM és LSM mindig naprakészek alapján a helyreállítási műveletről. Nincsenek megfelelően biztosítva a GSM és LSM tükrözik a pontos információk lekérdezésének a feladatátadási esemény után öt alapvető lépéseit. Az alkalmazás kódjának a lépések végrehajtása integrálható a meglévő eszközöket és a munkafolyamat. 

1. A RecoveryManager lekérése a ShardMapManager. 
2. Válassza le a régi shard shard leképezés.
3. Az új shard csatolása a shard térkép, beleértve az új shard helyre.
4. A globális szolgáltatásfigyelő szolgáltatás és a LSM közötti leképezést inkonzisztenciát észlelését. 
5. Szüntesse meg a globális szolgáltatásfigyelő szolgáltatás és a LSM, megbízva abban a LSM közötti különbséget. 

Ebben a példában a következő lépéseket végzi el:

1. A szilánkok térkép, hogy tükrözze a feladatátvétel megtörténte előtt shard helyek szilánkok eltávolítja.
2. A szilánkok térkép az új shard helyek (a "Configuration.SecondaryServer" paramétere az új kiszolgáló nevét, de azonos adatbázisnévvel) tükröző szilánkok csatlakozik.
3. A helyreállítási jogkivonatok lekéri a globális szolgáltatásfigyelő szolgáltatás és az egyes shard LSM leképezési különbségei észlelésével. 
4. Oldja fel az inkonzisztenciák által az egyes shard LSM a leképezés a megbízó. 
   
   ```
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

