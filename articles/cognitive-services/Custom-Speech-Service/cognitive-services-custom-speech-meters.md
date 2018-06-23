---
title: Egyéni beszéd szolgáltatás mérőszámok és az Azure kvótái |} Microsoft Docs
description: Az Azure-on mérőszámok és kvóták egyéni beszéd szolgáltatás vonatkozó információk.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: d2225dec818c600febfad2f9ebc42594f6ac09ac
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346931"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Egyéni beszéd szolgáltatás mérőszámok és kvóták

A felhőalapú egyéni beszéd szolgáltatás segítségével testre szabhatja vonatkozó beszéd szöveg beszéd átalakítás modellek.

Az egyéni beszéd szolgáltatás használatának megkezdéséhez keresse fel a [egyéni beszéd portálon](https://cris.ai).

Az aktuális árképzési mérőszámok, keresse fel a [kognitív szolgáltatások egyéni beszéd szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) lap.

## <a name="tiers-explained"></a>A rétegek ismertetése
A teszteléshez és prototípus csak azt javasolja a szabad F0 csomagot használja. Éles rendszerek esetén azt javasolja a S2 csomagot használja. A S2 réteg használatával méretezheti a méretezési egységek számát (SUs) adott esetben szükség van a központi telepítést.

> [!NOTE]
> Ön *nem* telepítse át a F0 réteg és a S2 szint között.
>

## <a name="meters-explained"></a>A mérőszámok alapján

### <a name="scale-out"></a>Bővítés
Horizontális Felskálázás az, hogy az új árképzési modellt kiadtuk új szolgáltatása. Horizontális Felskálázás segítségével szabályozhatja, amely képes a modell egyidejű kérelmek számát jelenti.

A SU mértékcsoportban használatával beállíthatja az egyidejű kérelmek a **létrehozása a modell központi telepítési** nézet. További információkért lásd: [egyéni beszéd-szöveg-végpont létrehozása](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Attól függően, hogy Ön a modell fel előirányozni forgalom mennyiségét kiválaszthatja a megfelelő számú SUS-t. 

> [!NOTE]
> Minden egyes méretezési egység biztosítja, hogy az 5 egyidejű kérelmet. 1 vagy több SUS-t, szükség szerint vásárolhatja meg. SUs száma növekszik 1, mert az egyidejű kérelmek száma garantáltan 5 növekednek.
>

### <a name="log-management"></a>Napló kezelése
Dönthet úgy is, egy további költségekkel egy újonnan telepített modell hang nyomkövetések kikapcsolásához. Egyéni beszéd nem naplózza a hang kéréseket, és a ki, hogy a modellből.

## <a name="next-steps"></a>További lépések
Az egyéni beszéd szolgáltatás használatával kapcsolatos további információkért látogasson el a [egyéni beszéd portálon](https://cris.ai).

* [Első lépések](cognitive-services-custom-speech-get-started.md)
* [GYIK](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
 