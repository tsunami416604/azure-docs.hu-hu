---
title: Megosztott lemezek engedélyezése az Azure Managed Disks szolgáltatásban
description: Egy megosztott lemezzel (előzetes verzió) rendelkező Azure felügyelt lemez konfigurálása, amely több virtuális gépen is megosztható
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dc7cf6e3f72ff9fa7ef5d7cbd85c1fb93f76f9a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146771"
---
# <a name="enable-shared-disk"></a>Megosztott lemez engedélyezése

Ez a cikk bemutatja, hogyan engedélyezheti a megosztott lemezek (előzetes verzió) szolgáltatást az Azure Managed Disks szolgáltatásban. Az Azure Shared Disks (előzetes verzió) az Azure Managed Disks új funkciója, amely lehetővé teszi a felügyelt lemezek egyidejű csatlakoztatását több virtuális géphez (VM). A felügyelt lemezek több virtuális géphez való csatolásával új vagy meglévő fürtözött alkalmazásokat telepíthet át az Azure-ba. 

Ha olyan felügyelt lemezeken keres elméleti információkat, amelyeken engedélyezve vannak a megosztott lemezek, tekintse meg az [Azure megosztott lemezeket](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]