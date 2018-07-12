---
title: Virtuális gép létrehozása az Azure Portalon |} A Microsoft Docs
description: Windows virtuális gép létrehozása az Azure Portalon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5fd2128ff436d3211f41c7dfdcc4c2b8aabd0eb0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232326"
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Az Azure Portalon Windows rendszerű virtuális gép létrehozása
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [PowerShell: Klasszikus üzembe helyezéssel](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, hogyan [ezeket a lépéseket a Resource Manager üzemi modell használatával](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával a **az Azure portal**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Azure virtuális gép (VM) az Azure Portalon Windows rendszerű. Példaként egy Windows Server-rendszerképet használunk, de ez csak egyike az Azure számos rendszerképnek. Vegye figyelembe, hogy a lemezkép előfizetéstől függenek-e az előfizetés. Windows asztali rendszerképek például az MSDN-előfizetők számára elérhető lehet.

Ez a szakasz bemutatja, hogyan használható a **irányítópult** válassza ki, és majd a virtuális gép létrehozása az Azure Portalon.

Virtuális gépek használatával is létrehozhat [saját lemezképek](createupload-vhd.md). Ez és egyéb módszerekkel kapcsolatos további információkért lásd: [Windows virtuális gépek létrehozásának különböző módszerei](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"> </a>A virtuális gép létrehozása
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozzon létre egy virtuális Gépet, a Resource Manager üzemi modell használatával](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure Portalon.
* Jelentkezzen be a virtuális gépet. Útmutatásért lásd: [jelentkezzen be a Windows Server rendszerű virtuális gép](connect-logon.md).
* Adatok tárolása a lemez csatolása. Üres lemez és az adatokat tartalmazó lemezeket csatolhat. Útmutatásért tekintse meg a [adatlemez csatolása a klasszikus üzemi modellel létrehozott Windows virtuális gép](attach-disk.md).
