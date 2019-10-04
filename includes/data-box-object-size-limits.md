---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244591"
---
Az alábbiakban az Azure objektumok lehet írni a méretét. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek-e ezeket a korlátokat.

| Az Azure-objektum típusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 Tib-ra                                                 |
| Lapblob         | 8 TiB <br> Minden blob formátumban feltöltött fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> VHD és VHDX igazítva 512 bájt. |
| Azure Files        | 1 TiB                                                      |
| Felügyelt lemezek     | 4 TiB <br> A mérete és a korlátok további információkért lásd: <li>[Standard SSD-k, a skálázási célértékei](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[A prémium szintű SSD-k skálázási célértékei](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Standard HDD skálázási célértékei](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Árak és számlázás, a felügyelt lemezek](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
