---
title: Egyéni kulcsszavak létrehozása – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A készülék mindig figyel egy kulcsszót (vagy kifejezést). Amikor a felhasználó azt mondja, a kulcsszó, az eszköz elküldi az összes további hangot a felhőbe, amíg a felhasználó abbahagyja a beszédet. A kulcsszó testreszabása hatékony módja annak, hogy megkülönböztesse az eszközt és erősítse a márkajelzést.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717020"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Egyéni kulcsszó létrehozása a Speech Studio használatával

A készülék mindig figyel egy kulcsszót (vagy kifejezést). A "Hey Cortana" például a Cortana-asszisztens egyik kulcsszava. Amikor a felhasználó azt mondja, a kulcsszó, az eszköz elküldi az összes további hangot a felhőbe, amíg a felhasználó abbahagyja a beszédet. A kulcsszó testreszabása hatékony módja annak, hogy megkülönböztesse az eszközt és erősítse a márkajelzést.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni kulcsszót az eszközéhez.

## <a name="create-your-keyword"></a>A kulcsszó létrehozása

Egyéni kulcsszó használata előtt létre kell hoznia egy kulcsszót a [Speech Studio](https://aka.ms/sdsdk-speechportal) [Egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapján. Miután megadott egy kulcsszót, létrehoz egy fájlt, amelyet telepít az eszközre.

1. Nyissa meg a [Beszédstúdiót,](https://aka.ms/sdsdk-speechportal) vagy **jelentkezzen be,** vagy ha még nem rendelkezik beszédalapú előfizetéssel, válassza [**az Előfizetés létrehozása lehetőséget.**](https://go.microsoft.com/fwlink/?linkid=2086754)

1. Az [Egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapon hozzon létre egy **új projektet.** 

1. Írjon be egy **nevet**, egy nem kötelező **leírást,** és válassza ki a nyelvet. Nyelvnként egy projektre lesz szüksége, és a támogatás jelenleg az en-US nyelvre korlátozódik.

    ![A kulcsszóprojekt leírása](media/custom-keyword/custom-kws-portal-new-project.png)

1. Válassza ki a projektet a listából. 

    ![A kulcsszóprojekt kiválasztása](media/custom-keyword/custom-kws-portal-project-list.png)

1. Új kulcsszómodell indításához kattintson **a Betanítási modell gombra.**

1. Adja meg a kulcsszómodell **nevét,** és a választható **Leírás** és típus a választott **kulcsszó** mezőbe, majd kattintson a **Tovább**gombra. Van néhány [irányelveket,](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) hogy segítsen kiválasztani a hatékony kulcsszót.

    ![Adja meg a kulcsszót](media/custom-keyword/custom-kws-portal-new-model.png)

1. A portál mostantól jelölt kiejtéseket hoz létre a kulcsszóhoz. Hallgassa meg az egyes jelölteket a lejátszás gombokra kattintva, és távolítsa el a helytelen kiejtések melletti ellenőrzéseket. Ha csak a jó kiejtések vannak bejelölve, kattintson **a Vonat** gombra a kulcsszó létrehozásának megkezdéséhez. 

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. A modell létrehozása akár harminc percet is igénybe vehet. A kulcsszólista a modell befejezése után **a Feldolgozás** ról **sikeresre** változik. Ezután letöltheti a fájlt.

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-download-model.png)

1. Mentse a .zip fájlt a számítógépre. Szüksége lesz erre a fájlra, hogy az egyéni kulcsszót telepítse az eszközére.

## <a name="next-steps"></a>További lépések

Tesztelje egyéni kulcsszóját a [Beszédeszközök SDK-rövidútmutatóval.](https://aka.ms/sdsdk-quickstart)
