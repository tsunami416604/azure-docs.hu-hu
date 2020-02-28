---
title: Az Azure Application Insights hibáinak megoldása Snapshot Debugger
description: Ez a cikk hibaelhárítási lépéseket és információkat nyújt a Application Insights Snapshot Debugger engedélyezésével vagy használatával kapcsolatos problémákat okozó fejlesztőknek.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671409"
---
# <a id="troubleshooting"></a>A Application Insights Snapshot Debugger engedélyezésével vagy a pillanatképek megtekintésével kapcsolatos problémák elhárítása
Ha engedélyezte Application Insights Snapshot Debugger az alkalmazáshoz, de nem tekinti meg a kivételekhez tartozó pillanatképeket, a következő útmutatást használhatja a hibák megoldásához:. Számos különböző oka lehet annak, hogy a pillanatképek miért nem jönnek létre. A pillanatkép állapotának ellenőrzését futtatva azonosíthatja a lehetséges gyakori okok némelyikét.

## <a name="use-the-snapshot-health-check"></a>Használja a pillanatkép állapotának ellenőrzése
Számos gyakori problémára eredményez a hibakeresési pillanatfelvétel megnyitása nem jelenik meg. Egy elavult Snapshot Collector, például; használatával a napi feltöltési korlát elérése vagy esetleg a pillanatkép van csak hosszú ideig tart a feltöltendő. A pillanatkép állapotát ellenőrző használatával kapcsolatos gyakori problémák elhárítása.

A teljes körű nyomkövetési nézet, amellyel a pillanatkép állapotának ellenőrzése a kivétel ablaktáblán egy kapcsolat áll fenn.

![Adja meg a pillanatkép-állapot-ellenőrzése](./media/snapshot-debugger/enter-snapshot-health-check.png)

Az interaktív, csevegés-szerű felületet keres a gyakori problémákat, és végigvezeti Önt felé azok javításához.

![Állapot-ellenőrzése](./media/snapshot-debugger/healthcheck.png)

Ha ez nem oldja meg a problémát, majd tekintse meg a következő manuális hibaelhárítási lépéseket.

## <a name="verify-the-instrumentation-key"></a>A kialakítási kulcs ellenőrzése

Ellenőrizze, hogy a megfelelő kialakítási kulcsot használ a közzétett alkalmazásban. Általában a kialakítási kulcs az ApplicationInsights.config fájlban olvasható. Ellenőrizze, hogy értéke ugyanaz, mint a rendszerállapotkulcsot az Application Insights-erőforrás számára, a portálon.

## <a name="preview-versions-of-net-core"></a>A .NET Core előzetes verziói
Ha az alkalmazás a .NET Core előzetes verzióját használja, és Snapshot Debugger a portál [Application Insights paneljén](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) keresztül volt engedélyezve, akkor előfordulhat, hogy Snapshot Debugger nem indul el. Kövesse az [Snapshot Debugger engedélyezése más környezetekhez](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) című témakör utasításait, hogy a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) ***NuGet-csomagot*** az alkalmazással együtt, a [Application Insights panelen](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)is engedélyezze.


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>A legújabb verzióra a NuGet-csomag frissítése

Ha Snapshot Debugger engedélyezte a [portálon a Application Insights ablaktáblán](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), akkor az alkalmazásnak már a legújabb NuGet-csomagot kell futtatnia. Ha a Snapshot Debugger a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomaggal együtt engedélyezte, a Visual Studio NuGet csomagkezelő segítségével ellenőrizze, hogy a Microsoft. ApplicationInsights. snapshotcollector nugetcsomag legújabb verzióját használja-e. A kibocsátási megjegyzések a következő címen találhatók: https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Az adatfeltöltő naplókat

Pillanatkép létrehozása után egy kis memóriakép-fájl (.dmp) jön létre a lemezen. Egy külön adatfeltöltő-folyamatot hoz létre a tömörített memóriaképet fájlt, és feltölti azt, együtt minden társított PDBs, az Application Insights Snapshot Debugger storage. Miután a tömörített memóriaképet sikeres feltöltését követően a lemezről törlődik. Az adatfeltöltő-folyamat a naplófájlok őrzi meg a lemezen. App Service-környezetben ezek a naplók a `D:\Home\LogFiles`ban találhatók. A Kudu felügyeleti hely az App Service használatával kereshet ezekben a naplófájlokban.

1. Nyissa meg az App Service-alkalmazás az Azure Portalon.
2. Kattintson a **speciális eszközök**elemre, vagy keressen rá a **kudu**.
3. Kattintson az **Ugrás**gombra.
4. A **hibakeresési konzol** legördülő listájában válassza a **cmd**elemet.
5. Kattintson a **LogFiles**elemre.

Meg kell jelennie legalább egy olyan nevű fájlnak, amelynek neve `Uploader_` vagy `SnapshotUploader_` és `.log` kiterjesztéssel kezdődik. Kattintson a megfelelő ikonra, töltse le a naplófájlokat vagy a böngészőben megnyitja őket.
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
> A fenti példában a Microsoft.ApplicationInsights.SnapshotCollector NuGet-csomag 1.2.0-s vagy annál újabb verziójából származik. A korábbi verziókban a feltöltő folyamat neve `MinidumpUploader.exe`, és a napló kevésbé részletes.

Az előző példában a kialakítási kulcs `c12a605e73c44346a984e00000000000`. Ezt az értéket meg kell egyeznie a kialakítási kulcsot az alkalmazáshoz.
A minidump egy `139e411a23934dc0b9ea08a626db16c5`AZONOSÍTÓval rendelkező pillanatképhez van társítva. Keresse meg a kapcsolódó kivétel telemetriát az Application Insights-elemzési később használhatja ezt az Azonosítót.

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

