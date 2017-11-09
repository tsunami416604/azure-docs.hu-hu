---
title: "A MongoDB telepítése a Windows Azure-ban |} Microsoft Docs"
description: "Útmutató a MongoDB telepítése a Windows Server rendszert futtató klasszikus telepítési modellel létrehozott, egy Azure virtuális gépen."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.openlocfilehash: d70194f30b7866cab38cfbbe1ea06664b0ddc1f5
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>A MongoDB telepítése a Windows Azure-ban
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md).  Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Telepítse, és a Resource Manager üzembe helyezési modellel használatával MongoDB konfigurálására, [Ez a cikk](../install-mongodb.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[MongoDB] [ MongoDB] egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk végigvezeti a lépésein, egy Windows Server virtuális gép (VM) használatával a [Azure-portálon][AzurePortal]. Ezután hozzon létre és adatlemezt csatolni a virtuális gép telepítése és konfigurálása a MongoDB előtt. Ha egy meglévő virtuális gép, amely a használni kívánt Azure-ban, de is ugorhat rögtön [telepítése és konfigurálása a MongoDB](#install-and-run-mongodb-on-the-virtual-machine).

## <a name="create-a-virtual-machine-running-windows-server"></a>Windows Servert futtató virtuális gép létrehozása
Kövesse az alábbi utasításokat a virtuális gép létrehozásához.

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> Mongodb-protokolltámogatással végpont hozzáadása a virtuális gép létrehozása során, és konfigurálja az alábbiak szerint: nevezze el, mint a **Mongo**, használjon **TCP** protokollt, és csak a nyilvános és magánhálózati portot adjon meg  **27017**.
>
>

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Tárhely biztosításához a virtuális gép, adatlemezzel és majd inicializálnia, hogy a Windows használni tudja. Ha már rendelkezik adatlemezt, csatolhat a meglévő lemezt, vagy csatolhat egy üres lemez.

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

A lemez inicializálása, lásd: "hogyan: a Windows Server új adatlemezt inicializálása" a [hogyan lehet adatlemezt csatolni egy Windows rendszerű virtuális gép](attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Telepítése és futtatása a MongoDB a virtuális gépen
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban megtudta, hogyan hozzon létre egy Windows Server rendszert futtató virtuális gépet, távolról kapcsolódni hozzá, és adatlemezt csatolni.  Is megtanulta, hogyan kell telepíteni, és a MongoDB konfigurálása a Windows-alapú virtuális gépen. Most már elérheti az MongoDB a Windows-alapú virtuális gépen, a következő speciális témakörei a [MongoDB dokumentációt][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

