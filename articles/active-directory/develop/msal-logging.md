---
title: Bejelentkezés az MSAL alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a Microsoft Authentication Library (MSAL) alkalmazások naplózásáról.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084894"
---
# <a name="logging-in-msal-applications"></a>Naplózás az MSAL alkalmazásokban

A Microsoft Authentication Library (MSAL) alkalmazások naplóüzeneteket hoznak létre, amelyek segíthetnek a problémák diagnosztizálásában. Az alkalmazások beállíthatják a naplózást néhány sornyi kóddal, és egyénileg szabályozhatják a részletesség szintjét, valamint azt, hogy a rendszer naplózza-e a személyes és szervezeti adatokat. Javasoljuk, hogy hozzon létre egy MSAL naplózási visszahívást, és lehetővé teszi a felhasználók számára, hogy naplókat küldjenek el, ha hitelesítési problémákkal rendelkeznek.

## <a name="logging-levels"></a>Naplózási szintek

Az MSAL a naplózás részleteinek több szintjét biztosítja:

- Hiba: Azt jelzi, hogy valami elromlott, és hiba történt. Hibakeresésre és problémák azonosítására használható.
- Figyelmeztetés: Nem feltétlenül hiba vagy hiba történt, hanem diagnosztikai és rámutatási problémákra szolgálnak.
- Információ: Az MSAL naplózza a nem feltétlenül hibakeresésre szánt, tájékoztató jellegű eseményeket.
- Részletes: Alapértelmezett. Az MSAL naplózza a könyvtár viselkedésének részleteit.

## <a name="personal-and-organizational-data"></a>Személyes és szervezeti adatok

Alapértelmezés szerint az MSAL naplózó nem rögzít semmilyen rendkívül érzékeny személyes vagy szervezeti adatot. A tár lehetővé teszi a személyes és szervezeti adatok naplózását, ha úgy dönt, hogy így tesz.

Az MSAL-naplózással kapcsolatos részletekért válassza ki a nyelvének megfelelő lapot:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Bejelentkezés MSAL.NET

 > [!NOTE]
 > Tekintse meg a [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) mintákat MSAL.NET fakitermelés és így tovább.

Az MSAL 3.x-ben a naplózás alkalmazásonként van beállítva az alkalmazás létrehozásakor a `.WithLogging` szerkesztő módosító használatával. Ez a módszer nem kötelező paramétereket vesz igénybe:

- `Level`lehetővé teszi annak eldöntését, hogy milyen szintű naplózást szeretne. Ha a beállítás a hibák csak akkor kap hibákat
- `PiiLoggingEnabled`lehetővé teszi a személyes és szervezeti adatok naplózását, ha igaz értékre van állítva. Alapértelmezés szerint ez hamis, így az alkalmazás nem naplózza a személyes adatokat.
- `LogCallback`olyan delegáltra van állítva, amely a naplózást végzi. Ha `PiiLoggingEnabled` ez igaz, akkor ez a módszer `containsPii` kétszer kapja meg az üzeneteket: egyszer a paraméter `containsPii` egyenlő hamis, és az üzenet személyes adatok nélkül, és másodszor a paraméter értéke igaz, és az üzenet személyes adatokat tartalmazhat. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatokat), az üzenet ugyanaz lesz.
- `DefaultLoggingEnabled`lehetővé teszi a platform alapértelmezett naplózását. Alapértelmezés szerint ez hamis. Ha igazra állítja, akkor az Asztali / UWP alkalmazásokban, az NSLog az iOS-en és az Android logcat-ban használja az Eseménykövetést.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Bejelentkezés az Android MSAL-ban Java használatával

Naplózási visszahívás létrehozásával kapcsolja be a bejelentkezést az alkalmazáslétrehozásánál. A visszahívás a következő paramétereket veszi figyelembe:

- `tag`a könyvtár által visszahívott karakterlánc. A naplóbejegyzéshez van társítva, és a naplózási üzenetek rendezésére használható.
- `logLevel`lehetővé teszi annak eldöntését, hogy milyen szintű naplózást szeretne. A támogatott naplószintek `Error` `Warning`a `Info`következők: , , , és `Verbose`.
- `message`a naplóbejegyzés tartalma.
- `containsPII`megadja, hogy a személyes adatokat tartalmazó üzenetek vagy a szervezeti adatok naplózása. Alapértelmezés szerint ez hamis, így az alkalmazás nem naplózza a személyes adatokat. Ha `containsPII` `true`igen, akkor ez a módszer kétszer `containsPII` kapja `false` meg `message` az üzeneteket: egyszer a `containsPii` paraméter beállítása `true` és a személyes adatok nélküli paraméter, másodszor pedig a paraméter beállítása, és az üzenet személyes adatokat tartalmazhat. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatokat), az üzenet ugyanaz lesz.

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

