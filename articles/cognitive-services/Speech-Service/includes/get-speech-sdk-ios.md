---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f3864b28ba6f5bdbffe6601d059a21328d56a5e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656634"
---
:::row:::
    :::column span="3":::
        Az iOS-re való fejlesztés során két beszédbeszéd-SDK érhető el. Az Objective-C Speech SDK natívan elérhető iOS CocoaPod csomagként. Másik lehetőségként a .NET Speech SDK használható a Xamarin.iOS-szel a .NET Standard 2.0 megvalósítása ként.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Az Objective-C Speech SDK with Swift használatával kapcsolatos részletekért <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">lásd: C célkitűzés importálása a <span class="docon docon-navigate-external x-hidden-focus"> </span>Swiftbe. </a>

### <a name="system-requirements"></a>Rendszerkövetelmények

- MacOS 10.3-as vagy újabb verziója
- Az iOS 9.3-as vagy újabb verzióinak megcélzása

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Az iOS CocoaPod csomag letölthető és használható az <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (vagy újabb) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> integrált fejlesztői környezettel (IDE). Először is, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">töltse <span class="docon docon-navigate-external x-hidden-focus"> </span>le a bináris CocoaPod </a>. Bontsa ki a pod ugyanabban a könyvtárban a `pod` tervezett `target`használatra, hozzon létre egy *Podfile* és sorolja fel a .
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
        A Xamarin.iOS a teljes iOS SDK-t teszi elérhetővé . A Visual Studio teljes mértékben natív iOS-alkalmazásait C# vagy F# használatával hozhat létre. További információ: <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK rövid útmutató Objective-C forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK gyorsindítás swift forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>