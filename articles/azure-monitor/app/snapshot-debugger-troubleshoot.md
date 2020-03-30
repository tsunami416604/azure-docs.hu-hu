---
title: Az Azure Application Insights pillanatkép-hibakeresőjének hibaelhárítása – problémamegoldás
description: Ez a cikk hibaelhárítási lépéseket és információkat tartalmaz, amelyek segítséget nyújtanak az Application Insights Snapshot Debugger engedélyezésével vagy használatával kapcsolatos problémák kalkulálása érdekében.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671409"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Az Application Insights snapshot debugger engedélyezésével vagy a pillanatképek megtekintésével kapcsolatos problémák elhárítása
Ha engedélyezte az Application Insights Snapshot Debugger az alkalmazáshoz, de nem látja a kivételek pillanatképeit, ezekkel az utasításokkal elháríthatja a hibákat. Számos különböző oka lehet annak, hogy miért nem jönnek létre pillanatképek. A pillanatkép állapot-ellenőrzése futtatásához a lehetséges gyakori okok azonosítására.

## <a name="use-the-snapshot-health-check"></a>A pillanatkép állapot-ellenőrzése
Számos gyakori probléma miatt az Open Debug Snapshot nem jelenik meg. Egy elavult Snapshot Collector, például; a napi feltöltési korlát elérése; vagy talán a pillanatkép van csak bevétel egy vágyódik idő -hoz feltölt. A Pillanatkép állapot-ellenőrzése segítségével elháríthatja a gyakori problémákat.

Van egy hivatkozás a végpontok közötti nyomkövetési nézet kivételablakában, amely a Pillanatkép állapot-ellenőrzése.

![Pillanatkép állapotának ellenőrzése megadása](./media/snapshot-debugger/enter-snapshot-health-check.png)

Az interaktív, csevegéshez hasonló felület gyakori problémákat keres, és végigvezeti önt azok kijavításához.

![Állapot-ellenőrzés](./media/snapshot-debugger/healthcheck.png)

Ha ez nem oldja meg a problémát, olvassa el az alábbi manuális hibaelhárítási lépéseket.

## <a name="verify-the-instrumentation-key"></a>A műszerezési kulcs ellenőrzése

Győződjön meg arról, hogy a megfelelő instrumentation kulcsot használja a közzétett alkalmazásban. A műszerezési kulcsot általában az ApplicationInsights.config fájlból olvassa be a rendszer. Ellenőrizze, hogy az érték megegyezik-e a portálon látható Application Insights-erőforrás instrumentation kulcsával.

## <a name="preview-versions-of-net-core"></a>A .NET Core verzióinak előzetes verziói
Ha az alkalmazás a .NET Core előzetes verzióját használja, és a Snapshot Debugger engedélyezve volt a portál [Application Insights ablaktábláján](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) keresztül, akkor előfordulhat, hogy a Snapshot Debugger nem indul el. Kövesse a [Snapshot Debugger engedélyezése más környezetekben](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) című utasításokat, hogy a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomagot is tartalmazza az alkalmazással, az [Application Insights ablaktáblán](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)keresztüli engedélyezés ***mellett.***


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Frissítés a NuGet csomag legújabb verziójára

Ha a Snapshot Debugger engedélyezve volt az [Application Insights ablaktáblán keresztül a portálon,](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)majd az alkalmazásmár fut a legújabb NuGet-csomag. Ha a Snapshot Debugger engedélyezve volt a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomag gal, használja a Visual Studio NuGet csomagkezelőjét, és győződjön meg arról, hogy a Microsoft.ApplicationInsights.SnapshotCollector legújabb verzióját használja. A kibocsátási megjegyzések ahttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>A feltöltő naplóinak ellenőrzése

A pillanatkép létrehozása után egy minidump fájl (.dmp) jön létre a lemezen. Egy külön feltöltő folyamat létrehozza a minidump fájlt, és feltölti azt, valamint a kapcsolódó PDF-fájlokat, az Application Insights Snapshot Debugger tárolóba. Miután a minidump sikeresen feltöltötte, törlődik a lemezről. A feltöltő folyamat naplófájljai a lemezen maradnak. App Service-környezetben ezeket a naplókat a alkalmazásban `D:\Home\LogFiles`találja. Használja a Kudu felügyeleti webhely az App Service ezeket a naplófájlokat.

