---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "66244591"
---
Itt láthatók a megírható Azure-objektumok méretei. Győződjön meg arról, hogy az összes feltöltött fájl megfelel a fenti korlátoknak.

| Azure-objektumtípus | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 TiB                                                 |
| Lapblob         | 8 TiB <br> Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, és a feltöltés sikertelen lesz. <br> A VHD-k és a VHDX 512 bájttal vannak igazítva. |
| Azure Files        | 1 TiB                                                      |
| Felügyelt lemezek     | 4 TiB <br> A mérettel és a korlátokkal kapcsolatos további információkért lásd: <li>[Standard SSD-k skálázhatósági céljai](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Prémium SSD-k skálázhatósági céljai](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[A standard HDD-k skálázhatósági céljai](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[A felügyelt lemezek díjszabása és számlázása](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
