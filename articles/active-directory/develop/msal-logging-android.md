---
title: Hibák és kivételek naplózása az Android rendszerhez készült MSAL.
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan naplózhatja a hibákat és a kivételeket a MSAL for Android alkalmazásban.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: b5d11c1bd091e3d7fbe5be87004b91f242e807f5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763406"
---
# <a name="logging-in-msal-for-android"></a>Naplózás az Androidhoz készült MSAL-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Bejelentkezés a MSAL for Android használatával Javával

Jelentkezzen be az alkalmazás létrehozásakor egy naplózási visszahívás létrehozásával. A visszahívás a következő paramétereket veszi figyelembe:

- `tag` a könyvtár által visszahívásra átadott karakterlánc. A naplóbejegyzés társítva van, és a naplózási üzenetek rendezésére használható.
- `logLevel` lehetővé teszi a kívánt naplózási szint eldöntését. A támogatott naplózási szintek a következők:,, `Error` `Warning` `Info` és `Verbose` .
- `message` a naplóbejegyzés tartalma.
- `containsPII` Megadja, hogy a rendszer naplózza-e a személyes vagy szervezeti adatüzeneteket tartalmazó üzeneteket. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait. Ha `containsPII` `true` Ez a módszer, akkor a metódus kétszer fogja fogadni az üzeneteket: egyszer a (z) `containsPII` `false` és a személyes adatként megadott paraméterrel `message` , és egy második alkalommal, amelynél a `containsPii` paraméter be van állítva, `true` és az üzenet személyes adattal is rendelkezhet. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatfájlokat), az üzenet ugyanaz lesz.

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

## <a name="next-steps"></a>További lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.