1. Nyissa meg az App Service-alkalmazást az Azure Portalon.
2. Kattintson **a Speciális eszközök gombra,** vagy keresse meg a **Kudu**kifejezést.
3. Kattintson **az Ugrás gombra.**
4. A **Debug konzol** legördülő listájában válassza a **CMD**lehetőséget.
5. Kattintson **a LogFiles gombra.**

Legalább egy fájlnak látnia kell, `Uploader_` amelynek neve kezdődik vagy `SnapshotUploader_` és egy `.log` kiterjesztés. Kattintson a megfelelő ikonra a naplófájlok letöltéséhez vagy böngészőben való megnyitásához.
A fájlnév tartalmaz egy egyedi utótagot, amely azonosítja az App Service-példányt. Ha az App Service-példány egynél több gépen van tárolva, minden egyes számítógéphez külön naplófájlok tartoznak. Amikor a feltöltő új minidump fájlt észlel, az a naplófájlban rögzítésre kerül. Íme egy példa a sikeres pillanatfelvételre és feltöltésre:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> A fenti példa a Microsoft.ApplicationInsights.SnapshotCollector NuGet csomag 1.2.0-s verziójából származik. A korábbi verziókban a feltöltő folyamat neve, `MinidumpUploader.exe` és a napló kevésbé részletes.

Az előző példában a műszerezési kulcs . `c12a605e73c44346a984e00000000000` Ennek az értéknek meg kell egyeznie az alkalmazás instrumentation kulcsának.
A minidump egy pillanatképhez van `139e411a23934dc0b9ea08a626db16c5`társítva az azonosítóval. Ezt az azonosítót később is használhatja a társított kivételtelemetriai adatok megkereséséhez az Application Insights Analytics ben.

A feltöltő 15 percenként keres új PDF-eket. Például:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Az App Service-ben _nem_ üzemeltetett alkalmazások esetében a feltöltőnaplók ugyanabban a `%TEMP%\Dumps\<ikey>` mappában vannak, mint a minidumps: (hol `<ikey>` van a műszerezési kulcs).

## <a name="troubleshooting-cloud-services"></a>A felhőszolgáltatások kalkapcsolatos hibaelhárítása
A Cloud Services szerepkörei esetében az alapértelmezett ideiglenes mappa túl kicsi lehet a minidump fájlok tárolására, ami elveszett pillanatképekhez vezet.
A szükséges hely az alkalmazás teljes munkakészletétől és az egyidejű pillanatképek számától függ.
A 32 bites ASP.NET webes szerepkör munkakészlete általában 200 MB és 500 MB között van.
Legalább két egyidejű pillanatkép engedélyezése.
Ha például az alkalmazás 1 GB teljes munkakészletet használ, győződjön meg arról, hogy legalább 2 GB lemezterület áll fenn a pillanatképek tárolásához.
Az alábbi lépésekkel konfigurálhatja a Felhőszolgáltatás-szerepkört egy dedikált helyi erőforrással a pillanatképekhez.

