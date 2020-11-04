---
title: Teljes tartománynév létrehozása egy virtuális géphez a Azure Portal
description: Megtudhatja, hogyan hozhat létre teljes tartománynevet (FQDN) egy virtuális géphez a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351909"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Teljes tartománynév létrehozása a Azure Portal Linux rendszerű virtuális gép számára

Amikor létrehoz egy virtuális gépet (VM) a [Azure Portal](https://portal.azure.com), a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. Ezt az IP-címet használja a virtuális gép távoli eléréséhez. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után egyet is hozzáadhat. Ez a cikk a DNS-név vagy a teljes tartománynév létrehozásának lépéseit mutatja be. 

## <a name="create-a-fqdn"></a>Teljes tartománynév létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet. Ha szükséges, [Linux](./linux/quick-create-portal.md) vagy [Windows rendszerű](./windows/quick-create-portal.md) virtuális gépet is létrehozhat a portálon. A virtuális gép működése után kövesse az alábbi lépéseket:


1. Válassza ki a virtuális gépet a portálon. A **DNS-név** területen válassza a **Konfigurálás** lehetőséget.
2. Adja meg a DNS-nevet, majd kattintson a **Save (Mentés** ) gombra az oldal tetején.
3. Ha vissza szeretne térni a virtuális gép áttekintés paneljére, a jobb felső sarokban található **X** gombra kattintva zárjuk be a **konfiguráció** panelt. 
4. Győződjön meg arról, hogy a *DNS-név* helyesen jelenik meg.
   



## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-címmel és egy DNS-névvel, olyan általános alkalmazás-keretrendszereket vagy szolgáltatásokat telepíthet, mint például az Nginx, a MongoDB és a Docker.

További információt a [Resource Manager használatával](../azure-resource-manager/management/overview.md) kapcsolatban az Azure-beli üzembe helyezések létrehozásával kapcsolatos tippekhez is találhat.

