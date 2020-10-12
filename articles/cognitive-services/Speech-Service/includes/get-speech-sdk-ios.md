---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399909"
---
:::row:::
    :::column span="3":::
        Az iOS rendszerre való fejlesztéskor két beszédfelismerési SDK érhető el. A Objective-C Speech SDK natív módon iOS CocoaPod-csomagként érhető el. Azt is megteheti, hogy a .NET Speech SDK-t a Xamarin. iOS használatával is használhatja, mivel az a .NET Standard 2,0-es implementációja.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> A Swift Objective-C Speech SDK használatával kapcsolatos részletekért lásd: az <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-c importálása Swift <span class="docon docon-navigate-external x-hidden-focus"></span> -be </a>.

### <a name="system-requirements"></a>Rendszerkövetelmények

- MacOS 10,3-es vagy újabb verzió
- Cél iOS 9,3 vagy újabb

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Az iOS CocoaPod csomag letölthető és használható a <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (vagy újabb) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> integrált fejlesztési környezettel (ide). Először <a href="https://aka.ms/csspeech/iosbinary" target="_blank">töltse le a bináris CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Bontsa ki a pod-t ugyanabban a címtárban a kívánt használatra, hozzon létre egy *cocoapods* , és sorolja fel a következőt: `pod` `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        A Xamarin. iOS a .NET-fejlesztők számára készült iOS SDK-t teszi elérhetővé. Teljes natív iOS-alkalmazások készítése C# vagy F # használatával a Visual Studióban. További információ: <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin. iOS <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK gyors üzembe helyezési célkitűzés – C forráskód <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK gyors útmutató Swift-forráskódja <span class="docon docon-navigate-external x-hidden-focus"></span></a>