Alapértelmezés szerint az MSAL naplózó nem rögzít semmilyen személyes azonosításra alkalmas információt vagy szervezeti azonosításra alkalmas információt.
A személyes azonosításra alkalmas adatok vagy szervezeti azonosítható adatok naplózásának engedélyezése:

```java
Logger.getInstance().setEnablePII(true);
```

A személyes adatok és szervezeti adatok naplózásának letiltása:

```java
Logger.getInstance().setEnablePII(false);
```

Alapértelmezés szerint a logcat-ba való naplózás le van tiltva. Az engedélyezéshez:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Engedélyezze a naplózást az MSAL.js (JavaScript) fájlban `UserAgentApplication` úgy, hogy egy naplózó objektumot ad át a példány létrehozásához a konfiguráció során. Ez a naplózó objektum a következő tulajdonságokkal rendelkezik:

- `localCallback`: a fejlesztő által a naplók egyéni felhasználásához és közzétételéhez biztosított visszahívási példány. Valósítsa meg a localCallback metódust attól függően, hogy hogyan szeretné átirányítani a naplókat.
- `level`(nem kötelező): a konfigurálható naplószint. A támogatott naplószintek `Error` `Warning`a `Info`következők: , , , és `Verbose`. A mező alapértelmezett értéke: `Info`.
- `piiLoggingEnabled`(nem kötelező): ha igaz értékre van állítva, személyes és szervezeti adatokat naplóz. Alapértelmezés szerint ez hamis, így az alkalmazás nem naplózza a személyes adatokat. A személyes adatnaplók soha nem kerülnek alapértelmezett kimenetekbe, például a Console, a Logcat vagy az NSLog.
- `correlationId`(nem kötelező): egyedi azonosító, amely a kérelem és a hibakeresés céljából adott válasz leképezésére szolgál. Alapértelmezés szerint az RFC4122 4-es verziója guid (128 bit).

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

## <a name="objective-c"></a>[Célkitűzés-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL iOS és macOS naplózás-ObjC

Állítsa be a visszahívást az MSAL-naplózás rögzítéséhez és a saját alkalmazás naplózásába való beépítéséhez. A visszahívás aláírása így néz ki:

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

### <a name="personal-data"></a>Személyes adatok

Alapértelmezés szerint az MSAL nem rögzít és nem naplóz személyes adatokat. A könyvtár lehetővé teszi az alkalmazásfejlesztők számára, hogy bekapcsolják ezt az MSALLogger osztály egyik tulajdonságán keresztül. A bekapcsolásával `pii.Enabled`az alkalmazás felelősséget vállal a rendkívül érzékeny adatok biztonságos kezeléséért és a szabályozási követelmények betartatásáért.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Naplózási szintek

Ha a naplózási szintet az MSAL iOS és macOS rendszerhez való bejelentkezéskor szeretné beállítani, használja az alábbi értékek egyikét:

|Szint  |Leírás |
|---------|---------|
| `MSALLogLevelNothing`| Az összes naplózás letiltása |
| `MSALLogLevelError` | Alapértelmezett szint, csak akkor nyomtat információt, ha hiba történik |
| `MSALLogLevelWarning` | Figyelmeztetések |
| `MSALLogLevelInfo` |  Könyvtár belépési pontjai paraméterekkel és különböző kulcskarika-műveletekkel |
|`MSALLogLevelVerbose`     |  API-nyomkövetés |

Példa:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Naplóüzenet formátuma

