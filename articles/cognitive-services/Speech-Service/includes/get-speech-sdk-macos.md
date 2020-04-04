---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 18a2e5118ab8ab30de5cc4dbf75342cc5275256c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656602"
---
A macOS-hez való fejlesztés során három beszédbeszéd-SDK érhető el.

- Az Objective-C Speech SDK natívan kapható CocoaPod csomagként
- A .NET Speech SDK használható a **Xamarin.Mac-kel** a .NET Standard 2.0 megvalósítása kor
- A Python Speech SDK PyPI modulként érhető el

> [!TIP]
> Az Objective-C Speech SDK with Swift használatával kapcsolatos részletekért <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">lásd: C célkitűzés importálása a <span class="docon docon-navigate-external x-hidden-focus"> </span>Swiftbe. </a>

### <a name="system-requirements"></a>Rendszerkövetelmények

- MacOS 10.13-as vagy újabb verziója

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        A macOS CocoaPod csomag letölthető és használható az <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (vagy újabb) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> integrált fejlesztői környezettel (IDE). Először is, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">töltse <span class="docon docon-navigate-external x-hidden-focus"> </span>le a bináris CocoaPod </a>. Bontsa ki a pod ugyanabban a könyvtárban a `pod` tervezett `target`használatra, hozzon létre egy *Podfile* és sorolja fel a .
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        A Xamarin.Mac a .NET fejlesztők számára elérhetővé teszi a teljes macOS SDK-t, hogy natív Mac-alkalmazásokat hozzon létre a C# használatával. További információ: <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK rövid útmutató objektív-C forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK gyorsútmutató Swift forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>