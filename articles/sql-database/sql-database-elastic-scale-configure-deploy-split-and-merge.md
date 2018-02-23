---
title: "Vegyes egyesítéses szolgáltatás telepítése |} Microsoft Docs"
description: "A felosztott egyesítéses túl ezen adatok áthelyezése a szilánkos adatbázisok között."
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: 9a993c0f-7052-46cd-aa59-073bea8d535a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 4505aebc8919a5d7b8f3debe0db9f49b465176bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-a-split-merge-service"></a>Felosztási-egyesítési szolgáltatás üzembe helyezése
A felosztott egyesítéses eszköz lehetővé teszi az adatok áthelyezése a szilánkos adatbázisok között. Lásd: [adatok kiterjesztett felhő adatbázisok közötti áthelyezése](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>A felosztott egyesítéses csomagok letöltése
1. Töltse le a legfrissebb NuGet [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Nyisson meg egy parancssort, és keresse meg a könyvtárat, amelybe letöltötte nuget.exe. A letöltés PowerShell-parancsokat tartalmazza.
3. Töltse le a legfrissebb vegyes egyesítéses csomagot be az aktuális könyvtár és az alábbi parancsot:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

A fájlok kerülnek nevű **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** ahol *x.x.xxx.x* tükrözi a verziószámot. A felosztott egyesítéses szolgáltatásfájlokat található a **content\splitmerge\service** alkönyvtára, és a felosztott egyesítéses PowerShell-parancsfájlok (és szükséges ügyfél DLL-fájlok) a **content\splitmerge\powershell** alkönyvtárát.

## <a name="prerequisites"></a>Előfeltételek
1. A felosztott egyesítéses állapot adatbázisként használandó Azure SQL Database-adatbázis létrehozása. Nyissa meg az [Azure Portal](https://portal.azure.com). Hozzon létre egy új **SQL-adatbázis**. Nevezze el az adatbázist, és hozzon létre egy új rendszergazda és a jelszót. Ne felejtse el a név és jelszó későbbi használatra.
2. Győződjön meg arról, hogy az Azure SQL Database-kiszolgáló lehetővé teszi, hogy a csatlakozáshoz Azure-szolgáltatások. A portálon a a **tűzfalbeállítások**, győződjön meg arról a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítása **a**. Kattintson a "Mentés" ikonra.
   
   ![Engedélyezett szolgáltatások][1]
3. Azure-tárfiók létrehozása, amely jelzi a diagnosztikai kimenetet. Ugrás az Azure-portálon. A bal oldali sávon kattintson **hozzon létre egy erőforrást**, kattintson a **adatok + tárolás**, majd **tárolási**.
4. Hozzon létre egy Azure felhőalapú szolgáltatás, amely a vegyes egyesítéses szolgáltatás fogja tartalmazni.  Ugrás az Azure-portálon. A bal oldali sávon kattintson **hozzon létre egy erőforrást**, majd **számítási**, **Felhőszolgáltatás**, és **létrehozása**. 

## <a name="configure-your-split-merge-service"></a>A felosztott egyesítéses szolgáltatás konfigurálása
### <a name="split-merge-service-configuration"></a>Vegyes egyesítéses szolgáltatás konfigurációja
1. A mappában, amelybe letöltötte a felosztás egyesítéses szerelvényeket, készítsen másolatot a **ServiceConfiguration.Template.cscfg** fájl mellett szállított **SplitMergeService.cspkg** és adjon neki **ServiceConfiguration.cscfg**.
2. Nyissa meg **ServiceConfiguration.cscfg** egy szövegszerkesztőben, például a Visual Studio, amely a bemeneti adatok, például a tanúsítvány-ujjlenyomatok formátuma.
3. Hozzon létre egy új adatbázist, vagy válasszon egy meglévő adatbázist a felosztott-Merge műveletek állapotának adatbázisként szolgál, és az adatbázis a kapcsolati karakterlánc lekéréséhez. 
   
   > [!IMPORTANT]
   > Ilyenkor az állapot-adatbázis a Latin rendezést kell használnia (SQL\_Latin1\_általános\_CP1\_CI\_AS). További információkért lásd: [Windows Collation Name (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Az Azure SQL Database a kapcsolati karakterlánc általában a következő formátumban:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Adja meg a kapcsolati karakterlánc mindkét cscfg-fájl a **SplitMergeWeb** és **SplitMergeWorker** szerepkör részei a ElasticScaleMetadata beállítást.
5. Az a **SplitMergeWorker** szerepkör, adjon meg egy érvényes kapcsolati karakterláncot az Azure Storage a **WorkerRoleSynchronizationStorageAccountConnectionString** beállítást.

### <a name="configure-security"></a>Biztonság konfigurálása
A szolgáltatás biztonsági konfigurálása részletes utasításokért tekintse meg a [vegyes egyesítéses biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md).

Ebben az oktatóanyagban egy egyszerű próbatelepítés céljából a konfigurációs lépések minimális számú elvégezni a szolgáltatás eléréséhez és futó lesz. Ezeket a lépéseket csak az egy gépen/fiók engedélyezése végrehajtása a szolgáltatással való kommunikációra őket.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Hozzon létre egy új könyvtárat, és a következő könyvtár a következő parancs használatával végrehajtani egy [Visual Studio Developer-parancssorból](http://msdn.microsoft.com/library/ms229859.aspx) ablakban:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

A titkos kulcs védelme jelszó kéri. Adjon meg egy erős jelszót, és erősítse meg. Majd kéri a jelszót még egyszer ezt követően használható. Kattintson a **Igen** ezzel importálja azt a megbízható hitelesítésszolgáltatók hitelesítésszolgáltatók legfelső szintű hitelesítésszolgáltatók tárolójába.

### <a name="create-a-pfx-file"></a>A PFX-fájl létrehozása
Hajtsa végre a következő parancsot a azonos ablakban, ahol makecert végre lett hajtva; a tanúsítvány létrehozásához használt jelszavának használata:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importálja az ügyféltanúsítványt a személyes tárolóba
1. A Windows Intézőt, kattintson duplán a **MyCert.pfx**.
2. Az a **Tanúsítványimportáló varázsló** válasszon **aktuális felhasználó** kattintson **következő**.
3. Ellenőrizze a fájl elérési útját, majd kattintson **következő**.
4. Írja be a jelszót, hagyja **tartalmazza az összes kiterjesztett tulajdonságok** be van jelölve, és kattintson a **következő**.
5. Hagyja **automatikusan a tanúsítvány választása [...]**  be van jelölve, és kattintson a **következő**.
6. Kattintson a **Befejezés** és **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>A PFX-fájl feltöltése a felhőalapú szolgáltatáshoz
1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Válassza ki **a felhőalapú szolgáltatások**.
3. Válassza ki a felhőszolgáltatást, a felosztás/egyesítés szolgáltatás számára létrehozott fent.
4. Kattintson a **tanúsítványok** a felső menüben.
5. Kattintson a **feltöltése** az alsó sáv a.
6. Válassza ki a PFX-fájlt, és adja meg ugyanazt a jelszót a fenti.
7. Ezt követően másolja a tanúsítvány ujjlenyomata az új bejegyzést a listában.

### <a name="update-the-service-configuration-file"></a>A szolgáltatás konfigurációs fájljának frissítése
Illessze be a tanúsítvány ujjlenyomata fent átkerül az ujjlenyomat-érték attribútum ezeket a beállításokat.
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

Vegye figyelembe, hogy az éles központi telepítések tanúsítványok önálló adja a hitelesítésszolgáltató, a titkosítási tanúsítvány és az ügyféltanúsítványok történjen. Ez a részletes utasításokért lásd: [biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>A szolgáltatás üzembe helyezése
1. Nyissa meg az [Azure Portal](https://manage.windowsazure.com).
2. Kattintson a **Felhőszolgáltatások** a bal oldali lapon, és válassza ki a korábban létrehozott felhőszolgáltatást.
3. Kattintson a **irányítópult**.
4. Válassza ki az átmeneti, majd kattintson a **töltse fel az új átmeneti üzembe helyezésének**.
   
   ![Fájlok másolása folyamatban][3]
5. A párbeszédpanelen adja meg egy üzemelő példány címkéje. "Csomag", mind a "Konfiguráció" részen kattintson "A helyi", és válassza ki a **SplitMergeService.cspkg** fájl- és a korábban megadott cscfg-fájl.
6. Győződjön meg arról, hogy a feliratú jelölőnégyzet **üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** be van jelölve.
7. Kattintson a jobb alsó a telepítésének megkezdéséhez a osztásjelek gombra. Tehát az néhány percet igénybe vehet.

   ![Feltöltés][4]

## <a name="troubleshoot-the-deployment"></a>A telepítés hibáinak
Ha a webes szerepkör nem hozható online állapotba, azt valószínűleg probléma a biztonsági beállítások. Ellenőrizze, hogy az SSL a fent leírt módon van-e konfigurálva.

A feldolgozói szerepkör nem tudja ismét online elérhető, de a webes szerepkör sikeres, ha azt valószínűleg probléma a korábban létrehozott állapot-adatbázishoz szeretne csatlakozni.

* Győződjön meg arról, hogy a kapcsolati karakterláncot a szolgáltatáskonfigurációs séma a pontos.
* Ellenőrizze, hogy a kiszolgáló és az adatbázis létezik, és a felhasználóazonosító és jelszó helyességét.
* Az Azure SQL Database a kapcsolati karakterlánc a következő formátumban kell lennie:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Győződjön meg arról, hogy a kiszolgáló neve nem kezdődhet **https://**.
* Győződjön meg arról, hogy az Azure SQL Database-kiszolgáló lehetővé teszi, hogy a csatlakozáshoz Azure-szolgáltatások. Ehhez nyissa meg a https://manage.windowsazure.com, a bal oldalon kattintson az "SQL-adatbázisok", a lap tetején kattintson a "Kiszolgáló", és válassza ki a kiszolgálót. Kattintson a **konfigurálása** tetején, és győződjön meg arról, hogy a **Azure Services** beállítás "Yes" értékre van állítva. (Az Előfeltételek című Ez a cikk tetején).

## <a name="test-the-service-deployment"></a>A szolgáltatás központi telepítés tesztelése
### <a name="connect-with-a-web-browser"></a>Egy webböngészővel rendelkező csatlakozás
Határozza meg, hogy a vegyes egyesítéses szolgáltatás webes végpontja. Ez az található a klasszikus Azure portálra a a **irányítópult** a felhőszolgáltatás és a keresése **webhely URL-címe** jobb oldalán. Cserélje le **http://** rendelkező **https://** óta az alapértelmezett biztonsági beállításairól, tiltsa le a HTTP-végpont. A lap betöltése a böngészőbe az URL-címhez.

### <a name="test-with-powershell-scripts"></a>A PowerShell-parancsfájlok tesztelése
A központi telepítés és a környezet tesztelhető a mellékelt PowerShell-parancsfájlok futtatásával.

A parancsfájlok tartalmazza a következők:

1. **SetupSampleSplitMergeEnvironment.ps1** -állítja be a teszt adatrétegbeli vegyes/egyesítés (lásd az alábbi táblázatban részletes leírása)
2. **ExecuteSampleSplitMerge.ps1** -végrehajtja a teszteléshez használt teszt műveleteit adatok réteg (lásd az alábbi táblázatban részletes leírása)
3. **GetMappings.ps1** – legfelső szintű mintaparancsfájl a shard leképezések aktuális állapotát megjeleníti.
4. **ShardManagement.psm1** -segítő parancsfájl tördelve a ShardManagement API
5. **SqlDatabaseHelpers.psm1** -segítő parancsfájl létrehozásához és kezeléséhez az SQL-adatbázisok
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájl</th>
       <th>Lépések</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Létrehoz egy shard térkép manager adatbázis</td>
     </tr>
     <tr>
       <td>2.    2 shard-adatbázisokat hoz létre.
     </tr>
     <tr>
       <td>3.    A szilánkok leképezése ezeket az adatbázisokat (törli ezeket az adatbázisokat a maps bármely létező szilánkok) hoz. </td>
     </tr>
     <tr>
       <td>4.    Táblát hoz létre egy kis minta mindkét a szilánkok, tölti fel a tábla a szilánkok egyikében.</td>
     </tr>
     <tr>
       <td>5.    A szilánkos táblához SchemaInfo deklarálja.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájl</th>
       <th>Lépések</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Felosztja a fele az adatokat az első shard a második shard vegyes egyesítéses szolgáltatás webes előtér vegyes kérést küld.</td>
     </tr>
     <tr>
       <td>2.    Kérdezze le a webes előtér vegyes kérés állapotát, és megvárja, amíg a kérelem befejeződött.</td>
     </tr>
     <tr>
       <td>3.    Egyesítési kérést küld az áll át az adatokat a második shard vissza az első shard vegyes egyesítéses szolgáltatás webes előtér.</td>
     </tr>
     <tr>
       <td>4.    Kérdezze le a webes előtér az egyesítési kérelem állapot, és megvárja, amíg a kérelem befejeződött.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>A telepítés ellenőrzése a PowerShell használatával
1. Új PowerShell ablakban keresse meg a könyvtárat, amelybe letöltötte a felosztás egyesítéses csomag és keresse meg a "powershell" könyvtárba.
2. Hozzon létre egy Azure SQL adatbázis-kiszolgáló (vagy válasszon egy meglévő kiszolgálót) Ha a shard térkép manager és a szilánkok jön létre.
   
   > [!NOTE]
   > A SetupSampleSplitMergeEnvironment.ps1 parancsfájl hoz létre, tartsa a parancsfájl egyszerű alapértelmezés szerint ezek az adatbázisok ugyanazon a kiszolgálón. Ez a korlátozás nem osztott egyesítéses szolgáltatás magát.
   >
   
   A felosztott egyesítéses szolgáltatás helyezi át az adatokat, és frissíti a shard leképezés egy SQL-hitelesítési bejelentkezési olvasási/írási hozzáférést a adatbázisok lesz szükség. A felosztott egyesítéses szolgáltatás fut a felhőben, mert azt jelenleg nem támogatja integrált hitelesítést.
   
   Győződjön meg arról, hogy engedélyezzék az IP-cím a gép, ezek a parancsfájlok futtatása az Azure SQL-kiszolgáló van konfigurálva. Ezt a beállítást, az Azure SQL-kiszolgáló található / configuration / engedélyezett IP-címeket.
3. Hajtsa végre a SetupSampleSplitMergeEnvironment.ps1 parancsfájlt a minta környezetet hozhat létre.
   
   A parancsfájl futtatása lesz kitöröl shard térkép meglévő felügyeleti adatokat a shard manager adatbázist és a szilánkok struktúrákat. Hasznos, ha a shard térkép vagy szilánkok inicializálja újra kívánja a parancsfájl lehet.
   
   Minta parancssor:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Hajtsa végre a Getmappings.ps1 parancsfájlt a jelenleg létező hozzárendelések megtekintése a minta-környezetben.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Hajtsa végre a ExecuteSampleSplitMerge.ps1 parancsfájlt egy vegyes művelet (továbblép fele az adatokat az első shard a második szilánkok) és egy (az adatok áthelyezése vissza az első shard alakzatot) egyesítési művelet végrehajtásához. Ha SSL konfigurálva, és a bal oldali a http-végpont le van tiltva, győződjön meg arról, hogy inkább a https:// végpont.
   
   Minta parancssor:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Ha megjelenik a alatt hiba, akkor nagy valószínűséggel a webalkalmazás-végpontot tanúsítványával kapcsolatos problémára. Próbáljon meg csatlakozni a kedvenc webböngésző webes végpontjának, és ellenőrizze, hogy a tanúsítvány hiba.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Ha sikeres volt, a kimeneti hasonlóan kell kinéznie az alábbi:
   
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
6. Más adattípusok kísérletezhet! Választható - ShardKeyType paraméter, amely lehetővé teszi a írja be mindegyik parancsfájl érvénybe. Az alapértelmezett érték Int32, de azt is megadhatja Int64, Guid vagy bináris.

## <a name="create-requests"></a>Létrehozási kérelmek
A szolgáltatás használható a webes felhasználói felületen vagy importálásáról és használatáról a SplitMerge.psm1 PowerShell modult, amely a kéréseket a webes szerepkör keresztül.

A szolgáltatás mozgathatja az adatokat a szilánkos táblák és a hivatkozási táblák. A szilánkos táblához egy horizontális skálázási kulcsoszlopa, és különböző soradatok rendelkezzen minden szilánkcímtárban. A referenciatábla nincs szilánkos, a minden shard azonos sor adatokat tartalmazza. Hivatkozási táblák nem változik gyakran, és csatlakozzon a lekérdezésekben szilánkos táblák használt adatok hasznosak.

A felosztott-egyesítési művelet végrehajtásához a szilánkos táblákat és a referencia-táblázatot, amely került át szeretné kell deklarálni. Ez a érhető el a **SchemaInfo** API. Az API-t a **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** névtér.

1. Minden szilánkos táblához, hozzon létre egy **ShardedTableInfo** a tábla szülő sémanév leíró objektum (nem kötelező, az alapértelmezett érték a "dbo"), a táblázat nevét, és az oszlop nevét a horizontális kulcsot tartalmazó tábla.
2. Minden egyes összefoglaló táblázatot is létrehozhat egy **ReferenceTableInfo** a tábla szülő sémanév leíró objektum (nem kötelező, az alapértelmezett érték a "dbo") és a tábla neve.
3. A fenti TableInfo objektumokat hozzáadni egy új **SchemaInfo** objektum.
4. A hivatkozás egy **ShardMapManager** objektum és hívás **GetSchemaInfoCollection**.
5. Adja hozzá a **SchemaInfo** számára a **SchemaInfoCollection**, a shard leképezésnév biztosítása.

Példa erre a SetupSampleSplitMergeEnvironment.ps1 parancsfájl látható.

A felosztott egyesítéses szolgáltatás nem a céladatbázis (vagy bármely táblák az adatbázisban séma) hozza létre. Fel kell előre létrehozott egy kérést küld a szolgáltatás előtt.

## <a name="troubleshooting"></a>Hibaelhárítás
Megjelenik a alatt jelenik meg, amikor a minta powershell-parancsfájlok futtatásakor:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Ez a hiba azt jelenti, hogy az SSL-tanúsítvány nincs megfelelően konfigurálva. Kérjük, kövesse a szakaszban található útmutatásokat "Böngészővel csatlakozás".

Nem küldenek kéréseket jelenhet meg ezt:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Ebben az esetben ellenőrizze, a konfigurációs fájlban, különösen a beállítás a **WorkerRoleSynchronizationStorageAccountConnectionString**. Ez a hiba általában azt jelzi, hogy a feldolgozói szerepkör sikeresen inicializálása sikertelen első használatkor a metaadatokat tároló adatbázis. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

