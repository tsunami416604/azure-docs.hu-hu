---
title: 'Oktatóanyag: fényképek készítése egy olyan iOS-alkalmazáshoz, amely elkészíti a fényképet, és elindítja azt a gördülékeny olvasóban (Swift)'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létrehoz egy iOS-alkalmazást a semmiből, és hozzáadja a képet az olvasói funkciókhoz.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 48e74f7dd6fa6f2c7fafe10797a301b3d4cc7f1d
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045245"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Oktatóanyag: hozzon létre egy iOS-alkalmazást, amely egy fényképből származó tartalommal (Swift) indít el egy lebilincselő olvasót

A teljes [olvasó](https://www.onenote.com/learningtools) egy olyan, integráltan kialakított eszköz, amely bevált technikákat valósít meg az olvasási szövegértés javítására.

A [Computer Vision Cognitive Services Read API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) észleli a képek szöveges tartalmát a Microsoft legújabb felismerési modelljeivel, és átalakítja a azonosított szöveget egy géppel olvasható karakteres adatfolyamba.

Ebben az oktatóanyagban egy iOS-alkalmazást hoz létre a semmiből, és integrálja az olvasási API-t, valamint a magával ragadó olvasót az olvasói SDK használatával. Ebben az oktatóanyagban egy teljes körű működő minta érhető [el.](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* [XCode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A minta projekt tulajdonságainak konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* A minta használatához Azure-előfizetés szükséges a Computer Vision kognitív szolgáltatáshoz. [Hozzon létre egy Computer Vision kognitív szolgáltatási erőforrást a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Hozzon létre egy új projektet a Xcode-ben.

![Új projekt](./media/ios/xcode-create-project.png)

Válassza az **Egynézetes alkalmazás**lehetőséget.

![Új egynézetes alkalmazás](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Az SDK-CocoaPod beszerzése
A részletes olvasói SDK a CocoaPods-on keresztül a legkönnyebben használható. Telepítés a Cocoapods-on keresztül:
1. A [CocoaPods telepítése](http://guides.cocoapods.org/using/getting-started.html) – kövesse az első lépéseket ismertető útmutatót a CocoaPods telepítéséhez.
2. Hozzon létre egy Cocoapods a Xcode-projekt gyökérkönyvtárában lévő `pod init` futtatásával.
3.  `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`hozzáadásával adja hozzá a CocoaPod a Cocoapods. A Cocoapods a következőhöz hasonlóan kell kinéznie a cél nevével, a kép és a-olvasó-Swift közötti váltáshoz:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. A terminálban, a Xcode-projekt könyvtárában futtassa a parancsot a `pod install` segítségével telepítse a lebilincselő olvasó SDK-Pod-t.
5. `import immersive_reader_sdk` hozzáadása az SDK-ra hivatkozó összes fájlhoz.
6. Nyissa meg a projektet úgy, hogy megnyitja a `.xcworkspace` fájlt, és nem a `.xcodeproj` fájlt.

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Ehhez a részhez az Azure AD-hitelesítés konfigurációjának előfeltétele lépésnél néhány értéket kell megadnia. Nézze vissza az adott munkamenetből mentett szövegfájlt.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

A ViewController. Swift fájlt tartalmazó fő projekt mappájában hozzon létre egy Konstanss. Swift nevű Swift-osztály fájlt. Cserélje le az osztályt a következő kódra, és adja hozzá az értékeket, ha van ilyen. Tartsa meg ezt a fájlt helyi fájlként, amely csak a gépen található, és ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni. Javasoljuk, hogy ne tartsa meg a titkokat az alkalmazásban. Ehelyett azt javasoljuk, hogy a háttérrendszer használatával szerezze be a jogkivonatot, ahol a titkok az eszközön kívül is megtarthatók. A háttérbeli API-végpontot biztosítani kell a hitelesítés valamilyen formája (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a munka meghaladja az oktatóanyag hatókörét.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Az alkalmazás beállítása storyboard nélkül való futtatásra

Nyissa meg a AppDelegate. Swift fájlt, és cserélje le az alábbi kódra.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Funkciók hozzáadása fényképek készítéséhez és feltöltéséhez

Nevezze át a ViewController. Swift fájlt a PictureLaunchViewController. Swift névre, és cserélje le a fájlt a következő kódra.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Állítsa be az archiválási sémát a Xcode-ben egy szimulátor vagy egy eszköz céljának kiválasztásával.
![archiválási séma](./media/ios/xcode-archive-scheme.png)<br/>
![válassza a cél](./media/ios/xcode-select-target.png)

A Xcode nyomja le a CTRL + R billentyűkombinációt, vagy kattintson a lejátszás gombra a projekt futtatásához, és az alkalmazásnak a megadott szimulátoron vagy eszközön kell elindulnia.

Az alkalmazásban a következőket kell látnia:

![Minta alkalmazás](./media/ios/picture-to-immersive-reader-ipad-app.png)

Az alkalmazáson belül készítsen vagy töltsön fel egy fényképet a "fénykép készítése" vagy a "fénykép kiválasztása a könyvtárból" gomb megnyomásával, és a magával ragadó olvasó ekkor elindítja a szöveg megjelenítését a fényképből.

![Immersive Reader](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)
