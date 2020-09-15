---
title: Microsoft Translator szolgáltatás
titlesuffix: Azure Cognitive Services
description: A fordítót integrálhatja alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba a többnyelvű felhasználói élmény biztosítása érdekében.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: swmachan
ms.custom: cog-serv-seo-aug-2020
keywords: fordító, szöveg fordítása, gépi fordítás, fordítási szolgáltatás
ms.openlocfilehash: 32ae7f75c1b953e8af7dfef83c1971c2f78b0b62
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530335"
---
# <a name="what-is-the-translator-service"></a>Mi a Translator Service?

A Translator egy felhőalapú gépi fordítási szolgáltatás, amely az intelligens alkalmazások készítéséhez használt, a kognitív API-k [Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) családjának részét képezi. A Translator egyszerűen integrálható alkalmazásaiba, webhelyeire, eszközeibe és megoldásaiba. Lehetővé teszi a többnyelvű felhasználói élmény [több mint 70 nyelven](languages.md)való hozzáadását, és bármilyen hardveres platformon használható bármilyen operációs rendszerrel a szöveges fordításhoz.

## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése

A Translator számos Microsoft-terméket és-szolgáltatást felhasznál, és világszerte több ezer vállalat használja alkalmazásaikban és munkafolyamataiban, ami lehetővé teszi, hogy a tartalmak globális közönséget érjenek el.

A fordító által működtetett beszéd fordítás az [Azure Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/)-en keresztül is elérhető. Egyesíti a Translator Speech API és a Custom Speech Service funkcióit egy egységes és teljes mértékben testreszabható szolgáltatásban. 

## <a name="language-support"></a>Nyelvi támogatás

A fordító többnyelvű támogatást biztosít a szöveg fordításához, az íráshoz, a nyelvfelismerés és a szótárakhoz. A teljes listát a [nyelvi támogatást](language-support.md) ismertető szakaszban, vagy programozott módon a [REST API-t](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) megnyitva találja.  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator – neurális gépi fordítás

A neurális gépi fordítás (Neural Machine Translation, NMT) az új etalon a minőségi, mesterséges intelligencián alapuló gépi fordítások területén. A korábbi statisztikai gépi fordítási (Statistical Machine Translation, SMT) technológia helyébe lép, amely a 2010-es évek közepére elérte a minősége felső korlátját.

Az NMT nem csupán a nyers fordítások pontozását tekintve biztosít jobb fordításokat az SMT-nél, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT ezzel szemben az adott szavak előtt és után álló néhány szóból álló közvetlen kontextust vette alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Ami észrevehető, az a fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab.

További információ a [NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>A fordítások javítása egyéni fordítóval

A fordítói szolgáltatás bővítménye, az egyéni fordító a fordítóval együtt használható a neurális fordítási rendszer testreszabásához és az adott terminológia és stílus fordításának javításához.

A Custom Translator használatával olyan fordítási rendszereket állíthat össze, amelyek kezelik a vállalkozásában vagy az iparágban alkalmazott terminológiát. A testreszabott fordítási rendszer ezután egyszerűen integrálható a meglévő alkalmazásaiba, munkafolyamataiba és webhelyeibe, a normál fordítón keresztül, a kategória paraméter használatával.

További információ az [Egyéni fordítóról](customization.md).

## <a name="next-steps"></a>Következő lépések

- [Regisztráljon](translator-text-how-to-signup.md) egy hozzáférési kulcsra.
- Próbálja ki [a](quickstart-translator.md) fordítói szolgáltatást gyorsan meghívó rövid útmutatóban.
- Az [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) az API-k műszaki dokumentációját biztosítják.
- [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
