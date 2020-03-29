---
title: Az Azure NetApp-fájlok kapacitáskészletének vagy kötetének átméretezése | Microsoft dokumentumok
description: Ez a témakör a kapacitáskészlet vagy kötet méretének módosítását ismerteti.
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
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794617"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>A kapacitáskészlet vagy kötet átméretezése
Szükség esetén módosíthatja a kapacitáskészlet vagy a kötet méretét. 

## <a name="resize-the-capacity-pool"></a>A kapacitáskészlet átméretezése 

A kapacitáskészlet méretét 1-TiB lépésekben vagy kisebb lépésekben módosíthatja. A kapacitáskészlet mérete azonban nem lehet kisebb 4 TiB-nél. A kapacitáskészlet átméretezése megváltoztatja a megvásárolt Azure NetApp Files kapacitását.

1. A NetApp-fiók kezelése panelen kattintson az átméretezni kívánt kapacitáskészletre. 
2. Kattintson a jobb gombbal a kapacitáskészlet nevére, vagy kattintson a "..." ikont a kapacitáskészlet sorának végén a helyi menü megjelenítéséhez. 
3. A helyi menü beállításaival átméretezheti vagy törölheti a kapacitáskészletet.

## <a name="resize-a-volume"></a>Kötet átméretezése

Szükség szerint módosíthatja a kötet méretét. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.

1. A NetApp-fiók kezelése panelen kattintson a **Kötetek gombra.** 
2. Kattintson a jobb gombbal az átméretezni kívánt kötet nevére, vagy kattintson a "..." ikont a hangerő sorának végén a helyi menü megjelenítéséhez.
3. A helyi menü beállításaival átméretezheti vagy törölheti a kötetet.

