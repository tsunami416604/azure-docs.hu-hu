---
title: 'Gyors útmutató: Language Understanding (LUIS) SDK-ügyfél kódtárai'
description: Hozzon létre és kérdezzen egy LUIS-alkalmazást a LUIS SDK ügyféloldali kódtárakkal a C#, a Python vagy a JavaScript használatával.
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, mesterséges intelligencia, AI, természetes nyelvi feldolgozás, NLP, természetes nyelvi megértés, NLU, AI-beszélgetés, társalgási AI, AI Csevegőrobot, Csevegőrobot Maker, LUIS, NLP AI, Luis AI, Azure Luis, természetes nyelv megismerése
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323237"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>Gyors útmutató: Language Understanding (LUIS) SDK-ügyfél kódtárai a LUIS-alkalmazás létrehozásához és lekérdezéséhez

Hozzon létre és kérdezzen egy LUIS-alkalmazást a LUIS SDK ügyféloldali kódtárakkal a C#, a Python vagy a JavaScript használatával.

Language Understanding (LUIS) lehetővé teszi, hogy egyéni gépi tanulási intelligenciát alkalmazzon egy felhasználó társalgási, természetes nyelvi szövegére az általános jelentés megbecslése érdekében, és a vonatkozó, részletes információk kiolvasását.

* Az **authoring SDK** ügyféloldali kódtára lehetővé teszi a Luis-alkalmazás létrehozását, szerkesztését, betanítását és közzétételét. * Az **előrejelzési FUTTATÓKÖRNYEZET SDK** -ügyfelének könyvtára lehetővé teszi a közzétett alkalmazás lekérdezését.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az alkalmazást a Luis- [portálról](https://www.luis.ai) , és törölheti az Azure-erőforrásokat a [Azure Portalból](https://portal.azure.com/).

## <a name="troubleshooting"></a>Hibaelhárítás

* Hitelesítés az ügyfél-függvénytárban – a hitelesítési hibák általában azt jelzik, hogy helytelen kulcs & végpontot használtak. Ez a rövid útmutató az előrejelzési futtatókörnyezet szerzői kulcsát és végpontját használja kényelmi feltételként, de csak akkor működik, ha még nem használta a havi kvótát. Ha nem tudja használni a szerzői kulcsot és a végpontot, használja az előrejelzési futtatókörnyezet kulcsát és a végpontot az előrejelzési futtatókörnyezet SDK-ügyfelének eléréséhez.
* Entitások létrehozása – ha hibaüzenet jelenik meg az oktatóanyagban használt beágyazott gépi tanulási entitás létrehozásakor, győződjön meg róla, hogy átmásolta a kódot, és nem változtatta meg a kódot egy másik entitás létrehozásához.
* Példa hosszúságú kimondott szöveg létrehozása – ha hibaüzenet jelenik meg, amely az oktatóanyagban használt címkézett példa kiírását hozza létre, ügyeljen arra, hogy a kódot másolja, és ne változtassa meg a kódot egy másik címkézett példa létrehozásához.
* Képzés – ha betanítási hibaüzenetet kap, ez általában üres alkalmazást (például hosszúságú kimondott szöveg), vagy helytelenül formázott, szándékosan vagy entitásokat tartalmazó alkalmazást jelez.
* Egyéb hibák – mivel a kód szöveg-és JSON-objektumokkal meghívja az ügyféloldali kódtárakat, ügyeljen arra, hogy ne változzon meg a kód.

Egyéb hibák – ha az előző listában nem szereplő hibaüzenetet kap, tudassa velünk az oldal alján található visszajelzések megadásával. Adja meg a telepített ügyféloldali kódtárak programozási nyelvét és verzióját. 

## <a name="next-steps"></a>Következő lépések

* [Mi a Language Understanding (LUIS) API?](what-is-luis.md)
* [Mi újság?](whats-new.md)
* [Szándékok](luis-concept-intent.md), [entitások](luis-concept-entity-types.md)és [példa hosszúságú kimondott szöveg](luis-concept-utterance.md)és [előre összeépített entitások](luis-reference-prebuilt-entities.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code)található.
