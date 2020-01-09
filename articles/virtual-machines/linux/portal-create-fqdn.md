---
title: Teljes tartománynév létrehozása Linux rendszerű virtuális gép számára a Azure Portal
description: Megtudhatja, hogyan hozhat létre teljes tartománynevet vagy teljes TARTOMÁNYNEVEt a Azure Portal Resource Manager-alapú virtuális géphez.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 256e1e60ec8f50df2faea64f31e88d00370b33c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458717"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Teljes tartománynév létrehozása a Azure Portal Linux rendszerű virtuális gép számára

Amikor létrehoz egy virtuális gépet (VM) a [Azure Portal](https://portal.azure.com), a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. Ezt az IP-címet használja a virtuális gép távoli eléréséhez. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után egyet is hozzáadhat. Ez a cikk a DNS-név vagy a teljes tartománynév létrehozásának lépéseit mutatja be.

## <a name="create-a-fqdn"></a>Teljes tartománynév létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet. Ha szükséges, [létrehozhat egy virtuális gépet a portálon](quick-create-portal.md) vagy [Az Azure CLI](quick-create-cli.md)használatával. A virtuális gép működése után kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Mostantól távolról is csatlakozhat a virtuális géphez a következő DNS-névvel, például a `ssh azureuser@mydns.westus.cloudapp.azure.com`használatával.

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-címmel és egy DNS-névvel, üzembe helyezhet olyan általános alkalmazás-keretrendszereket vagy szolgáltatásokat, mint például az Nginx, a MongoDB, a Docker stb.

További információt a [Resource Manager használatával](../../azure-resource-manager/management/overview.md) kapcsolatban az Azure-beli üzembe helyezések létrehozásával kapcsolatos tippekhez is találhat.

