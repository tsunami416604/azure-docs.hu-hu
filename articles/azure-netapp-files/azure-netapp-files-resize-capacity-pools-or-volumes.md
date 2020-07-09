---
title: A kapacitási készlet vagy a kötet átméretezése Azure NetApp Files | Microsoft Docs
description: Útmutató a kapacitási készlet vagy kötet méretének módosításához.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7da604e8e49b0732680e5f641d1ff6e899ad474d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483482"
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

