---
title: A kapacitási készlet vagy a kötet átméretezése Azure NetApp Files | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja a kapacitási készlet vagy a kötet méretét. A kapacitás-készlet átméretezése megváltoztatja a megvásárolt Azure NetApp Files kapacitást.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325504"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>A kapacitáskészlet vagy kötet átméretezése
Szükség szerint módosíthatja a kapacitási készlet vagy a kötet méretét. 

## <a name="resize-the-capacity-pool"></a>A kapacitási készlet átméretezése 

A kapacitási készlet mérete 1 – TiB-es vagy csökkenő értékben módosítható. A kapacitási készlet mérete azonban nem lehet kisebb, mint 4 TiB. A kapacitás-készlet átméretezése megváltoztatja a megvásárolt Azure NetApp Files kapacitást.

1. A NetApp-fiók kezelése panelen kattintson az átméretezni kívánt kapacitási készletre. 
2. Kattintson a jobb gombbal a kapacitási készlet nevére, vagy kattintson a "..." elemre. a kapacitási készlet sorainak végén található ikon a helyi menü megjelenítéséhez. 
3. A kapacitási készlet átméretezéséhez vagy törléséhez használja a helyi menü beállításait.

## <a name="resize-a-volume"></a>Kötet átméretezése

Szükség szerint módosíthatja a kötetek méretét. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.

1. A NetApp-fiók kezelése panelen kattintson a **kötetek**elemre. 
2. Kattintson a jobb gombbal az átméretezni kívánt kötet nevére, vagy kattintson a "..." elemre. ikon a kötet sor végén a helyi menü megjelenítéséhez.
3. A kötet átméretezéséhez vagy törléséhez használja a helyi menü beállításait.

## <a name="next-steps"></a>Következő lépések

- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
- [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
- [Kötetek szolgáltatásszintjének dinamikus módosítása](dynamic-change-volume-service-level.md) 