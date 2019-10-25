---
title: Naplózás a Microsoft Authentication Library (MSAL) alkalmazásokban
titleSuffix: Microsoft identity platform
description: Tudnivalók a Microsoft Authentication Library-(MSAL-) alkalmazások naplózásáról.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87102e3ea71695006e465d1becad0f2ece2a426b
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802976"
---
# <a name="logging-in-msal-applications"></a>MSAL-alkalmazások naplózása

A Microsoft Authentication Library (MSAL) alkalmazásai olyan naplófájlokat hoznak elő, amelyek segíthetnek a problémák diagnosztizálásában. Egy alkalmazás több sornyi kóddal is konfigurálhatja a naplózást, és egyéni vezérléssel láthatja el a részletességi szintet, valamint azt, hogy a rendszer naplózza-e a személyes és a szervezeti adatokat. Javasoljuk, hogy hozzon létre egy MSAL-naplózási visszahívást, és adja meg a felhasználók számára a naplók küldését a hitelesítési problémák esetén.

## <a name="logging-levels"></a>Naplózási szintek

A MSAL számos naplózási részletességi szintet biztosít:

- Hiba: azt jelzi, hogy valami hiba történt, és a rendszer hibát generált. Hibakereséshez és a problémák azonosításához használható.
- Figyelmeztetés: nem szükségszerűen hiba vagy hiba történt, de a diagnosztika és a problémák megoldására szolgál.
- Információ: a MSAL a tájékoztató célokra szánt eseményeket naplózza, nem feltétlenül a hibakereséshez.
- Részletes: alapértelmezett. A MSAL a könyvtár működésének részletes adatait naplózza.

## <a name="personal-and-organizational-data"></a>Személyes és szervezeti adatkezelés

Alapértelmezés szerint a MSAL-naplózó nem gyűjt kényes személyes vagy szervezeti adatokat. A könyvtár lehetővé teszi a személyes és szervezeti adatnaplózás engedélyezését, ha úgy dönt, hogy erre van szükség.

## <a name="logging-in-msalnet"></a>Naplózás a MSAL.NET

 > [!NOTE]
 > Tekintse meg a [MSAL.net wikit](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) a MSAL.net-naplózási és egyéb mintákhoz.

A 3. x MSAL a naplózás az alkalmazások létrehozásakor az `.WithLogging` Builder-módosító használatával van beállítva. Ez a metódus nem kötelező paramétereket vesz igénybe:

- `Level` lehetővé teszi a kívánt naplózási szint eldöntését. A hibák beállítása csak hibaüzeneteket kap
- a `PiiLoggingEnabled` lehetővé teszi a személyes és szervezeti adatnaplózást, ha az igaz értékre van állítva. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait.
- a `LogCallback` egy olyan delegált értékre van beállítva, amely a naplózást végzi. Ha a `PiiLoggingEnabled` értéke igaz, a metódus kétszer kapja meg az üzeneteket: egyszer a `containsPii` paraméter értéke false (hamis), az üzenet pedig személyes adatként nem jelenik meg, és a `containsPii` paraméterrel való második idő igaz, és az üzenet személyes adatmennyiséget is tartalmazhat. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatfájlokat), az üzenet ugyanaz lesz.
- a `DefaultLoggingEnabled` engedélyezi a platform alapértelmezett naplózását. Alapértelmezés szerint hamis. Ha úgy állítja be az igaz értéket, hogy az asztali/UWP-alkalmazásokban az esemény-nyomkövetést használja, az iOS-és a logcat-NSLog az Androidon.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

## <a name="logging-in-msal-for-android-using-java"></a>Bejelentkezés a MSAL for Android használatával Javával

Jelentkezzen be az alkalmazás létrehozásakor egy naplózási visszahívás létrehozásával. A visszahívás a következő paramétereket veszi figyelembe:

