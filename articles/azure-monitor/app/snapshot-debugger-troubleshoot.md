---
title: Az Azure Application Insights Snapshot Debugger kapcsolatos problémák elhárítása |} A Microsoft Docs
description: Ez a cikk bemutatja a hibaelhárítási lépéseket és információk nyújtanak segítséget a fejlesztőknek szól, akik engedélyezése és használata az Application Insights Snapshot Debugger problémákat tapasztal, amikor.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: bf19d4f5ce60411413c21fce12f9fe9d2f391bf1
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2019
ms.locfileid: "58094939"
---
# <a id="troubleshooting"></a> Application Insights Snapshot Debugger engedélyezése és pillanatképek megtekintése kapcsolatos problémák elhárítása
Ha engedélyezve van az Application Insights Snapshot Debugger az alkalmazáshoz, de nem jelennek meg a kivételek pillanatképeinek, ezek az utasítások segítségével hibaelhárítása. Számos különféle oka lehet, hogy miért pillanatképeket a rendszer nem hozza létre lehet. Egyes gyakori oka lehet az pillanatkép állapot-ellenőrzés futtatása.

## <a name="use-the-snapshot-health-check"></a>Használja a pillanatkép állapotának ellenőrzése
Számos gyakori problémára eredményez a hibakeresési pillanatfelvétel megnyitása nem jelenik meg. Egy elavult Snapshot Collector, például; használatával a napi feltöltési korlát elérése vagy esetleg a pillanatkép van csak hosszú ideig tart a feltöltendő. A pillanatkép állapotát ellenőrző használatával kapcsolatos gyakori problémák elhárítása.

A teljes körű nyomkövetési nézet, amellyel a pillanatkép állapotának ellenőrzése a kivétel ablaktáblán egy kapcsolat áll fenn.

![Adja meg a pillanatkép-állapot-ellenőrzése](./media/snapshot-debugger/enter-snapshot-health-check.png)

Az interaktív, csevegés-szerű felületet keres a gyakori problémákat, és végigvezeti Önt felé azok javításához.

![Állapot-ellenőrzése](./media/snapshot-debugger/healthcheck.png)

Ha ez nem oldja meg a problémát, majd tekintse meg a következő manuális hibaelhárítási lépéseket.

## <a name="verify-the-instrumentation-key"></a>A kialakítási kulcs ellenőrzése

Ellenőrizze, hogy a megfelelő kialakítási kulcsot használ a közzétett alkalmazásban. Általában a kialakítási kulcs az ApplicationInsights.config fájlban olvasható. Ellenőrizze, hogy értéke ugyanaz, mint a rendszerállapotkulcsot az Application Insights-erőforrás számára, a portálon.

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>A legújabb verzióra a NuGet-csomag frissítése

Ha Snapshot Debugger engedélyezve volt a [a portál Application Insights paneljén](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), majd az alkalmazás már futniuk kell a legújabb NuGet-csomagot. Ha a pillanatkép-hibakereső engedélyezetté többek között a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot, használja a Visual Studio NuGet-Csomagkezelőt, hogy a legújabb verzióját használja-e Microsoft.ApplicationInsights.SnapshotCollector. Kibocsátási megjegyzések található https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Az adatfeltöltő naplókat

Pillanatkép létrehozása után egy kis memóriakép-fájl (.dmp) jön létre a lemezen. Egy külön adatfeltöltő-folyamatot hoz létre a tömörített memóriaképet fájlt, és feltölti azt, együtt minden társított PDBs, az Application Insights Snapshot Debugger storage. Miután a tömörített memóriaképet sikeres feltöltését követően a lemezről törlődik. Az adatfeltöltő-folyamat a naplófájlok őrzi meg a lemezen. App Service Environment-környezetben, keresse meg ezeket a naplókat a `D:\Home\LogFiles`. A Kudu felügyeleti hely az App Service használatával kereshet ezekben a naplófájlokban.

