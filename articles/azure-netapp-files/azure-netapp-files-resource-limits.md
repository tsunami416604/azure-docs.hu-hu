---
title: Erőforráskorlátok NetApp Azure-fájlok |} A Microsoft Docs
description: A NetApp fájlokat az Azure-erőforrások, többek között a NetApp fiókok, kapacitás készletek, kötetek, pillanatképeket és a delegált alhálózatra vonatkozó korlátok korlátozásait ismerteti.
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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668158"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure NetApp Files erőforráskorlátai

Erőforráskorlátok ismertetése a NetApp Azure-fájlok segítségével kezelheti a köteteket.

- Minden Azure-előfizetés legfeljebb 10 NetApp fiókok.
- Egyes NetApp fiókok legfeljebb 25 kapacitás-készletek.
- Minden kapacitás címkészlet NetApp csak egy fiók is tartozik.  
- Egyetlen kapacitás készlet minimális mérete 4 Tib-ra, és a maximális mérete 500 Tib-ra. 
- Minden kapacitás készlet legfeljebb 500 kötetek.
- Egy kötetet minimális mérete 100 GB, és a maximális méretet 92 Tib-ra.
- Minden kötet legfeljebb 255 pillanatképek.
- Minden Azure virtuális hálózat (Vnet) csak egy alhálózatot az Azure Files-NetApp delegált rendelkezhet.

**Következő lépések**

[Megismerheti a NetApp Azure-fájlok hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
