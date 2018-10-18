---
title: Mi a Translator Text API?
titlesuffix: Azure Cognitive Services
description: A Translator Text API az alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba való integrálásával többnyelvű felhasználói felületeket hozhat létre.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: c0004960b7a234d374ec0ae1bdc2f6576b197705
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385025"
---
# <a name="what-is-translator-text-api"></a>Mi a Translator Text API?

A Translator Text API zökkenőmentesen integrálható az alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba, és [több mint 60 nyelven](languages.md) képes többnyelvű felhasználói felületeket létrehozni. Bármely hardverplatformon és bármely operációs rendszerrel használható szöveg-szöveg vektorú nyelvi fordítások végrehajtásához.

A Translator Text API a felhőalapú gépi tanulási és mesterséges intelligenciára épülő algoritmusokat biztosító, a fejlesztési projektekben készen használható [Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai)-gyűjtemény részét képezi.

## <a name="about-microsoft-translator"></a>A Microsoft Translator ismertetése

A Microsoft Translator egy felhőalapú gépi fordítási szolgáltatás. A szolgáltatás magját a Translator Text API és a [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation) adja, amelyek a Microsoft különféle termékeit és szolgáltatásait szolgálják ki, és amelyeket világszerte vállalkozások ezrei használnak alkalmazásaikban és munkafolyamataikban, hogy tartalmaikat a globális közönség elé tárják.

A beszédfordítás is elérhető a [Cognitive Services Speech előzetes verziójával](https://docs.microsoft.com/azure/cognitive-services/speech-service/), amely a meglévő Translator Speech és Bing Speech API-t és a Custom Speech Service előzetes verzióját egyesíti egy egységes és teljesen testre szabható szolgáltatásban.  

Itt tudhat meg többet a [Microsoft Translator szolgáltatásról](https://www.microsoft.com/en-us/translator/home.aspx).

## <a name="language-customization"></a>Nyelvi testreszabás

Az alap Microsoft Translator szolgáltatás kiterjesztéseként a Custom Translator a Translator Text API-val együtt alkalmazva segít testre szabni a neurális fordítási rendszert, illetve feljavítani a fordításokat a fejlesztő egyedi terminológiájával és stílusával.

A Custom Translator használatával olyan fordítási rendszereket állíthat össze, amelyek kezelik a vállalkozásában vagy az iparágban alkalmazott terminológiát. A testre szabott fordítási rendszer ezután könnyen integrálható a meglévő alkalmazásaiba, munkafolyamataiba és webhelyeibe több eszköztípuson is, a rendes Microsoft Translator Text API-n keresztül, a „category” (kategória) paraméter használatával. 

További információk [a nyelvi testreszabásról](customization.md).

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator – neurális gépi fordítás

A neurális gépi fordítás (Neural Machine Translation, NMT) az új etalon a minőségi, mesterséges intelligencián alapuló gépi fordítások területén. A korábbi statisztikai gépi fordítási (Statistical Machine Translation, SMT) technológia helyébe lép, amely a 2010-es évek közepére elérte a minősége felső korlátját.

Az NMT nem csupán a nyers fordítások pontozását tekintve biztosít jobb fordításokat az SMT-nél, hanem az általa fordított szövegek gördülékenyebbnek, illetve sokkal emberibbnek is hatnak. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához. Az SMT ezzel szemben az adott szavak előtt és után álló néhány szóból álló közvetlen kontextust vette alapul.

Az NMT-modellek az API magját képezik, és a végfelhasználók számára nem láthatók. Ami észrevehető, az a fordítás minőségének javulása, különösen az olyan nyelvek esetében, mint a kínai, a japán vagy az arab. 

További információ [az NMT működéséről](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="next-steps"></a>További lépések

- Olvassa át a [díjszabási részleteket](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Regisztráljon](translator-text-how-to-signup.md) egy hozzáférési kulcs beszerzéséhez.

- A [rövid útmutató](quickstarts/csharp.md) a C# nyelven írt REST API-hívásokat mutatja be részletesen. Ismerje meg, hogyan fordíthat le szövegeket az egyik nyelvről egy másikra minimális kódolással.

- Az [API referenciadokumentációja](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) tartalmazza az API-k műszaki dokumentációját.

## <a name="see-also"></a>Lásd még

- [A Cognitive Services dokumentációjának oldala](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
- [A megoldásokkal és a díjszabással kapcsolatos információk](https://www.microsoft.com/en-us/translator/default.aspx)
