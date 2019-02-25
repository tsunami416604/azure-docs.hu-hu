---
title: Naplózás és diagnosztika Azure térbeli horgonyok |} A Microsoft Docs
description: Hogyan hozhat létre, illetve beolvasni a naplózás és diagnosztika Azure térbeli horgonyok részletes leírását.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: d9377e2b5b66a7d426373a8a85e4880dafeaeee6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753172"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Naplózás és diagnosztika Azure térbeli horgonyok

Azure térbeli horgonyok lehetővé teszi a standard naplózási hasznos az alkalmazások fejlesztéséhez. Ezenkívül esetén a diagnosztikai naplózás mód hasznos még több információra szükség a hibakereséshez. Diagnosztikai naplózás magában foglalja a környezet a rendszerképek tárolására.

## <a name="standard-logging-in-azure-spatial-anchors"></a>Az Azure térbeli horgonyok standard naplózása
Az Azure térbeli horgonyok API lehetővé teszi a naplózás, amely az alkalmazások az alkalmazásfejlesztéshez hasznos naplóadatokat fogad és a hibakereséshez előfizethetnek. A standard API-k naplózási képek az eszköz lemezre a környezet nem maradnak. Az SDK esemény visszahívások ezeket a naplókat biztosít. Van arra, hogy ezek a naplók integrálása az alkalmazás naplózási mechanizmus.

### <a name="how-to-configure-the-log-messages"></a>A naplózási üzenetek konfigurálása
Nincsenek a lényeges a felhasználó két visszahívásokat. Az alábbi példában láthatja, hogyan konfigurálhatja a munkamenet.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events--properties"></a>Esemény & tulajdonságai

Naplók és a hibák a munkamenetből feldolgozni a megadott esemény-visszahívások.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Itt adhatja meg az események úgy, hogy a futtatókörnyezet fogadjon részletességi szintjét.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Az esemény visszahívási biztosít a standard szintű hibakeresési napló események.
- [Hiba](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Az esemény visszahívási alkalmazásnapló-események által a futásidejű hibák tekintett biztosít.

## <a name="diagnostics-logging-in-azure-spatial-anchors"></a>Diagnosztikai naplózás az Azure térbeli horgonyok

A művelet a fent ismertetett naplózási normál üzemmódban Azure térbeli horgonyok egy diagnosztikai módját, amely a fejlesztők akkor választható, is most. Diagnosztika a környezet lemezképek rögzíti, és naplózhatja őket a lemezt. Ebben a módban bizonyos típusú problémákra, például a hibakeresés során nem képes kiszámítható módon található horgonyra hasznos. Csak engedélyezze a diagnosztikai naplózás egy meghatározott probléma reprodukálásához és tiltsa le. Ne futtassa az alkalmazások általában az engedélyezve van a diagnosztika.

A Microsoft támogatási interakció során a Microsoft képviselője kérheti, hogy arra, hogy további teendőkért Microsoft diagnostics köteg elküldéséhez. Ebben az esetben előfordulhat, hogy kívánja-diagnosztika engedélyezése, a probléma újbóli előidézését és küldje el a diagnosztikai csomagot a Microsoftnak további teendőkért. Diagnosztikai naplók elküldése a Microsoft előzetes nyugtázási nélkül a Microsoft képviselőjével közösen megválaszolatlan kerül.

Az alábbi kódrészletek bemutatják, miként diagnosztikai mód engedélyezéséhez, továbbá hogyan küldhet a Microsoftnak a diagnosztikai naplók.

### <a name="enabling-diagnostics-logging"></a>Diagnosztikai naplózás engedélyezése

A munkamenet a diagnosztikai naplózás engedélyezve van, minden művelet a munkamenet a megfelelő diagnosztikai naplózása a helyi fájlrendszerben fog rendelkezni. Naplózás tartoznak a lemezképek a környezet lemezre.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // Opt-in to diagnostics logging of environment images.
    // If this is enabled, the diagnostics bundle will include images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submitting-the-diagnostic-bundle"></a>A diagnosztikai köteg elküldése

A következő kódrészletet bemutatja, hogyan elküldése a Microsoftnak a diagnosztikai csomagot. Vegye figyelembe, hogy ez a környezet diagnosztika engedélyezése után a munkamenet által rögzített rendszerképek tartalmazza. Ezenkívül a Microsoftnak elküldött nélkül előzetes nyugtázása a Microsoft képviselőjével közösen diagnosztikai csomagjaiból megválaszolatlan kerül.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest  to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="anatomy-of-the-diagnostics-bundle"></a>A diagnosztika csomag szerkezete
Lehet, hogy egy diagnosztikai csomagban található a következő információkat:

- Kulcsképkocka-lemezképek – képek a környezet a munkamenet során rögzített diagnosztikai engedélyezve lettek.
- Naplók – a modul által rögzített naplózási események.
- Munkamenet metaadat - metaadatokat, amelyek a munkamenetet.
