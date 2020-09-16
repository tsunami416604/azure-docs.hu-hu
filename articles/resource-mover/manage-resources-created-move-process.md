---
title: A virtuális gép mozgatási folyamata során létrehozott erőforrások kezelése az Azure-erőforrás-Mozgatóban
description: Megtudhatja, hogyan kezelheti a virtuális gépek áthelyezési folyamata során létrehozott erőforrásokat az Azure-erőforrás-mozgató szolgáltatásban
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 6d6f56fb47bfdaadc6704e2a13ebcf0e893b8b1c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602353"
---
# <a name="manage-resources-created-for-the-vm-move"></a>A virtuális gép áthelyezéséhez létrehozott erőforrások kezelése

Ez a cikk azt ismerteti, hogyan kezelheti az [Azure-erőforrás-mozgató](overview.md) explicit módon létrehozott erőforrásokat a virtuális gépek áthelyezési folyamatának megkönnyítésére. 

A virtuális gépek régiók közötti áthelyezése után az erőforrás-mozgató által létrehozott számos erőforrást manuálisan kell tisztítani.

## <a name="delete-resources-created-for-vm-move"></a>VIRTUÁLIS gépek áthelyezéséhez létrehozott erőforrások törlése

Törölje kézzel az áthelyezési gyűjteményt, és Site Recovery a virtuális gép áthelyezéséhez létrehozott erőforrásokat.

1. Tekintse át az erőforráscsoport erőforrásait ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Győződjön meg arról, hogy a virtuális gép és az áthelyezési gyűjteményben lévő összes többi forrás erőforrás át lett helyezve/törölve lett. Ezzel biztosíthatja, hogy ne legyenek használatban függő erőforrások.
2. Törölje ezeket az erőforrásokat.

    - Az áthelyezési gyűjtemény neve: ```movecollection-<sourceregion>-<target-region>``` .
    - A cache Storage-fiók neve ```resmovecache<guid>```
    - A tár neve: ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>További lépések

[Helyezzen át egy virtuális gépet](tutorial-move-region-virtual-machines.md) egy másik régióba az erőforrás-mozgató használatával.