A App Serviceban _nem_ üzemeltetett alkalmazások esetén a feltöltő naplók ugyanabban a mappában találhatók, mint a minidumps: `%TEMP%\Dumps\<ikey>` (ahol `<ikey>` a kialakítási kulcs).

## <a name="troubleshooting-cloud-services"></a>A Cloud Services hibaelhárítása
Szerepkörhöz a Felhőszolgáltatásokban az alapértelmezett ideiglenes mappa esetleg túl kicsi ahhoz, hogy a kis memóriaképfájl, vezető elveszett pillanatképek.
A szükséges hely az alkalmazás és a párhuzamos pillanatképek számát teljes munkakészletének függ.
32 bites ASP.NET webes szerepkör munkakészletének általában 200 MB-TAL és 500 MB között van.
Legalább két egyidejű pillanatképek lehetővé.
Például ha az alkalmazás használja a teljes munkakészletet az 1 GB, akkor gondoskodnia kell, hogy nincs-e tárolni a pillanatképek lemezterület legalább 2 GB.
Kövesse az alábbi lépéseket a felhőalapú szolgáltatás szerepkör konfigurálása a pillanatképek egy dedikált helyi erőforrás.

1. Adjon hozzá egy új helyi erőforrást a Felhőszolgáltatás a felhőalapú szolgáltatás definíciós (.csdef) fájl szerkesztésével. Az alábbi példa egy `SnapshotStore` nevű erőforrást definiál, amelynek mérete 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Módosítsa a szerepkör indítási kódját egy olyan környezeti változó hozzáadásához, amely a `SnapshotStore` helyi erőforrásra mutat. A feldolgozói szerepkörök esetében a kódot hozzá kell adni a szerepkör `OnStart` metódusához:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Webes szerepkörök (ASP.NET) esetén a kódot hozzá kell adni a webalkalmazás `Application_Start` metódusához:
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

3. Frissítse a szerepkör ApplicationInsights. config fájlját a `SnapshotCollector` által használt ideiglenes mappa helyének felülbírálásához.
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

Ha nem található megfelelő mappa, Snapshot Collector a következő hibaüzenetet jelenti: _"nem található a megfelelő árnyékmásolat-mappa."_

Ha a másolás sikertelen, Snapshot Collector `ShadowCopyFailed` hibát jelez.

Ha a feltöltőt nem lehet elindítani, Snapshot Collector `UploaderCannotStartFromShadowCopy` hibát jelez. Az üzenet törzse gyakran `System.UnauthorizedAccessException`tartalmaz. Ez a hiba általában akkor fordul elő, mert az alkalmazás korlátozott engedélyekkel rendelkező fiókkal futtatja. A fiók az árnyékmásolat mappa másolása írási engedéllyel rendelkezik, de nem rendelkezik engedéllyel a kódot.

Mivel ezek a hibák általában az indítás során történnek, általában egy `ExceptionDuringConnect` hiba következik be, amely azt jelzi, _hogy a "feltöltő nem indult el."_

A hibák megkerüléséhez manuálisan megadhatja az árnyékmásolat-mappát a `ShadowCopyFolder` konfigurációs lehetőséggel. Például az applicationinsights.config fájlban:

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

Egy pillanatkép jön létre, amikor a rtesítő kivétel van-e megjelölve a pillanatkép-azonosítót. A pillanatkép-azonosító alkalmazás egyéni tulajdonság része, a kivétel telemetriát az Application Insights jelentésekor. A **Keresés** a Application Insights használatával megkeresheti az összes telemetria az `ai.snapshot.id` egyéni tulajdonsággal.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
2. Kattintson a **Keresés**gombra.
3. Írja be a `ai.snapshot.id` kifejezést a keresés szövegmezőbe, majd nyomja le az ENTER billentyűt.

![Keresse meg a telemetriai adatok a portálon egy pillanatkép-azonosító](./media/snapshot-debugger/search-snapshot-portal.png)

Ha a keresés eredménytelen, majd pillanatképek nem jelzett az Application insights szolgáltatásba az alkalmazás a kijelölt időtartományban található.

Keresse meg a naplók feltöltése egy adott pillanatkép-azonosító, a keresőmezőbe írja be Azonosítóval. Ha telemetriai adatokat, amelyeket feltöltött egy pillanatkép nem találja, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy dolgozik a megfelelő Application Insights-erőforrást a kialakítási kulcsot ellenőrzésével.

2. Az időtartomány szűrő ahhoz, hogy biztosítsák az adott időtartományt a keresés használatával történő küldés időbélyegzője legyen a Adatfeltöltő-naplóból, állítsa be.

Ha továbbra sem látja, hogy a pillanatkép-Azonosítóval rendelkező kivételt, a kivétel telemetriát az Application Insights nem jelentett. Ez a helyzet akkor fordulhat elő, ha az alkalmazás összeomlott ideig tartott a pillanatkép után, de mielőtt azt jelentette, hogy a kivétel telemetriát. Ebben az esetben tekintse meg a App Service naplókat a `Diagnose and solve problems` területen, és ellenőrizze, hogy nem történt-e váratlan újraindítás vagy nem kezelt kivétel.

## <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy vagy tűzfal-szabályok szerkesztése

Ha az alkalmazás csatlakozik az interneten keresztül proxy vagy tűzfal, szükség lehet ahhoz, hogy a pillanatkép-hibakereső szolgáltatással folytatott kommunikációhoz az alkalmazás a szabályok szerkesztése. Az Snapshot Debugger által használt IP-címek szerepelnek a Azure Monitor szolgáltatás címkéjén.
