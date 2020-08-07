---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 07/31/2020
ms.author: rgarcia
ms.openlocfilehash: 310c0f547ee11a3243589c364755a30a84be1a25
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810172"
---
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

* Olvassa el az [Azure térbeli horgonyok áttekintése című témakört](../articles/spatial-anchors/overview.md).
* Az [5 perces rövid](../articles/spatial-anchors/index.yml)útmutatók egyikét fejezte be.
* A C# és az Unity alapszintű ismerete.
* A <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> alapszintű ismerete, ha androidos vagy <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> -t szeretne használni, ha iOS-et szeretne használni.
* Egy Windows rendszerű számítógép, amelyen a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> -es vagy újabb verziója telepítve van a **ASP.net és a webes fejlesztési** munkaterheléssel.
* A [.net Core 3,1 SDK](https://dotnet.microsoft.com/download).
* Egy vagy több eszköz (iOS vagy Android), amelyen üzembe helyezheti és futtathatja az alkalmazást.
  * Android használata esetén a következőkre lesz szüksége:
    * <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3,3</a> vagy újabb, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,4 (LTS)</a>és <a href="https://git-scm.com/download/win" target="_blank">git for Windows</a> telepítve a Windows rendszerű számítógépén.
    * Egy <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztői</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-</a> kompatibilis Android-eszköz.
  * Ha iOS-et használ, a következőkre lesz szüksége:
    * Egy macOS rendszerű számítógép, amelyen telepítve van a <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> vagy újabb, a <a href="https://cocoapods.org" target="_blank">CocoaPods</a>és az <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,4 (LTS)</a> .
    * A fejlesztők által engedélyezett <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibilis iOS-</a> eszköz.
    * A git telepítése a Homebrew használatával történik. Adja meg a következő parancsot a terminál egyetlen sorában: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` majd futtassa a parancsot `brew install git` .