- `tag` a könyvtár által a visszahívás számára átadott karakterlánc. A naplóbejegyzés társítva van, és a naplózási üzenetek rendezésére használható.
- `logLevel` lehetővé teszi a kívánt naplózási szint eldöntését. A támogatott naplózási szintek a következők: `Error`, `Warning`, `Info`és `Verbose`.
- `message` a naplóbejegyzés tartalma.
- `containsPII` megadja, hogy a rendszer naplózza-e a személyes vagy szervezeti adatelemeket tartalmazó üzeneteket. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait. Ha `containsPII` `true`, ez a metódus kétszer kapja meg az üzeneteket: egyszer a `containsPII` paraméterrel `false`, a `message` pedig személyes adatként nem, és egy második alkalommal, amikor a `containsPii` paraméter értéke `true`, és az üzenet tartalmazhat személyes adatként. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatfájlokat), az üzenet ugyanaz lesz.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Alapértelmezés szerint a MSAL-naplózó nem rögzíti a személyes azonosításra alkalmas adatokat vagy a szervezeti azonosításra alkalmas adatokat.
A személyes azonosításra alkalmas adatok vagy a szervezeti azonosításra alkalmas adatok naplózásának engedélyezése:

```java
Logger.getInstance().setEnablePII(true);
```

Személyes és szervezeti adataik naplózásának letiltása:

```java
Logger.getInstance().setEnablePII(false);
```

Alapértelmezés szerint a logcat naplózása le van tiltva. Engedélyezés: 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>Naplózás a MSAL. js fájlban

 Engedélyezze a naplózást a MSAL. js-ben egy `UserAgentApplication` példány létrehozásakor a konfiguráció során egy naplózó objektum átadásával. Ez a naplózó objektum a következő tulajdonságokkal rendelkezik:

- `localCallback`: egy visszahívási példány, amelyet a fejlesztő biztosíthat a naplók egyéni módon történő felhasználásához és közzétételéhez. A localCallback metódus implementálása attól függően, hogy hogyan szeretné átirányítani a naplókat.
- `level` (nem kötelező): a konfigurálható naplózási szint. A támogatott naplózási szintek a következők: `Error`, `Warning`, `Info`és `Verbose`. A mező alapértelmezett értéke: `Info`.
- `piiLoggingEnabled` (nem kötelező): ha igaz értékre van állítva, a személyes és szervezeti adatnaplókat naplózza. Alapértelmezés szerint ez hamis, így az alkalmazás nem naplózza a személyes adatait. A személyes adatnaplókat soha nem írja az alapértelmezett kimenetekre, például a konzolra, a Logcat vagy a NSLog.
- `correlationId` (nem kötelező): egyedi azonosító, amely a kérelem hibakeresési célú hozzárendelésére szolgál. Az alapértelmezett érték a RFC4122 4-es verziójának GUID-azonosítója (128 bit).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “<Enter your client id>”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>Bejelentkezés az iOS és a macOS MSAL

Állítsa be a visszahívást, hogy rögzítse a MSAL-naplózást, és építse be azt a saját alkalmazás naplózása során. A visszahívás aláírása így néz ki:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Példa:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>Személyes azonosításra alkalmas adatok

Alapértelmezés szerint a MSAL nem rögzíti vagy nem naplózza az összes személyes adatfelvételt. A könyvtár lehetővé teszi, hogy az alkalmazások fejlesztői ezt a MSALLogger osztály egyik tulajdonságán keresztül kapcsolják be. A személyes adatok bekapcsolásával az alkalmazás felelősséget vállal a szigorúan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Naplózási szintek

Ha az iOS-es és macOS-es MSAL használatával jelentkezik be, a naplózási szint beállításához használja a következő értékek egyikét:

|Szint  |Leírás |
|---------|---------|
| `MSALLogLevelNothing`| Az összes naplózás letiltása |
| `MSALLogLevelError` | Alapértelmezett szint, csak akkor jeleníti meg az adatokat, ha hibák történnek |
| `MSALLogLevelWarning` | Figyelmeztetések |
| `MSALLogLevelInfo` |  A könyvtár belépési pontjai, paraméterekkel és különböző kulcstartó műveletekkel |
|`MSALLogLevelVerbose`     |  API-nyomkövetés       |

Példa:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Napló üzenetének formátuma

A MSAL üzenetek részének formátuma `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Példa:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

A korrelációs azonosítók és időbélyegek megadása a problémák nyomon követéséhez hasznos. Az időbélyeg és a korrelációs azonosító információi elérhetők a napló üzenetében. Az egyetlen megbízható hely a MSAL naplózási üzeneteiből.
