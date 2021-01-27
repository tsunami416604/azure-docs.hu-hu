---
title: Windows rendszerű virtuális gépek RDP-problémáinak elhárítása az Azure-ban | Microsoft Docs
description: Az Azure-beli RDP-problémák hibaelhárítása Windows rendszerű virtuális gépeken.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/13/2018
ms.author: genli
ms.openlocfilehash: 512130928c8e18aad90bce351e3be85d6fbe03f0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878646"
---
# <a name="troubleshoot-rdp-issues"></a>RDP-problémák elhárítása

A virtuális géphez való kapcsolódáshoz szükséges RDP létrehozásával kapcsolatos alábbi problémák a jelen szakaszban találhatók:

- [RDP alaphelyzetbe állítása](reset-rdp.md)
- [RDP hibaelhárítása](troubleshoot-rdp-connection.md)
- [RDP részletes hibaelhárítása](detailed-troubleshoot-rdp.md)
- [Letiltott DHCP miatti RDP-hiba hibaelhárítása](troubleshoot-rdp-dhcp-disabled.md)
- [Az NSG-beállítás miatti RDP-hiba hibaelhárítása](troubleshoot-rdp-nsg-problem.md)
- [Konkrét hibák elhárítása](troubleshoot-specific-rdp-errors.md)
- [Licenckiszolgáló hiányával kapcsolatos hibák elhárítása](troubleshoot-rdp-no-license-server.md)
- [Távoli asztali szolgáltatásokkal kapcsolatos hibák elhárítása](troubleshoot-remote-desktop-services-issues.md)
- [Belső hibák elhárítása](Troubleshoot-rdp-internal-error.md)
- [Gyakori kapcsolatmegszakadási hibák elhárítása](troubleshoot-rdp-intermittent-connectivity.md)
- [Általános hibák elhárítása](troubleshoot-rdp-general-error.md)
- [Hitelesítési hibák hibaelhárítása](/troubleshoot/azure/virtual-machines/cannot-connect-rdp-azure-vm)
- [Az Azure-beli virtuális gépek kapcsolati hibáinak elhárítása eseményazonosító alapján](event-id-troubleshoot-vm-rdp-connecton.md)
- [Virtuális gép statikus IP-cím miatti RDP-hibájának hibaelhárítása](troubleshoot-rdp-static-ip.md)
- [A virtuális gép letiltott hálózati adapter miatti RDP-hibájának hibaelhárítása](troubleshoot-rdp-nic-disabled.md)
- [Csökkentett üzemmód által okozott RDP-hiba elhárítása](troubleshoot-rdp-safe-mode.md)
- [A vendég operációs rendszer tűzfalának letiltása az Azure-beli virtuális gépen](disable-guest-os-firewall-windows.md)
- [Tűzfalszabály engedélyezése vagy letiltása egy vendég operációs rendszeren](enable-disable-firewall-rule-guest-os.md)
- [A vendég operációs rendszer tűzfala blokkolja a bejövő forgalmat](guest-os-firewall-blocking-inbound-traffic.md)
- [A vendég operációs rendszer tűzfalának konfigurációja helytelen](guest-os-firewall-misconfigured.md) 
- [netvsc.sys által okozott RDP-hiba elhárítása](troubleshoot-rdp-driver-netvsc.md)