---
title: Ismerkedés az egyéni beszédfelismeréssel – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Az Egyéni beszéd olyan online eszközök készlete, amelyek lehetővé teszik az alkalmazások, eszközök és termékek beszéd-szöveg pontosságának kiértékelését és javítását. Mindössze annyit kell tenni az induláshoz egy maroknyi teszt audio fájlokat. Az alábbi hivatkozásokra kattintva egyéni beszéd-szöveg élmény jön létre.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: a52735ee62f564a5dc536fd2b7d3539406388341
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402070"
---
# <a name="what-is-custom-speech"></a>Mi az egyéni beszéd?

[Az Egyéni beszéd](https://aka.ms/customspeech) olyan online eszközök készlete, amelyek lehetővé teszik a Microsoft beszéd-szöveg pontosságának kiértékelését és javítását alkalmazásai, eszközei és termékei esetében. Mindössze annyit kell tenni az induláshoz egy maroknyi teszt audio fájlokat. Az alábbi hivatkozásokra kattintva egyéni beszéd-szöveg élmény jön létre.

## <a name="whats-in-custom-speech"></a>Mi található az egyéni beszédben?

Mielőtt bármit is tehetne az egyéni beszédfelismeréssel, szüksége lesz egy Azure-fiókra és egy Beszédszolgáltatás-előfizetésre. Miután rendelkezik egy fiókkal, előkészítheti az adatokat, betaníthatja és tesztelheti a modelleket, ellenőrizheti a felismerés minőségét, kiértékelheti a pontosságot, és végső soron üzembe helyezheti és használhatja az egyéni beszéd-szöveg modellt.

Ez az ábra kiemeli az [egyéni beszédportált](https://aka.ms/customspeech)alkotó darabokat. Az alábbi hivatkozásokra kattintva többet is megtudhat az egyes lépésekről.

![Kiemeli az egyéni beszédportált alkotó különböző összetevőket.](./media/custom-speech/custom-speech-overview.png)

1. [Iratkozzon fel és hozzon létre egy projektet](#set-up-your-azure-account) – Hozzon létre egy Azure-fiókot, és iratkozzon fel a beszédfelismerési szolgáltatásra. Ez az egyesített előfizetés hozzáférést biztosít a beszédfelismeréshez, a szövegfelolvasáshoz, a beszédfordításhoz és az [Egyéni beszédportálhoz.](https://speech.microsoft.com/customspeech) Ezután a Beszédszolgáltatás-előfizetés használatával hozza létre az első egyéni beszédprojektet.

2. [Tesztadatok feltöltése](how-to-custom-speech-test-data.md) – Tesztadatok (hangfájlok) feltöltése a Microsoft alkalmazásokhoz, eszközökhöz és termékekhez tartozó beszéd-szöveg ajánlatának értékeléséhez.

3. [A felismerés minőségének vizsgálata](how-to-custom-speech-inspect-data.md) – Az [Egyéni beszédportálon](https://speech.microsoft.com/customspeech) lejátszhatja a feltöltött hangot, és ellenőrizheti a tesztadatok beszédfelismerési minőségét. A kvantitatív mérésekről [lásd: Adatok vizsgálata](how-to-custom-speech-inspect-data.md).

4. [A pontosság kiértékelése](how-to-custom-speech-evaluate-data.md) – A beszéd-szöveg modell pontosságának kiértékelése. Az [egyéni beszédportál](https://speech.microsoft.com/customspeech) *wordhiba-arányt*biztosít, amellyel megállapítható, hogy szükség van-e további képzésre. Ha elégedett a pontossággal, közvetlenül használhatja a beszédfelismerési szolgáltatás API-jait. Ha relatív átlagban 5–20%-kal szeretné javítani a pontosságot, a portál **Edzés** lapján további betanítási adatokat tölthet fel, például emberi címkével ellátott átiratokat és kapcsolódó szöveget.

5. [A modell betanítása](how-to-custom-speech-train-model.md) – A szöveggé bírásaként imitáló modell pontosságának javítása írásos átiratok (10–1000 óra) és a kapcsolódó szöveg (<200 MB) és a hangtesztadatok biztosításával. Ezek az adatok segítenek a beszéd-szöveg modell betanításához. Betanítás után tesztelje újra, és ha elégedett az eredménnyel, telepítheti a modellt.

6. [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md) – Hozzon létre egy egyéni végpontot a beszéd-szöveg modellhez, és használja azt az alkalmazásokban, eszközökben vagy termékekben.

## <a name="set-up-your-azure-account"></a>Az Azure-fiók beállítása

A beszédfelismerési szolgáltatás előfizetése szükséges, mielőtt az [egyéni beszédportálon](https://speech.microsoft.com/customspeech) egyéni modell létrehozásához. Kövesse az alábbi utasításokat a szabványos beszédszolgáltatás-előfizetés létrehozásához: [Beszédfelismerési előfizetés létrehozása](get-started.md#new-resource).

> [!NOTE]
> Kérjük, győződjön meg róla, hogy hozzon létre szabványos (S0) előfizetések, ingyenes próbaverziós (F0) előfizetések nem támogatottak.

Miután létrehozott egy Azure-fiókot és egy Speech Service-előfizetést, be kell jelentkeznie az [egyéni beszédportálra,](https://speech.microsoft.com/customspeech) és csatlakoztatnia kell az előfizetést.

1. A beszédfelismerési szolgáltatás előfizetési kulcsának beolvasása az Azure Portalról.
2. Jelentkezzen be az [egyéni beszédportálra.](https://aka.ms/custom-speech)
3. Válassza ki azt az előfizetést, amelyen dolgoznia kell, és hozzon létre egy beszédprojektet.
4. Ha módosítani szeretné az előfizetését, használja a felső navigációs sávon található **fogaskerék** ikont.

## <a name="how-to-create-a-project"></a>Hogyan hozzunk létre egy projektet

Az egyéni [beszédfelismerési portálon](https://speech.microsoft.com/customspeech)a tartalom, például az adatok, modellek, tesztek és végpontok **projektekbe** vannak rendezve. Minden projekt egy tartományra és országra/nyelvre vonatkozik. Létrehozhat például egy projektet az Egyesült Államokban angol ultrásokat használó hívásközpontok számára.

Az első projekt létrehozásához jelölje ki a **beszédfelismerést/egyéni beszédet,** majd kattintson az **Új projekt**gombra. A projekt létrehozásához kövesse a varázsló utasításait. A projekt létrehozása után négy lapnak kell megjelennie: **Adatok**, **Tesztelés**, **Betanítás**és **Telepítés**. A [Következő lépések](#next-steps) című részben található hivatkozások segítségével megtudhatja, hogyan használhatja az egyes lapokat.

> [!IMPORTANT]
> Az [egyéni beszédportál](https://aka.ms/custom-speech) nemrég frissült! Ha korábbi adatokat, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai portálon vagy API-kkal, új projektet kell létrehoznia az új portálon, hogy ezekhez a régi entitásokhoz kapcsolódjon.

## <a name="next-steps"></a>További lépések

* [Az adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
