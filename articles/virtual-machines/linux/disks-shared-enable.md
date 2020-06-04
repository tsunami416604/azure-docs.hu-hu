---
title: Megosztott lemezek engedélyezése az Azure Managed Disks szolgáltatásban
description: Egy megosztott lemezzel (előzetes verzió) rendelkező Azure felügyelt lemez konfigurálása, amely több virtuális gépen is megosztható
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8fdbe71ab1b4104fed9ecc7d6cd2cb637da860f6
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337349"
---
# <a name="enable-shared-disk"></a>Megosztott lemez engedélyezése

Ez a cikk bemutatja, hogyan engedélyezheti a megosztott lemezek (előzetes verzió) szolgáltatást az Azure Managed Disks szolgáltatásban. Az Azure Shared Disks (előzetes verzió) az Azure Managed Disks új funkciója, amely lehetővé teszi a felügyelt lemezek egyidejű csatlakoztatását több virtuális géphez (VM). A felügyelt lemezek több virtuális géphez való csatolásával új vagy meglévő fürtözött alkalmazásokat telepíthet át az Azure-ba. 

Ha olyan felügyelt lemezeken keres elméleti információkat, amelyeken engedélyezve vannak a megosztott lemezek, tekintse meg az [Azure megosztott lemezeket](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]