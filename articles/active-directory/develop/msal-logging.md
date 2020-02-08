---
title: Bejelentkezés MSAL-alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a Microsoft Authentication Library-(MSAL-) alkalmazások naplózásáról.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084894"
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

Egy adott nyelv MSAL-naplózási adatainak megtekintéséhez válassza a nyelvének megfelelő fület:

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

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

## <a name="androidtabandroid"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Bejelentkezés a MSAL for Android használatával Javával

Jelentkezzen be az alkalmazás létrehozásakor egy naplózási visszahívás létrehozásával. A visszahívás a következő paramétereket veszi figyelembe:

- `tag` a könyvtár által a visszahívás számára átadott karakterlánc. A naplóbejegyzés társítva van, és a naplózási üzenetek rendezésére használható.
- `logLevel` lehetővé teszi a kívánt naplózási szint eldöntését. A támogatott naplózási szintek a következők: `Error`, `Warning`, `Info`és `Verbose`.
- `message` a naplóbejegyzés tartalma.
- `containsPII` megadja, hogy a rendszer naplózza-e a személyes vagy szervezeti adatelemeket tartalmazó üzeneteket. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait. Ha `containsPII` `true`, akkor ez a módszer kétszer fog megjelenni az üzenetekben: egyszer a `containsPII` paraméter értéke `false` és a `message` személyes érték nélkül, és egy második alkalommal, amikor a `containsPii` paraméter `true` értékre van állítva, és az üzenet tartalmazhat személyes adattípust. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatfájlokat), az üzenet ugyanaz lesz.

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

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

 Engedélyezze a naplózást a MSAL. js (JavaScript) alkalmazásban egy `UserAgentApplication`-példány létrehozásakor a konfiguráció során egy naplózó objektum átadásával. Ez a naplózó objektum a következő tulajdonságokkal rendelkezik:

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
        clientId: "<Enter your client id>",
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

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL iOS és macOS rendszerű naplózáshoz – ObjC

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

Például:

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

### <a name="personal-data"></a>Saját adatok

A MSAL alapértelmezés szerint nem rögzíti vagy nem naplózza a személyes adatokkal kapcsolatos adatgyűjtést. A könyvtár lehetővé teszi, hogy az alkalmazások fejlesztői ezt a MSALLogger osztály egyik tulajdonságán keresztül kapcsolják be. A `pii.Enabled`bekapcsolásával az alkalmazás felelősséget vállal a szigorúan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Naplózási szintek

Ha az iOS-es és macOS-es MSAL használatával jelentkezik be, a naplózási szint beállításához használja a következő értékek egyikét:

|Szint  |Leírás |
|---------|---------|
| `MSALLogLevelNothing`| Az összes naplózás letiltása |
| `MSALLogLevelError` | Alapértelmezett szint, csak akkor jeleníti meg az adatokat, ha hibák történnek |
| `MSALLogLevelWarning` | Figyelmeztetések |
| `MSALLogLevelInfo` |  A könyvtár belépési pontjai, paraméterekkel és különböző kulcstartó műveletekkel |
|`MSALLogLevelVerbose`     |  API-nyomkövetés |

Például:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Napló üzenetének formátuma

A MSAL üzenetek részének formátuma `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Például:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

A korrelációs azonosítók és időbélyegek megadása a problémák nyomon követéséhez hasznos. Az időbélyeg és a korrelációs azonosító információi elérhetők a napló üzenetében. Az egyetlen megbízható hely a MSAL naplózási üzeneteiből.

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL iOS és macOS rendszerű naplózáshoz – Swift

Állítsa be a visszahívást, hogy rögzítse a MSAL-naplózást, és építse be azt a saját alkalmazás naplózása során. A visszahíváshoz tartozó aláírás (az Objective-C-ben) a következőképpen néz ki:

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

Például:

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

### <a name="personal-data"></a>Saját adatok

A MSAL alapértelmezés szerint nem rögzíti vagy nem naplózza a személyes adatokkal kapcsolatos adatgyűjtést. A könyvtár lehetővé teszi, hogy az alkalmazások fejlesztői ezt a MSALLogger osztály egyik tulajdonságán keresztül kapcsolják be. A `pii.Enabled`bekapcsolásával az alkalmazás felelősséget vállal a szigorúan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

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
|`MSALLogLevelVerbose`     |  API-nyomkövetés |

Például:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Napló üzenetének formátuma

A MSAL üzenetek részének formátuma `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Például:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