1. Nyissa meg az App Service-alkalmazás az Azure Portalon.
2. Kattintson a **speciális eszközök**, vagy keressen **Kudu**.
3. Kattintson a **Go**.
4. Az a **hibakereső konzol** legördülő listában jelölje ki **CMD**.
5. Kattintson a **LogFiles**.

Legalább egy fájlt névvel kezdődő kell megjelennie `Uploader_` vagy `SnapshotUploader_` és a egy `.log` bővítmény. Kattintson a megfelelő ikonra, töltse le a naplófájlokat vagy a böngészőben megnyitja őket.
A fájl nevét tartalmazza, amely azonosítja az App Service-példány egyedi utótagja. Az App Service-példányhoz egynél több gépen üzemel, van-e az egyes gépek külön naplófájlokat. Amikor a feltöltő egy új tömörített memóriaképet fájlt észlel, azt a naplófájl rögzíti. Íme egy példa a sikeres pillanatkép és a feltöltés:

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
> A fenti példában a Microsoft.ApplicationInsights.SnapshotCollector NuGet-csomag 1.2.0-s vagy annál újabb verziójából származik. A korábbi verziókban a adatfeltöltő folyamatot nevezik `MinidumpUploader.exe` és a napló kevésbé részletes.

Az előző példában a műszerezettségi kulcs jelenleg `c12a605e73c44346a984e00000000000`. Ezt az értéket meg kell egyeznie a kialakítási kulcsot az alkalmazáshoz.
A tömörített memóriaképet társítva a Azonosítóval rendelkező pillanatkép `139e411a23934dc0b9ea08a626db16c5`. Keresse meg a kapcsolódó kivétel telemetriát az Application Insights-elemzési később használhatja ezt az Azonosítót.

Az adatfeltöltő új PDBs 15 percenként egyszer kapcsolatban keres. Például:

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

Alkalmazások, amelyek _nem_ adatfeltöltő naplók vannak az App Service-ben üzemeltetett, a tömörített memóriaképek ugyanabban a mappában: `%TEMP%\Dumps\<ikey>` (ahol `<ikey>` a kialakítási kulcs).

## <a name="troubleshooting-cloud-services"></a>A Cloud Services hibaelhárítása
Szerepkörhöz a Felhőszolgáltatásokban az alapértelmezett ideiglenes mappa esetleg túl kicsi ahhoz, hogy a kis memóriaképfájl, vezető elveszett pillanatképek.
A szükséges hely az alkalmazás és a párhuzamos pillanatképek számát teljes munkakészletének függ.
32 bites ASP.NET webes szerepkör munkakészletének általában 200 MB-TAL és 500 MB között van.
Legalább két egyidejű pillanatképek lehetővé.
Például ha az alkalmazás használja a teljes munkakészletet az 1 GB, akkor gondoskodnia kell, hogy nincs-e tárolni a pillanatképek lemezterület legalább 2 GB.
Kövesse az alábbi lépéseket a felhőalapú szolgáltatás szerepkör konfigurálása a pillanatképek egy dedikált helyi erőforrás.

1. Adjon hozzá egy új helyi erőforrást a Felhőszolgáltatás a felhőalapú szolgáltatás definíciós (.csdef) fájl szerkesztésével. Az alábbi példa meghatározza nevű erőforrás `SnapshotStore` 5 GB-os mérettel.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Módosítsa a szerepkör indítási kódot, amellyel hozzáadja egy környezeti változó, amely a `SnapshotStore` helyi erőforrás. A feldolgozói szerepkörök esetében a kód fel kell venni a szerepkör `OnStart` módszer:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   A webes szerepkörök (ASP.NET), a kód hozzá kell adni a webalkalmazás `Application_Start` módszer:
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

