---
title: Szöveg-hang transzformációs kimenet – beszédszolgáltatások finomhangolása
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés SDK naplózásának engedélyezése.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 8d3e25f8217f3cc8772de9fbbb06a407008ca6f6
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65593893"
---
# <a name="fine-tune-text-to-speech-output"></a>Szövegfelolvasási kimenet finomhangolása

Az Azure Speech a szolgáltatások lehetővé teszik a sebesség, írásmódja, kötet, felébresztve és körvonalának használatával kimeneti szöveg-hang transzformációs [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md). SSML egy XML-alapú jelölőnyelv, amely tájékoztatja a szolgáltatást milyen funkcióhoz hangolása címkék segítségével. A SSML majd üzenettel a szöveg-hang transzformációs szolgáltatás minden kérés törzsében. A testreszabás egyszerűbbé a beszédfelismerési szolgáltatás most már olyan egy [hangalapú hangolása](https://aka.ms/voicetuning) eszköz, amellyel vizuálisan vizsgálatát és finomhangolása az szöveg-hang transzformációs kimenete valós időben.

A szóbeli hangolása eszköz támogatja a Microsoft [standard](language-support.md#standard-voices), [Neurális](language-support.md#text-to-speech), és [egyéni beszédhangot](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>A beszédfelismerési hangolása eszköz használatának első lépései

A szöveg-hang transzformációs kimenetet a hangalapú hangolása eszközzel való elősegítésével előtt el kell hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy [Microsoft-fiók ingyenes](https://account.microsoft.com/account) Ha még nem rendelkezik.
2. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) Ha még nem rendelkezik. Kattintson a **ingyenes próba megkezdése**, és az új Azure-fiók létrehozása a Microsoft-fiókjával.

3. Beszédszolgáltatások előfizetés létrehozása az Azure Portalon. Részletes útmutató [speech erőforrás létrehozása](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) érhetők el.
   >[!NOTE]
   >Beszéd erőforrás létrehozásakor az Azure Portalon az Azure-beli hely információkat kell felel meg a szöveg-beszéd átalakítás hangalapú régióban. Neurális beszédfelismerő támogat egy sub Azure-helyen. Támogatási teljes listáját lásd: [régiók](regions.md#text-to-speech).

   >[!NOTE]
   >Szüksége lesz egy F0 vagy az Azure Portalon hozza létre a szolgáltatás használata előtt S0 kulcsot. Hangolási hangtípus **nem** támogatja a [30 napos ingyenes próbaverzió kulcs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Jelentkezzen be a [hangalapú hangolása](https://aka.ms/voicetuning) -portálon, és csatlakozzon a Speech Services-előfizetés. Válasszon egy beszédszolgáltatások előfizetéssel, és ezután hozzon létre egy projektet.
5. Válassza ki **új hangolása**. Ezután kövesse az alábbi lépéseket:

   * Keresse meg és jelölje ki **minden előfizetés**.  
   * Válassza ki **csatlakozás meglévő előfizetés**.  
     ![Csatlakozás a meglévő előfizetés](./media/custom-voice/custom-voice-connect-subscription.png).
   * Adja meg az Azure Speech Services előfizetési kulcs, majd válassza ki **Hozzáadás**. Az előfizetési kulcsok a Speech testreszabási portálon érhetők el a [előfizetések lapján](https://go.microsoft.com/fwlink/?linkid=2090458). Az erőforrás-kezelés panelről is sikerült beolvasni a kulcsokat a [az Azure portal](https://portal.azure.com/).
   * Ha több mint beszédszolgáltatások előfizetéssel rendelkezik, amelyet használni szeretne, ismételje meg ezeket a lépéseket minden egyes előfizetés esetén.

## <a name="customize-the-text-to-speech-output"></a>A szöveg-hang transzformációs kimeneti testreszabása

Most, hogy létrehozta a fiókok és az előfizetés csatolva, elkezdheti a szöveg-hang transzformációs kimeneti hangolása.

1. Válassza ki a hangot.
2. Adjon meg a szerkeszteni kívánt szöveg.
3. A kezdéshez szerkesztéseket hanglejátszás betekintést nyerhet a kimenethez.
4. Válassza ki a word/mondat finomítani szeretné, és kísérletezzen a különböző SSML-alapú függvényekkel.

>[!TIP]
> SSML módosításával és Finomhangolás a hangalapú kimeneti kapcsolatos részletes információkért lásd: [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Korlátozások

Neurális hangalapú hangolásra kismértékben eltér a Standard és egyéni beszédhangot hangolása.

* Intonation Neurális beszédhangot nem támogatott.
* Lényegét és mennyiségi funkciók csak a teljes mondatokból dolgozhat. Ezek a funkciók nem érhetők el a word szintjén.
* A sebesség néhány Neurális beszédhangot alapján szavak, míg mások igényelnek, hogy válassza ki a teljes mondatokból hangolásával.

> [!TIP]
> A beszédfelismerési hangolása eszköz környezetfüggő arról nyújt tájékoztatást funkciók és a finomhangolással kapcsolatban.

## <a name="next-steps"></a>További lépések
* [Beszéd erőforrás létrehozása az Azure-ban](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Hangalapú hangolás elkezdéséhez](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Beszéd összefoglaló Markup Language (SSML)](speech-synthesis-markup.md)
