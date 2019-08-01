---
title: Azure Disk Storage felügyelt lemez – áttekintés Linux rendszerű virtuális gépekhez | Microsoft Docs
description: A Linux rendszerű virtuális gépek használatakor a Storage-fiókokat kezelő Azure Managed Disks áttekintése
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5052504b4fb63ce9d638a9d2505ad1c08d3324de
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695468"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure Managed Disks használatába

Az Azure Managed Disk egy virtuális merevlemez (VHD). Úgy gondolhatja, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. Az Azure Managed Disks szolgáltatás blobként tárolódik, amely az Azure-ban egy véletlenszerű IO Storage-objektum. A felügyelt lemezeket nevezzük felügyelt, mivel ez egy absztrakt lap Blobok, blob-tárolók és Azure Storage-fiókok esetében. A felügyelt lemezekkel mindössze annyit kell tennie, hogy kiépíti a lemezt, az Azure pedig gondoskodik a többiről.

Ha kijelöli az Azure Managed Disks használatát a számítási feladatokkal, az Azure létrehozza és felügyeli a lemezt. A rendelkezésre álló lemezek a következők: ultra Disks (előzetes verzió), Premium SSD-meghajtók (SSD), standard SSD és standard merevlemez-meghajtók (HDD). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](disks-types.md)