A korrelációs azonosítók és időbélyegek megadása a problémák nyomon követéséhez hasznos. Az időbélyeg és a korrelációs azonosító információi elérhetők a napló üzenetében. Az egyetlen megbízható hely a MSAL naplózási üzeneteiből.

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL a Java-naplózáshoz

A MSAL for Java lehetővé teszi, hogy az alkalmazással már használt naplózási könyvtárat használja, amennyiben az kompatibilis a SLF4J-mel. A MSAL for Java a Java (SLF4J) egyszerű [naplózási](http://www.slf4j.org/) homlokzatát használja egyszerű homlokzatként vagy absztraktként a különböző naplózási keretrendszerek, például a [Java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), a [Logback](http://logback.qos.ch/) és a [Log4j](https://logging.apache.org/log4j/2.x/). A SLF4J lehetővé teszi a felhasználó számára, hogy a központi telepítési időszakban csatlakoztassa a kívánt naplózási keretrendszert.

Ha például a Logback-t az alkalmazás naplózási keretrendszereként szeretné használni, adja hozzá a Logback függőséget az alkalmazás Maven Pom-fájljához:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Ezután adja hozzá a Logback konfigurációs fájlt:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

A SLF4J a telepítéskor automatikusan kötést hoz létre a Logback. A rendszer a MSAL-naplókat a konzolra írja.

A más naplózási keretrendszerek kötésével kapcsolatos utasításokért tekintse meg a [SLF4J kézikönyvét](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Személyes és szervezeti adatok

Alapértelmezés szerint a MSAL naplózása nem rögzíti vagy nem naplózza személyes vagy szervezeti adataikat. A következő példában a személyes vagy szervezeti adatai naplózása alapértelmezés szerint ki van kapcsolva:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

A személyes és szervezeti adatnaplózás bekapcsolásához állítsa be `logPii()` az ügyfélalkalmazás-szerkesztőben. Ha bekapcsolja a személyes vagy szervezeti adatnaplózást, az alkalmazásnak felelősséget kell vállalnia a fokozottan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények teljesítéséhez.

A következő példában a személyes vagy szervezeti adatai naplózása engedélyezve van:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL Python-naplózáshoz

A MSAL Python-ba való bejelentkezés a szabványos Python-naplózási mechanizmust használja, például `logging.info("msg")` a következőképpen konfigurálhatja a MSAL-naplózást (és megtekintheti működés közben a [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Hibakeresési naplózás engedélyezése az összes modulhoz

Alapértelmezés szerint a Python-parancsfájlok naplózása ki van kapcsolva. Ha engedélyezni szeretné a hibakeresési naplózást a teljes Python-parancsfájl összes moduljához, használja a következőt:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Csak csendben lévő MSAL-naplózás

Ha csak a MSAL szeretné meghallgatni, miközben a Python-szkript összes többi moduljában engedélyezi a hibakeresési naplózást, kapcsolja ki a MSAL Python által használt naplózó:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Személyes és szervezeti adatgyűjtés a Pythonban

A MSAL for Python nem naplózza a személyes és a szervezeti adatszolgáltatásokat. Nincs olyan tulajdonság, amely be-vagy kikapcsolja a személyes vagy szervezeti adatnaplózást.

A standard Python-naplózással bármit naplózhat, amit csak szeretne, de Ön felelős a bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

A Pythonban történő naplózással kapcsolatos további információkért tekintse meg a Python [naplózási](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)útmutatóit.

---
