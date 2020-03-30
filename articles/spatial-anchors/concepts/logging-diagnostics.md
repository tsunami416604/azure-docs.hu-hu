---
title: Naplózás és diagnosztika
description: Részletes magyarázat a naplózás és a diagnosztika létrehozásáról és lekéréséről az Azure Spatial Anchors szolgáltatásban.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270131"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Naplózás és diagnosztika az Azure Spatial Anchors szolgáltatásban

Az Azure Spatial Anchors egy szabványos naplózási mechanizmust biztosít, amely hasznos az alkalmazás fejlesztéséhez. A Térbeli horgonyok diagnosztikai naplózási módja akkor hasznos, ha további információra van szüksége a hibakereséshez. A diagnosztikai naplózás a környezet képeit tárolja.

## <a name="standard-logging"></a>Szabványos naplózás
A Térbeli horgonyok API-ban előfizethet a naplózási mechanizmushoz, hogy hasznos naplókat kapjon az alkalmazásfejlesztéshez és a hibakereséshez. A szabványos naplózási API-k nem tárolnak képeket a környezetről az eszközlemezen. Az SDK ezeket a naplókat esemény-visszahívásként biztosítja. Ez rajtad múlik, hogy integrálja ezeket a naplókat az alkalmazás naplózási mechanizmus.

### <a name="configuration-of-log-messages"></a>A naplóüzenetek konfigurálása
A felhasználó számára két fontos visszahívás van. A következő minta bemutatja, hogyan kell konfigurálni a munkamenetet.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Események és tulajdonságok

Ezek az esemény-visszahívások a munkamenet naplóinak és hibáinak feldolgozásához vannak biztosítva:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Megadja a futásidejű események részletességi szintjét.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Szabványos hibakeresési naplóeseményeket biztosít.
- [Hiba](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Olyan naplóeseményeket biztosít, amelyeket a futásidejű hibáknak tekint.

## <a name="diagnostics-logging"></a>Diagnosztikai naplózás

A naplózás szabványos működési módja mellett a Térbeli horgonyok diagnosztikai móddal is rendelkezik. A diagnosztikai mód rögzíti a környezet képeit, és naplózza azokat a lemezre. Ezzel a móddal debugálhat bizonyos típusú problémákat, például a horgony előre látható megkeresésének elmulasztását. Engedélyezze a diagnosztika naplózását csak egy adott probléma reprodukálásához. Akkor tiltsd le. Ne engedélyezze a diagnosztikát, ha az alkalmazásokat a szokásos módon futtatja.

A Microsofttal folytatott támogatási interakció során a Microsoft egyik képviselője megkérdezheti, hogy hajlandó-e diagnosztikai csomagot küldeni további vizsgálatra. Ebben az esetben dönthet úgy, hogy engedélyezi a diagnosztikát, és reprodukálja a problémát, így elküldheti a diagnosztikai csomagot.

Ha diagnosztikai naplót küld a Microsoftnak a Microsoft képviselőjének előzetes visszaigazolása nélkül, a beküldés megválaszolatlan marad.

A következő szakaszok bemutatják, hogyan engedélyezheti a diagnosztikai módot, és hogyan küldheti el a diagnosztikai naplókat a Microsoftnak.

### <a name="enable-diagnostics-logging"></a>Diagnosztikai naplózás engedélyezése

Ha engedélyez egy munkamenetet a diagnosztikai naplózáshoz, a munkamenet minden művelete rendelkezik a megfelelő diagnosztikai naplózással a helyi fájlrendszerben. A naplózás során a rendszer a környezet képeit menti a lemezre.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>A diagnosztikai csomag elküldése

A következő kódrészlet bemutatja, hogyan küldhet diagnosztikai csomagot a Microsoftnak. Ez a csomag a diagnosztika engedélyezése után a munkamenet által rögzített környezet képeit tartalmazza.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>A diagnosztikai csomag részei
A diagnosztikai csomag a következő információkat tartalmazhatja:

- **Kulcsképkocka-képek:** A munkamenet során rögzített környezet képei, miközben a diagnosztika engedélyezve volt.
- **Naplók**: A futásidejű események naplózása.
- **Munkamenet-metaadatok**: A munkamenetet azonosító metaadatok.
