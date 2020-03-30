---
title: Hálózati virtuális berendezésekkel kapcsolatos problémák elhárítása az Azure-ban | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a hálózati virtuális berendezésekkel kapcsolatos problémákat az Azure-ban.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056835"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Hálózati virtuális berendezésekkel kapcsolatos problémák az Azure-ban

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Előfordulhat, hogy a virtuális gép vagy a VPN-kapcsolat problémák és hibák használata során egy harmadik fél hálózati virtuális berendezés (NVA) a Microsoft Azure-ban. Ez a cikk alapvető lépéseket tartalmaz az Alapvető Azure Platform-követelmények érvényesítéséhez az NVA-konfigurációkhoz.

A külső nva-k technikai támogatását és az Azure platformmal való integrálásukat az NVA szállítója biztosítja.

> [!NOTE]
> Ha nva-val kapcsolatos kapcsolódási vagy útválasztási problémája van, forduljon közvetlenül [az NVA forgalmazójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Ellenőrzőlista az NVA szállítójával kapcsolatos hibaelhárításhoz

- Szoftverfrissítések Az NVA VM szoftverhez
- A szervizfiók beállítása és funkciói
- Felhasználó által definiált útvonalak (UDR- ek) a virtuális hálózati alhálózatokon, amelyek az NVA-ba irányítják a forgalmat
- UdRs virtuális hálózati alhálózatokon, amelyek az NVA-ból érkező forgalmat irányítják
- Táblák és szabályok útválasztása az NVA-n belül (például nic1-től NIC2-ig)
- A hálózati forgalom vételének és küldésének ellenőrzésére szolgáló NV hálózati adapterek nyomon követése
- Szabványos termékváltozat és nyilvános IP-k használata esetén létre kell hozni egy NSG-t, és egy explicit szabályt, amely lehetővé teszi a forgalom nva-hoz való átirányításának engedélyezését.

## <a name="basic-troubleshooting-steps"></a>Alapvető hibaelhárítási lépések

- Az alapkonfiguráció ellenőrzése
- Az NVA teljesítményének ellenőrzése
- Speciális hálózati hibaelhárítás

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>A nem megfelelő konfigurációs követelmények ellenőrzése az Azure-ban

Minden NVA rendelkezik az Azure-beli megfelelő működéséhez. A következő szakasz ezeket az alapvető konfigurációkat ismerteti. További információért [forduljon az NVA forgalmazójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

**Annak ellenőrzése, hogy engedélyezve van-e az IP-továbbítás az NVA-n**

Az Azure Portal használata

1. Keresse meg az NVA-erőforrást az [Azure Portalon,](https://portal.azure.com)válassza a Hálózat lehetőséget, majd válassza ki a hálózati felületet.
2. A Hálózati csatoló lapon válassza az IP-konfiguráció lehetőséget.
3. Győződjön meg arról, hogy az IP-továbbítás engedélyezve van.

A PowerShell használata

1. Nyissa meg a PowerShellt, majd jelentkezzen be azure-fiókjába.
2. Futtassa a következő parancsot (cserélje le a zárójelbe tett értékeket az adatokra):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Ellenőrizze az **EnableIPForwarding tulajdonságot.**
4. Ha az IP-továbbítás nincs engedélyezve, futtassa a következő parancsokat az engedélyezéséhez:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**NSG ellenőrzése a Standard SKU Pubilc IP használatakor** Szabványos termékváltozat és nyilvános IP-k használata esetén létre kell hozni egy NSG-t és egy explicit szabályt, hogy engedélyezze a forgalmat az NVA-ba.

**Ellenőrizze, hogy a forgalom átirányítható-e az NVA-ba**

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a **Network Watcher**lehetőséget a **Következő ugrás lehetőségre.**
2. Adjon meg egy virtuális gép, amely úgy van beállítva, hogy átirányítsa a forgalmat az NVA-ra, és egy cél IP-címet, amelyen a következő ugrás megtekintéséhez. 
3. Ha az NVA nem szerepel a **következő ugrás,** ellenőrizze és frissítse az Azure-útvonaltáblákat.

**Ellenőrizze, hogy a forgalom elérheti-e az NVA-t**

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a **Network Watcher programot,** majd válassza az **IP-folyamat ellenőrzése**lehetőséget. 
2. Adja meg a virtuális gép és az IP-címét az NVA, majd ellenőrizze, hogy a forgalmat blokkolja a hálózati biztonsági csoportok (NSG).
3. Ha van egy NSG-szabály, amely blokkolja a forgalmat, keresse meg az NSG **hatékony biztonsági** szabályokat, majd frissítse azt, hogy a forgalom át. Ezután futtassa újra **az IP-folyamat ellenőrzése,** és használja **kapcsolat hibaelhárítás** i teszt TCP kommunikáció virtuális gép a belső vagy külső IP-cím.

**Annak ellenőrzése, hogy az NVA és a virtuális gépek figyelik-e a várható forgalmat**

1. Csatlakozzon az NVA-hoz RDP vagy SSH használatával, majd futtassa a következő parancsot:

    Windows esetén:

        netstat -an

    Linux esetén:

        netstat -an | grep -i listen
2. Ha nem látja az NVA-szoftver által használt TCP-portot, amely szerepel az eredményekben, konfigurálnia kell az alkalmazást az NVA-n és a virtuális gépen, hogy figyelje és válaszoljon az okra a portokat elérő forgalomra. [Szükség esetén forduljon az NVA forgalmazójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="check-nva-performance"></a>Az NVA teljesítményének ellenőrzése

### <a name="validate-vm-cpu"></a>Virtuálisgép-processzor ellenőrzése

Ha a PROCESSZOR-használat megközelíti a 100 százalékot, problémákléphetnek fel, amelyek hatással vannak a hálózati csomagok cseppjeire. A virtuális gép az Azure Portalon egy adott időtartam átlagos processzora. Egy CPU-csúcs során vizsgálja meg, hogy a vendég virtuális gép melyik folyamat okozza a magas CPU-t, és ha lehetséges, enyhítse. Előfordulhat, hogy a virtuális gépet nagyobb termékváltozatméretre kell átméreteznie, vagy a virtuálisgép-méretezési csoport esetében növelheti a példányok számát, vagy a PROCESSZOR-használat automatikus méretezésére kell beállítania. A problémák bármelyikével kapcsolatban szükség esetén [forduljon az NVA forgalmazójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

### <a name="validate-vm-network-statistics"></a>Virtuálisgép-hálózat statisztikáinak ellenőrzése

Ha a virtuális gép hálózat a csúcsok használata, vagy azt mutatja, időszakok magas használat, előfordulhat, hogy a virtuális gép termékváltozatának méretét is növelni kell a nagyobb átviteli képességek eléréséhez. A virtuális gép is újratelepítheti, ha a gyorsított hálózatkezelés engedélyezve van. Annak ellenőrzéséhez, hogy az NVA támogatja-e az Accelerated Networking funkciót, szükség esetén [forduljon az NVA forgalmazójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="advanced-network-administrator-troubleshooting"></a>Speciális hálózati rendszergazdai hibaelhárítás

### <a name="capture-network-trace"></a>Hálózati nyomkövetés rögzítése
Rögzítsen egy egyidejű hálózati nyomkövetést a forrásvirtuális gépen, az NVA-n és a célvirtuális gépen a **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** vagy az **Nmap**futtatása közben, majd állítsa le a nyomkövetést.

1. Az egyidejű hálózati nyomkövetés rögzítéséhez futtassa a következő parancsot:

   **Windows esetén**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

   **Linux esetén**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Használja **psping** vagy **Nmap** a forrás virtuális gép a `PsPing 10.0.0.4:80` cél `Nmap -p 80 10.0.0.4`virtuális gép (például: vagy ).
3. Nyissa meg a hálózati nyomkövetést a cél virtuális gépről a [Hálózatfigyelő](https://www.microsoft.com/download/details.aspx?id=4865) vagy a tcpdump használatával. A **PsPing** vagy az **Nmap** által futtatott forrásvirtuális gép IP-címéhez alkalmazzon megjelenítési szűrőt, például `IPv4.address==10.0.0.4 (Windows netmon)` vagy `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Nyomkövetések elemzése

Ha nem látja a háttérrendszer beli virtuális gép nyomkövetésébe érkező csomagokat, akkor valószínűleg NSG vagy UDR zavarja, vagy az NVA útválasztási táblák helytelenek.

Ha a csomagok beérkeznek, de nincs válasz, akkor a virtuálisgép-alkalmazáshoz vagy a tűzfalhoz kapcsolódó problémáról lehet szó. A problémák bármelyikével [kapcsolatban szükség esetén forduljon az NVA forgalmazójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)