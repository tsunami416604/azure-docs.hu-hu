---
title: SPX-Speech szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az SPX egy parancssori eszköz, amellyel a beszédfelismerési szolgáltatás bármilyen kód írása nélkül használható. Az SPX minimálisan szükséges beállítással rendelkezik, és a beszédfelismerési szolgáltatás legfontosabb funkcióinak kipróbálásával azonnal megtekintheti, hogy a használati esetek teljesíthetők-e.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: b473bdc516c59b55eeb44f227352497142a4383b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202281"
---
# <a name="what-is-spx"></a>Mi az az SPX?

Az SPX egy parancssori eszköz, amellyel a beszédfelismerési szolgáltatás bármilyen kód írása nélkül használható. Az SPX minimális beállítást igényel, és a beszédfelismerési szolgáltatás legfontosabb funkcióinak kipróbálásával azonnal megtekintheti, hogy a használati esetek teljesíthetők-e. Néhány percen belül futtathat olyan egyszerű tesztelési munkafolyamatokat, mint a Batch Speech-Recognition egy fájlból, vagy szöveges – beszéd típusú sztringek egy fájlból származó karakterláncok gyűjteményében. Az egyszerű munkafolyamatokon túl az SPX éles használatra kész, és akár nagyobb folyamatok futtatására is képes az automatizált `.bat` vagy a Shell parancsfájlokkal.

A Speech SDK elsődleges funkcióinak többsége az SPX-ben érhető el, de néhány speciális funkció és testreszabás az SPX-ben egyszerűbb. Az alábbi útmutatást követve döntse el, mikor érdemes az SPX-et vagy az SDK-t használni.

SPX használata:
* A beszédfelismerési szolgáltatás funkcióit minimális telepítéssel szeretné kísérletezni, és nincs kód
* A beszédfelismerési szolgáltatást használó éles alkalmazásokhoz viszonylag egyszerű követelmények vonatkoznak.

Az SDK használata:
* Egy adott nyelven vagy platformon belül szeretné integrálni a beszédfelismerő szolgáltatás funkcióit (például C#, Python, C++)
* Olyan összetett követelményekkel rendelkezik, amelyek speciális szolgáltatási kérelmeket igényelhetnek, vagy egyéni viselkedést fejlesztenek, beleértve a válaszadási adatfolyamot is

## <a name="core-features"></a>Alapvető funkciók

* Beszédfelismerés – beszéd – szöveg konvertálása hangfájlokból vagy közvetlenül egy mikrofonból, vagy egy rögzített beszélgetés átírása.

* Beszédfelismerés – a szöveg és a beszéd szöveggé konvertálása szöveges fájlok bevitele vagy közvetlenül a parancssorból való bevitel használatával. [SSML-konfigurációk](speech-synthesis-markup.md), valamint [standard vagy neurális hangok](speech-synthesis-markup.md#standard-neural-and-custom-voices)használatával testreszabhatja a beszédfelismerési kimenet jellemzőit.

* Beszéd fordítása – a hanganyagot nyelvre lefordíthatja a célnyelv szövegére.

* Futtatás Azure számítási erőforrásokon – SPX-parancsok küldése egy Azure távoli számítási erőforráson való futtatásra a használatával `spx webjob` .

## <a name="get-started"></a>Bevezetés

Az SPX használatának megkezdéséhez tekintse meg az alapvető tudnivalókat ismertető [cikket](spx-basics.md). Ebből a cikkből megtudhatja, hogyan futtathat néhány alapszintű parancsot az SPX-ben, és hogyan jeleníti meg valamivel több speciális parancsot a Batch-műveletek futtatásához a beszédfelismerés és a szöveg közötti kommunikációhoz. Az alapismeretek című cikk elolvasása után elegendő az SPX-szintaxis megismerése az egyéni parancsok írásának megkezdéséhez, illetve az egyszerű beszédfelismerési műveletek automatizálásához.

## <a name="next-steps"></a>Következő lépések

- [Az SPX alapjai](spx-basics.md)
- Ha a használati eset összetettebb, [a SPEECH SDK beszerzése](speech-sdk.md)
