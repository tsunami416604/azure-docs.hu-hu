---
title: 'Gyors útmutató: szintetizálás beszéd, Objective-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhat beszédeket a macOS Objective-C-ben a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: 2c859965c951ad271b1b9e272ce60de64aa3d3d5
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391366"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Rövid útmutató: a beszédfelismerést a Objective-C-ben, a Speech SDK használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy macOS-alkalmazást a Objective-C-ben a Cognitive Services Speech SDK használatával a beszéd szövegből való létrehozásához és az alapértelmezett hangkimenet lejátszásához.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések megkezdése előtt tekintse meg az előfeltételek listáját:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](~/articles/cognitive-services/Speech-Service/get-started.md)
* MacOS rendszerű gép [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval, MacOS 10,13 vagy újabb verzióval

## <a name="get-the-speech-sdk-for-macos"></a>A macOS-hez készült Speech SDK beszerzése

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Vegye figyelembe, hogy ez az oktatóanyag nem fog működni a 1.7.0-nál korábbi SDK-verzióval.

A Mac rendszerhez készült Cognitive Services Speech SDK-t keretrendszer-csomagként terjesztik.
Xcode-projektekben használható [CocoaPod](https://cocoapods.org/), illetve manuálisan is letölthető https://aka.ms/csspeech/macosbinary és csatolható. Ez az útmutató egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el a Xcode, és indítsa el az új projektet a **fájl**  >  **új**  >  **projekt**elemre kattintva.
A sablon kiválasztása párbeszédpanelen válassza a "kakaó-alkalmazás" sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik Apple Developer-fiókkal, adja meg a megfelelő szervezet nevét és a szervezet azonosítóját. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További részletekért tekintse meg az [Apple Developer webhelyét](https://developer.apple.com/) .
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Tiltsa le a jelölőnégyzeteket a forgatókönyvek használatához és egy dokumentum alapú alkalmazás létrehozásához. A minta alkalmazás egyszerű felhasználói felülete programozott módon lesz létrehozva.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válassza ki azt a könyvtárat, ahová a projektet be szeretné állítani. Ez létrehoz egy `helloworld` könyvtárat a saját könyvtárában, amely a Xcode projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a jogosultságokat a hálózati hozzáféréshez. Kattintson az alkalmazás nevére a bal oldali áttekintés első sorában, hogy beolvassa az alkalmazás konfigurációját, majd válassza a "képességek" fület.
    1. Engedélyezze az alkalmazáshoz tartozó "homokozó" beállítást.
    1. Engedélyezze a "kimenő kapcsolatok" hozzáférés jelölőnégyzeteit.
    ![A homokozó beállításai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. A Xcode projekt lezárása. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Navigáljon a minta alkalmazás ( `helloworld` ) könyvtárába. Helyezzen el egy szövegfájlt a névvel `Podfile` és a következő tartalommal a könyvtárban:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Navigáljon a `helloworld` címtárhoz egy terminálon, és futtassa a parancsot `pod install` . Ez egy `helloworld.xcworkspace` Xcode-munkaterületet hoz majd, amely a minta alkalmazást és a SPEECH SDK-t is függőségként fogja tartalmazni. Ezt a munkaterületet a következőben fogjuk használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa meg a `helloworld.xcworkspace` munkaterületet a Xcode.
1. Cserélje le az automatikusan létrehozott `AppDelegate.m` fájl tartalmát az alábbi kódra:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. A hibakeresési kimenet láthatóvá tétele **(a**  >  **hibakeresési területek**  >  **aktiválása konzolon**).
1. Hozza létre és futtassa a példát a **termék**  ->  **futtatása** elem kiválasztásával a menüből, vagy kattintson a **Lejátszás** gombra.
1. Miután beírt valamilyen szöveget, és rákattintott a gombra az alkalmazásban, hallania kell a szintetizált hanglejátszást.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Objective-C minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

