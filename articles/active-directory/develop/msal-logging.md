---
title: MSAL-alkalmazások naplózása | Microsoft Identity platform
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
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268590"
---
# <a name="logging-in-msal-applications"></a>MSAL-alkalmazások naplózása

A Microsoft Authentication Library (MSAL) alkalmazásai olyan naplófájlokat hoznak elő, amelyek segíthetnek a problémák diagnosztizálásában. Egy alkalmazás több sornyi kóddal is konfigurálhatja a naplózást, és egyéni vezérléssel láthatja el a részletességi szintet, valamint azt, hogy a rendszer naplózza-e a személyes és a szervezeti adatokat. Javasoljuk, hogy hozzon létre egy MSAL-naplózási visszahívást, és adja meg a felhasználók számára a naplók küldését a hitelesítési problémák esetén.

## <a name="logging-levels"></a>Naplózási szintek

A MSAL számos naplózási részletességi szintet biztosít:

- Hiba: Azt jelzi, hogy valami hiba történt, és a rendszer hibát generált. Hibakereséshez és a problémák azonosításához használható.
- Figyelmeztetés: Nem szükségszerűen hiba vagy hiba történt, de a diagnosztika és a problémák megoldására szolgál.
- Információ A MSAL a tájékoztató célokra szánt eseményeket naplózza, nem feltétlenül a hibakereséshez.
- részletes Default (Alapértelmezett): A MSAL a könyvtár működésének részletes adatait naplózza.

## <a name="personal-and-organizational-data"></a>Személyes és szervezeti adatkezelés

Alapértelmezés szerint a MSAL-naplózó nem gyűjt kényes személyes vagy szervezeti adatokat. A könyvtár lehetővé teszi a személyes és szervezeti adatnaplózás engedélyezését, ha úgy dönt, hogy erre van szükség.

## <a name="logging-in-msalnet"></a>Naplózás a MSAL.NET

 > [!NOTE]
 > A MSAL.NET kapcsolatos további információkért tekintse meg a [MSAL.net wikit](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). MSAL.NET-naplózási és egyéb példákat is találhat.
 
A 3. x MSAL a naplózás az alkalmazáson belül, a `.WithLogging` Builder módosító használatával van beállítva. Ez a metódus nem kötelező paramétereket vesz igénybe:

- A *szint* lehetővé teszi a kívánt naplózási szint eldöntését. A hibák beállítása csak hibaüzeneteket kap
- A *PiiLoggingEnabled* lehetővé teszi a személyes és szervezeti adatnaplózást, ha az igaz értékre van állítva. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait.
- A *LogCallback* olyan delegált értékre van beállítva, amely a naplózást végzi. Ha a *PiiLoggingEnabled* értéke TRUE (igaz), ez a metódus kétszer kapja meg az üzeneteket: egyszer a *containsPii* paraméter értéke false (hamis), az üzenet pedig személyes adatként nem, a *containsPii* paraméter pedig a true értékkel egyenlő, a másik pedig a az üzenet tartalmazhat személyes adattartalmakat is. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatfájlokat), az üzenet ugyanaz lesz.
- A *DefaultLoggingEnabled* lehetővé teszi a platform alapértelmezett naplózását. Alapértelmezés szerint hamis. Ha úgy állítja be az igaz értéket, hogy az asztali/UWP-alkalmazásokban az esemény-nyomkövetést használja, az iOS-és a logcat-NSLog az Androidon.

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

 ## <a name="logging-in-msaljs"></a>Naplózás a MSAL. js fájlban

 A naplózást a MSAL. js fájlban engedélyezheti, ha átadja egy naplózó objektumot egy `UserAgentApplication` példány létrehozásához a konfiguráció során. Ez a naplózó objektum a következő tulajdonságokkal rendelkezik:

- `localCallback`: egy visszahívási példány, amelyet a fejlesztő biztosíthat a naplók egyéni módon történő felhasználásához és közzétételéhez. A localCallback metódus implementálása attól függően, hogy hogyan szeretné átirányítani a naplókat.

- `level`(nem kötelező): a konfigurálható naplózási szint. A támogatott naplózási szintek a következők: Hiba, figyelmeztetés, információ, részletes. Az alapértelmezett érték az info.

- `piiLoggingEnabled`(nem kötelező): lehetővé teszi a személyes és szervezeti adatnaplózást, ha az igaz értékre van állítva. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait. A személyes adatnaplókat soha nem írja az alapértelmezett kimenetekre, például a konzolra, a Logcat vagy a NSLog. Az alapértelmezett érték false (hamis).

- `correlationId`(nem kötelező): egyedi azonosító, amely a kérésnek a hibakeresési célú hozzárendelésére szolgál. Az alapértelmezett érték a RFC4122 4-es verziójának GUID-azonosítója (128 bit).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
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

A MSAL üzenetek része a következő formátumú:`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Példa:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

A korrelációs azonosítók és időbélyegek megadása a problémák nyomon követéséhez hasznos. Az időbélyeg és a korrelációs azonosító információi elérhetők a napló üzenetében. Az egyetlen megbízható hely a MSAL naplózási üzeneteiből.
