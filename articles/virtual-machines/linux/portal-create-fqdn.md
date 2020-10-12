---
title: Teljes tartománynév létrehozása egy virtuális géphez a Azure Portal
description: Megtudhatja, hogyan hozhat létre teljes tartománynevet (FQDN) egy linuxos virtuális géphez a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331471"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Teljes tartománynév létrehozása a Azure Portal Linux rendszerű virtuális gép számára

Amikor létrehoz egy virtuális gépet (VM) a [Azure Portal](https://portal.azure.com), a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. Ezt az IP-címet használja a virtuális gép távoli eléréséhez. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után egyet is hozzáadhat. Ez a cikk a DNS-név vagy a teljes tartománynév létrehozásának lépéseit mutatja be.

## <a name="create-a-fqdn"></a>Teljes tartománynév létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet. Ha szükséges, [létrehozhat egy virtuális gépet a portálon](quick-create-portal.md) vagy [Az Azure CLI](quick-create-cli.md)használatával. A virtuális gép működése után kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Most már távolról is csatlakozhat a virtuális géphez a következő DNS-névvel, például: `ssh azureuser@mydns.westus.cloudapp.azure.com` .

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-címmel és egy DNS-névvel, üzembe helyezhet olyan általános alkalmazás-keretrendszereket vagy szolgáltatásokat, mint például az Nginx, a MongoDB, a Docker stb.

További információt a [Resource Manager használatával](../../azure-resource-manager/management/overview.md) kapcsolatban az Azure-beli üzembe helyezések létrehozásával kapcsolatos tippekhez is találhat.

