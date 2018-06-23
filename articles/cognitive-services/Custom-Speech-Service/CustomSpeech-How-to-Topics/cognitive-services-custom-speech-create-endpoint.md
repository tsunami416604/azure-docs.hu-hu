---
title: Egyéni beszéd-végpont létrehozása az Azure-on egyéni beszéd szolgáltatással |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy egyéni beszéd-szöveg végpontot kognitív szolgáltatásban az egyéni beszéd szolgáltatással.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 99bc275db1f0c1b45b3db440d2e03d0db9ab5cf6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347039"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Egyéni beszéd-szöveg-végpont létrehozása
Egyéni akusztikus modellek vagy nyelvi modellek létrehozása után egy egyéni beszéd-szöveg végpont telepítheti őket. 

## <a name="create-an-endpoint"></a>Végpont létrehozása
Új egyéni végpont létrehozásához válassza **központi telepítések** a a **egyéni beszéd** menü az oldal tetején. Ez a művelet időt vesz igénybe, hogy a **központi telepítések** lapon, amely a jelenlegi egyéni végpontok táblát tartalmaz. Ha még nem hozott létre végpontok, a tábla mező üres. Az aktuális területi beállítása a táblázat címsorában megjelenik. 

A központi telepítés más nyelven létrehozásához válassza **módosítás területi**. További információ a támogatott nyelvek: [területi beállításokat támogatja egyéni beszéd szolgáltatás](cognitive-services-custom-speech-change-locale.md).

Új végpont létrehozásához válassza **hozzon létre új**. Az a **létrehozása központi telepítési** panelen adja meg az adatokat a **neve** és **leírása** mezőkbe az egyéni központi telepítés.

Az a **előfizetés** kombinált jelölje ki a használni kívánt előfizetést. Ha olyan S2-előfizetéssel, kiválaszthatja a méretezési egységek és a tartalom naplózása. További információ a méretezési egységek és naplózás: [egyéni beszéd szolgáltatás mérőszámok és kvóták](../cognitive-services-custom-speech-meters.md).

Az alábbi táblázat bemutatja, hogyan használható egyidejű kérelmek méretezési egységek hozzárendelését:

| Méretezési egység | Egyidejű kérelmek száma |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Választhatja, hogy a tartalom naplózási be- és kikapcsolása át lett váltva. Ez azt jelenti hogy a végpont forgalom tárolja a Microsoft belső használatra van kiválasztása. Ha nincs bejelölve, a forgalom tárolja a rendszer letiltja. Tartalom naplózási eredményez további költségek letiltása. Tekintse át a [információk díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) részleteiről.

> [!NOTE]
> Tartalom naplózás "No Trace" nevezzük az árképzést ismertető oldalra.
>


Emellett a Microsoft a nyers becsült költség biztosítja, hogy tisztában lehet a méretezési egységek és a tartalom naplózási járó költségek gyakorolt hatása. Ez a becslés egy durva becslést, és a tényleges költségek eltérő lehet.

> [!NOTE]
> Ezek a beállítások F0 (ingyenes szint) előfizetések nem érhetők el.
>

Az a **akusztikus modell** listára, válassza ki a használni kívánt akusztikus modell és a **nyelvi modell** listára, válassza ki a használni kívánt nyelvi modell. A választási lehetőségek akusztikus és nyelvi modellek mindig adja meg az alap Microsoft modellek. A kijelölés alap modell a kombinációk korlátozza. Nem conversational alap modellek keverje keresési és előírják az alap modellek.

![A központi telepítés létrehozása lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Ügyeljen arra, hogy fogadja el a feltételeket és a jelölőnégyzet bejelölésével díjszabási információkat.
>

Miután kiválasztotta a akusztikus és nyelvi modellek, válassza ki a **létrehozása**. Ez a művelet visszaadja, hogy a **központi telepítések** lap. A tábla most már tartalmaz egy bejegyzést, amely megfelel az új végpont. A végpont állapota a jelenlegi állapotában tükrözi, akkor létrehozása közben. Az egyéni modellek új végpont példányosítani akár 30 percet is igénybe vehet. Ha a központi telepítés állapota *Complete*, a végpont esetében használatra kész.

![A központi telepítések lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Amikor készen áll a központi telepítést, a telepítés neve lesz hivatkozást. Megjeleníti a hivatkozási kiválasztása a **központi telepítési információk** lapon, amely megjeleníti vagy HTTP-kérések használata az egyéni végpontjának URL-címei vagy a Microsoft kognitív szolgáltatások beszéd ügyféloldali kódtár, amely webes szoftvercsatornák használ.

![A központi telepítési információi lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>További lépések

További oktatóanyagokat tekintse meg:
* [Egyéni beszéd-szöveg végpont használatára](cognitive-services-custom-speech-use-endpoint.md)
* [Egyéni akusztikus modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md)
* [Egyéni nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md)
