---
title: Alkalmazás közzététele – LUIS
titleSuffix: Azure Cognitive Services
description: Ha befejezte az aktív LUIS-alkalmazás kiépítését és tesztelését, tegye elérhetővé az ügyfélalkalmazás számára a végponton való közzététellel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053436"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Az aktív, betanított alkalmazás közzététele átmeneti vagy éles végponton

Amikor befejezte az aktív LUIS-alkalmazás kiépítését, betanítását és tesztelését, tegye elérhetővé az ügyfélalkalmazás számára a végponton való közzététellel. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Közzététel

1. A végponton való közzétételhez válassza a Jobb felső **panelen** a Közzététel lehetőséget. 

    ![Közzététel gomb felül, jobb oldali navigációs sáv](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Adja meg a közzétett előrejelzési végpont beállításait, majd válassza a **Közzététel lehetőséget.**

    ![Válassza a közzétételi beállítások lehetőséget, majd válassza a Közzététel gombot](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Közzétételi tárolóhelyek

Az előugró ablak megjelenítésekor válassza ki a megfelelő foglalatot: 

* Előkészítés
* Production 

Mindkét közzétételi bővítőhely használatával ez lehetővé teszi, hogy az alkalmazás két különböző verziója érhető el a közzétett végpontokon, vagy ugyanazt a verziót két különböző végpontokon. 

### <a name="publishing-regions"></a>Közzétételi régiók

Az alkalmazás az Azure-erőforrások **kezelése** -> lapon a LUIS-portálon hozzáadott LUIS-előrejelzési végpont-erőforrásokhoz társított összes régióban**[megjelenik.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

Ha például egy [www.luis.ai-én](https://www.luis.ai)létrehozott alkalmazás esetén két régióban, a **Westusban** és **az Eastusban**hoz létre LUIS-erőforrást, és ezeket erőforrásként adja hozzá az alkalmazáshoz, az alkalmazás mindkét régióban megjelenik. A LUIS-régiókról a [Régiók című témakörben talál](luis-reference-regions.md)további információt.

> [!TIP]
> Jelenleg 3 szerzői régiók. Abban a régióban kell szerzőnek lennie, ahol közzé kíván tenni. Ha minden régióban közzé kell tennie, mind a 3 szerzői régióban kezelnie kell a szerzői folyamatot és az eredményül kapott betanított modellt. 


## <a name="configuring-publish-settings"></a>Közzétételi beállítások konfigurálása

Miután kiválasztotta a tárolóhelyet, adja meg a közzétételi beállításokat:

* Hangulatelemzés
* Helyesírás-ellenőrzés - csak a v2 előrejelzési végpont
* Beszéd alapozása 

A közzététel után ezek a beállítások a **Kezelés** szakasz **Közzétételi beállítások** lapján tekinthetők meg. A beállításokat minden közzétételnél módosíthatja. Ha megszakítja a közzétételt, a közzététel során végrehajtott módosítások is törlődnek. 

### <a name="when-your-app-is-published"></a>Az alkalmazás közzétételekor

Az alkalmazás sikeres közzététele után a böngésző tetején megjelenik egy sikeres értesítés. Az értesítés a végpontokra mutató hivatkozást is tartalmaz. 

Ha szüksége van a végpont URL-címére, jelölje ki a hivatkozást. A végpont URL-címeit úgy is megérheti, hogy a felső menüben a Kezelés lehetőséget **választja,** majd a bal oldali **menüben** válassza az Azure Resources lehetőséget. 

## <a name="sentiment-analysis"></a>Hangulatelemzés

<a name="enable-sentiment-analysis"></a>

A hangulatelemzés lehetővé teszi a LUIS számára, hogy integrálódjon [a Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) szolgáltatással, hogy hangulat- és kulcskifejezés-elemzéseket biztosítson. 

Nem kell megadnia a Text Analytics-kulcsot, és nincs számlázási díj a szolgáltatás az Azure-fiók. 

A hangulatadatok az adatok pozitív (1-hez közelebbi) vagy negatív (0-hoz közelebbi) hangulatát jelző 1 és 0 közötti pontszám. A , `positive` `neutral`és `negative` a hangulatcímkéje támogatott kulturális környezetszerint történik. Jelenleg csak az angol nyelv támogatja a hangulatcímkéket. 

A JSON-végpontra adott válaszról a hangulatelemzéssel kapcsolatos további információkért [lásd: Hangulatelemzés](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Helyesírás-javítás

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

A helyesírás-javítás a LUIS-felhasználó kimondott szövegének előrejelzése előtt történik. A válaszban láthatja az eredeti utterance (kifejezés) bármilyen módosítását, beleértve a helyesírást is.

## <a name="speech-priming"></a>Beszéd alapozása

A beszédfelismerési alapozás a LUIS-modell beszédfelismerési szolgáltatásokba küldésének folyamata a szöveg beszédfelismerésre való átalakítása előtt. Ez lehetővé teszi, hogy a beszédszolgáltatás pontosabban biztosítsa a beszédkonverziót a modellhez. Ez lehetővé teszi a robot beszéd- és LUIS-kéréseit és válaszait egy hívásban egy beszédhívással és egy LUIS-válasz visszaszerzésével. Összességében kevesebb késést biztosít.

## <a name="next-steps"></a>További lépések

* A [Kulcsok kezelése](./luis-how-to-azure-subscription.md) című témakörben található kulcsok hozzáadása a LUIS-hoz, valamint a Bing helyesírás-ellenőrző kulcsának beállítása és az összes leképezés felvétele az eredményekbe című témakörben.
* A közzétett alkalmazás tesztelésével kapcsolatos útmutatást [az alkalmazás betanítása és tesztelése](luis-interactive-test.md) című témakörben találja.

