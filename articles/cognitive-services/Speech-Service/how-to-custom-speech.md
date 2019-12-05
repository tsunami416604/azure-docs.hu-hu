---
title: Ismerkedés a Custom Speech-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: A Custom Speech olyan online eszközöket biztosít, amelyekkel kiértékelheti és javíthatja az alkalmazásaihoz, eszközeihez és termékeihez tartozó beszéd-szöveg pontosságot. Minden, ami az első lépések elvégzéséhez szükséges, néhány teszt hangfájlt használ. Az alábbi hivatkozásokat követve hozzon létre egy egyéni beszéd – szöveg élményt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c8c849cb83ecb1db5e972c660d94c795092c458e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806011"
---
# <a name="what-is-custom-speech"></a>Mi az Custom Speech?

A [Custom Speech](https://aka.ms/customspeech) olyan online eszközöket biztosít, amelyek segítségével kiértékelheti és javíthatja a Microsoft beszéd-szöveg pontosságát alkalmazásai, eszközei és termékei számára. Minden, ami az első lépések elvégzéséhez szükséges, néhány teszt hangfájlt használ. Az alábbi hivatkozásokat követve hozzon létre egy egyéni beszéd – szöveg élményt.

## <a name="whats-in-custom-speech"></a>Mi a Custom Speech?

Mielőtt bármit elvégezhet a Custom Speech, szüksége lesz egy Azure-fiókra és egy Speech Service-előfizetésre. Ha már rendelkezik fiókkal, előkészítheti az adatait, betaníthatja és tesztelheti a modelleket, ellenőrizheti a minőség felismerését, kiértékelheti a pontosságot, és végső soron üzembe helyezheti és használhatja az egyéni beszéd – szöveg modellt.

Ez a diagram kiemeli a [Custom Speech portált](https://aka.ms/customspeech)alkotó elemeket. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

![Kiemeli a Custom Speech portált alkotó különböző összetevőket.](./media/custom-speech/custom-speech-overview.png)

1. [Előfizetés és projekt létrehozása](#set-up-your-azure-account) – hozzon létre egy Azure-fiókot, és fizessen elő a beszédfelismerési szolgáltatásra. Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szöveg és a beszéd fordításához, valamint a [Custom Speech portálhoz](https://speech.microsoft.com/customspeech). Ezután használja a Speech Service-előfizetését, és hozza létre első Custom Speech projektjét.

2. [Töltse fel a tesztelési](how-to-custom-speech-test-data.md) adatok feltöltésének tesztelési adatait (hangfájlok), hogy kiértékelje a Microsoft beszéd-szöveges ajánlatát az alkalmazásaihoz, eszközeihez és termékeihez.

3. A [felismerés minőségének vizsgálata](how-to-custom-speech-inspect-data.md) – a [Custom Speech-portálon](https://speech.microsoft.com/customspeech) visszajátszhatja a feltöltött hangfelvételeket, és megvizsgálhatja a tesztelési adatok beszédfelismerési minőségét. A mennyiségi mérések esetében lásd: [az adatvizsgálat](how-to-custom-speech-inspect-data.md).

4. [Pontosság kiértékelése](how-to-custom-speech-evaluate-data.md) – a beszédfelismerési modell pontosságának kiértékelése. A [Custom Speech-portál](https://speech.microsoft.com/customspeech) egy *szó szerinti hibát jelez*, amellyel meghatározható, hogy szükség van-e további képzésre. Ha elégedett a pontossággal, a Speech Service API-jait közvetlenül is használhatja. Ha a pontosságot 5%-20%-os relatív átlagban szeretné növelni, a portálon a **betanítás** lapon további betanítási adatok (például emberi feliratú átiratok és kapcsolódó szövegek) tölthetők fel.

5. [A modell betanítása](how-to-custom-speech-train-model.md) – a beszédfelismerési modell pontosságának javítása írásos átiratokkal (10 – 1000 óra) és a kapcsolódó szöveggel (< 200 MB) és az audio-tesztelési adatokkal együtt. Ez az érték segít a beszéd – szöveg modell betanításában. Ha betanítást, újratesztelést végez, és ha elégedett az eredménnyel, üzembe helyezheti a modellt.

6. [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md) – hozzon létre egy egyéni végpontot a beszéd – szöveg modellhez, és használja azt az alkalmazásokban, eszközökben vagy termékekben.

## <a name="set-up-your-azure-account"></a>Azure-fiók beállítása

Egy beszédfelismerési szolgáltatás előfizetése szükséges ahhoz, hogy a [Custom Speech portálon](https://speech.microsoft.com/customspeech) egyéni modellt hozzon létre. A standard Speech Service-előfizetés létrehozásához kövesse az alábbi utasításokat: [beszédfelismerési előfizetés létrehozása](get-started.md#try-the-speech-service-using-a-new-azure-account).

> [!NOTE]
> Ügyeljen arra, hogy standard (S0) előfizetéseket hozzon létre, az ingyenes próbaverziós (F0) előfizetések nem támogatottak.

Miután létrehozott egy Azure-fiókot és egy Speech Service-előfizetést, be kell jelentkeznie [Custom Speech portálra](https://speech.microsoft.com/customspeech) , és hozzá kell kötnie az előfizetését.

1. Szerezze be a beszédfelismerési szolgáltatás előfizetési kulcsát a Azure Portal.
2. Jelentkezzen be a [Custom Speech portálra](https://aka.ms/custom-speech).
3. Válassza ki azt az előfizetést, amelyre dolgozni szeretne, és hozzon létre egy beszédfelismerési projektet.
4. Ha módosítani szeretné az előfizetését, használja a felső navigációs sávban található **fogaskerék** ikont.

## <a name="how-to-create-a-project"></a>Projekt létrehozása

A tartalmak, például az adattípusok, a modellek, a tesztek és a végpontok a [Custom Speech portálon](https://speech.microsoft.com/customspeech)belüli **projektekben** vannak rendszerezve. Minden projekt egy tartományra és országra/nyelvre vonatkozik. Létrehozhat például egy olyan projektet a hívási központok számára, amelyek az angol nyelvet használják a Egyesült Államok.

Az első projekt létrehozásához válassza a **beszéd-szöveg/egyéni beszéd**lehetőséget, majd kattintson az **új projekt**elemre. A projekt létrehozásához kövesse a varázsló utasításait. Miután létrehozott egy projektet, négy lapot kell látnia: **az adatelemzést**, a **tesztelést**, a **betanítást**és az **üzembe helyezést**. Az egyes lapok használatának megismeréséhez használja a [következő lépések](#next-steps) hivatkozásait.

## <a name="next-steps"></a>Következő lépések

* [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
