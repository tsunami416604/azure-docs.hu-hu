---
title: Az Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: A Speech CLI egy parancssori eszköz, amellyel a beszédfelismerési szolgáltatás bármilyen kód írása nélkül használható. A beszédfelismeréshez minimálisan szükséges beállítani a CLI-t, és a beszédfelismerési szolgáltatás legfontosabb funkcióinak kipróbálásával azonnal megtekintheti, hogy a használati esetek teljesíthetők-e.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab019250e03feb9a776d628c06e792b884252260
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501814"
---
# <a name="what-is-the-speech-cli"></a>Mi az a Speech parancssori felület?

A Speech CLI egy parancssori eszköz, amellyel a beszédfelismerési szolgáltatás bármilyen kód írása nélkül használható. A beszédfelismerési parancssori felület minimális beállítást igényel, és a beszédfelismerési szolgáltatás legfontosabb funkcióinak kipróbálásával azonnal megtekintheti, hogy a használati esetek teljesíthetők-e. Néhány percen belül futtathat olyan egyszerű tesztelési munkafolyamatokat, mint a Batch Speech-Recognition egy fájlból, vagy szöveges – beszéd típusú sztringek egy fájlból származó karakterláncok gyűjteményében. Az egyszerű munkafolyamatokon túl a Speech CLI éles használatra kész, és akár több folyamat futtatására is alkalmas automatizált `.bat` vagy rendszerhéj-parancsfájlok használatával.

A Speech SDK elsődleges funkcióinak többsége a Speech CLI-ben érhető el, de néhány speciális funkció és testreszabás egyszerűsített a Speech CLI-ben. Az alábbi útmutatást követve döntse el, mikor kell használni a Speech CLI-t vagy a Speech SDK-t.

A Speech CLI használata:
* A beszédfelismerési szolgáltatás funkcióit minimális telepítéssel szeretné kísérletezni, és nincs kód
* A beszédfelismerési szolgáltatást használó éles alkalmazásokhoz viszonylag egyszerű követelmények vonatkoznak.

A Speech SDK használata:
* Egy adott nyelven vagy platformon belül szeretné integrálni a beszédfelismerő szolgáltatás funkcióit (például C#, Python, C++)
* Olyan összetett követelményekkel rendelkezik, amelyek speciális szolgáltatási kérelmeket igényelhetnek, vagy egyéni viselkedést fejlesztenek, beleértve a válaszadási adatfolyamot is

## <a name="core-features"></a>Alapvető funkciók

* Beszédfelismerés – beszéd – szöveg konvertálása hangfájlokból vagy közvetlenül egy mikrofonból, vagy egy rögzített beszélgetés átírása.

* Beszédfelismerés – a szöveg és a beszéd szöveggé konvertálása szöveges fájlok bevitele vagy közvetlenül a parancssorból való bevitel használatával. [SSML-konfigurációk](speech-synthesis-markup.md), valamint [standard vagy neurális hangok](speech-synthesis-markup.md#standard-neural-and-custom-voices)használatával testreszabhatja a beszédfelismerési kimenet jellemzőit.

* Beszéd fordítása – a hanganyagot nyelvre lefordíthatja a célnyelv szövegére.

* Futtatás Azure számítási erőforrásokon – SPX-parancsok küldése egy Azure távoli számítási erőforráson való futtatásra a használatával `spx webjob` .

## <a name="get-started"></a>Bevezetés

A Speech CLI használatának megkezdéséhez tekintse meg az alapvető tudnivalókat ismertető [cikket](spx-basics.md). Ebből a cikkből megtudhatja, hogyan futtathat néhány alapszintű parancsot az SPX-vel, és hogyan jeleníti meg valamivel fejlettebb parancsokat a Batch-műveletek futtatásához a beszédfelismerés és a szöveg közötti kommunikációhoz. Az alapismeretek című cikk elolvasása után elegendő az SPX-szintaxis megismerése az egyéni parancsok írásának megkezdéséhez, illetve az egyszerű beszédfelismerési műveletek automatizálásához.

## <a name="next-steps"></a>További lépések

- [Beszédfelismerési parancssori felület alapjai](spx-basics.md)
- Ha a használati eset összetettebb, [a SPEECH SDK beszerzése](speech-sdk.md)
