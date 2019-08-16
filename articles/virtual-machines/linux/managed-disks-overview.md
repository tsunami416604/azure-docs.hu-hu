---
title: Azure Disk Storage felügyelt lemez – áttekintés Linux rendszerű virtuális gépekhez | Microsoft Docs
description: A Linux rendszerű virtuális gépek használatakor a Storage-fiókokat kezelő Azure Managed Disks áttekintése
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 08/15/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3ba797088eb23262c583906d7023f20fb7d408ba
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516150"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure Managed Disks használatába

Az Azure Managed Disk egy virtuális merevlemez (VHD). Úgy gondolhatja, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. Az Azure Managed Disks szolgáltatás blobként tárolódik, amely az Azure-ban egy véletlenszerű IO Storage-objektum. A felügyelt lemezeket nevezzük felügyelt, mivel ez egy absztrakt lap Blobok, blob-tárolók és Azure Storage-fiókok esetében. A felügyelt lemezekkel mindössze annyit kell tennie, hogy kiépíti a lemezt, az Azure pedig gondoskodik a többiről.

Ha kijelöli az Azure Managed Disks használatát a számítási feladatokkal, az Azure létrehozza és felügyeli a lemezt. A rendelkezésre álló lemezek a következők: ultra Disk, Premium SSD-meghajtó (SSD), standard SSD és standard merevlemez-meghajtó (HDD). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](disks-types.md)
