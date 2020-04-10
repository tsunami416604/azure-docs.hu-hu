---
title: Megosztott lemezek engedélyezése az Azure által kezelt lemezekhez
description: Azure-beli felügyelt lemez konfigurálása megosztott lemezekkel (előzetes verzió), hogy több virtuális gépen is megoszthassa
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1cb581f0914dbafff4a46510924115e82e42b990
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011140"
---
# <a name="enable-shared-disk"></a>Megosztott lemez engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a megosztott lemezek (előzetes verzió) szolgáltatás az Azure felügyelt lemezek. Az Azure megosztott lemezei (előzetes verzió) egy új funkció az Azure felügyelt lemezek, amely lehetővé teszi, hogy csatolja a felügyelt lemez több virtuális gépek (VM) egyszerre. Egy felügyelt lemez csatlakoztatása több virtuális géphez lehetővé teszi, hogy új üzembe helyezése vagy a meglévő fürtözött alkalmazások áttelepítése az Azure-ba. 

Ha általános információkat keres olyan felügyelt lemezeken, amelyeken engedélyezve van a megosztott lemez, olvassa el az [Azure megosztott lemezeit.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]