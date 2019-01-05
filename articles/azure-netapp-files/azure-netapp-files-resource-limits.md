---
title: Erőforráskorlátok NetApp Azure-fájlok |} A Microsoft Docs
description: A NetApp fájlokat az Azure-erőforrások, többek között a kapacitás-készletek, kötetek és a delegált alhálózat korlátai korlátozásait ismerteti.
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057014"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure Files-NetApp erőforráskorlátok
Erőforráskorlátok ismertetése a NetApp Azure-fájlok segítségével kezelheti a köteteket.

## <a name="capacity_pools"></a>Kapacitáskészletek

- Egyetlen kapacitás készlet minimális mérete 4 Tib-ra, és a maximális mérete 500 Tib-ra. 
- Minden kapacitás címkészlet NetApp csak egy fiók is tartozik. Azonban akkor is több kapacitás készletek NetApp-fiókon belül.  

## <a name="volumes"></a>Kötetek

- Egy kötetet minimális mérete 100 GB, és a maximális méretet 92 Tib-ra.
- Legfeljebb 100 kötetek Azure-előfizetésenként és régiónként rendelkezhet.  

## <a name="delegated_subnet"></a>Delegált alhálózat 

Az egyes Azure virtuális hálózatok (Vnet) csak egy alhálózatot az Azure Files-NetApp delegálható.

## <a name="next-steps"></a>További lépések

[Megismerheti a NetApp Azure-fájlok hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
