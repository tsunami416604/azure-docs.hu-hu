---
title: 'Oktatóanyag: Hozzon létre egy iOS alkalmazást, amely fényképet készít, és elindítja azt a Magával ragadó olvasóban (Swift)'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban a semmiből épít egy iOS-alkalmazást, és hozzáadhatja a Képet a Magával ragadó olvasó funkcióhoz.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841846"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Oktatóanyag: Hozzon létre egy iOS-alkalmazást, amely elindítja a Magával ragadó olvasót egy fénykép (Swift) tartalmával

A [Magával ragadó olvasó](https://www.onenote.com/learningtools) egy inkluzívan tervezett eszköz, amely bevált technikákat valósít meg az olvasás megértésének javítása érdekében.

A [Computer Vision Cognitive Services Read API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) a Microsoft legújabb felismerési modelljeivel észleli a kép szöveges tartalmát, és az azonosított szöveget géppel olvasható karakterfolyammá alakítja.

Ebben az oktatóanyagban a semmiből hozhat létre iOS-alkalmazást, és integrálhatja az Olvasási API-t és a Magával ragadó olvasót a Magával ragadó olvasó SDK használatával. A teljes munka minta ez a bemutató [itt](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift)érhető el .

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Az Azure Active Directory-hitelesítéshez konfigurált magával ragadó reader-erőforrás. A beállításhoz kövesse [az alábbi utasításokat.](./how-to-create-immersive-reader.md) A mintaprojekt tulajdonságainak konfigurálásakor szüksége lesz néhány itt létrehozott értékre. Mentse a munkamenet kimenetét egy szöveges fájlba későbbi használatra.
* A minta használatához a Computer Vision Cognitive Service Azure-előfizetése szükséges. [Hozzon létre egy Computer Vision Cognitive Service erőforrást az Azure Portalon.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)

## <a name="create-an-xcode-project"></a>Xcode projekt létrehozása

Hozzon létre egy új projektet az Xcode-ban.

![Új projekt](./media/ios/xcode-create-project.png)

Válassza **az Egynézetű alkalmazás lehetőséget.**

![Új egynézetes alkalmazás](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Szerezd meg az SDK CocoaPod
A immersive Reader SDK használatának legegyszerűbb módja a CocoaPods. A Cocoapods-on keresztül történő telepítés:
1. [Telepítse CocoaPods](http://guides.cocoapods.org/using/getting-started.html) - Kövesse az első lépések útmutató telepíteni Cocoapods.
2. Podfile létrehozása az `pod init` Xcode projekt gyökérkönyvtárában való futtatással.
3.  Adja hozzá a CocoaPod-ot `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`a Podfile fájlhoz a hozzáadásával. A Podfile-nak a következőkhöz kell hasonlítania, és a cél neve helyett kép-magával ragadó-olvasó-gyors:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. A terminál, a könyvtárban az Xcode projekt, futtassa a parancsot, `pod install` hogy telepítse a magával ragadó Reader SDK pod.
5. Adja `import immersive_reader_sdk` hozzá az összes fájlt, hogy kell hivatkozni az SDK.
6. Győződjön meg arról, `.xcworkspace` hogy a `.xcodeproj` projektet a fájl megnyitásával nyitja meg, nem pedig a fájlmegnyitásával.

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Szüksége van néhány értéket az Azure AD hitelesítési konfigurációs előfeltétel lépés felett ebben a részben. Hivatkozzon vissza a munkamenetből mentett szövegfájlra.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

A ViewController.swift fájlt tartalmazó fő projektmappában hozzon létre egy Constants.swift nevű Swift osztályfájlt. Cserélje le az osztályt a következő kódra, adott esetben az értékekhez adva. Tartsa meg ezt a fájlt olyan helyi fájlként, amely csak a számítógépen található, és győződjön meg róla, hogy nem véglegesíti a fájlt a forrásvezérlőbe, mivel olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni. Javasoljuk, hogy ne tartson titkokat az alkalmazásban. Ehelyett azt javasoljuk, hogy egy háttérszolgáltatás a jogkivonat beszerzéséhez, ahol a titkos kulcsokat az alkalmazáson kívül és az eszközön kívül is lehet tartani. A háttér-API-végpontot valamilyen hitelesítési forma (például [OAuth)](https://oauth.net/2/)mögött kell biztosítani, hogy illetéktelen felhasználók ne szerezzenek be jogkivonatokat a Magával ragadó olvasó szolgáltatás és a számlázás ellen; hogy a munka túlmutat a bemutató.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Az alkalmazás beállítása történet nélküli futtatásra

Nyissa meg az AppDelegate.swift fájlt, és cserélje le a fájlt a következő kódra.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Fényképek elvételéhez és feltöltéséhez szükséges funkciók hozzáadása

Nevezze át a ViewController.swift fájlt PictureLaunchViewController.swift fájlra, és cserélje le a fájlt a következő kódra.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Állítsa be az archív sémát az Xcode-ban egy szimulátor vagy eszközcél kiválasztásával.
![Archiválási séma](./media/ios/xcode-archive-scheme.png)<br/>
![Cél kiválasztása](./media/ios/xcode-select-target.png)

Az Xcode-ban nyomja meg a Ctrl + R billentyűkombinációt, vagy kattintson a lejátszás gombra a projekt futtatásához, és az alkalmazásnak el kell indulnia a megadott szimulátoron vagy eszközön.

Az alkalmazásban a következőket kell látnia:

![Mintaalkalmazás](./media/ios/picture-to-immersive-reader-ipad-app.png)

Az alkalmazáson belül a "Fényképfelvétel" vagy a "Fénykép kiválasztása a könyvtárból" gomb megnyomásával fényképet vehet fel vagy tölthet fel a szövegről, majd a Magával ragadó olvasó ekkor elindítja a fénykép szövegének megjelenítését.

![Modern olvasó](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-referenciát](./reference.md)
