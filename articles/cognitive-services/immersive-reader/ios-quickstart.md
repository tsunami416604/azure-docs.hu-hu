---
title: 'Gyors útmutató: Hozzon létre egy iOS-alkalmazást, amely elindítja a részletes olvasót (Swift)'
titlesuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy teljesen új iOS-alkalmazást fog létrehozni, és hozzáadja a magával ragadó olvasó funkcióit.
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: 4b2bdc1a2babadbe5cb7f12135de0eaef8557a97
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950799"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Gyors útmutató: Hozzon létre egy iOS-alkalmazást, amely elindítja a részletes olvasót (Swift)

A teljes [olvasó](https://www.onenote.com/learningtools) egy olyan, integráltan kialakított eszköz, amely bevált technikákat valósít meg az olvasási szövegértés javítására.

Ebben a rövid útmutatóban egy iOS-alkalmazást hoz létre a semmiből, és integrálja a magával ragadó olvasót a saját olvasó SDK használatával. Ebben a rövid útmutatóban egy teljes körű működő [](https://github.com/microsoft/immersive-reader-sdk/iOS/samples/quickstart-swift)minta érhető el.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* [XCode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* A Azure Active Directory-(Azure AD-) hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./azure-active-directory-authentication.md) . A minta projekt tulajdonságainak konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Hozzon létre egy új projektet a Xcode-ben.

![Új projekt](./media/ios/xcode-create-project.png)

Válassza az egynézetes **alkalmazás**lehetőséget.

![Új egynézetes alkalmazás](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Az SDK-CocoaPod beszerzése
A részletes olvasói SDK a CocoaPods-on keresztül a legkönnyebben használható. Telepítés a Cocoapods-on keresztül:
1. A [Cocoapods telepítése](http://guides.cocoapods.org/using/getting-started.html) – kövesse az első lépéseket ismertető útmutatót a Cocoapods telepítéséhez.
2. Hozzon létre egy cocoapods `pod init` a Xcode projekt gyökérkönyvtárában való futtatásával.
3.  A hozzáadásával `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/iOS/immersive-reader-sdk'`adja hozzá a CocoaPod a cocoapods. A pofile a következőhöz hasonlóan kell kinéznie, és a cél neve a gyors üzembe helyezés:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. A terminálban, a Xcode-projekt könyvtárában futtassa a parancsot `pod install` a magától elolvasó SDK-Pod telepítéséhez.
5. Adja `import immersive_reader_sdk` hozzá az összes olyan fájlhoz, amelyre hivatkozni kell az SDK-ra.
6. Győződjön meg arról, hogy megnyitja a projektet `.xcworkspace` a fájl megnyitásával `.xcodeproj` , nem pedig a fájllal.

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Ehhez a részhez az Azure AD-hitelesítés konfigurációjának előfeltétele lépésnél néhány értéket kell megadnia. Nézze vissza az adott munkamenetből mentett szövegfájlt.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

A ViewController. Swift fájlt tartalmazó fő projekt mappájában hozzon létre egy Konstanss. Swift nevű Swift-osztály fájlt. Cserélje le az osztályt a következő kódra, és adja hozzá az értékeket, ha van ilyen. Tartsa meg ezt a fájlt helyi fájlként, amely csak a gépen található, és ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni. Javasoljuk, hogy ne tartsa meg a titkokat az alkalmazásban. Ehelyett azt javasoljuk, hogy a háttérrendszer használatával szerezze be a jogkivonatot, ahol a titkok az eszközön kívül is megtarthatók. A háttérbeli API-végpontot biztosítani kell a hitelesítés valamilyen formája (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a rövid útmutató hatókörén kívül esik.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Az alkalmazás beállítása storyboard nélkül való futtatásra

Nyissa meg a AppDelegate. Swift fájlt, és cserélje le az alábbi kódra.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>A Launch View vezérlő létrehozása és a minta tartalmának hozzáadása

Nevezze át a ViewController. Swift fájlt a LaunchViewController. Swift névre, és cserélje le a fájlt a következő kódra.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Állítsa be az archiválási sémát a Xcode-ben egy szimulátor vagy egy eszköz céljának kiválasztásával.
![Archiválási séma](./media/ios/xcode-archive-scheme.png)<br/>
![Cél kiválasztása](./media/ios/xcode-select-target.png)

A Xcode nyomja le a CTRL + R billentyűkombinációt, vagy kattintson a lejátszás gombra a projekt futtatásához, és az alkalmazásnak a megadott szimulátoron vagy eszközön kell elindulnia.

Az alkalmazásban a következőket kell látnia:

![Minta alkalmazás](./media/ios/sample-app-ipad.png)

Ha a "magára olvasó" gombra kattint, a rendszer az alkalmazásban található tartalommal ellátott, magával ragadó olvasót fogja látni.

![Modern olvasó](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg a [lebilincselő olvasó iOS SDK](https://github.com/microsoft/immersive-reader-sdk/iOS) -t és a részletes [olvasó iOS SDK](./ios-reference.md) -referenciát
