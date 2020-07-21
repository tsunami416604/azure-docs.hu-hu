---
title: Felügyelt lemez kitörése
description: További tudnivalók az Azure-beli lemezek és az Azure-beli virtuális gépek lemezes kitöréséről
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4408b7d442ce3f4fbb1ea61031295375ae036493
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494547"
---
# <a name="managed-disk-bursting"></a>Felügyelt lemez kitörése
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtuális gépek szintjének felszakadása
A virtuális gépek szintjének feltört támogatása a nyilvános felhőben lévő összes régióban a következő támogatott méreteken engedélyezett: 
- [Lsv2 sorozat](../lsv2-series.md)

A burst beállítás alapértelmezés szerint engedélyezve van az azt támogató virtuális gépek esetében.

## <a name="disk-level-bursting"></a>Lemez szintjének felszakadása
A [prémium szintű SSD](disks-types.md#premium-ssd) -lemezeken az Azure-beli nyilvános, kormányzati és kínai felhők minden régiójában P20 és kisebb méretekben is elérhetők a betörések. A lemez kitörése alapértelmezés szerint engedélyezve van az azt támogató lemezek összes új és meglévő központi telepítésén. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
