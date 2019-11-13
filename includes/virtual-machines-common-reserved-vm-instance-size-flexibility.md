---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 9a7de2b41c8016bdb7849cdef428d6f54a8ccf64
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005451"
---
Egy fenntartott virtuálisgép-példány, amely a példányok méretének rugalmasságára van optimalizálva, a megvásárolt foglalás a virtuális gépek (VM-EK) méretére is vonatkozhat ugyanabban a példány-méret rugalmassági csoportban. Ha például egy, a DSv2-sorozatban felsorolt virtuálisgép-mérethez (például Standard_DS5_v2) foglal le egy foglalást, akkor a foglalási kedvezmény a következő négy méretre is vonatkozhat, amely az adott példány méretének rugalmassági csoportjában szerepel:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

A foglalási kedvezmény azonban nem vonatkozik azokra a virtuális gépekre, amelyek a különböző példányszámú rugalmassági csoportokban vannak felsorolva, például a DSv2 Series nagy memóriában található SKU-ban: Standard_DS11_v2, Standard_DS12_v2 stb.

A példányok méretének rugalmassága csoportban a foglalási kedvezmény által érintett virtuális gépek száma a foglalás megvásárlásakor kiválasztott virtuálisgép-mérettől függ. Emellett a futtatott virtuális gépek méretétől is függ. A Ratio (arány) oszlop összehasonlítja az adott példány méretének rugalmassági csoportjának minden virtuálisgép-méretéhez tartozó relatív lábnyomot. A Ratio érték használatával kiszámíthatja, hogy a foglalási kedvezmény Hogyan vonatkozik a futtatott virtuális gépekre.

## <a name="examples"></a>Példák

Az alábbi példák a DSv2 sorozatú táblában lévő méreteket és arányokat használják.

A fenntartott VM-példányok olyan mérettel rendelkeznek, Standard_DS4_v2, ahol az arány vagy a relatív lábnyom a sorozat többi méretéhez képest 8.

- 1\. forgatókönyv: nyolc Standard_DS1_v2 méretű virtuális gép futtatása 1-es aránnyal. A foglalási kedvezmény mind a nyolc virtuális gép esetében érvényes.
- 2\. forgatókönyv: két Standard_DS2_v2 méretű virtuális gépet futtasson 2. arányban. Futtasson egy Standard_DS3_v2 méretű virtuális gépet is, amelynek a aránya 4. A teljes helyigény 2 + 2 + 4 = 8. Így a foglalási kedvezmény mindhárom virtuális gépre vonatkozik.
- 3\. forgatókönyv: egy Standard_DS5_v2 futtatása 16-os aránnyal. A foglalási kedvezmény a virtuális gép számítási díja felére vonatkozik.

A következő fejezetek azt mutatják be, hogy az azonos méretű adatsorozat-csoport milyen méreteket tartalmaz, ha olyan fenntartott VM-példányt vásárol, amely a példány méretének rugalmasságára optimalizált

## <a name="instance-size-flexibility-ratio-for-vms"></a>Példányok méretének rugalmassági aránya a virtuális gépeknél 

Az alábbi CSV a példányok méretének rugalmassági csoportjai, a ArmSkuName és az arányok.  

[Példány méretének rugalmassági aránya](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Megtartjuk a fájl URL-címét és a séma által rögzített sémát, hogy programozott módon használhassa ezt a fájlt. Az adatközpont hamarosan elérhető lesz az API-n keresztül is.