1. Új helyi erőforrás hozzáadása a felhőszolgáltatáshoz a Cloud Service-definíció (.csdef) fájl szerkesztésével. A következő példa egy `SnapshotStore` 5 GB-os nevű erőforrást határoz meg.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Módosítsa a szerepkör indítási kódját, és adjon `SnapshotStore` hozzá egy környezeti változót, amely a helyi erőforrásra mutat. A dolgozói szerepkörök esetében a kódot `OnStart` hozzá kell adni a szerepkör metódusához:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Webes szerepkörök (ASP.NET esetén a kódot hozzá kell `Application_Start` adni a webalkalmazás metódusához:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. A szerepkör ApplicationInsights.config fájljának frissítése a szerepkör által használt ideiglenes mappa helyének felülbírálásához`SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Az Árnyékmásolat mappa felülbírálása

Amikor a Snapshot Collector elindul, megpróbál találni egy mappát a lemezen, amely alkalmas a Snapshot Uploader folyamat futtatására. A kiválasztott mappa árnyékmásolat mappájának ismert.

A Pillanatképgyűjtő ellenőriz néhány jól ismert helyet, és gondoskodik arról, hogy rendelkezik-e a Snapshot Uploader bináris fájljának másolásához szükséges engedélyekkel. A következő környezeti változókat használjuk:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- Appdata
- Temp

Ha nem található megfelelő mappa, a Snapshot Collector a következő hibaüzenetet jelenti: _"Nem található megfelelő árnyékmásolat-mappa."_

Ha a másolat sikertelen, `ShadowCopyFailed` a Snapshot Collector hibát jelez.

Ha a feltöltő nem indítható el, `UploaderCannotStartFromShadowCopy` a Snapshot Collector hibát jelez. Az üzenet törzse `System.UnauthorizedAccessException`gyakran tartalmaz . Ez a hiba általában azért fordul elő, mert az alkalmazás csökkentett engedélyekkel rendelkező fiók alatt fut. A fiók nak van engedélye az árnyékmásolat mappába való írásra, de nincs engedélye a kód végrehajtására.

Mivel ezek a hibák általában az indítás során `ExceptionDuringConnect` történnek, általában egy _"Feltöltő nem indult el"_ hibaüzenet követi őket.

A hibák kerülő kerülő száma manuálisan is `ShadowCopyFolder` megadható a konfigurációs beállítással. Az ApplicationInsights.config használata például:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ha az appsettings.json t használjuk a .NET Core alkalmazással:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Az Application Insights-keresés használatával kivételeket kereshet a pillanatképekkel

Pillanatkép létrehozásakor a dobási kivétel egy pillanatkép-azonosítóval van címkézve. Ez a pillanatkép-azonosító egyéni tulajdonságként szerepel, amikor a kivétel telemetriai adatok at jelentik az Application Insights. Az Application Insights **keresés** használatával az egyéni tulajdonsággal összes `ai.snapshot.id` telemetriai elemetria megtalálható.

1. Tallózzon az Application Insights-erőforrás ban az Azure Portalon.
2. Kattintson a **Keresés** gombra.
3. Írja `ai.snapshot.id` be a Keresés mezőbe, és nyomja le az Enter billentyűt.

![Telemetriai adatok keresése pillanatkép-azonosítóval a portálon](./media/snapshot-debugger/search-snapshot-portal.png)

Ha ez a keresés nem ad vissza eredményt, majd nem pillanatképek jelentések Application Insights az alkalmazás a kiválasztott időtartományban.

Ha egy adott pillanatkép-azonosítót szeretne keresni a Feltöltő naplókból, írja be az azonosítót a Keresőmezőbe. Ha nem találja a telemetriai adatokat egy pillanatfelvételhez, amelyről tudja, hogy feltöltötték, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy a megfelelő Application Insights-erőforrást keresi-e a műszerezési kulcs ellenőrzésével.

2. A Feltöltő naplójának időbélyegét használva állítsa be a keresés időtartomány-szűrőjét, hogy az lefedje az adott időtartományt.

Ha továbbra sem lát kivételt a pillanatkép-azonosítóval, majd a kivétel telemetriai adatok nem jelentmeg az Application Insights. Ez a helyzet akkor fordulhat elő, ha az alkalmazás összeomlott a pillanatkép után, de mielőtt jelentette volna a kivétel telemetriai adatokat. Ebben az esetben ellenőrizze az App `Diagnose and solve problems` Service-naplók alatt, hogy nem várt újraindítások vagy nem kezelt kivételek voltak-e.

## <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy- vagy tűzfalszabályok szerkesztése

Ha az alkalmazás proxyn vagy tűzfalon keresztül csatlakozik az internethez, előfordulhat, hogy szerkesztenie kell a szabályokat, hogy az alkalmazás kommunikálhasson a Snapshot Debugger szolgáltatással. A Snapshot Debugger által használt IP-címeket az Azure Monitor szolgáltatáscímke tartalmazza.
