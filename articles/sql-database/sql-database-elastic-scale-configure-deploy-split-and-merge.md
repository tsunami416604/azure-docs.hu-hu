---
title: Felosztási-egyesítési szolgáltatás üzembe helyezése |} A Microsoft Docs
description: Használja a szétválasztás és egyesítés túl szilánkokra osztott adatbázisok közötti.
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
ms.date: 10/05/2018
ms.openlocfilehash: d44ce52e923c8f5b8ad8b873992163dca56ae0af
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875195"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Szilánkokra osztott adatbázisok közötti felosztási-egyesítési szolgáltatás üzembe helyezése

A felosztási-egyesítési eszközének szilánkokra osztott adatbázisok közötti adatáthelyezés teszi lehetővé. Lásd: [adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>A szétválasztás és egyesítés-csomagok letöltése
1. Töltse le a legújabb NuGet verziót [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Nyisson meg egy parancssort, és lépjen abba a könyvtárba, amelybe letöltötte a nuget.exe. A letöltés a PowerShell-parancsokat is tartalmaz.
3. Töltse le a legújabb felosztási-egyesítési csomagot ebbe a könyvtárba az az alábbi parancsot:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

A fájlok kerülnek egy nevű könyvtárat **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** ahol *x.x.xxx.x* tükrözi a verziószámot. Keresse meg a szétválasztás és egyesítés szolgáltatásfájlokat a **content\splitmerge\service** pillanatképmappa, és a szétválasztás és egyesítés PowerShell-parancsfájlok (és szükséges ügyfél DLL-ek) az a **content\splitmerge\powershell** pillanatképmappa.

## <a name="prerequisites"></a>Előfeltételek
1. A szétválasztás és egyesítés állapot adatbázisként használt Azure SQL Database-adatbázis létrehozása. Nyissa meg az [Azure Portal](https://portal.azure.com). Hozzon létre egy új **SQL Database**. Adja meg az adatbázis nevét, és hozzon létre egy új rendszergazdai felhasználóval és jelszóval. Mindenképpen jegyezze fel a nevet és jelszót későbbi használatra.
2. Győződjön meg arról, hogy az Azure SQL Database-kiszolgáló lehetővé teszi, hogy csatlakozzon hozzá Azure-szolgáltatások. A portálon az a **tűzfalbeállítások**, győződjön meg arról a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítása **a**. Kattintson a "Mentés" ikonra.
3. Hozzon létre egy Azure Storage-fiók diagnosztikai kimenetet.
4. Hozzon létre egy Azure-Felhőszolgáltatás a felosztási-egyesítési szolgáltatás.

## <a name="configure-your-split-merge-service"></a>A felosztási-egyesítési szolgáltatás konfigurálása
### <a name="split-merge-service-configuration"></a>Felosztási-egyesítési szolgáltatás konfigurációja
1. A mappa, amelybe letöltötte a szétválasztás és egyesítés szerelvényeket, hozzon létre egy példányát a **ServiceConfiguration.Template.cscfg** együtt szállított fájl **SplitMergeService.cspkg** , és nevezze át **ServiceConfiguration.cscfg**.
2. Nyissa meg **ServiceConfiguration.cscfg** egy szövegszerkesztőben, például a Visual Studiót, amely ellenőrzi az bemenetekből, például a tanúsítvány-ujjlenyomatok formátumát.
3. Hozzon létre egy új adatbázist, vagy válasszon egy meglévő adatbázist, az állapot database felosztási-egyesítési műveletek szolgál, és le a kapcsolati karakterláncot, hogy az adatbázis. 
   
   > [!IMPORTANT]
   > Jelenleg az állapot-adatbázis a Latin rendezést kell használnia (az SQL\_Latin1\_általános\_CP1\_CI\_AS). További információkért lásd: [Windows Collation Name (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Az Azure SQL DB a kapcsolati karakterlánc általában a következő formában:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Adja meg ezt a kapcsolati karakterláncot a cscfg fájlban is a **SplitMergeWeb** és **SplitMergeWorker** szerepkör részei a ElasticScaleMetadata beállítást.
5. Az a **SplitMergeWorker** szerepkör, adjon meg egy érvényes kapcsolati karakterláncot az Azure storage-bA a **WorkerRoleSynchronizationStorageAccountConnectionString** beállítás.

### <a name="configure-security"></a>Biztonság konfigurálása
A szolgáltatás biztonságának konfigurálása részletes utasításokért tekintse meg a [felosztási-egyesítési biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md).

Ebben az oktatóanyagban egy egyszerű tesztelési üzembe helyezés az alkalmazásában a konfigurációs lépéseket a legszükségesebb történik, a szolgáltatás használatának és futó lesz. Ezeket a lépéseket csak az egyik machine/fiók engedélyezése végrehajtása őket a szolgáltatással való kommunikációra.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Hozzon létre egy új könyvtárat, és ebből a könyvtárból, hajtsa végre a következő parancs használatával egy [for Visual Studio Developer-parancssorból](https://msdn.microsoft.com/library/ms229859.aspx) ablakban:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

A rendszer felkéri a jelszót a titkos kulcs védelme érdekében. Adjon meg egy erős jelszót, és erősítse meg. Ezután kéri a jelszót, amely után még egyszer használható. Kattintson a **Igen** , ezzel importálja azt a megbízható hitelesítésszolgáltató hitelesítésszolgáltatók legfelső szintű tárolójában.

### <a name="create-a-pfx-file"></a>A PFX-fájlok létrehozása
Hajtsa végre a következő parancsot a azonos ablak, ahol makecert végre lett hajtva; használja ugyanazt a jelszót, amellyel a tanúsítvány létrehozásához:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importálja az ügyféltanúsítványt a személyes tárolóba
1. A Windows Intézőben kattintson duplán a **MyCert.pfx**.
2. Az a **Tanúsítványimportáló varázsló** kiválasztása **aktuális felhasználó** kattintson **tovább**.
3. Erősítse meg a fájl elérési útját, és kattintson a **tovább**.
4. Adja meg a jelszót, és távozzon **közé tartozik az összes kiterjesztett tulajdonságok** be van jelölve, és kattintson a **tovább**.
5. Hagyja **automatikusan kiválasztja a tanúsítványtároló [...]**  be van jelölve, és kattintson a **tovább**.
6. Kattintson a **Befejezés** és **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>A PFX-fájl feltöltése a felhőszolgáltatáshoz
1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Válassza ki **Cloud Services**.
3. Válassza ki a felhőszolgáltatást, a felosztás/egyesítés szolgáltatás a fent létrehozott.
4. Kattintson a **tanúsítványok** felső menüjében.
5. Kattintson a **feltöltése** alsó sávján lévő.
6. Válassza ki a PFX-fájlt, és adja meg ugyanazt a jelszót a fenti.
7. Ha kész, másolja a tanúsítvány ujjlenyomatát az új bejegyzést a listában.

### <a name="update-the-service-configuration-file"></a>A szolgáltatás konfigurációs fájljának frissítése
Illessze be a fenti bemásolja ezeket a beállításokat az ujjlenyomat-érték attribútum tanúsítvány ujjlenyomatát.
A feldolgozói szerepkör:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

A webes szerepkör:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Kérjük vegye figyelembe, hogy éles környezetben üzemelő példányok külön tanúsítványokat kell használni a hitelesítésszolgáltató, a titkosítási tanúsítvány és az ügyféltanúsítványok. Ez a részletes utasításokért lásd: [biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>A szolgáltatás üzembe helyezése
1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Válassza ki a felhőalapú szolgáltatás, amelyet korábban hozott létre.
3. Kattintson az **Áttekintés** elemre.
4. Válassza ki az átmeneti környezetben, majd kattintson a **feltöltése**.
5. A párbeszédpanelen adja meg egy üzemelő példány címkéje. A "Csomag" és "Konfiguráció" kattintson "A helyi", és válassza a **SplitMergeService.cspkg** fájl- és a cscfg-fájl, amely a korábban beállított.
6. Ellenőrizze, hogy a feliratú jelölőnégyzet **telepítse, még akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** be van jelölve.
7. Kattintson a jobb alsó az üzembe helyezés megkezdéséhez az osztásjelek gombra. Várható, hogy néhány percet igénybe vehet.


## <a name="troubleshoot-the-deployment"></a>Az üzembe helyezés hibaelhárítása
A webes szerepkör nem lehet online állapotba, ha azt valószínűleg probléma a biztonsági konfigurációval. Ellenőrizze, hogy az SSL a fentieknek megfelelően van-e konfigurálva.

Ha nem sikerül dolgozó munkaköre online állapotba, de a webes szerepkör sikeres lesz, nagy valószínűséggel kapcsolódni a korábban létrehozott állapot-adatbázis.

* Győződjön meg arról, hogy a kapcsolati karakterláncot a cscfg a pontos.
* Ellenőrizze, hogy a kiszolgáló és az adatbázis létezik, és a felhasználói azonosító és jelszó helyességét.
* Az Azure SQL Database a kapcsolati karakterláncot a következő formában kell lennie:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Győződjön meg arról, hogy a kiszolgáló neve nem kezdődhet **https://**.
* Győződjön meg arról, hogy az Azure SQL Database-kiszolgáló lehetővé teszi, hogy csatlakozzon hozzá Azure-szolgáltatások. Ehhez nyissa meg az adatbázis a portálon, és ellenőrizze, hogy a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítása ** a x.

## <a name="test-the-service-deployment"></a>A szolgáltatás központi telepítés tesztelése
### <a name="connect-with-a-web-browser"></a>Kapcsolódás egy webes böngésző
A webes végpont a felosztási-egyesítési szolgáltatás határozza meg. Megtalálhatja a portálon lépjen a **áttekintése** a cloud Services és a alapján keresi **webhely URL-címe** jobb oldalán. Cserélje le **http://** a **https://** óta az alapértelmezett biztonsági beállítások letiltása a HTTP-végpontot. A lap betöltéséhez a böngészőbe az URL-címhez.

### <a name="test-with-powershell-scripts"></a>A PowerShell-parancsfájlok tesztelése
Az üzembe helyezés és a környezet tesztelhető legyen a mellékelt PowerShell-parancsprogramok futtatásával.

A parancsfájl-fájlt is tartalmaz a következők:

1. **SetupSampleSplitMergeEnvironment.ps1** -állítja be a teszt adatrétegbeli szétválasztás és egyesítés (lásd az alábbi táblázatban részletes leírása)
2. **ExecuteSampleSplitMerge.ps1** -végrehajtja a teszteléshez használt teszt műveleteit adatok réteg (lásd az alábbi táblázatban részletes leírása)
3. **GetMappings.ps1** – legfelső szintű mintaparancsfájl kiírja a szilánkleképezés-leképezések aktuális állapotát.
4. **ShardManagement.psm1** -segítő parancsfájl, amely a ShardManagement API
5. **SqlDatabaseHelpers.psm1** -segítő parancsfájl létrehozásához és az SQL Database adatbázisok felügyelete
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájlra</th>
       <th>Lépések</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    A szegmenstérkép-kezelő adatbázis létrehozása</td>
     </tr>
     <tr>
       <td>2.    2 bontott adatbázisokat hoz létre.
     </tr>
     <tr>
       <td>3.    Horizontálispartíció-térkép, ezeket az adatbázisokat (bármely meglévő szilánkleképezések ezeket az adatbázisokat a törli) az hoz létre. </td>
     </tr>
     <tr>
       <td>4.    Mindkét lévő kis minta táblázatot hoz létre, és tölti fel a szegmensek egyik táblájában.</td>
     </tr>
     <tr>
       <td>5.    Deklarálja a SchemaInfo szilánkos táblához.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájlra</th>
       <th>Lépések</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Split kérelmet küld a felosztási-egyesítési szolgáltatás webes előtérrendszer, amely bontja az első szegmens a második szegmensre fél adatait.</td>
     </tr>
     <tr>
       <td>2.    Kérdezze le a webes előtérrendszer a felosztás kérés állapotáról, és megvárja, amíg befejeződik a kérelmet.</td>
     </tr>
     <tr>
       <td>3.    Egy egyesítéses kérést küld a felosztási-egyesítési szolgáltatás webes előtérrendszer, amely adatokat helyez át a, a második szegmens vissza az első szegmensre.</td>
     </tr>
     <tr>
       <td>4.    Kérdezze le a webes előtérrendszer egyesítési kérelem állapotát, és megvárja, amíg befejeződik a kérelmet.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>A telepítés ellenőrzése a PowerShell használatával
1. Nyisson meg egy új PowerShell-ablakot, és lépjen abba a könyvtárba, amelybe letöltötte a szétválasztás és egyesítés csomagot, és navigáljon a "powershell" könyvtárba.
2. Hozzon létre egy Azure SQL database-kiszolgálót (vagy válassza ki a meglévő kiszolgáló), a szilánkleképezés-kezelővel és a szegmensek jön létre.
   
   > [!NOTE]
   > A SetupSampleSplitMergeEnvironment.ps1 parancsfájl hoz létre, a parancsfájl egyszerű tartani alapértelmezés szerint ezek az adatbázisok ugyanazon a kiszolgálón. Ez a korlátozás nem kötelező a felosztási-egyesítési szolgáltatás magát.
   >
   
   Az adatok áthelyezése, és frissíti a szegmenstérkép felosztási-egyesítési szolgáltatás olvasási/írási hozzáférést az adatbázisok SQL-hitelesítési bejelentkezési lesz szükség. A felosztási-egyesítési szolgáltatás fut a felhőben, mert azt jelenleg nem támogatja beépített hitelesítés.
   
   Győződjön meg arról, hogy engedélyezze a hozzáférést ezen parancsprogramokat futtató gép IP-címét az Azure SQL-kiszolgáló van konfigurálva. Ez a beállítás az Azure SQL-kiszolgáló alatt található / configuration / IP-címek engedélyezett.
3. Hajtsa végre a SetupSampleSplitMergeEnvironment.ps1 parancsfájlt a minta környezetet hozhat létre.
   
   Ez a szkript futtatása fogja törölni, bármely meglévő szegmens térkép felügyeleti adatai a szegmenstérkép-kezelő adatbázis és a szegmensek struktúrák. Kattintva futtassa újra a parancsfájlt, ha újból inicializálja a szegmenstérkép- vagy szilánkkészletté oszthat fel szeretné hasznos lehet.
   
   Minta parancssor:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Hajtsa végre a Getmappings.ps1 parancsfájlt a jelenleg létező leképezések megtekintéséhez a minta-környezetben.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Hajtsa végre a ExecuteSampleSplitMerge.ps1 parancsfájlt a felosztási művelet (fele az adatok áthelyezése a az első szegmens a második szegmensre), majd egy (áthelyezni az adatokat vissza az első szegmens) merge művelet végrehajtásához. Ha SSL konfigurálva, és le van tiltva a http-végpont bal, győződjön meg arról, hogy inkább a https:// végpontot.
   
   Minta parancssor:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Ha megjelenik a hibaüzenetet, azt valószínűleg probléma a webes végpont tanúsítvánnyal. Próbáljon meg csatlakozni a webes végpontra a a kedvenc webböngészőjét, és ellenőrizze, hogy van-e tanúsítvány.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Ha sikeres, a kimeneti hasonlóan kell kinéznie az alábbi:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Egyéb adattípusok kísérletezhet! Ezek a szkriptek mindegyikét igénybe választható - ShardKeyType paraméter, amely lehetővé teszi, hogy adja meg a kulcs típusa. Az alapértelmezett érték az Int32, de Int64, Guid vagy bináris is megadhat.

## <a name="create-requests"></a>Létrehozási kérelmek
A szolgáltatás használható, a webes felhasználói felületen vagy importálásáról és használatáról a SplitMerge.psm1 PowerShell-modult, amely a kéréseket a webes szerepkör keresztül.

A szolgáltatás áthelyezheti az adatokat a horizontálisan particionált táblák és a referencia táblák. Szilánkos táblához egy horizontális skálázási kulcsoszlopa, és minden egyes szegmens a különböző sor-adatokat tartalmaz. A referenciatábla nem áll horizontálisan skálázott, így a minden szegmens ugyanazt sor adatokat tartalmazza. Referencia táblák hasznosak az adatokat, amelyeket nem változik gyakran JOIN lekérdezéseket horizontálisan skálázott tábláival szolgál.

A felosztási-egyesítési művelet elvégzéséhez be kell állítania a horizontálisan particionált táblák és áthelyezett kívánt referencia táblák. Mindez a **SchemaInfo** API-t. Az API-t a **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** névtér.

1. Minden szilánkos táblához, hozzon létre egy **ShardedTableInfo** objektum leíró a tábla szülő séma neve (nem kötelező, alapértelmezett érték: "dbo"), a tábla nevét, és az oszlop neve az adott tábla, amely tartalmazza a horizontális skálázási kulcs.
2. Minden hivatkozás táblához, hozzon létre egy **ReferenceTableInfo** a tábla szülő sémanév leíró objektum (nem kötelező, alapértelmezett érték: "dbo") és a tábla neve.
3. A fenti TableInfo objektumok hozzáadása egy új **SchemaInfo** objektum.
4. Hivatkozás beszerzése egy **ShardMapManager** objektum és a hívási **GetSchemaInfoCollection**.
5. Adja hozzá a **SchemaInfo** , a **SchemaInfoCollection**, a szegmens leképezésnév megadása.

Példa erre a SetupSampleSplitMergeEnvironment.ps1 szkriptben látható.

A felosztási-egyesítési szolgáltatás nem hoz létre a céladatbázis (vagy bármely táblák az adatbázisban séma) az Ön számára. Ezek előre létrehozott előtt kell egy kérelmet küld a szolgáltatásnak.

## <a name="troubleshooting"></a>Hibaelhárítás
Láthatja az alábbi jelenik meg, amikor a minta powershell-parancsfájlok futtatásakor:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Ez a hiba, az azt jelenti, hogy az SSL-tanúsítvány nincs megfelelően konfigurálva. Kövesse a szakaszban található útmutatásokat: Kapcsolódás egy webes böngésző".

Kérelmek elküldése nem lehetséges, hogy ezt látja:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Ebben az esetben ellenőrizze, a konfigurációs fájlt, különösen a beállítását **WorkerRoleSynchronizationStorageAccountConnectionString**. Ez a hiba általában azt jelzi, hogy a feldolgozói szerepkör nem tudta sikeresen inicializálni az első használatkor a metaadatokat tároló adatbázis. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

