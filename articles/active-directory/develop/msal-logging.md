---
title: Az MSAL alkalmazásokban naplózása |} Az Azure
description: Ismerje meg a Bejelentkezés Microsoft-hitelesítési tár (MSAL) alkalmazások.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544098"
---
# <a name="logging"></a>Naplózás
A Microsoft-hitelesítési tár (MSAL) alkalmazások készítése a naplózási üzeneteket, amelyek segítségével diagnosztizálhatja a problémákat, és adja meg az adatokat. Alkalmazás naplózási konfigurálhatja néhány sornyi kóddal, és egyéni szabályozhatják a részletességi szintet és -e a személyes és munkahelyi adatokat naplózza. Javasoljuk, állítsa be az MSAL naplózási visszahívási, és a egy lehetőséget biztosíthat felhasználóinak a naplók beküldése, ha azok hitelesítési problémák merülnek fel.

## <a name="logging-levels"></a>Naplózási szintek

A MSAL naplózó részletességi szintek számos rögzítési kell lehetővé teszi:

- Hiba: Azt jelzi, hogy probléma merült fel, és a egy hiba történt. Hibakeresés és a problémák azonosítása használható.
- Figyelmeztetés: Kérdés és az alkalmazás össze a további információra van szüksége. Van még nem feltétlenül egy hiba vagy sikertelen lett, de alkalmas a diagnosztikai és a felügyelő problémákat.
- Információ: Az MSAL naplózza az eseményeket nem feltétlenül szánt hibakeresés információs célokat.
- részletes: Default (Alapértelmezett): Az MSAL jelentkezzen egy nagy mennyiségű olyan információt, és a teljes részletességgel, milyen könyvtár viselkedés.

## <a name="personal-and-organizational-data"></a>Személyes és munkahelyi adatok
Alapértelmezés szerint az MSAL naplózó nem szigorúan bizalmas személyes vagy céges adatok rögzítéséhez. A könyvtár engedélyezheti a naplózást a személyes és munkahelyi adatokat, ha úgy dönt, hogy ehhez nyújt.

## <a name="logging-in-msalnet"></a>Bejelentkezés az MSAL.NET
Az MSAL 3.x, naplózás van állítva egy alkalmazás létrehozása az alkalmazás a `.WithLogging` builder-módosítót. Ez a módszer nem kötelező paramétereket fogadja:

- *Szint* lehetővé teszi, hogy Ön döntheti el, a kívánt naplózási szintet. Hibák állítja, csak első hibák
- *PiiLoggingEnabled* lehetővé teszi a bejelentkezést a személyes és munkahelyi adatokat, ha igaz értékre kell állítani. Alapértelmezés szerint ez értéke FALSE, úgy, hogy az alkalmazás nem naplózza a személyes adatok.
- *LogCallback* egy meghatalmazott, amely a naplózás értékre van állítva. Ha *PiiLoggingEnabled* értéke igaz, ez a módszer kétszer az üzeneteket fogja kapni: egyszer az *containsPii* paraméter értéke HAMIS, valamint az üzenet nélkül a személyes adatok és a amásodikalkalommal*containsPii* paraméter értéke TRUE, és az üzenet személyes adatokat tartalmazhat. Bizonyos esetekben (amikor az üzenet nem tartalmaz személyes adatokat) az üzenet azonos lesz.
- *DefaultLoggingEnabled* lehetővé teszi, hogy az alapértelmezett naplózás a platform. Alapértelmezés szerint a False (hamis). Ha ez igaz értékre, használja a események követése az asztali és UWP-alkalmazásokban NSLog iOS-en és androidon logcat.

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
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Bejelentkezés az MSAL.js

 Engedélyezheti a naplózó objektum létrehozásához a konfiguráció során átadásával MSAL.js bejelentkezés egy `UserAgentApplication` példány. A naplózó objektum a következő tulajdonságokkal rendelkezik:

- *localCallback*: a visszahívási példányt, amely a fejlesztő fogadni, és a naplók közzététele egy egyéni módon adható meg. Attól függően, hogyan irányíthat át naplókat szeretné a localCallback metódus megvalósításához.

- *szint* (nem kötelező): a konfigurálható naplózási szintet. A támogatott naplózási szintek a következők: Hiba, figyelmeztetés, információ részletes. Alapértelmezett érték az adatok.

- *piiLoggingEnabled* (nem kötelező): lehetővé teszi a bejelentkezést a személyes és munkahelyi adatokat, ha igaz értékre kell állítani. Alapértelmezés szerint ez értéke HAMIS, hogy az alkalmazás nem naplózza a személyes adatok. Személyes adatokat tartalmazó naplók alapértelmezett kimenetek, mint a konzolon, Logcat vagy NSLog soha nem kerülnek. Alapértelmezett értéke false.

- *korrelációs azonosító* (nem kötelező): egy egyedi azonosítót, a kérelmet, a válasz hibakeresési célra leképezés. Az alapértelmezett RFC4122 4-es verzió guid (128 bit).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
