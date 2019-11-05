---
title: 'Rövid útmutató: beszéd felismerése mikrofonból, Objective-C-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést a macOS-ben a Speech SDK használatával
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0025e6e84a8a76ad3a332056f51f802ca95da23f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503590"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Gyors útmutató: beszéd felismerése a macOS-ben a "Objective-C"-ben a Speech SDK használatával

A hangfelismerések is elérhetők a [Speech szintézishez](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

Ebből a cikkből megtudhatja, hogyan hozhat létre egy macOS-alkalmazást az Objective-C-ben az Azure Cognitive Services Speech SDK használatával, amely a mikrofonról szövegre rögzített beszédet mutatja be.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](~/articles/cognitive-services/Speech-Service/get-started.md) .
* MacOS rendszerű gép [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval, MacOS 10,13 vagy újabb verzióval.

## <a name="get-the-speech-sdk-for-macos"></a>A macOS-hez készült Speech SDK beszerzése

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

A Mac rendszerhez készült Cognitive Services Speech SDK-t keretrendszer-csomagként terjesztik. Xcode-projektekben használható [CocoaPod](https://cocoapods.org/) , és manuálisan is letölthetők a https://aka.ms/csspeech/macosbinaryból. Ez a cikk egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el a Xcode, és indítson el egy új projektet a **fájl** > **új** > **projekt**kiválasztásával. A sablon kiválasztása párbeszédpanelen válassza ki a kakaó- **alkalmazás** sablonját.

Az alábbi párbeszédpaneleken végezze el a következő beállításokat.

1. A **Project Options (projekt beállításai** ) párbeszédpanelen:
    1. Adja meg a Gyorsindítás alkalmazás nevét, például *HelloWorld*.
    1. Ha már rendelkezik Apple Developer-fiókkal, adja meg a megfelelő szervezet nevét és a szervezet azonosítóját. Tesztelési célból használjon olyan nevet, mint például a *testorg*. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További információt az [Apple fejlesztői webhelyén](https://developer.apple.com/)talál.
    1. Ügyeljen arra, hogy a projekt nyelve legyen a **Objective-C** beállítás.
    1. Törölje a jelölést a forgatókönyvek használatához, és hozzon létre egy dokumentum alapú alkalmazást. A minta alkalmazás egyszerű felhasználói felületét programozott módon hozza létre.
    1. Törölje az összes jelölőnégyzetet a tesztekhez és az alapértékekhez.

    ![Projekt beállításai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Válasszon egy projekt könyvtárat:
    1. Válassza ki azt a könyvtárat, ahová a projektet be szeretné állítani. Ez a lépés létrehoz egy HelloWorld könyvtárat a saját könyvtárában, amely a Xcode projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a jogosultságokat a hálózati és a mikrofonhoz való hozzáféréshez. Válassza ki az alkalmazás nevét a bal oldali áttekintés első sorában az alkalmazás konfigurációjának megismeréséhez. Ezután válassza a **képességek** fület.
    1. Engedélyezze az alkalmazáshoz tartozó **sandbox** -beállítást.
    1. Jelölje be a **Kimenő kapcsolatok** és a **mikrofon** -hozzáférés jelölőnégyzetét.

    ![A homokozó beállításai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Az alkalmazásnak be kell jelentenie a mikrofon használatát a `Info.plist` fájlban. Válassza ki a fájlt az áttekintésben, és adja hozzá az **Adatvédelem-mikrofon használati Leírás** kulcsát egy olyan értékkel, mint például a *beszédfelismeréshez a mikrofon szükséges*.

    ![Beállítások az info. plist fájlban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. A Xcode projekt lezárása. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Nyissa meg a HelloWorld, amely a minta alkalmazás könyvtára. Helyezzen egy *cocoapods* nevű szövegfájlt és a következő tartalmat a könyvtárba:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Nyissa meg a HelloWorld könyvtárat egy terminálon, és futtassa a parancsot `pod install`. Ez a parancs egy `helloworld.xcworkspace` Xcode-munkaterületet hoz létre, amely a minta alkalmazást és a Speech SDK-t is tartalmazza függőségként. Ezt a munkaterületet a következő lépésekben lehet használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa meg a munkaterület `helloworld.xcworkspace` a Xcode.
1. Cserélje le az automatikusan generált `AppDelegate.m` fájl tartalmát a következő kódra:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` karakterláncot az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . Használja például az ingyenes próba-előfizetéshez `westus`.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. A hibakeresési kimenet láthatóvá tételéhez válassza a > **hibakeresési területek** **megtekintése** lehetőséget > **aktiválja a konzolt**.
1. Hozza létre és futtassa a példaként szolgáló kódot a menüből válassza a **termék** > **Futtatás** lehetőséget. Választhatja a **Lejátszás**lehetőséget is.
1. Miután kiválasztotta a gombot, és mondjuk egy pár szót, látnia kell a képernyő alsó részén megjelenő szöveget. Amikor első alkalommal futtatja az alkalmazást, a rendszer kérni fogja, hogy az alkalmazás hozzáférjen a számítógép mikrofonja számára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Objective-C minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
