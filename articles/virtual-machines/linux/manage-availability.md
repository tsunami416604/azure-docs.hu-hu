---
title: Az Azure-ban a Linux virtuális gépek rendelkezésre állásának kezelése |} Microsoft Docs
description: Több virtuális gép használata a Linux-alkalmazás az Azure-ban a magas rendelkezésre állásának biztosításához
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecddbb54137c018c1acc202e4056672eb626f87d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Linux virtuális gépek rendelkezésre állásának kezelése

Ismerje meg, hogy módszereket állíthat be és felügyelhet több virtuális gép magas rendelkezésre állásának a Linux-alkalmazás az Azure-ban. Emellett [Windows virtuális gépek rendelkezésre állásának kezelése](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Rendelkezésre állási készlet parancssori felület használatával a Resource Manager üzembe helyezési modellel létrehozásával kapcsolatos utasításokért lásd: [azure availset: a rendelkezésre állási készletek kezelésére szolgáló parancsok](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>További lépések
Hálózati terheléselosztást a virtuális gépek kapcsolatos további információkért lásd: [terheléselosztás virtuális gépek](../virtual-machines-linux-load-balance.md).

