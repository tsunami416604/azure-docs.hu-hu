---
title: Nem lehet csatlakozni az Azure virtuális gépekhez, mert az RDP-port nincs engedélyezve az NSG- ben | Microsoft dokumentumok
description: Megtudhatja, hogyan hárítható el egy olyan probléma elhárítása, amely nek az RDP-sikertelen az Azure PortalNSG-konfigurációja miatt | Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 6c877690cf27edd73f1d828b8a1dda6f4f34e780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918172"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nem lehet távolról csatlakozni egy virtuális géphez, mert az RDP-port nincs engedélyezve az NSG-ben

Ez a cikk bemutatja, hogyan oldható meg az a probléma, amely nem tud csatlakozni egy Azure Windows virtuális géphez (VM), mert a Távoli asztali protokoll (RDP) portja nincs engedélyezve a hálózati biztonsági csoportban (NSG).


## <a name="symptom"></a>Hibajelenség

Az Azure-ban nem hozhat létre RDP-kapcsolatot egy virtuális géptel, mert az RDP-port nincs megnyitva a hálózati biztonsági csoportban.

## <a name="solution"></a>Megoldás 

Amikor új virtuális gép létrehozása, az internetről érkező összes forgalom alapértelmezés szerint blokkolva lesz. 

Az RDP-port NSG-ben való engedélyezéséhez kövesse az alábbi lépéseket:
1. Jelentkezzen be [az Azure Portalra.](https://portal.azure.com)
2. A **virtuális gépek,** válassza ki a virtuális gépet, amely a problémát. 
3. A **Beállítások csoportban**válassza a **Hálózat**lehetőséget. 
4. A **Bejövő portszabályok**ban ellenőrizze, hogy az RDP portja megfelelően van-e beállítva. A következő példa a konfiguráció: 

    **Prioritás**: 300 </br>
    **Név**: Port_3389 </br>
    **Port(Cél)**: 3389 </br>
    **Protokoll**: TCP </br>
    **Forrás**: Bármely </br>
    **Úti célok**: Bármely </br>
    **Művelet**: Engedélyezés </br>

Ha megadja a forrás IP-címet, ez a beállítás csak egy adott IP-címről vagy IP-címtartományból származó forgalmat engedélyez a virtuális géphez való csatlakozáshoz. Győződjön meg arról, hogy az RDP-munkamenet indításához használt számítógép a tartományon belül van.

Az NSG-kről további információt a [hálózati biztonsági csoport ban](../../virtual-network/security-overview.md)talál.

> [!NOTE]
> A 3389-es RDP-port elérhető az interneten. Ezért azt javasoljuk, hogy ezt a portot csak tesztelésre ajánlott. Éles környezetben azt javasoljuk, hogy vpn-t vagy privát kapcsolatot használjon.

## <a name="next-steps"></a>További lépések

Ha az RDP-port már engedélyezve van az NSG-ben, olvassa el Az [RdP általános hiba elhárítása az Azure Virtuális gépben című témakört.](./troubleshoot-rdp-general-error.md)



