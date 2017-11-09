---
title: "Virtuális gép létrehozása az Azure portálon |} Microsoft Docs"
description: "Windows virtuális gép létrehozása az Azure portálon."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 61d71479ae39c1ee22618f2c7fc6677b5bae8fe4
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Az Azure portálon Windows rendszerű virtuális gép létrehozása
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [PowerShell: Klasszikus telepítési](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Megtudhatja, hogyan [a következő lépések segítségével a Resource Manager üzembe helyezési modellel](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával a **Azure-portálon**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az oktatóanyag bemutatja, hogyan hozzon létre egy Azure virtuális gép (VM) fut a Windows Azure-portálon. A Windows Server lemezkép használjuk példaként, de ez csak egyike a számos rendszerképet az Azure kínál. Vegye figyelembe, hogy a rendszerképek függ-e az előfizetés. Windows asztali rendszerképek például MSDN-előfizetők számára elérhető lehet.

Ez a szakasz bemutatja, hogyan használható a **irányítópult** válassza ki, majd létre az a virtuális gépet az Azure portálon.

Virtuális gépek használatával is létrehozhat [a saját lemezképek](createupload-vhd.md). Ezzel és más módszerekkel kapcsolatos további tudnivalókért lásd: [Windows virtuális gépek létrehozásának különböző módszerei](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<!-- 02/27/2017 Video removed as it was based on the classic portal. -->

## <a id="createvirtualmachine"></a>a virtuális gép létrehozása
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a Resource Manager üzembe helyezési modellel virtuális gép létrehozása](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure portálon.
* Jelentkezzen be a virtuális gép. Útmutatásért lásd: [jelentkezzen be a Windows Server rendszerű virtuális gép](connect-logon.md).
* Adatok tárolására lemezt csatlakoztatni. Csatolhat is üres és a lemezek, amelyek adatokat tartalmaznak. Útmutatásért lásd: a [adatlemezt csatolni a klasszikus üzembe helyezési modellel létrehozott Windows virtuális gépek](attach-disk.md).
