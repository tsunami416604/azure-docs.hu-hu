---
title: Felügyelt lemez kitörése
description: További tudnivalók az Azure-beli lemezek és az Azure-beli virtuális gépek lemezes kitöréséről
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594433"
---
# <a name="disk-bursting"></a>Lemezek adatlöket-kezelése
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtuális gépek szintjének felszakadása
A virtuális gépek szintjének feltört támogatása a nyilvános felhőben lévő összes régióban a következő támogatott méreteken engedélyezett: 
- [Lsv2 sorozat](../lsv2-series.md)

A burst beállítás alapértelmezés szerint engedélyezve van az azt támogató virtuális gépek esetében.

## <a name="disk-level-bursting"></a>Lemez szintjének felszakadása
Az adattelen [prémium SSD](disks-types.md#premium-ssd) -lemezeken is elérhetők a P20 és kisebb méretekben az összes régióban. A lemez kitörése alapértelmezés szerint engedélyezve van az azt támogató lemezek új központi telepítésen. A meglévő lemezek mérete, ha támogatják a lemezek kitörését, a következő módszerek egyikével engedélyezheti a kitörést: 
- **A virtuális gép újraindítása** 
- **Lemez leválasztása és újracsatolása**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
