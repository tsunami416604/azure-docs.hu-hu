---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471455"
---
Ha egy fenntartott virtuálisgép-példányt vásárol, választhat, hogy optimalizálja a például a méret rugalmasságát vagy a kapacitás prioritást. A fenntartott virtuálisgép-példányok optimalizálási beállításának beállításáról vagy módosításáról [a Fenntartott virtuálisgép-példányok optimalizálási beállításának módosítása című témakörben](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)talál további információt.

Egy fenntartott virtuálisgép-példány, amely például a méret rugalmassága, a foglalás térhet a virtuális gépek (VM-ek) méretei az azonos példányméret rugalmassági csoportban. Ha például a DSv2 sorozatban felsorolt virtuális gépmérethez vásárol foglalást, például Standard_DS5_v2, a foglalási kedvezmény az adott példányméret-rugalmassági csoportban felsorolt másik négy méretre is vonatkozhat:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

De ez a foglalási kedvezmény nem vonatkozik a különböző példányméret-rugalmassági csoportokban felsorolt virtuális gépekre, például a DSv2 sorozatú nagy memória sus-ok: Standard_DS11_v2, Standard_DS12_v2 és így tovább.

A példány méretrugalmassági csoportján belül a foglalási kedvezmény ben szereplő virtuális gépek száma a foglalás megvásárlásakor választott virtuális gép méretétől függ. Ez is függ a virtuális gépek, amelyek futnak. Az arány oszlop összehasonlítja a relatív lábnyom minden virtuális gép mérete az adott példány méret rugalmassági csoportban. Használja a ratio érték kiszámításához, hogyan vonatkozik a foglalási kedvezmény a virtuális gépek fut.

## <a name="examples"></a>Példák

Az alábbi példák a DSv2 sorozatú táblázat méreteit és arányait használják.

Egy fenntartott virtuálisgép-példányt vásárol a Standard_DS4_v2 méretével, ahol az arány vagy a relatív lábnyom az adott sorozat többi méretéhez képest 8.

- 1. forgatókönyv: Futtasson nyolc Standard_DS1_v2 méretű virtuális gépek 1-es arányú. A foglalási kedvezmény mind a nyolc virtuális gépre vonatkozik.
- 2. forgatókönyv: Futtasson két Standard_DS2_v2 méretű virtuális gépek, amelyek aránya 2.2. Is futtasson egy Standard_DS3_v2 méretű virtuális gép 4-es arányú. A teljes lábnyom: 2+2+4=8. Így a foglalási kedvezmény mindhárom virtuális gépre vonatkozik.
- 3. forgatókönyv: Futtasson egy Standard_DS5_v2 16-os aránnyal. A foglalási kedvezmény a virtuális gép számítási költségének felére vonatkozik.

A következő szakaszok azt mutatják, hogy milyen méretűek ugyanabban a méretsorozat-csoportban, ha például a méret rugalmasságára optimalizált fenntartott virtuálisgép-példányt vásárol.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Példányméret rugalmassági aránya a virtuális gépekhez 

CSV alatt a példány mérete rugalmassági csoportok, ArmSkuName és az arányok.  

[Példányméret rugalmassági arányai](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

A fájl URL-címét és sémáját rögzítettük, így programozott módon használhatja fel ezt a fájlt. Az adatok hamarosan elérhetők lesznek az API-n keresztül is.
