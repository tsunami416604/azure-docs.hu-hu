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
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882828"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Naplózás és diagnosztika Azure térbeli horgonyok

Azure térbeli horgonyok biztosít egy standard naplózási mechanizmus, amely hasznos az alkalmazások fejlesztéséhez. A térbeli horgonyok diagnosztikai naplózás mód hasznos, ha további információra van szüksége a hibakereséshez. Diagnosztikai naplózás tárolja a lemezképeket a környezet.

## <a name="standard-logging"></a>Standard naplózása
A térbeli horgonyok API előfizethet a naplózási mechanizmus hasznos naplók lekérése az alkalmazások fejlesztését és a hibakereséshez. A standard API-k naplózást az eszköz lemezen ne tároljon képek a környezet. Az SDK esemény visszahívások ezeket a naplókat biztosít. Van arra, hogy ezek a naplók integrálása az alkalmazás naplózási mechanizmus.

### <a name="configuration-of-log-messages"></a>Naplóüzenetek konfigurációja
Nincsenek a lényeges a felhasználó két visszahívásokat. A következő minta bemutatja, hogyan konfigurálhatja a munkamenet.

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

Ezen esemény visszahívások feldolgozni a naplók és a hibák a munkamenetből áll rendelkezésre:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Itt adhatja meg az események úgy, hogy a futtatókörnyezet fogadjon részletességi szintjét.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): A standard szintű hibakeresési napló események biztosít.
- [Hiba](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Itt a naplóesemények, amely a modul úgy ítéli meg, a hibák.

## <a name="diagnostics-logging"></a>Diagnosztikai naplózás

A normál módú naplózási művelet térbeli horgonyok is most egy diagnosztikai módját. Diagnosztikai módját a környezet lemezképek rögzíti, és naplózhatja őket a lemezt. Ebben a módban bizonyos típusú problémákra, például lehetővé teszi a kiszámítható található horgonyra hiba hibakeresése használhatja. Diagnosztikai naplózás csak egy adott probléma reprodukálásának engedélyezése. Tiltsa le. Diagnosztika nem engedélyezi, amikor az alkalmazások általában futtatja.

A Microsoft támogatási interakció során a Microsoft képviselője kérheti, hogy arra, hogy további teendőkért diagnosztikai köteg elküldéséhez. Ebben az esetben dönthet, hogy engedélyezze a diagnosztikát és Reprodukálja a problémát, elküldheti a diagnosztikai csomagot. 

Ha elküldi egy diagnosztikai naplót a Microsoft előzetes nyugtázási nélkül a Microsoft képviselőjétől, a Küldés megválaszolatlan kerül.

A következő szakaszok bemutatják engedélyezése diagnosztikai módját, és hogyan lehet elküldeni a diagnosztikai naplókat a Microsoftnak.

### <a name="enable-diagnostics-logging"></a>Diagnosztikai naplózás engedélyezése

Ha engedélyezi a munkamenet a diagnosztikai naplózás, minden a munkamenet elvégzett művelet a helyi fájlrendszer naplózása megfelelő diagnosztika. Képek a környezet során naplózást, a lemez kerülnek.

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

### <a name="submit-the-diagnostics-bundle"></a>A diagnosztika köteg elküldéséhez

A következő kódrészletet bemutatja, hogyan elküldése a Microsoftnak a diagnosztikai csomagot. A csomag tartalmazza a környezet, miután engedélyezte a diagnosztikai munkamenet által rögzített rendszerképeket. 

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

### <a name="parts-of-a-diagnostics-bundle"></a>A diagnosztika csomag részei
A diagnosztika csomag tartalmazhatja a következő információkat:

- **Kulcsképkocka lemezképek**: A környezet diagnosztika engedélyezve lett a munkamenet során rögzített rendszerképeket.
- **Naplók**: A modul által rögzített események naplózása.
- **Munkamenet-metaadatok**: A metaadatok, amely azonosítja a munkamenet.
