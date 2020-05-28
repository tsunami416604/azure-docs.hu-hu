---
title: Mi a fordító? -Translator
titlesuffix: Azure Cognitive Services
description: A fordítót integrálhatja alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba a többnyelvű felhasználói élmény biztosítása érdekében.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 7044954d2ba67e61172c08c0a77acaaaec3822c6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992674"
---
# <a name="what-is-the-translator"></a>Mi a fordító?

A fordító egyszerűen integrálható alkalmazásaiba, webhelyeire, eszközeibe és megoldásaiba. Lehetővé teszi többnyelvű felhasználói felületek hozzáadását [több mint 60 nyelven](languages.md), és bármely hardverplatformon és bármely operációs rendszerrel használható szöveg-szöveg vektorú nyelvi fordítások végrehajtásához.

A fordító a gépi tanulási és AI-algoritmusok [Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) -gyűjteményének része a felhőben, és könnyen fogyasztható a fejlesztési projektekben.

## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése

A Translator egy felhőalapú gépi fordítási szolgáltatás. A fő szolgáltatás a fordító, amely számos Microsoft-terméket és-szolgáltatást biztosít, és világszerte több ezer vállalat használja az alkalmazásaikban és munkafolyamataiban, ami lehetővé teszi, hogy a tartalom globális közönség számára is elérhető legyen.

A fordító által működtetett beszéd fordítás a [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/)-en keresztül is elérhető. Egyesíti a Translator Speech API és a Custom Speech Service funkcióit egy egységes és teljes mértékben testreszabható szolgáltatásban.A Speech Service leváltja a Translator Speech API-t, amely 2019. október 15-től kezdve visszavonunk.

## <a name="language-support"></a>Nyelvi támogatás

A Microsoft Translator többnyelvű támogatást nyújt a fordításhoz, az átíráshoz, a nyelvfelismeréshez és a szótárakhoz. A teljes listát a [nyelvi támogatást](language-support.md) ismertető szakaszban, vagy programozott módon a [REST API-t](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) megnyitva találja.  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator – neurális gépi fordítás

A neurális gépi fordítás (Neural Machine Translation, NMT) az új etalon a minőségi, mesterséges intelligencián alapuló gépi fordítások területén. A korábbi statisztikai gépi fordítási (Statistical Machine Translation, SMT) technológia helyébe lép, amely a 2010-es évek közepére elérte a minősége felső korlátját.

Az NMT nem csupán a nyers fordítások pontozását tekintve biztosít jobb fordításokat az SMT-nél, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT ezzel szemben az adott szavak előtt és után álló néhány szóból álló közvetlen kontextust vette alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Ami észrevehető, az a fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab.

További információ [az NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="language-customization"></a>Nyelvi testreszabás

A Microsoft Translator szolgáltatás alapszintű, egyéni fordítója a fordítóval együttműködve segíti a neurális fordítói rendszer testreszabását és az adott terminológia és stílus fordításának javítását.

A Custom Translator használatával olyan fordítási rendszereket állíthat össze, amelyek kezelik a vállalkozásában vagy az iparágban alkalmazott terminológiát. A testreszabott fordítási rendszer ezután egyszerűen integrálható a meglévő alkalmazásaiba, munkafolyamataiba és webhelyeibe, a normál fordítón keresztül, a kategória paraméter használatával.

További információk [a nyelvi testreszabásról](customization.md).

## <a name="next-steps"></a>További lépések

- [Regisztráljon](translator-text-how-to-signup.md) egy hozzáférési kulcsra.
- Az [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) az API-k műszaki dokumentációját biztosítják.
- [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
