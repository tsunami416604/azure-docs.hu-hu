---
title: Az Azure Virtual Machines nem csatlakoztatható, mert nincs engedélyezve az NSG-t RDP-portra |} A Microsoft Docs
description: Ismerje meg, amelyben RDP meghiúsul, az Azure Portalon az NSG-konfiguráció miatt probléma elhárítása |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 89af30533e10df0968b60039d7ea15886e89bc25
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52266927"
---
#  <a name="cannot-rdp-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nem végezhető a virtuális gép RDP-vel, mert az RDP-port nincs engedélyezve az NSG-t

Ez a cikk bemutatja, hogyan, amelyben nem lehet csatlakoztatni az Azure Windows Virtual Machines (VM), mert a port nincs engedélyezve a hálózati biztonsági csoport a probléma megoldásához.


> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell, amely az új központi telepítéseknél helyett a klasszikus üzemi modell használatát javasoljuk. 

## <a name="symptom"></a>Jelenség

A távoli asztal protokoll (RDP) miatt az RDP-port az Azure-beli virtuális Géphez való kapcsolat nem nyílik meg a hálózati biztonsági csoport nem lehet.

## <a name="solution"></a>Megoldás 

Amikor létrehoz egy új virtuális Gépet, az összes forgalom az internetről alapértelmezés szerint le van tiltva. 

Ahhoz, hogy a hálózati biztonsági csoport az RDP-portjára, kövesse az alábbi lépéseket:
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A **virtuális gépek**, a virtuális gép, amelyen hiba jelentkezik. 
3. A **beállítások**válassza **hálózatkezelés**. 
4. A **bejövőport-szabályok**, ellenőrizze, hogy ha a port RDP megfelelően van-e beállítva. A konfiguráció mintája a következő: 

    **Prioritás**: 300 </br>
    **Port**: 3389-es </br>
    **Név**: Port_3389 </br>
    **Port**: 3389-es </br>
    **Protokoll**: TCP </br>
    **Forrás**: összes </br>
    **Célok**: összes </br>
    **A művelet**: engedélyezése </br>

Azt adja meg a forrás IP-címe, ez a beállítás lehetővé teszi, hogy a forgalom csak az egy adott IP- vagy IP-címtartományt a virtuális Géphez való csatlakozáshoz. Ellenőrizze, hogy a tartományban van a számítógépen, az RDP-munkamenet inicializálása.

Hálózati biztonsági csoport kapcsolatos további információkért lásd: [hálózati biztonsági csoport](../../virtual-network/security-overview.md).

> [!NOTE]
> 3389-es RDP-portra van közzétéve az interneten. Ez csak tesztelésre ajánlott. Éles üzemi környezetek esetében javasoljuk egy VPN vagy magánhálózati kapcsolatot használni.

## <a name="next-steps"></a>További lépések

Ha az RDP-port már engedélyezve van a hálózati biztonsági csoport, [hibaelhárítása az Azure virtuális Gépen RDP általános hiba](./troubleshoot-rdp-general-error.md).



