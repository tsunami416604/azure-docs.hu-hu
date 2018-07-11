---
title: A Custom Vision Service gépi tanulás áttekintése – Azure Cognitive Services | Microsoft Docs
description: A Custom Vision Service egy Microsoft Cognitive Services-szolgáltatás, amellyel egyéni képosztályozókat hozhat létre az Azure platformon.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087681"
---
# <a name="what-is-the-custom-vision-service"></a>Mi a Custom Vision Service?

A Custom Vision Service egy Microsoft Cognitive-szolgáltatás, amellyel egyéni képosztályozókat hozhat létre. Jelentősen megkönnyíti és felgyorsítja a képosztályozók létrehozását, üzembe helyezését és továbbfejlesztését. A Custom Vision Service REST API-t és egy webes felületet biztosít, ahol képek tölthetők fel, és ahol be lehet tanítani a képosztályozókat.

## <a name="what-does-custom-vision-service-do-well"></a>Mire alkalmas a Custom Vision Service?

A Custom Vision Service akkor működik a legjobban, ha az osztályozni próbált elem kiemelt helyen szerepel a képen. 

Egy osztályozó vagy érzékelő létrehozásához képekre van szükség. Osztályonként 50 kép elegendő a prototípus elindításához. A Custom Vision Service által használt módszerek a jelentős különbségeket érzékelik, így már kevés adattal is neki lehet állni a prototípus készítésének. Ez ugyanakkor azt is jelenti, hogy a Custom Vision Service nem megfelelő olyan esetekben, amelyekben apró különbségeket kell azonosítani, például kisebb repedéseket és horpadásokat minőségbiztosítási célból.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="may-7-2018"></a>2018. május 7.
- Előzetes verziójú objektumészlelési szolgáltatás bevezetése korlátozott próbaverziójú projektekhez.
- Frissítés 2.0 API-kra
- Az S0 szint kibővítve akár 250 címkére és 50 000 képre. 
- Jelentős háttérbeli fejlesztések a képosztályozási projektek gépi tanulási folyamataiban. A 2018. április 27. után betanított projektek már kihasználják ezen frissítések előnyeit.
- Hozzáadott modellexportálás az ONNX-hez, Windows ML-el való használathoz.
- Hozzáadott modellexportálás a Dockerfile-hoz. Ez lehetővé teszi az összetevők, például a DockerFile, a TensorFlow-modell és a szolgáltatáskód letöltését saját Windows- vagy Linux-tárolók létrehozásához. 
- A TensorFlow-ba exportált, újonnan betanított modellek esetében az Általános (Kompakt) és Tereptárgy (Kompakt) tartományokban a [középérték mostantól (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) lesz a projektek közötti konzisztencia érdekében. 

### <a name="march-1-2018"></a>2018. március 1.
- A fizetős előzetes verzió elérhetővé válik az Azure Portalon. Mostantól csatolhatók projektek az F0 (Ingyenes) vagy S0 (Standard) szintű Azure-erőforrásokhoz. S0 szintű projektek bevezetve, amelyek akár 100 címkét és 25 000 képet is engedélyeznek. 
- Háttérbeli módosítások a gép tanulási folyamatban/normalizálási paraméterben. Ezzel a felhasználók jobban kezelhetik a pontosság-visszahívási előnyöket és hátrányokat a valószínűségi küszöbérték beállításakor. A módosítások részeként az alapértelmezett valószínűségi küszöbérték a CustomVision.ai portálon 50%-ra lett állítva.

### <a name="december-19-2017"></a>2017. december 19.

- Hozzáadott exportálás Androidba (TensorFlow), a korábban kiadott, iOS-be történő (CoreML) exportálási lehetőség mellett. Ez lehetővé teszi, hogy egy betanított kompakt modell exportálása offline fusson egy alkalmazásban.
- Kiskereskedelem és Tereptárgy „kompakt” tartományok hozzáadva, így elérhetővé válik bennük a modellexportálás.
- Az [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) és az [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) kiadva. A frissített API-k támogatják a modellexportálást, illetve az új előrejelzési műveletet, amely nem menti a képeket az előrejelzések közé. Bevezetett kötegelt műveletek a Training API-ban.
- UX-finomhangolások, beleértve azt a lehetőséget, amellyel megtekinthető, hogy melyik tartomány használatával lett betanítva az ismétlés.
- Frissült a [C# SDK és minta](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>További lépések

[Osztályozó létrehozása](getting-started-build-a-classifier.md)
