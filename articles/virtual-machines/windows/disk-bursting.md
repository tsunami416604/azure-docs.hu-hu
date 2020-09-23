---
title: Felügyelt lemez kitörése
description: További tudnivalók az Azure-beli lemezek és az Azure-beli virtuális gépek lemezes kitöréséről
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889100"
---
# <a name="disk-bursting"></a>Lemezek adatlöket-kezelése
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtuális gépek szintjének felszakadása
A virtuális gépek szintjének feltört támogatása a nyilvános felhőben lévő összes régióban a következő támogatott méreteken engedélyezett: 
- [Lsv2 sorozat](../lsv2-series.md)

A virtuális gépek szintjének kitörése az USA nyugati középső régiójában is elérhető a következő támogatott méreteknél:
- [Dsv3-sorozat](../dv3-dsv3-series.md)
- [Esv3 sorozat](../ev3-esv3-series.md)

A burst beállítás alapértelmezés szerint engedélyezve van az azt támogató virtuális gépek esetében.

## <a name="disk-level-bursting"></a>Lemez szintjének felszakadása
Az adattelen [prémium SSD](disks-types.md#premium-ssd) -lemezeken is elérhetők a P20 és kisebb méretekben az összes régióban. A lemez kitörése alapértelmezés szerint engedélyezve van az azt támogató lemezek új központi telepítésen. A meglévő lemezek mérete, ha támogatják a lemezek kitörését, a következő módszerek egyikével engedélyezheti a kitörést: 
- **A virtuális gép újraindítása** 
- **Lemez leválasztása és újracsatolása**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
