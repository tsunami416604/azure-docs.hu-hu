---
title: Mi az a Translator? -Translator
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
ms.openlocfilehash: 206e1bc86404fa41b84647c50482dbe63301c0af
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425959"
---
# <a name="what-is-translator"></a>Mi az a Translator?

Az Azure Cognitive Services Translator egy felhőalapú gépi fordítási szolgáltatás, amely az intelligens alkalmazások létrehozásához szükséges, az [azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) a kognitív API-család része. A Translator egyszerűen integrálható alkalmazásaiba, webhelyeire, eszközeibe és megoldásaiba. Lehetővé teszi a többnyelvű felhasználói élmény [több mint 70 nyelven](languages.md)való hozzáadását, és bármilyen hardveres platformon használható bármely operációs rendszerrel a szöveg és a szöveg közötti nyelvi fordításhoz.

## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése

Az Azure Cognitive Services Translator egy felhőalapú gépi fordítási szolgáltatás. A Translator számos Microsoft-terméket és-szolgáltatást felhasznál, és világszerte több ezer vállalat használja alkalmazásaikban és munkafolyamataiban, ami lehetővé teszi, hogy a tartalmak globális közönséget érjenek el.

A fordító által működtetett beszédfelismerés a [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/)-en keresztül is elérhető. Egyesíti a Translator Speech API és a Custom Speech Service funkcióit egy egységes és teljes mértékben testreszabható szolgáltatásban. 

## <a name="language-support"></a>Nyelvi támogatás

A Microsoft Translator többnyelvű támogatást nyújt a fordításhoz, az átíráshoz, a nyelvfelismeréshez és a szótárakhoz. A teljes listát a [nyelvi támogatást](language-support.md) ismertető szakaszban, vagy programozott módon a [REST API-t](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) megnyitva találja.  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator – neurális gépi fordítás

A neurális gépi fordítás (Neural Machine Translation, NMT) az új etalon a minőségi, mesterséges intelligencián alapuló gépi fordítások területén. A korábbi statisztikai gépi fordítási (Statistical Machine Translation, SMT) technológia helyébe lép, amely a 2010-es évek közepére elérte a minősége felső korlátját.

Az NMT nem csupán a nyers fordítások pontozását tekintve biztosít jobb fordításokat az SMT-nél, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT ezzel szemben az adott szavak előtt és után álló néhány szóból álló közvetlen kontextust vette alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Ami észrevehető, az a fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab.

További információ [az NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="language-customization"></a>Nyelvi testreszabás

A fordítói szolgáltatás bővítménye, az egyéni fordító a fordítóval együtt használható a neurális fordítási rendszer testreszabásához és az adott terminológia és stílus fordításának javításához.

A Custom Translator használatával olyan fordítási rendszereket állíthat össze, amelyek kezelik a vállalkozásában vagy az iparágban alkalmazott terminológiát. A testreszabott fordítási rendszer ezután egyszerűen integrálható a meglévő alkalmazásaiba, munkafolyamataiba és webhelyeibe, a normál fordítón keresztül, a kategória paraméter használatával.

További információk [a nyelvi testreszabásról](customization.md).

## <a name="next-steps"></a>Következő lépések

- [Regisztráljon](translator-text-how-to-signup.md) egy hozzáférési kulcsra.
- Az [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) az API-k műszaki dokumentációját biztosítják.
- [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
