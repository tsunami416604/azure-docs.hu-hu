---
title: BeszédsDK-naplózás – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: További információ a beszédfelismerési SDK-ban (C++, C#, Python, Objective-C, Java) való naplózás engedélyezéséről.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805790"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Naplózás engedélyezése a beszédfelismerési SDK-ban

A fájlba való bejelentkezés a beszédstifájl nem kötelező szolgáltatása. A fejlesztés naplózása során további információkat és diagnosztikát biztosít a beszédska fő összetevőiből. Ez úgy engedélyezhető, `Speech_LogFilename` hogy a tulajdonságot egy beszédkonfigurációs objektumon a naplófájl helyére és nevére állítja be. A naplózás globálisan aktiválódik, amint létrejön egy felismerő abból a konfigurációból, és később nem tiltható le. Futó naplózási munkamenet során nem módosíthatja a naplófájl nevét.

> [!NOTE]
> A naplózás a Speech SDK 1.4.0-s verziója óta érhető el az összes támogatott Beszéd SDK programozási nyelven, a JavaScript kivételével.

## <a name="sample"></a>Sample

A naplófájl neve egy konfigurációs objektumon van megadva. A `SpeechConfig` példaként való figyelembe véve, és `config`feltételezve, hogy létrehozott egy példányt, aminek neve:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

A konfigurációs objektumból létrehozhat egy felismerőt. Ez lehetővé teszi a naplózást az összes felismerő számára.

> [!NOTE]
> Ha létrehoz `SpeechSynthesizer` egy konfigurációs objektumból, az nem engedélyezi a naplózást. Ha a naplózás engedélyezve van, akkor `SpeechSynthesizer`a tól is kap diagnosztikát.

## <a name="create-a-log-file-on-different-platforms"></a>Naplófájl létrehozása különböző platformokon

Windows vagy Linux esetén a naplófájl bármely elérési úton lehet, amelyhez a felhasználó írási engedéllyel rendelkezik. Előfordulhat, hogy a más operációs rendszerek fájlrendszerének helyeire vonatkozó írási engedélyek alapértelmezés szerint korlátozottak vagy korlátozottak.

### <a name="universal-windows-platform-uwp"></a>Univerzális Windows-platform (UWP)

Az UWP-alkalmazásoknak naplófájlokat kell elhelyezniük az alkalmazás adathelyeinek egyikében (helyi, roaming vagy ideiglenes). Naplófájl hozható létre a helyi alkalmazásmappában:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Az UWP-alkalmazások fájlhozzáférési engedélyéről [itt](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)olvashat.

### <a name="android"></a>Android

A naplófájlokat belső tárolóba, külső tárolóba vagy a gyorsítótárkönyvtárba mentheti. A belső tárolóban vagy a gyorsítótárkönyvtárban létrehozott fájlok az alkalmazás magánjellegűek. Célszerű létrehozni egy naplófájlt a külső tárolóban.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

A fenti kód egy naplófájlt ment a külső tárolóba egy alkalmazásspecifikus könyvtár gyökérkönyvtárába. A felhasználó a fájlkezelővel (általában `Android/data/ApplicationName/logfile.txt`a) érheti el a fájlt. A fájl az alkalmazás eltávolításakor törlődik.

Engedélyt kell kérnie `WRITE_EXTERNAL_STORAGE` a jegyzékfájlban is:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Az Android-alkalmazások adatairól és fájltárolásáról [itt](https://developer.android.com/guide/topics/data/data-storage.html)olvashat.

#### <a name="ios"></a>iOS

Csak az alkalmazás sandboxán belüli könyvtárak érhetők el. A fájlok a dokumentumokban, a tárban és az ideiglenes könyvtárakban hozhatók létre. A dokumentumkönyvtárban lévő fájlok a felhasználó számára is elérhetővé tehetők. A következő kódrészlet egy naplófájl létrehozását mutatja az alkalmazás dokumentumkönyvtárában:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Létrehozott fájl eléréséhez adja hozzá az `Info.plist` alábbi tulajdonságokat az alkalmazás tulajdonságlistájához:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

További információk az iOS fájlrendszerről [itt](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)érhető el.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fedezze fel mintáinkat a GitHubon](https://aka.ms/csspeech/samples)
