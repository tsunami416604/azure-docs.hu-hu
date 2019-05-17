---
title: A kapacitás-készlet vagy kötet átméretezése NetApp Azure-fájlok |} A Microsoft Docs
description: Ismerteti, hogyan lehet kapacitást készlet vagy kötet méretének módosítása.
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
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794617"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>A kapacitáskészlet vagy kötet átméretezése
Módosíthatja a kapacitást készlet vagy szükség szerint kötet méretét. 

## <a name="resize-the-capacity-pool"></a>A kapacitás a készlet átméretezése 

Módosíthatja a kapacitást készlet mérete 1-Tib-ra kerekítve vagy csökkentésére. A kapacitás a készlet méretét azonban nem lehet kisebb, mint 4 Tib-ra. A kapacitás a készlet átméretezése módosítja a megvásárolt Azure NetApp fájlok kapacitást.

1. A NetApp fiók kezelése panelen kattintson a kapacitás a készlet átméretezése kívánt. 
2. Kattintson a jobb gombbal a kapacitás-készlet neve, vagy kattintson a "..." ikonra a helyi menü megjelenítése a kapacitás a készlet sor végén található. 
3. A környezet menüpontok segítségével méretezze át, vagy a kapacitás-készlet törlése.

## <a name="resize-a-volume"></a>A kötet átméretezése

Egy kötet szükség szerint módosíthatja. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.

1. A NetApp fiók kezelése panelen kattintson az **kötetek**. 
2. Kattintson a jobb gombbal a kötet átméretezése, vagy kattintson a "..." ikonra a helyi menü megjelenítése a kötet sor végén található kívánt nevét.
3. A környezet menüpontok segítségével méretezze át vagy törölje a kötetet.

