---
title: Felosztási-egyesítési szolgáltatás üzembe helyezése
description: Használja a felosztásos egyesítése is az adatok áthelyezése szilánkos adatbázisok között.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: b6f61de23ab4b637cfb5b8ee365ddea9764bf515
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810204"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Felosztásos egyesítési szolgáltatás üzembe helyezése az adatok szilánkos adatbázisok közötti áthelyezéséhez

Az osztott egyesítési eszköz lehetővé teszi az adatok szilánkos adatbázisok közötti áthelyezését. Lásd: [Adatok áthelyezése kicsinyített felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>A Split-Merge csomagok letöltése

1. Töltse le a legújabb NuGet verziót a [NuGet-ből.](https://docs.nuget.org/docs/start-here/installing-nuget)

1. Nyisson meg egy parancssort, és keresse meg azt a könyvtárat, ahol letöltötte a nuget.exe programot. A letöltés PowerShell-parancsokat tartalmaz.

1. Töltse le a legújabb Split-Merge csomagot az aktuális könyvtárba az alábbi paranccsal:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

A fájlok egy **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** könyvtár ba kód, ahol az *x.x.xxx.x* a verziószámot tükrözi. Keresse meg a service\split-merge service fájlokat a **content\splitmerge\service** alkönyvtárban, valamint a PowerShell-parancsfájlok split-merge (és a szükséges ügyféldlls) a **content\splitmerge\powershell** alkönyvtárban.

## <a name="prerequisites"></a>Előfeltételek

1. Hozzon létre egy Azure SQL DB-adatbázist, amely felosztás-egyesítési állapot-adatbázisként lesz használva. Nyissa meg az [Azure Portalt.](https://portal.azure.com) Hozzon létre egy új **SQL-adatbázist**. Adjon nevet az adatbázisnak, és hozzon létre egy új rendszergazdát és jelszót. Ügyeljen arra, hogy rögzítse a nevet és a jelszót későbbi használatra.

1. Győződjön meg arról, hogy az Azure SQL DB-kiszolgáló lehetővé teszi az Azure Services számára, hogy csatlakozzon hozzá. A **portálon**a tűzfal beállításai ban győződjön meg arról, hogy az **Azure Services elérésének engedélyezése** beállítás **be**van kapcsolva. Kattintson a "mentés" ikonra.

1. Hozzon létre egy Azure Storage-fiókot a diagnosztikai kimenethez.

1. Hozzon létre egy Azure-felhőszolgáltatást a split-merge szolgáltatáshoz.

## <a name="configure-your-split-merge-service"></a>A split-merge szolgáltatás konfigurálása

### <a name="split-merge-service-configuration"></a>Split-Merge szolgáltatás konfigurációja

1. Abban a mappában, amelybe a Split-Merge szerelvényeket töltötte le, hozzon létre egy másolatot a *ServiceConfiguration.Template.cscfg* fájlról, amely a *SplitMergeService.cspkg* fájl mellett került ki, és nevezze át *ServiceConfiguration.cscfg*.

1. Nyissa meg *a ServiceConfiguration.cscfg fájlt* egy szövegszerkesztőben, például a Visual Studio-ban, amely olyan bemeneteket érvényesít, mint például a tanúsítvány ujjlenyomatainak formátuma.

1. Hozzon létre egy új adatbázist, vagy válasszon egy meglévő adatbázist, amely az egyesítési műveletek állapot-adatbázisaként szolgál, és lekéri az adatbázis kapcsolati karakterláncát.

   > [!IMPORTANT]
   > Ebben az időben az állapot-adatbázisnak a\_latin\_rendezést (SQL Latin1 General\_CP1\_CI\_AS) kell használnia. További információt a [Windows rendezési neve (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx)című témakörben talál.

   Az Azure SQL DB esetén a kapcsolati karakterlánc általában a következő formában érhető el:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Adja meg ezt a kapcsolati karakterláncot a *.cscfg* fájlban az ElasticScaleMetadata beállítás **SplitMergeWeb** és **SplitMergeWorker** szerepkörszakaszaiban.

1. A **SplitMergeWorker** szerepkörhöz adjon meg egy érvényes kapcsolati karakterláncot az Azure storage-hoz a **WorkerRoleSynchronizationStorageAccountConnectionString** beállításhoz.

### <a name="configure-security"></a>Biztonság konfigurálása

A szolgáltatás biztonságának konfigurálásához részletes útmutatást a [Biztonsági modul felosztása biztonsági konfiguráció című](sql-database-elastic-scale-split-merge-security-configuration.md)dokumentumtartalmaz.

Az oktatóanyag egyszerű teszttelepítésének céljából a szolgáltatás üzembe helyezéséhez minimális konfigurációs lépéseket kell végrehajtani. Ezek a lépések csak a szolgáltatást végrehajtó egy számítógép/fiók/fiók számára teszik lehetővé a kommunikációt.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Hozzon létre egy új könyvtárat, és ebből a könyvtárból hajtsa végre a következő parancsot a [Visual Studio fejlesztői parancssora](https://msdn.microsoft.com/library/ms229859.aspx) ablakban:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

A rendszer jelszót kér a személyes kulcs védelméhez. Adjon meg egy erős jelszót, és erősítse meg. Ezután a rendszer kéri, hogy ezután még egyszer használja a jelszót. A végén kattintson az **Igen** gombra a Megbízható hitelesítésszolgáltatók gyökértárolóba való importálásához.

### <a name="create-a-pfx-file"></a>PFX-fájl létrehozása

A következő parancs végrehajtása ugyanabból az ablakból, ahol a makecert végrehajtásra került; használja ugyanazt a jelszót, amelyet a tanúsítvány létrehozásához használt:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Az ügyféltanúsítvány importálása a személyes tárolóba

1. A Windows Intézőben kattintson duplán a *MyCert.pfx ikonra.*
2. A **Tanúsítványimportálás varázslóban** válassza az **Aktuális felhasználó** lehetőséget, és kattintson a **Tovább**gombra.
3. Erősítse meg a fájl elérési útját, és kattintson a **Tovább**gombra.
4. Írja be a jelszót, hagyja **az Összes kiterjesztett tulajdonság felvétele** bejelölve, majd kattintson a **Tovább**gombra.
5. Hagyja **automatikusan válassza ki a tanúsítvány tároló [...]** bejelölve, és kattintson a **Tovább**gombra .
6. Kattintson **a Befejezés** **gombra, és az OK gombra.**

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>A PFX-fájl feltöltése a felhőszolgáltatásba

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza a **Felhőszolgáltatások lehetőséget.**
3. Válassza ki a fent létrehozott felhőszolgáltatást a Split/Merge szolgáltatáshoz.
4. Kattintson a felső menü **Tanúsítványok parancsára.**
5. Kattintson a **Feltöltés gombra** az alsó sávon.
6. Jelölje ki a PFX fájlt, és adja meg ugyanazt a jelszót, mint fent.
7. Miután elkészült, másolja a tanúsítvány ujjlenyomatát a lista új bejegyzéséből.

### <a name="update-the-service-configuration-file"></a>A szolgáltatás konfigurációs fájljának frissítése

Illessze be a fenti módon másolt tanúsítvány ujjlenyomatát a beállítások ujjlenyomata/értéke attribútumába.
A dolgozói szerepköresetében:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Webes szerepkör esetén:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Kérjük, vegye figyelembe, hogy az éles környezetben külön tanúsítványokat kell használni a hitelesítésszolgáltatóhoz, a titkosításhoz, a kiszolgálói tanúsítványhoz és az ügyféltanúsítványokhoz. Ennek részletes útmutatása a [Biztonsági beállítások című témakörben található.](sql-database-elastic-scale-split-merge-security-configuration.md)

## <a name="deploy-your-service"></a>A szolgáltatás üzembe helyezése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Válassza ki a korábban létrehozott felhőszolgáltatást.
3. Kattintson az **Áttekintés** elemre.
4. Válassza ki az átmeneti környezetet, majd kattintson a **Feltöltés gombra.**
5. A párbeszédpanelen adja meg a telepítési címkét. A "Csomag" és a "Konfiguráció" mezőben kattintson a "Helyiről" elemre, és válassza a *SplitMergeService.cspkg* fájlt és a korábban konfigurált cscfg fájlt.
6. Győződjön meg arról, hogy a jelölőnégyzet **címkével ellátott üzembe helyezés akkor is, ha egy vagy több szerepkör tartalmaz egy példányt** be van jelölve.
7. Nyomja meg a tick gombot a jobb alsó sarokban, hogy megkezdje a telepítést. Elvárják, hogy néhány percet vesz igénybe.

## <a name="troubleshoot-the-deployment"></a>A központi telepítés hibáinak elhárítása

Ha a webes szerepkör nem kapcsolódik online állapotba, valószínűleg probléma van a biztonsági konfigurációval. Ellenőrizze, hogy a TLS/SSL a fent leírt módon van-e konfigurálva.

Ha a feldolgozói szerepkör nem érhető el online állapotban, de a webes szerepkör sikeres, akkor valószínűleg probléma kapcsolódik a korábban létrehozott állapot-adatbázishoz.

- Győződjön meg arról, hogy a cscfg kapcsolati karakterlánca pontos.
- Ellenőrizze, hogy a kiszolgáló és az adatbázis létezik-e, és hogy a felhasználói azonosító és a jelszó helyes-e.
- Az Azure SQL DB esetében a kapcsolati karakterláncnak a következő űrlapon kell lennie:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Győződjön meg arról, hogy a kiszolgálónév nem **https://.**
- Győződjön meg arról, hogy az Azure SQL DB-kiszolgáló lehetővé teszi az Azure Services számára, hogy csatlakozzon hozzá. Ehhez nyissa meg az adatbázist a portálon, és győződjön meg arról, hogy az **Azure Services elérésének engedélyezése** beállítás **On****.

## <a name="test-the-service-deployment"></a>A szolgáltatás telepítésének tesztelése

### <a name="connect-with-a-web-browser"></a>Csatlakozás webböngészővel

Határozza meg a split-merge szolgáltatás webes végpontját. Ezt a portálon **megtalálhatja,** ha a felhőszolgáltatás áttekintése és a **webhely URL-címe** alatt a jobb oldalon keres. Cserélje le **http://** **https://,** mivel az alapértelmezett biztonsági beállítások letiltják a HTTP-végpontot. Töltse be az URL-cím lapját a böngészőbe.

### <a name="test-with-powershell-scripts"></a>Tesztelés PowerShell-parancsfájlokkal

A központi telepítés és a környezet tesztelhető a mellékelt PowerShell-parancsfájlok minta futtatásával.

A parancsfájlok a következők:

1. *SetupSampleSplitMergeEnvironment.ps1* - tesztadatréteget állít be a Split/Merge számára (részletes leírást lásd az alábbi táblázatban)
2. *ExecuteSampleSplitMerge.ps1* - tesztműveleteket hajt végre a tesztadatszinten (részletes leírást lásd az alábbi táblázatban)
3. *GetMappings.ps1* – legfelső szintű mintaparancsfájl, amely kinyomtatja a szegmensleképezések aktuális állapotát.
4. *ShardManagement.psm1* – a ShardManagement API-t burkáló segítő parancsfájl
5. *SqlDatabaseHelpers.psm1* – sql-adatbázisok létrehozására és kezelésére szolgáló súgóparancsfájl
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájl</th>
       <th>Lépések</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Shard térképkezelő adatbázis létrehozása</td>
     </tr>
     <tr>
       <td>2. 2 shard adatbázislétrehozása.
     </tr>
     <tr>
       <td>3. Létrehoz egy shard térképet ezekhez az adatbázisokhoz (törli az adatbázisokon lévő meglévő shard leképezéseket). </td>
     </tr>
     <tr>
       <td>4. Létrehoz egy kis mintatáblát a szegmensekben, és feltölti a táblát az egyik szegmensben.</td>
     </tr>
     <tr>
       <td>5. Deklarálja a SchemaInfo a szilánkos tábla.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájl</th>
       <th>Lépések</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Felosztásos kérelmet küld a Split-Merge Service webes előtér, amely felosztja az adatok felét az első shard a második shard.</td>
     </tr>
     <tr>
       <td>2. Lekérdezi a webes előtér a felosztási kérelem állapotát, és megvárja, amíg a kérelem befejeződik.</td>
     </tr>
     <tr>
       <td>3. Körlevélkérés küldése a Split-Merge Service webes előtér, amely az adatokat a második szegmens vissza az első shard.</td>
     </tr>
     <tr>
       <td>4. Lekérdezi a webes előtér az egyesítési kérelem állapotát, és megvárja, amíg a kérelem befejeződik.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>A PowerShell használata a telepítés ellenőrzéséhez

1. Nyisson meg egy új PowerShell-ablakot, és keresse meg azt a könyvtárat, ahol letöltötte a Split-Merge csomagot, majd lépjen be a "powershell" könyvtárba.

2. Hozzon létre egy Azure SQL Database-kiszolgálót (vagy válasszon egy meglévő kiszolgálót), ahol a shard map manager és a szilánkok jönnek létre.

   > [!NOTE]
   > A *SetupSampleSplitMergeEnvironment.ps1* parancsfájl alapértelmezés szerint ugyanazon a kiszolgálón hozza létre ezeket az adatbázisokat, hogy a parancsfájl egyszerű maradjon. Ez nem maga a split-merge szolgáltatás korlátozása.

   A DB-k olvasási/írási hozzáféréssel rendelkező SQL-hitelesítési bejelentkezésre lesz szükség ahhoz, hogy a Split-Merge szolgáltatás áthelyezze az adatokat, és frissítse a szegmenstérképet. Mivel a split-merge szolgáltatás fut a felhőben, jelenleg nem támogatja az integrált hitelesítés.

   Győződjön meg arról, hogy az Azure SQL-kiszolgáló konfigurálva van, hogy a hozzáférést az IP-cím a gép fut ezeket a parancsfájlokat. Ezt a beállítást az Azure SQL server / configuration / allowed IP-címek alatt találja.

3. A mintakörnyezet létrehozásához hajtsa végre a *SetupSampleSplitMergeEnvironment.ps1* parancsfájlt.

   A parancsfájl futtatása törli a shard térképfelügyeleti adatstruktúrákat a shard map manager adatbázisban és a szegmensekben. Hasznos lehet a parancsfájl újrafuttatása, ha újra szeretné inicializálni a szegmenstérképet vagy szilánkokat.

   Minta parancssor:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. A Getmappings.ps1 parancsfájl végrehajtásával tekintse meg a mintakörnyezetben jelenleg létező leképezéseket.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Hajtsa végre az *ExecuteSampleSplitMerge.ps1* parancsfájlt egy felosztott művelet végrehajtásához (az első szegmens adatainak felét a második szegmensbe helyezi), majd egy egyesítési műveletet (az adatok áthelyezése az első szegmensre). Ha konfigurálta a TLS-t, és letiltotta a http-végpontot, győződjön meg arról, hogy a https:// végpontot használja.

   Minta parancssor:

   ```cmd
   .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' 
    -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```

   Ha az alábbi hibaüzenet jelenik meg, akkor valószínűleg a webvégpont tanúsítványával van probléma. Próbáljon meg csatlakozni a webes végponthoz a kedvenc webböngészőjével, és ellenőrizze, hogy hiba történt-e a tanúsítványokkal.

     `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

   Ha ez sikerült, a kimenet nek az alábbihoz hasonlóan kell kinéznie:

   ```output
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

6. Kísérletezzen más adattípusokkal! Az összes parancsfájl egy választható -ShardKeyType paramétert vesz igénybe, amely lehetővé teszi a kulcstípus megadását. Az alapértelmezett érték az Int32, de megadhatja az Int64, a Guid vagy a Binary értéket is.

## <a name="create-requests"></a>Kérések létrehozása

A szolgáltatás használható a webes felhasználói felület használatával, vagy a SplitMerge.psm1 PowerShell modul importálásával és használatával, amely a webes szerepkörön keresztül küldi el a kérelmeket.

A szolgáltatás áthelyezheti az adatokat a szilánkos táblák és a referenciatáblák. A szilánkos tábla rendelkezik egy szilánkos kulcs oszlop, és különböző soradatokat minden szilánkon. A referenciatábla nem szilánkos, így minden szegmensen ugyanazokat a soradatokat tartalmazza. A hivatkozási táblák olyan adatok esetében hasznosak, amelyek nem változnak gyakran, és a lekérdezésekben szilánkos táblákkal való csatlakozásra szolgálnak.

A felosztásos egyesítési művelet végrehajtásához deklarált táblákat és hivatkozási táblákat kell deklarálnia, amelyeket át szeretne helyezni. Ez a **SchemaInfo** API-val érhető el. Ez az API a **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** névtérben található.

1. Minden szilánkos táblához hozzon létre egy **ShardedTableInfo** objektumot, amely leírja a tábla szülősémanevét (nem kötelező, alapértelmezés szerint "dbo"), a tábla nevét és a tábla oszlopnevét, amely a szilánklatos kulcsot tartalmazza.
2. Minden referenciatáblához hozzon létre egy **ReferenceTableInfo** objektumot, amely leírja a tábla szülősémanevét (nem kötelező, alapértelmezés szerint "dbo") és a tábla nevét.
3. Adja hozzá a fenti TableInfo objektumokat egy új **SchemaInfo** objektumhoz.
4. Hivatkozás tanusítani egy **ShardMapManager-objektumra,** és meghívhatja a **GetSchemaInfoCollection gyűjteményt.**
5. Adja hozzá a **SchemaInfo gyűjteményt** a **SchemaInfoCollection gyűjteményhez,** amely megadja a shard térkép nevét.

Erre példa a SetupSampleSplitMergeEnvironment.ps1 parancsfájl.

A Split-Merge szolgáltatás nem hozza létre a céladatbázist (vagy az adatbázis bármely táblájának sémáját) az Ön számára. A kérés nek a szolgáltatásnak való elküldése előtt előre létre kell hozni őket.

## <a name="troubleshooting"></a>Hibaelhárítás

A powershell-parancsfájlok mintafuttatásakor az alábbi üzenet jelenhet meg:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Ez a hiba azt jelenti, hogy a TLS/SSL tanúsítvány nincs megfelelően konfigurálva. Kérjük, kövesse a "Csatlakozás webböngészővel" című szakaszutasításait.

Ha nem tud beküldeni a kérelmeket, a következőt láthatja:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

Ebben az esetben ellenőrizze a konfigurációs fájlt, különösen a **WorkerRoleSynchronizationStorageAccountConnectionString**beállítását. Ez a hiba általában azt jelzi, hogy a feldolgozói szerepkör nem tudta inicializálni a metaadat-adatbázist az első használatkor.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
