---
title: "MySQL telepítése egy OpenSUSE virtuális gépen |} Microsoft Docs"
description: "Ismerje meg, MySQL telepítsen egy OpenSUSE Linux VMirtual gépet az Azure-ban."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>A MySQL telepítése Azure-ban működő, OpenSUSE Linux rendszerű virtuális gépen
[MySQL] [ MySQL] egy népszerű, nyílt forráskódú SQL-adatbázis. Ez az oktatóanyag bemutatja, hogyan OpenSUSE Linuxot futtató virtuális gép létrehozása, majd telepítse a MySQL.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linuxot futtató virtuális gép létrehozása
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Telepítse, MySQL futtassa a virtuális gépen
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Következő lépések
MySQL kapcsolatos részletekért lásd: a [MySQL dokumentáció][MySQLDocs].

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com

