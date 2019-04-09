---
title: Beszéd SDK-naplózás – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés SDK naplózásának engedélyezése.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012448"
---
# <a name="enable-logging-in-the-speech-sdk"></a>A beszédfelismerés SDK naplózásának engedélyezése

Naplózás fájlt egy olyan opcionális szolgáltatás, beszédfelismerés SDK. A fejlesztés során naplózás további információkat és diagnosztikához kínál megoldást a Speeck SDK alapvető összetevői a. A tulajdonság beállításával engedélyezheti `Speech_LogFilename` speech-konfigurációs objektum a hely és a naplófájl nevét. Naplózás aktiválódik globálisan egy felismerő konfigurációtól való létrehozása után, és ezt követően nem lehet letiltani. A naplófájl neve nem módosítható egy futó naplózási munkamenet során.

> [!NOTE]
> Naplózás a programozási nyelvek, JavaScript kivételével az összes támogatott beszéd SDK-ban érhető el.

## <a name="sample"></a>Sample

A naplófájl nevét egy konfigurációs objektumot van megadva. Véve a `SpeechConfig` például és feltételezve, hogy létrehozott egy példányt nevű `config`:

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

A konfigurációs objektum egy felismerő hozhat létre. Ez lehetővé teszi az összes felismerő naplózását.

> [!NOTE]
> Ha létrehoz egy `SpeechSynthesizer` a konfigurációs objektum, program nem engedélyezi a naplózást. Naplózás engedélyezve van, ha a rendszer, ha a diagnosztikai is kap a `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Különböző platformokon naplófájl létrehozása

Windows vagy Linux esetén lehet a naplófájl minden olyan elérési utat, a felhasználó rendelkezik írási engedéllyel. Más operációs rendszerekben fájlrendszerbeli helyeken írási jogosultságot korlátozható vagy korlátozott alapértelmezés szerint.

### <a name="universal-windows-platform-uwp"></a>Univerzális Windows Platform (UWP)

UWP-alkalmazások kell lennie a helyek naplófájlokat az alkalmazás adatokat (helyi, központi vagy ideiglenes) helyen. Egy naplófájlt is hozhatók létre az alkalmazás helyi mappában:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Több fájl hozzáférés UWP-alkalmazások engedély érhető el [Itt](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Belső tárhely, a külső tárhelyen, vagy a gyorsítótár könyvtárához mentheti egy naplófájlt. Fájlok létrehozása a belső tárolóban, vagy az alkalmazás a saját gyorsítótár könyvtárát. Célszerű a naplófájl létrehozása a külső tárhelyen található.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

A fenti kód egy naplófájl menti a külső tárolóeszközről az alkalmazásspecifikus címtár gyökerében. Egy felhasználó hozzáférhessen a fájlt a Fájlkezelőben az (általában `Android/data/ApplicationName/logfile.txt`). A fájl törlődik, ha az alkalmazás el lesz távolítva.

Emellett meg kell kérnie `WRITE_EXTERNAL_STORAGE` engedéllyel a jegyzékfájlban:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

További információ az adat- és Android-alkalmazásokhoz érhető el tárhely [Itt](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Csak az alkalmazás tesztkörnyezetben belül könyvtárak érhetők el. Fájlok a dokumentumok, könyvtár és ideiglenes könyvtárakat hozható létre. A Dokumentumok könyvtárban található fájlok rendelkezésre álljon egy felhasználó számára. Az alábbi kódrészlet látható az alkalmazás dokumentumok könyvtárába naplófájl létrehozása:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Egy létrehozott fájl hozzáférése, adja hozzá az alábbi tulajdonságokat a `Info.plist` az alkalmazás tulajdonságlista:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

További információk az eszközöknek fájlrendszer érhető el [Itt](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a minták a Githubon](https://aka.ms/csspeech/samples)

