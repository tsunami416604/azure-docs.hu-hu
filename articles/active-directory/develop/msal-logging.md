---
title: MSAL-alkalmazások naplózása | Azure
description: Tudnivalók a Microsoft Authentication Library-(MSAL-) alkalmazások naplózásáról.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 475b692a29edf5cdd05552e7b5c3dc5fde210275
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512525"
---
# <a name="logging"></a>Naplózás
A Microsoft Authentication Library (MSAL) alkalmazásai olyan naplófájlok előállítására szolgálnak, amelyek segíthetnek a problémák diagnosztizálásában és a részletekben. Egy alkalmazás több sornyi kóddal is konfigurálhatja a naplózást, és egyéni vezérléssel láthatja el a részletességi szintet, valamint azt, hogy a rendszer naplózza-e a személyes és a szervezeti adatokat. Javasoljuk, hogy állítson be egy MSAL-naplózási visszahívást, és adjon meg egy módszert a felhasználók számára a hitelesítési problémákkal rendelkező naplók elküldéséhez.

## <a name="logging-levels"></a>Naplózási szintek

A MSAL naplózó segítségével több részletességi szintet is rögzíthet:

- Hiba: Azt jelzi, hogy valami hiba történt, és a rendszer hibát generált. Hibakereséshez és a problémák azonosításához használható.
- Figyelmeztetés: A kérdéses eseményekről és az alkalmazásról további információra van szükség. Nem szükségszerűen hiba vagy hiba történt, de a diagnosztika és a problémák megoldására szolgál.
- Információ A MSAL a tájékoztató célokra szánt eseményeket naplózza, nem feltétlenül a hibakereséshez.
- Részletes Default (Alapértelmezett): A MSAL nagy mennyiségű információt fog naplózni, és részletesen ismerteti a könyvtár viselkedését.

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

- *localCallback*: egy visszahívási példány, amelyet a fejlesztő biztosíthat a naplók egyéni módon történő felhasználásához és közzétételéhez. A localCallback metódus implementálása attól függően, hogy hogyan szeretné átirányítani a naplókat.

- *szint* (nem kötelező): a konfigurálható naplózási szint. A támogatott naplózási szintek a következők: Hiba, figyelmeztetés, információ, részletes. Az alapértelmezett érték az info.

- *piiLoggingEnabled* (nem kötelező): lehetővé teszi a személyes és szervezeti adatnaplózást, ha az igaz értékre van állítva. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait. A személyes adatnaplókat soha nem írja az alapértelmezett kimenetekre, például a konzolra, a Logcat vagy a NSLog. Az alapértelmezett érték false (hamis).

- *correlationId* (nem kötelező): egyedi azonosító, amely a kérésnek a hibakeresési célú hozzárendelésére szolgál. Az alapértelmezett érték a RFC4122 4-es verziójának GUID-azonosítója (128 bit).

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
