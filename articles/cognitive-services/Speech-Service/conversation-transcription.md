---
title: Beszélgetés átírása (Előzetes verzió) – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszélgetésátírás olyan megoldás az értekezletekhez, amely egyesíti az elismerést, a hangszóró-azonosítót és a diarizációt, hogy minden beszélgetés átírását biztosítsa.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: ba56c7fb989658195f6394c7390c4f83027c7c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479739"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Mi az értekezleteken a beszélgetésátírás (előzetes verzió)?

A Beszélgetésátírás egy [beszéd-szöveg](speech-to-text.md) megoldás, amely egyesíti a beszédfelismerést, a hangszóró azonosítását és az egyes előadók (más néven _diarization)_ mondathozzárendelését, hogy valós idejű és/vagy aszinkron átírást biztosítson bármely beszélgetéshez. A Beszélgetésátírás megkülönbözteti a beszélgetések hangszóróit, hogy meghatározza, ki mit és mikor mondott, és megkönnyíti a fejlesztők számára, hogy beszédről szövegre írják a többhangszórós diarizálást végző alkalmazásaikat.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Időbélyegek** – minden egyes szódakiszöveg rendelkezik időbélyeggel, így könnyen megtalálhatja, amikor egy kifejezést mondott.
- **Olvasható átiratok** - az átiratok formázását és írásjeleket tartalmaznak, hogy a szöveg szorosan egyezik az elhangzottakkal.
- **Felhasználói profilok** – a felhasználói profilok felhasználói hangminták gyűjtésével és aláírásgenerálásra való elküldésével jönnek létre.
- **Hangszóró-azonosítás** – a hangszórókat felhasználói profilok segítségével azonosítják, és mindegyikhez _hangszóró-azonosítót_ rendelnek.
- **Többhangszórós diarizáció** - határozza meg, hogy ki mit mondott, az egyes hangszóró-azonosítókkal szintetizálva szintetizálja a hangfolyamot.
- **Valós idejű átírás** – élő átiratokat ad arról, hogy ki mit és mikor mond.
- **aszinkron transzkripció** – többcsatornás hangfolyam használatával nagyobb pontosságú átiratokat biztosíthat.

> [!NOTE]
> Bár a beszélgetés átírása nem korlátozza a beszélők számát a szobában, munkamenetenként 2-10 hangszóróra van optimalizálva.

## <a name="use-cases"></a>Használati esetek

### <a name="inclusive-meetings"></a>Befogadó találkozók

Ahhoz, hogy a találkozók mindenki számára befogadóak legyenek, például a siketek és nagyothallók számára, fontos, hogy az átírás valós időben legyen. A beszélgetésátírás valós idejű módban az értekezlet hangfelvételét veszi igénybe, és meghatározza, hogy ki mit mond, lehetővé téve az értekezlet minden résztvevőjének, hogy késedelem nélkül kövessék az átiratot, és részt vegyenek az értekezleten.

### <a name="improved-efficiency"></a>Nagyobb hatékonyság

Az értekezlet résztvevői az értekezletre összpontosíthatnak, és a beszélgetési átírásra hagyhatják a jegyzetelést. A résztvevők aktívan részt vehetnek az értekezleten, és gyorsan nyomon követhetik a következő lépéseket, az átirat használatával, ahelyett, hogy jegyzeteket vennének, és esetleg kihagynának valamit az értekezlet során.

## <a name="how-it-works"></a>Működés

Ez egy magas szintű áttekintést, hogyan beszélgetés átírás működik.

![Beszélgetésátírási diagram importálása](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Várható bemenetek

- **Többcsatornás hangfolyam** – A specifikációval és a tervezéssel kapcsolatos részletekről a [Microsoft Speech Device SDK mikrofonja látható.](https://aka.ms/cts/microphone) Ha többet szeretne megtudni, vagy fejlesztői készletet szeretne vásárolni, olvassa el [a Microsoft Speech Device SDK beolvasása](https://aka.ms/cts/getsdk).
- **Felhasználói hangminták** – Beszélgetés átírás szükséges felhasználói profilok előtt a beszélgetést. Minden felhasználótól össze kell gyűjtenie a hangfelvételeket, majd el kell küldenie a felvételeket az [Aláírás-generálási szolgáltatásnak](https://aka.ms/cts/signaturegenservice) a hang ellenőrzéséhez és a felhasználói profilok létrehozásához.

## <a name="real-time-vs-asynchronous"></a>Valós idejű és aszinkron

Beszélgetés átírás kínál három átírási mód:

### <a name="real-time"></a>Valós idejű

A hangadatokfeldolgozása élőben történik, hogy visszatérjen a hangszóró azonosítója + átirata. Akkor válassza ezt a módot, ha az átírási megoldás követelménye az, hogy a beszélgetés résztvevői nek élő átirati nézetet biztosítson a folyamatban lévő beszélgetésükről. Például egy olyan alkalmazás létrehozása, amely az értekezleteket hozzáférhetőbbé teszi a siketek és nagyothallók számára, ideális használati eset a valós idejű átíráshoz.

### <a name="asynchronous"></a>Aszinkron

A hangadatok kötegelt feldolgozása a hangszóró-azonosító és az átirat visszaadása érdekében történik. Válassza ki ezt a módot, ha az átírási megoldás követelménye, hogy nagyobb pontosságot biztosítson élő átiratnézet nélkül. Ha például olyan alkalmazást szeretne létrehozni, amely lehetővé teszi az értekezlet résztvevői számára, hogy könnyen felzárkózzanak a kihagyott értekezletekhez, használja az aszinkron átírási módot a nagy pontosságú átírási eredmények eléréséhez.

### <a name="real-time-plus-asynchronous"></a>Valós idejű és aszinkron

A hangadatokfeldolgozása élőben történik, hogy visszatérjen a hangszóró azonosítója + átirata, és ezen felül egy kérés jön létre, hogy az aszinkron feldolgozás révén is nagy pontosságú átiratot kapjon. Akkor válassza ezt a módot, ha az alkalmazásnak valós idejű átírásra van szüksége, de nagyobb pontossági átiratra is van szükség a beszélgetés vagy értekezlet után való használatra.

## <a name="language-support"></a>Nyelvi támogatás

Jelenleg a Beszélgetés átírása támogatja az "en-US" és a "zh-CN" a következő régiókban: *centralus* és *eastasia*. Ha további területi támogatásra van szüksége, forduljon a [Beszélgetésátírás szolgáltatás stábjához.](mailto:CTSFeatureCrew@microsoft.com)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszélgetések valós idejű átírása](how-to-use-conversation-transcription-service.md)
