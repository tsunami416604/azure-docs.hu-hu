---
title: Megosztott lemezek engedélyezése az Azure által kezelt lemezekhez
description: Azure-beli felügyelt lemez konfigurálása megosztott lemezekkel (előzetes verzió), hogy több virtuális gépen is megoszthassa
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970375"
---
# <a name="enable-shared-disk"></a>Megosztott lemez engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a megosztott lemezek (előzetes verzió) szolgáltatás az Azure felügyelt lemezek. Az Azure megosztott lemezei (előzetes verzió) egy új funkció az Azure felügyelt lemezek, amely lehetővé teszi, hogy csatolja a felügyelt lemez több virtuális gépek (VM) egyszerre. Egy felügyelt lemez csatlakoztatása több virtuális géphez lehetővé teszi, hogy új üzembe helyezése vagy a meglévő fürtözött alkalmazások áttelepítése az Azure-ba. 

Ha általános információkat keres olyan felügyelt lemezeken, amelyeken engedélyezve van a megosztott lemez, olvassa el az [Azure megosztott lemezeit.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]