---
title: Egyéni kulcsszavak létrehozása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717020"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Egyéni kulcsszó létrehozása a Speech Studio használatával

Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Például a "Hey Cortana" a Cortana segéd egyik kulcsszója. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni kulcsszót az eszközhöz.

## <a name="create-your-keyword"></a>A kulcsszó létrehozása

Egyéni kulcsszó használata előtt létre kell hoznia egy kulcsszót a [Speech Studio](https://aka.ms/sdsdk-speechportal) [Egyéni kulcsszavas](https://aka.ms/sdsdk-wakewordportal) oldalának használatával. A kulcsszó megadása után létrehoz egy fájlt, amelyet központilag telepít az eszközre.

1. Lépjen a [Speech studióba](https://aka.ms/sdsdk-speechportal) , és **Jelentkezzen be** , vagy ha még nem rendelkezik beszédfelismerési előfizetéssel, válassza az [**előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754)lehetőséget.

1. Az [egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapon hozzon létre egy **új projektet**. 

1. Adjon meg egy **nevet**, egy opcionális **leírást**, és válassza ki a nyelvet. Nyelv és támogatás esetén egy projektre lesz szüksége, és jelenleg csak az en-US nyelvre korlátozódik.

    ![A kulcsszavas projekt leírása](media/custom-keyword/custom-kws-portal-new-project.png)

1. Válassza ki a projektet a listából. 

    ![Válassza ki a kulcsszavas projektet](media/custom-keyword/custom-kws-portal-project-list.png)

1. Új kulcsszavas modell indításához kattintson a **betanítási modell**elemre.

1. Adja meg a kulcsszó-modell **nevét** , és ha szükséges, a **leírást** és a típust az Ön által választott **kulcsszóra** , majd kattintson a **tovább**gombra. Van néhány [irányelvünk](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) , amely segítséget nyújt egy hatékony kulcsszó kiválasztásához.

    ![Adja meg a kulcsszót](media/custom-keyword/custom-kws-portal-new-model.png)

1. A portál mostantól jelölt kiejtéseket hoz létre a kulcsszava számára. Hallgassa meg az egyes jelölteket a lejátszás gombokra kattintva, és távolítsa el az összes helytelen kiejtés melletti ellenőrzéseket. Ha csak a jó kiejtéseket jelölte be, kattintson a **betanítás** gombra a kulcsszó generálásának megkezdéséhez. 

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. A modell létrehozása akár harminc percet is igénybe vehet. A kulcsszó listája a **feldolgozástól** a **sikeres** modell befejeződése után módosul. Ezután letöltheti a fájlt.

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-download-model.png)

1. Mentse a. zip fájlt a számítógépére. Erre a fájlra szüksége lesz az egyéni kulcsszó üzembe helyezéséhez az eszközön.

## <a name="next-steps"></a>Következő lépések

Tesztelje egyéni kulcsszavait a [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)gyors útmutatójában.
