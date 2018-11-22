---
title: Azure virtuális gépeket nem lehet kapcsolódni, mert nincs engedélyezve az NSG-t az RDP-portra |} A Microsoft Docs
description: Ismerje meg, amelyben RDP meghiúsul, az Azure Portalon az NSG-konfiguráció miatt probléma elhárítása |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: cb9058d4f68b2dc202edeeaa6cafb2eefa82470b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284727"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nem lehet távoli csatlakozás egy virtuális Gépet, mert az RDP-port nincs engedélyezve az NSG-t

Ez a cikk bemutatja, hogyan, amelyben nem lehet csatlakoztatni egy Windows Azure virtuális gép (VM), mert a távoli asztal protokoll (RDP) port nem engedélyezett a hálózati biztonsági csoport (NSG) a probléma megoldásához.


> [!NOTE] 
> Az Azure az erőforrások létrehozásához és használatához két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Azt javasoljuk, hogy használja a Resource Manager üzemi modell helyett a klasszikus üzemi modellben az új üzembe helyezésekhez. 

## <a name="symptom"></a>Jelenség

RDP-kapcsolatok nem tudja elvégezni az Azure-beli virtuális géphez, mivel az RDP-port nem nyílik meg a hálózati biztonsági csoport.

## <a name="solution"></a>Megoldás 

Amikor létrehoz egy új virtuális Gépet, az összes forgalom az internetről alapértelmezés szerint le van tiltva. 

Ahhoz, hogy az RDP-portra egy NSG-ben, kövesse az alábbi lépéseket:
1. Jelentkezzen be a [az Azure Portalon](https://portal.azure.com).
2. A **virtuális gépek**, válassza ki a virtuális Gépet, a problémát. 
3. A **beállítások**válassza **hálózatkezelés**. 
4. A **bejövőport-szabályok**, ellenőrizze-e a port RDP megfelelően van-e beállítva. A következő egy példa a konfigurációs: 

    **Prioritás**: 300 </br>
    **Port**: 3389-es </br>
    **Név**: Port_3389 </br>
    **Port**: 3389-es </br>
    **Protokoll**: TCP </br>
    **Forrás**: összes </br>
    **Célok**: összes </br>
    **A művelet**: engedélyezése </br>

Azt adja meg a forrás IP-címe, ez a beállítás lehetővé teszi, hogy csak a megadott IP-címet vagy IP-címtartományt a virtuális Géphez való csatlakozáshoz érkező forgalmat. Győződjön meg arról, hogy az RDP-munkamenet elindításához használja a számítógépet a tartományba esik.

NSG-kkel kapcsolatos további információkért lásd: [hálózati biztonsági csoport](../../virtual-network/security-overview.md).

> [!NOTE]
> 3389-es RDP-portra van közzétéve az interneten. Ezért azt javasoljuk, hogy ezt a portot használja csak tesztelésre ajánlott. Éles környezetekben ajánlott VPN-t vagy magánhálózati kapcsolatot használni.

## <a name="next-steps"></a>További lépések

Ha az RDP-port már engedélyezve van az NSG-t, tekintse meg [hibaelhárítása az Azure virtuális Gépen RDP általános hiba](./troubleshoot-rdp-general-error.md).