3. A szerepkör ApplicationInsights.config fájl felülírásához használja az ideiglenes mappa frissítése `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Az árnyékmásolat-mappa felülbírálása

A Snapshot Collector indításakor próbál egy mappát a lemezen, amely lehetővé teszi a pillanatkép Adatfeltöltő folyamat futtatásához. A kiválasztott mappát a árnyékmásolat mappa néven ismert.

A Snapshot Collector ellenőrzi néhány jól ismert helyek, gondoskodik róla, hogy a pillanatkép Adatfeltöltő bináris fájlok másolását engedélyekkel rendelkezik. Az alábbi környezeti változókat használják:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Nem található a megfelelő mappát, ha a Snapshot Collector egy hiba üzenettel jelentések _"Nem található a megfelelő árnyékmásolat mappa másolása."_

Ha a példány nem sikerül, pillanatképek adatgyűjtője jelentések egy `ShadowCopyFailed` hiba.

Ha nem lehet elindítani a feltöltő, pillanatképek adatgyűjtője jelentések egy `UploaderCannotStartFromShadowCopy` hiba. Az üzenet törzsében gyakran tartalmaznak `System.UnauthorizedAccessException`. Ez a hiba általában akkor fordul elő, mert az alkalmazás korlátozott engedélyekkel rendelkező fiókkal futtatja. A fiók az árnyékmásolat mappa másolása írási engedéllyel rendelkezik, de nem rendelkezik engedéllyel a kódot.

Ezek a hibák általában indítása során fordulhat elő, mert azok fog általában následovat egy `ExceptionDuringConnect` hiba üzenettel _"Feltöltése nem sikerült elindítani."_

Ezek a hibák megoldása, adja meg manuálisan keresztül mappa árnyékmásolat másolása a `ShadowCopyFolder` konfigurációs beállítást. Például az applicationinsights.config fájlban:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Vagy ha egy .NET Core-alkalmazást az appsettings.json használja:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Az Application Insights keresés használatával pillanatképekkel kivételek keresése

Egy pillanatkép jön létre, amikor a rtesítő kivétel van-e megjelölve a pillanatkép-azonosítót. A pillanatkép-azonosító alkalmazás egyéni tulajdonság része, a kivétel telemetriát az Application Insights jelentésekor. Használatával **keresési** az Application Insights, az összes telemetriai adat található a `ai.snapshot.id` egyéni tulajdonság.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
2. Kattintson a **Keresés** gombra.
3. Típus `ai.snapshot.id` a keresőmezőbe, és nyomja le az Enter.

![Keresse meg a telemetriai adatok a portálon egy pillanatkép-azonosító](./media/snapshot-debugger/search-snapshot-portal.png)

Ha a keresés eredménytelen, majd pillanatképek nem jelzett az Application insights szolgáltatásba az alkalmazás a kijelölt időtartományban található.

Keresse meg a naplók feltöltése egy adott pillanatkép-azonosító, a keresőmezőbe írja be Azonosítóval. Ha telemetriai adatokat, amelyeket feltöltött egy pillanatkép nem találja, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy dolgozik a megfelelő Application Insights-erőforrást a kialakítási kulcsot ellenőrzésével.

2. Az időtartomány szűrő ahhoz, hogy biztosítsák az adott időtartományt a keresés használatával történő küldés időbélyegzője legyen a Adatfeltöltő-naplóból, állítsa be.

Ha továbbra sem látja, hogy a pillanatkép-Azonosítóval rendelkező kivételt, a kivétel telemetriát az Application Insights nem jelentett. Ez a helyzet akkor fordulhat elő, ha az alkalmazás összeomlott ideig tartott a pillanatkép után, de mielőtt azt jelentette, hogy a kivétel telemetriát. Ebben az esetben ellenőrizze az App Service-naplók alatt `Diagnose and solve problems` megtekintéséhez, hogy voltak-e váratlan újraindítást vagy nem kezelt kivételeket.

## <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy vagy tűzfal-szabályok szerkesztése

Ha az alkalmazás csatlakozik az interneten keresztül proxy vagy tűzfal, szükség lehet ahhoz, hogy a pillanatkép-hibakereső szolgáltatással folytatott kommunikációhoz az alkalmazás a szabályok szerkesztése. Íme [IP-címek és a pillanatkép-hibakereső által használt portok listáját](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
