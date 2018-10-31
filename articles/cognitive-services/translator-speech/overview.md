---
title: Mi az a Translator Speech szolgáltatás?
titleSuffix: Azure Cognitive Services
description: A Translator Speech szolgáltatás API-jával adhat tolmácsolást és a beszéd lefordított szöveggé alakítását alkalmazásaihoz.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 641081e4a01aa5810d569795c10e967e4b46568e
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023577"
---
# <a name="what-is-translator-speech-api"></a>Mi az a Translator Speech API?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

A Translator Speech API-val teljes körű, valós idejű beszédfordítással bővíthetők az alkalmazások, eszközök, illetve a többnyelvű beszédfordítást igénylő megoldások a cél operációs rendszertől vagy a fejlesztési nyelvtől függetlenül. Az API tolmácsoláshoz és beszéd lefordított szöveggé alakításához is használható.

A Translator Text API egy Azure-szolgáltatás, amely a felhőalapú gépi tanulási és mesterséges intelligenciára épülő algoritmusokat biztosító, a fejlesztési projektekben készen használható [Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)-gyűjtemény részét képezi.

A Translator Speech API-val az ügyfélalkalmazások beszédhangot streamelnek a szolgáltatásnak, és szöveg. és hangalapú eredmények streamjét kapják vissza, amelyek tartalmazzák a felismert szöveget a forrásnyelven és annak fordítását a célnyelven. A szövegeredmények a mély neurális hálózatok által működtetett automatikus beszédfelismerés (ASR) a bejövő audiostreamre való alkalmazásával jönnek létre. Az ASR nyers kimenetét az új, TrueText elnevezésű új technológia javítja, hogy pontosabban tükrözze a felhasználói szándékot. A TrueText például eltávolítja az olyan megakadásokat, mint a hümmögés és a köhögés, az ismétlődő szavakat, visszaállítja a helyes központozást, valamint a kis- és nagybetűs írásmódot. Lehetőség van a profanitás kitakarására vagy eltávolítására is. A felismerő- és fordítómotorok kifejezetten a beszélgetések kezelésére lettek kifejlesztve. 

A Translator Speech szolgáltatás csendészleléssel határozza meg a kimondott szöveg végét. A beszédhangokban beállt szünet esetén a szolgáltatás visszastreameli a végleges eredményt a kész kimondott szöveghez. A szolgáltatás visszaküldhet részleges eredményeket is, amelyek köztes felismerést és fordítást biztosítanak a folyamatban lévő kimondott szöveghez. 

Tolmácsolás esetén a szolgáltatás lehetőséget biztosít a beszédszintézisre (szövegfelolvasásra) a kimondott szövegből a célnyelveken. A szövegfelolvasás audioanyaga az ügyfél által megadott hangformátumban jön létre. A WAV és az MP3 formátum érhető el.

A Translator Speech API a WebSocket protokollt használja az ügyfél és a kiszolgáló közötti teljes körű duplex kommunikációs csatorna biztosításához. 

## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése
A Microsoft Translator egy felhőalapú gépi fordítási szolgáltatás. A szolgáltatás magját a [Translator Text API](https://www.microsoft.com/en-us/translator/translatorapi.aspx) és a Translator Speech API adja, amelyek a Microsoft különféle termékeit és szolgáltatásait szolgálják ki, és amelyeket világszerte vállalkozások ezrei használnak alkalmazásaikban és munkafolyamataikban, hogy tartalmaikat a globális közönség elé tárják.

Itt tudhat meg többet a [Microsoft Translator szolgáltatásról](https://www.microsoft.com/en-us/translator/home.aspx).

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Microsoft Translator – neurális gépi fordítás (NMT)
A Translator Speech API a korábbi statisztikai gépi fordítások (SMT) és az újabb neurális gépi fordítások (NMT) használatával biztosítja a fordítást.

A statisztikai gépi fordítás elérte a teljesítménye csúcsát. A fordítás minősége már nem fog jelentős mértékben fejlődni az SMT használatakor általános rendszerek esetében. Egy új, mesterséges intelligenciára épülő fordítási technológia van feltörekvőben, amely a neurális hálózatokon alapul.

Az NMT nem csupán a nyers fordítások minőségi pontozását tekintve biztosít jobb fordításokat, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak, mint az SMT. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT csak néhány, az adott szavak előtt és után álló szóból álló közvetlen kontextust veszi alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Mindössze a következő eltéréseket lehet észrevenni:
* A fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab.
* Inkompatibilitás a meglévő központtestreszabási funkciókkal (a Microsoft Translator Text API-val használva)

A támogatott beszédfordítási nyelvek mindegyikét az NMT működteti. Ezért az összes tolmácsolási funkció az NMT-t használja. 

A beszéd lefordított szöveggé alakítása esetenként az NMT és az SMT kombinációját használja a nyelvpártól függően. Abban az esetben, ha a célnyelvet az NMT támogatja, a teljes fordítás NMT-alapú. Ha a célnyelvet az NMT nem támogatja, a fordítás az NMT és SMT együttes használatával készül, és az angol nyelvet használja kiindulási pontként a két nyelv között. 

A támogatott nyelvek listáját a [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx) webhelyen találhatja meg. 

További információ [az NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Regisztráció](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Kódolás megkezdése](quickstarts/csharp.md)

## <a name="see-also"></a>Lásd még
- [A Cognitive Services dokumentációjának oldala](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
- [A megoldásokkal és a díjszabással kapcsolatos információk](https://www.microsoft.com/en-us/translator/home.aspx) 
