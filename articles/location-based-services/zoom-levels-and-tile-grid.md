---
title: "Nagyítás szinteket, és az Azure-beli hely egyéni alapú szolgáltatások |} Microsoft Docs"
description: "További tudnivalók a nagyítási szint és az Azure-beli hely egyéni alapú szolgáltatások"
services: location-based-services
keywords: 
author: jinzh-azureiot
ms.author: jinzh
ms.date: 3/6/2018
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: cpendle
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3a86bf84ad31933cc5008591a275d4f4aa52c9f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="zoom-levels-and-tile-grid"></a>A Nagyítás szintek és a csempe rács
Az Azure-alapú helyszolgáltatás használja a gömb Mercator leképezése koordináta-rendszerére (EPSG: 3857).

A világ négyzetes csempék van osztva. Leképezési (bitképes) 19 nagyítási szint, számozása 0-tól 18 rendelkezik. Leképezési (vektoros) 21 nagyítási szint, 0 és 20 közötti számozott rendelkezik. Nagyítási szint 0 a teljes globális fér el egyetlen csempe:

![A globális csempe](./media/zoom-levels-and-tile-grid/world0.png)

Nagyítási szintjének 1 4 csempék használja a világ megjelenítéséhez: 2 x 2 négyzetes

![A globális csempe bal felső](./media/zoom-levels-and-tile-grid/world1a.png)     ![A globális csempe jobb felső](./media/zoom-levels-and-tile-grid/world1c.png) 

![A globális csempe bal alsó](./media/zoom-levels-and-tile-grid/world1b.png)     ![A globális csempe alsó jobb oldali](./media/zoom-levels-and-tile-grid/world1d.png) 


Minden ezt követő nagyítási szint négyszeres-felosztása az előzőre, 2 álló rács létrehozása követ<sup>nagyítás</sup> x 2<sup>nagyítás</sup>. A nagyítási szint 20 a rács 2<sup>20</sup> x 2<sup>20</sup>, vagy 1 048 576 x 1 048 576 csempék (összesen 109,951,162,778).

A nagyítási szint értékeket a teljes táblázatot a következő helyen:

|a nagyítási szint|mérőszámok/képpont|ügyféloldali mérőszámok/csempe|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|

Csempék a csempe pozíciója a rács a nagyítási szint megfelelő nagyítási szintjét, és az x és y koordináták nevezzük.

Mely nagyítási szint kíván használni, ne feledje, hogy mindegyik helyen a csempe a rögzített pozícióban meghatározásakor. Ez azt jelenti, hogy, hogy egy adott expanse területének megjelenítéséhez szükséges csempék száma nem függ a világ nagyítás rács adott elhelyezését. Például, ha két mutat 900 mérőszámok egymástól, az *előfordulhat, hogy* csak a nagyítási szint 17 közöttük útvonal megjelenítendő három csempék vesz igénybe. Azonban ha a Nyugat pontot a jobb oldalon pedig a csempe, és a keleti parti pont bal oldalán a, eltarthat négy csempe:

![Nagyítás mértéke bemutató](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

A nagyítási szint határozza meg, ha az x és y értékek kerülhet sor. A felső bal csempe minden nagyítás rácsban x = 0, i = 0; a jobb alsó csempe jelenleg x = 2<sup>-1 nagyítás</sup>, y = 2<sup>Nagyítás-1</sup>.

A Nagyítás rács nagyítási szintjének 1 az itt található:

![A nagyítási szint 1 nagyítás rács](./media/zoom-levels-and-tile-grid/api_x_y.png)
