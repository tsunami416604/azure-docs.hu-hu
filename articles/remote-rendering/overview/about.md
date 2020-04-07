---
title: Névjegy
description: Bevezetés az Azure távoli renderelésébe
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: a06c63152cb56be6d94cccc472d2e1d65651d6ce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679953"
---
# <a name="about-azure-remote-rendering"></a>Az Azure távoli renderelése

> [!IMPORTANT]
> **Az Azure távoli renderelés** jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

*Az Azure Remote Rendering* (ARR) egy olyan szolgáltatás, amely lehetővé teszi, hogy kiváló minőségű, interaktív 3D-s tartalmakat jelenítsen meg a felhőben, és valós időben streamelje azokat olyan eszközökre, például a HoloLens 2-re.

![Mintamodell](../media/arr-engine.png)

A nem kötött eszközök korlátozott számítási teljesítménnyel rendelkeznek az összetett modellek rendereléséhez. Sok alkalmazás számára azonban elfogadhatatlan lenne a vizuális hűség bármilyen módon való csökkentése.

*A távoli renderelés* úgy oldja meg ezt a problémát, hogy a renderelési számítási feladatokat a felhőben lévő csúcskategóriás GPU-kba helyezi át. A felhőben üzemeltetett grafikus motor megjeleníti a képet, videoadatfolyamként kódolja, és továbbítja azt a céleszközre.

## <a name="hybrid-rendering"></a>Hibrid renderelés

A legtöbb alkalmazásban nem elegendő csak egy összetett modellt megjeleníteni. A felhasználó működésének biztosításához egyéni felhasználói felületre is szükség van. Az Azure távoli renderelés nem kényszeríti, hogy egy dedikált felhasználói felület keretrendszer, hanem támogatja a *hibrid renderelés.* Ez azt jelenti, hogy az eszközöket az előnyben részesített módszerrel, például az [MRTK-val](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html)jelenítheti meg.

A keret végén az Azure Remote Rendering, majd automatikusan egyesíti a helyileg megjelenített tartalmat a távoli lemezkép. Még a megfelelő elzáródással is képes erre.

## <a name="multi-gpu-rendering"></a>Több GPU-s renderelés

Egyes modellek túl bonyolultak ahhoz, hogy interaktív képkockasebességgel rendereljék, még egy csúcskategóriás GPU esetében is. Különösen az ipari vizualizációban ez gyakori probléma. A korlátok további leküldéses, az Azure Remote Rendering a számítási feladatok több GPU-k között is terjesztheti. Az eredmények egyetlen képpé egyesülnek, így a folyamat teljesen átlátható a felhasználó számára.

## <a name="high-level-architecture"></a>Magas szintű architektúra

Ez az ábra a távoli renderelési architektúrát szemlélteti:

![Architektúra](./media/arr-high-level-architecture.png)

A teljes képlétrehozási ciklus a következő lépéseket foglalja magában:

1. Ügyféloldal: Keret beállítása
    1. A kód: A felhasználói bevitel feldolgozásra kerül, a jelenetgrafikon frissül
    1. ARR kód: Jelenet grafikon frissítések és előre jelzett fej jelent kap küldött a szerverre
1. Kiszolgálóoldali: Távoli renderelés
    1. A renderelő motor elosztja a renderelést a rendelkezésre álló GPU-k között
    1. Több GPU kimenete egyetlen képpé válik
    1. A kép videoadatfolyamként van kódolva, és visszaküldve az ügyfélnek
1. Ügyféloldali: Véglegesítés
    1. Az Ön kódja: Az opcionális helyi tartalom (felhasználói felület, jelölők, ...)
    1. ARR-kód: A "jelen" esetén a helyileg megjelenített tartalom automatikusan egyesül a videoadatfolyammal

A hálózati késés a fő probléma. A kérelem küldése és az eredmény fogadása közötti áthajtási idő általában túl hosszú az interaktív képkockasebességhez. Ezért egynél több keret is repülhet bármikor.

## <a name="next-steps"></a>További lépések

* [Rendszerkövetelmények](system-requirements.md)
* [Rövid útmutató: Modell renderelése unityvel](../quickstarts/render-model.md)
