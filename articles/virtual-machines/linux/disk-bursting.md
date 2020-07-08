---
title: Felügyelt lemez kitörése
description: További tudnivalók az Azure-beli lemezek és az Azure-beli virtuális gépek lemezes kitöréséről
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: f92fae38d49c51dfe87a68b023ba779e89b0e0bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84295456"
---
# <a name="disk-bursting"></a>Lemezek adatlöket-kezelése
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtuális gépek szintjének felszakadása
A virtuális gépek szintjének feltört támogatása a nyilvános felhőben lévő összes régióban a következő támogatott méreteken engedélyezett: 
- [Lsv2 sorozat](../lsv2-series.md)

A burst beállítás alapértelmezés szerint engedélyezve van az azt támogató virtuális gépek esetében.

## <a name="disk-level-bursting"></a>Lemez szintjének felszakadása
A [prémium szintű SSD](disks-types.md#premium-ssd) -lemezeken az Azure-beli nyilvános, kormányzati és kínai felhők minden régiójában P20 és kisebb méretekben is elérhetők a betörések. A lemez kitörése alapértelmezés szerint engedélyezve van az azt támogató lemezek összes új és meglévő központi telepítésén. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
