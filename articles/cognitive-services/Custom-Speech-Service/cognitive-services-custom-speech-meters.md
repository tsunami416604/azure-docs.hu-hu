---
title: Egyéni beszédszolgáltatás mérőszámok és kvóták az Azure-ban |} A Microsoft Docs
description: Az Azure-ban mérőszámok és kvótákat a Custom Speech Service vonatkozó adatokat.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 0ddd6274051fed5de86a88270a0dcc2e8288d885
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974527"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Egyéni beszédszolgáltatás mérőszámok és kvóták

A Custom Speech Service felhőalapú testre szabható beszédmodellek hang-szöveg transzformációs beszédátírási.

A Custom Speech Service használatának megkezdéséhez nyissa meg a [Custom Speech Service portal](https://cris.ai).

Az aktuális árképzési mérőszámok, nyissa meg a [Cognitive Services Custom Speech Service díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) lapot.

## <a name="tiers-explained"></a>A rétegek ismertetése
Teszteléshez és prototípus csak műveletcsoportokat az F0 ingyenes használatára. Éles rendszerek esetén műveletcsoportokat az S2 szint használatához. Az S2 szint használatával, a központi telepítést az skálázási egységek (su), amely a forgatókönyvhöz szükséges számát is méretezhető.

> [!NOTE]
> Ön *nem* áttelepítése a F0 szint és az S2 szint között.
>

## <a name="meters-explained"></a>A mérőszámok ismertetése

### <a name="scale-out"></a>Bővítés
Horizontális Felskálázás egy új szolgáltatás, amely az új díjszabási modell bocsátottunk. Horizontális méretezés segítségével szabályozhatja, hogy a modell képes feldolgozni egyidejű kérelmek száma.

Az SU-mérték segítségével beállíthatja az egyidejű kérelmek a **létrehozása modell-üzembehelyezés** megtekintése. További információkért lásd: [hozzon létre egy egyéni hang-szöveg transzformációs végpontot](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Attól függően, a modell felhasználása előirányozni forgalom mennyiségét kiválaszthatja, SUS-t megfelelő számú. 

> [!NOTE]
> Minden egyes méretezési egység 5 egyidejű kérelmek garantálja. 1 vagy több SUS-t, szükség szerint vásárolhatók meg. SUs száma növekszik, 1, mert a számos egyidejű kérés garantáltan 5 egységnyi növekményekben növelése.
>

### <a name="log-management"></a>Naplókezelés
A naplózott kikapcsolhatja az audio nyomok egy újonnan üzembe helyezett modell további költségekkel. A Custom Speech Service nem naplózza a hang kérések vagy az átiratok a modellből.

## <a name="next-steps"></a>További lépések
A Custom Speech Service használatával kapcsolatos további információkért látogasson el a [Custom Speech Service portal](https://cris.ai).

* [Első lépések](cognitive-services-custom-speech-get-started.md)
* [GYIK](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
 