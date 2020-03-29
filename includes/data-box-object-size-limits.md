---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244591"
---
Az alábbiakban az Azure-objektumok megírható méretei láthatók. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek ezeknek a korlátoknak.

| Azure-objektumtípusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 TiB                                                 |
| Lapblob         | 8 TiB <br> A lapblob formátumban feltöltött összes fájlnak 512 bájtnak kell lennie (szerves többszörös), különben a feltöltés sikertelen lesz. <br> A VHD és a VHDX 512 bájttal vannak elrendezve. |
| Azure Files        | 1 TiB                                                      |
| Felügyelt lemezek     | 4 TiB <br> A méretről és a korlátokról további információt a következő témakörben talál: <li>[Standard SSD-k méretezhetőségi céljai](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Prémium SSD-k méretezhetőségi céljai](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[A szabványos HDD-k méretezhetőségi céljai](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Felügyelt lemezek díjszabása és számlázása](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
