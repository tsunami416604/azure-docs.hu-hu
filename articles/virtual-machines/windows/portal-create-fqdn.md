---
title: Windows rendszerű virtuális gép teljes tartománynevének létrehozása a Azure Portal
description: Megtudhatja, hogyan hozhat létre teljes tartománynevet vagy teljes TARTOMÁNYNEVEt a Azure Portal Resource Manager-alapú virtuális géphez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa4e4bd7561a7a745e6eb0b70016144b9fb57998
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371275"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Teljes tartománynév létrehozása a Azure Portal a Windows rendszerű virtuális gépekhez

Amikor létrehoz egy virtuális gépet (VM) a [Azure Portal](https://portal.azure.com), a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. Ezt az IP-címet használja a virtuális gép távoli eléréséhez. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, létrehozhat egyet a virtuális gép létrehozása után. Ez a cikk a DNS-név vagy a teljes tartománynév létrehozásának lépéseit mutatja be.

## <a name="create-a-fqdn"></a>Teljes tartománynév létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet. Ha szükséges, [létrehozhat egy virtuális gépet a portálon](quick-create-portal.md) vagy a [Azure PowerShell](quick-create-powershell.md)használatával. A virtuális gép működése után kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Mostantól távolról is csatlakozhat a virtuális géphez a következő DNS-névvel, például a RDP protokoll (RDP) protokollal.

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-címmel és egy DNS-névvel, olyan általános alkalmazás-keretrendszereket vagy szolgáltatásokat telepíthet, mint például az IIS, az SQL vagy a SharePoint.

További információt a [Resource Manager használatával](../../azure-resource-manager/management/overview.md) kapcsolatban az Azure-beli üzembe helyezések létrehozásával kapcsolatos tippekhez is találhat.