Az MSAL naplóüzenetek üzenetrésze`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Példa:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Korrelációs azonosítók és időbélyegek megadása hasznos a problémák nyomon követéséhez. Az időbélyeg- és korrelációs azonosító adatai a naplóüzenetben érhetők el. Az egyetlen megbízható hely, ahol lekérheti őket, az MSAL naplózási üzenetek.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL iOS és macOS naplózás-Swift

Állítsa be a visszahívást az MSAL-naplózás rögzítéséhez és a saját alkalmazás naplózásába való beépítéséhez. A visszahívás aláírása (a C célkitűzésben) a következőképpen néz ki:

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

### <a name="personal-data"></a>Személyes adatok

Alapértelmezés szerint az MSAL nem rögzít és nem naplóz személyes adatokat. A könyvtár lehetővé teszi az alkalmazásfejlesztők számára, hogy bekapcsolják ezt az MSALLogger osztály egyik tulajdonságán keresztül. A bekapcsolásával `pii.Enabled`az alkalmazás felelősséget vállal a rendkívül érzékeny adatok biztonságos kezeléséért és a szabályozási követelmények betartatásáért.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Naplózási szintek

Ha a naplózási szintet az MSAL iOS és macOS rendszerhez való bejelentkezéskor szeretné beállítani, használja az alábbi értékek egyikét:

|Szint  |Leírás |
|---------|---------|
| `MSALLogLevelNothing`| Az összes naplózás letiltása |
| `MSALLogLevelError` | Alapértelmezett szint, csak akkor nyomtat információt, ha hiba történik |
| `MSALLogLevelWarning` | Figyelmeztetések |
| `MSALLogLevelInfo` |  Könyvtár belépési pontjai paraméterekkel és különböző kulcskarika-műveletekkel |
|`MSALLogLevelVerbose`     |  API-nyomkövetés |

Példa:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Naplóüzenet formátuma

Az MSAL naplóüzenetek üzenetrésze`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Példa:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Korrelációs azonosítók és időbélyegek megadása hasznos a problémák nyomon követéséhez. Az időbélyeg- és korrelációs azonosító adatai a naplóüzenetben érhetők el. Az egyetlen megbízható hely, ahol lekérheti őket, az MSAL naplózási üzenetek.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL Java naplózáshoz

Az MSAL java-hoz lehetővé teszi az alkalmazással már használt naplózási könyvtár használatát, feltéve, hogy az kompatibilis az SLF4J-vel. MSAL java használja az [egyszerű naplózáshomlokzat java](http://www.slf4j.org/) (SLF4J), mint egy egyszerű homlokzat vagy absztrakció a különböző naplózási keretek, mint például [a java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) és [Log4j](https://logging.apache.org/log4j/2.x/). Az SLF4J lehetővé teszi a felhasználó számára, hogy a telepítéskor csatlakoztassa a kívánt naplózási keretrendszert.

Ha például a Logback-et szeretné naplózási keretrendszerként használni az alkalmazásban, adja hozzá a Logback-függőséget az alkalmazás Maven pom-fájljához:

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

Az SLF4J automatikusan kötődik a Logback-hez az üzembe helyezéskor. Az MSAL-naplók a konzolra kerülnek.

A más naplózási keretrendszerekhez való kötéssel kapcsolatos tudnivalókért tekintse meg az [SLF4J kézikönyvét.](http://www.slf4j.org/manual.html)

### <a name="personal-and-organization-information"></a>Személyes és szervezeti adatok

Alapértelmezés szerint az MSAL naplózása nem rögzít és nem naplóz személyes vagy szervezeti adatokat. A következő példában a személyes vagy szervezeti adatok naplózása alapértelmezés szerint ki van kapcsolva:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Kapcsolja be a személyes és `logPii()` szervezeti adatok naplózását az ügyfélalkalmazás-szerkesztő beállításával. Ha bekapcsolja a személyes vagy szervezeti adatok naplózását, az alkalmazásnak felelősséget kell vállalnia a rendkívül érzékeny adatok biztonságos kezeléséért és a szabályozási követelmények nek való megfelelésért.

A következő példában a személyes vagy szervezeti adatok naplózása engedélyezve van:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL python naplózáshoz

Az MSAL Python naplózása a szabványos `logging.info("msg")` Python naplózási mechanizmust használja, például az MSAL naplózást a következőképpen konfigurálhatja (és működés közben láthatja a [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Hibakeresési naplózás engedélyezése az összes modulban

Alapértelmezés szerint a naplózás bármely Python-parancsfájl ki van kapcsolva. Ha engedélyezni szeretné a hibakeresési naplózást a teljes Python-parancsfájl összes moduljához, használja a következőket:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Csend csak MSAL fakitermelés

Ha csak az MSAL-könyvtárnaplózást szeretné elhallgattatni, miközben engedélyezni szeretné a hibakeresési naplózást a Python-parancsfájl összes többi moduljában, kapcsolja ki az MSAL Python által használt naplózót:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Személyes és szervezeti adatok a Pythonban

Az MSAL for Python nem naplózza a személyes adatokat vagy a szervezeti adatokat. Nincs olyan tulajdonság, amely be- vagy kikapcsolja a személyes vagy szervezeti adatok naplózását.

A szabványos Python-naplózás segítségével bármit naplózhat, de ön felelős a bizalmas adatok biztonságos kezeléséért és a szabályozási követelmények bekövetéséért.

A Pythonban való naplózásról a [Pythonnaplózás útmutatójában](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)talál további információt.

---
