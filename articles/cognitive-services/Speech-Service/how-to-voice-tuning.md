---
title: Szöveg-beszéd kimeneti-beszédfelismerési szolgáltatás finomhangolása
titleSuffix: Azure Cognitive Services
description: Engedélyezze a naplózást a Speech SDK-ban.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562739"
---
# <a name="fine-tune-text-to-speech-output"></a>Szövegfelolvasási kimenet finomhangolása

Az Azure Speech Services lehetővé teszi a szöveg-beszéd kimenet sebességének, kiejtésének, mennyiségének, dobásának és kontúrjának módosítását a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használatával. A SSML egy XML-alapú Markup Language, amely címkék használatával tájékoztatja a szolgáltatást arról, hogy milyen szolgáltatásra van szükség a hangoláshoz. Ezután a SSML üzenetet küldi a rendszer az egyes kérések törzsében a szöveg-beszéd szolgáltatásnak. A testreszabási folyamat leegyszerűsítése érdekében a Speech Services most [](https://aka.ms/voicetuning) egy hanghangolási eszközt is kínál, amellyel valós időben vizsgálhatja és finomíthatja a szöveg-beszéd kimeneteket.

A hanghangolási eszköz támogatja a Microsoft [szabványos](language-support.md#standard-voices), [neurális](language-support.md#text-to-speech)és [Egyéni hangját](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Ismerkedés a hanghangolási eszközzel

Mielőtt elkezdené a szöveg-beszéd kimenet finomhangolását a hanghangolás eszközzel, el kell végeznie az alábbi lépéseket:

1. Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes Microsoft-fiók](https://account.microsoft.com/account) .
2. Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) . Kattintson az **ingyenes indítás**lehetőségre, és hozzon létre egy új Azure-fiókot a Microsoft-fiók használatával.

3. Hozzon létre egy Speech Services-előfizetést a Azure Portal. A [beszédfelismerési erőforrások létrehozásával](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) kapcsolatos részletes utasítások érhetők el.
   >[!NOTE]
   >Amikor létrehoz egy beszédfelismerési erőforrást a Azure Portalban, az Azure-beli hely adatainak egyezniük kell a TTS hangterülettel. A neurális TTS hang támogatja az Azure-beli helyszínek egy alkészletét. A támogatás teljes listáját a [régiók](regions.md#text-to-speech)című részben tekintheti meg.

   >[!NOTE]
   >A szolgáltatás használata előtt létre kell hoznia egy F0 vagy egy S0-kulcsot a Azure Portalban. A hanghangolás **nem** támogatja a [30 napos ingyenes próbaverziós kulcsot](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Jelentkezzen be a [](https://aka.ms/voicetuning) hanghangolási portálra, és kapcsolódjon a Speech Services-előfizetéshez. Válasszon egyetlen Speech Services-előfizetést, majd hozzon létre egy projektet.
5. Válassza az **új hangolás**lehetőséget. Ezután kövesse az alábbi lépéseket:

   * Keresse meg és válassza ki **az összes**előfizetést.  
   * Válassza ki **csatlakozás meglévő előfizetés**.  
     ![Meglévő előfizetés](./media/custom-voice/custom-voice-connect-subscription.png)összekötése.
   * Adja meg az Azure Speech Services előfizetési kulcsát, majd kattintson a **Hozzáadás**gombra. Az előfizetési kulcsok az [előfizetés lapról](https://go.microsoft.com/fwlink/?linkid=2090458)elérhető Speech customization Portalon érhetők el. A kulcsok a [Azure Portal](https://portal.azure.com/)erőforrás-kezelés paneljén is beszerezhetők.
   * Ha több, a használni kívánt Speech Services-előfizetéssel rendelkezik, ismételje meg ezeket a lépéseket minden előfizetéshez.

## <a name="customize-the-text-to-speech-output"></a>A szöveg és a beszéd közötti kimenet testreszabása

Most, hogy létrehozta a fiókokat, és összekapcsolta az előfizetését, megkezdheti a szöveg-beszéd kimenet finomhangolását.

1. Válasszon egy hangot.
2. Adja meg a szerkeszteni kívánt szöveget.
3. A szerkesztés megkezdése előtt játssza le a hanganyagot, hogy az a kimenetet is felhasználja.
4. Válassza ki a módosítandó szót/mondatot, és kezdjen el kísérletezni különböző SSML-alapú függvényekkel.

>[!TIP]
> A SSML beállításával és a hangkimenet hangolásával kapcsolatos részletes információkért lásd: [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Korlátozások

A neurális hang finomhangolása némileg eltér a standard és az egyéni hangok finomhangolásával.

* A hanglejtés nem támogatott a neurális hangok esetében.
* A szurok és a kötet funkciói csak teljes mondatokkal működnek. Ezek a funkciók a szó szintjén nem érhetők el.
* A díjszabás miatt bizonyos neurális hangokat a szavak alapján lehet hangolni, míg mások a teljes mondatok kiválasztását igénylik.

> [!TIP]
> A hanghangolási eszköz környezetfüggő információkat biztosít a funkciókról és a hangolásról.

## <a name="next-steps"></a>További lépések
* [Beszédfelismerési erőforrás létrehozása az Azure-ban](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Hang hangolásának megkezdése](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)
