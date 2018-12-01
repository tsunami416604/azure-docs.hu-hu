---
title: Az Azure-ban a hálózati virtuális berendezés problémák elhárítása |} A Microsoft Docs
description: Ismerje meg az Azure-ban a hálózati virtuális berendezés kapcsolatos problémák elhárítása.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 13cec39278577a818ef43f1215fd2e6653f15ed2
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678440"
---
#  <a name="network-virtual-appliance-issues-in-azure"></a>Hálózati virtuális berendezés problémák az Azure-ban

Előfordulhat, hogy virtuális Gépet, vagy ha VPN-kapcsolati hibák és a hibákat, amikor egy külső féltől származó hálózati virtuális készüléket (NVA) Microsoft Azure-ban. Ez a cikk segítséget nyújt az NVA-konfigurációk esetén az Azure Platform minimális követelményeinek ellenőrzése alapvető lépéseit ismerteti.

Technikai támogatás külső nva-k és azok az Azure platformmal való integráció az NVA gyártójától. 

> [!NOTE]
> Ha a kapcsolat vagy útválasztási problémát, amely magában foglalja egy nva-t, akkor [, forduljon az nva-t a](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) közvetlenül.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>NVA gyártójával hibaelhárítási ellenőrzőlista

- Szoftverfrissítések NVA virtuális gép szoftverek
- Szolgáltatásfiók beállítási módja és funkciókészlete
- Felhasználó által megadott útvonalak (udr-EK), amely a forgalmat az NVA virtuális hálózati alhálózatokon
- A virtuális alhálózatokkal, a forgalmat az nva-t az udr-EK
- Útválasztási táblázatok és szabályok (például az a NIC2 NIC1) az nva-n belül
- Az NVA hálózati adapterek ellenőrzése és forgalmat fogadó nyomon követése
- A Standard Termékváltozat és a nyilvános IP-cím használata esetén kell létrehozni egy NSG-t és egy explicit szabály engedélyezi a forgalmat az nva-n lesznek irányítva.

## <a name="basic-troubleshooting-steps"></a>Alapszintű hibaelhárítási lépéseket

- Az alapvető konfigurációjának ellenőrzése
- Ellenőrizze az nva-n teljesítménye
- A speciális hálózati hibáinak elhárítása

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Az nva-k az Azure-ban a minimális konfigurációs követelményeinek ellenőrzése

Minden NVA rendelkezik alapszintű konfigurációs követelményeknek megfelelő működéséhez az Azure-ban. Az alábbi szakasz lépései ezen alapszintű konfigurációk ellenőrzése. További információ [, forduljon az nva-t a](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Ellenőrizze, hogy az IP-továbbítás engedélyezve van-e az nva-n**

Az Azure Portal használata

1.  Keresse meg az NVA erőforrás a [az Azure portal](https://portal.azure.com), hálózatkezelés, majd válassza ki és a hálózati adaptert.
2.  A hálózati adapter oldalon válassza ki az IP-konfigurációt.
3.  Győződjön meg arról, hogy az IP-továbbítás engedélyezve van-e.

A PowerShell használata

1. Nyissa meg a Powershellt, és jelentkezzen be Azure-fiókjába.
2. Az alábbi parancsot (a zárójeles értékeket cserélje le az adatokat):

        Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. Ellenőrizze a **EnableIPForwarding** tulajdonság.
 
4. Ha IP-továbbítás nem engedélyezett, a következő parancsokat az engedélyezéshez:

          $nic2 = Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
          $nic2.EnableIPForwarding = 1
          Set-AzureRmNetworkInterface -NetworkInterface $nic2
          Execute: $nic2 #and check for an expected output:
          EnableIPForwarding   : True
          NetworkSecurityGroup : null

**Standard Termékváltozat Pubilc IP-cím használata esetén ellenőrizze az NSG** a Standard Termékváltozat és a nyilvános IP-cím használata esetén kell létrehozni egy NSG-t és a egy explicit szabály engedélyezi a forgalmat az nva-n való.

**Ellenőrizze, hogy a forgalom átirányítható az nva-n**

1. A [az Azure portal](https://portal.azure.com), nyissa meg **Network Watcher**válassza **következő Ugrás**.
2. Adjon meg egy virtuális Gépet, amely konfigurálva van a forgalom átirányítása az nva-t, és a egy cél IP-cím, amelynél a következő ugrás megjelenítéséhez. 
3. Ha az NVA nem szerepel a **következő Ugrás**, ellenőrizze, és frissítse az Azure útválasztási táblázatban.

**Ellenőrizze-e a forgalmat az nva-n elérhetővé**

1.  A [az Azure portal](https://portal.azure.com), nyissa meg **Network Watcher**, majd válassza ki **IP Flow ellenőrzése**. 
2.  Adja meg a virtuális gép és az IP-címét az nva-t, és ellenőrizze a minden olyan hálózati biztonsági csoportok (NSG) blokkolja a forgalmat.
3.  Ha van egy NSG-szabályt, amely blokkolja a forgalmat, keresse meg az NSG-t a **érvényben lévő biztonsági** szabályokat, és frissítse, hogy a forgalom áthaladását. Ezután futtassa **IP Flow ellenőrzése** újra, és **kapcsolódási hibák elhárítása** TCP által kezdeményezett kommunikáció a virtuális gép a belső vagy külső IP-cím tesztelése.

**Ellenőrizze-e az nva-t és a virtuális gépek figyelik-e a várt forgalomhoz**

1.  RDP vagy SSH használatával csatlakozhat az nva-t, és futtassa a következő parancsot:

    Windows esetén:

        netstat -an

    Linux esetén:

        netstat -an | grep -i listen
2.  Ha nem látja a TCP-portot, amelyet az nva-t szoftver, amely szerepel az eredmények konfigurálnia kell az alkalmazást az nva-t és a virtuális gép figyelését, valamint válaszoljanak a forgalomra, amely eléri a ezeket a portokat. [Segítségért forduljon az NVA szállító, igény szerint](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Ellenőrizze az nva-n teljesítménye

### <a name="validate-vm-cpu"></a>Ellenőrizze a virtuális gép Processzor

Ha a processzorhasználat megközelíti a 100 %-os lekérdezi, hálózati csomag csepp érintő problémát tapasztalhat. A virtuális gép jelentések átlagos CPU-számára egy adott időtartam, az Azure Portalon. Során CPU ugrásszerű vizsgálja meg a Vendég virtuális gép okozza a magas CPU folyamatokat, és csökkentheti, ha lehetséges. Méretezze át a virtuális Gépet egy nagyobb SKU-méret, vagy a virtuálisgép-méretezési csoport esetében, a példányszám növelése, vagy állítsa be az automatikus méretezést a CPU-használat is rendelkezhetnek. Ezeket a problémákat a [segítségért forduljon az NVA szállítói](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), igény szerint.

### <a name="validate-vm-network-statistics"></a>Virtuálisgép-hálózat statisztika ellenőrzése 

Ha a Virtuálisgép-hálózat hirtelen megugró kihasználtság, vagy megjeleníti a magas kihasználtságú időszakok is magasabb átviteli sebesség képességek beszerzése a virtuális gép Termékváltozata méretének növeléséhez. Is engedélyezve van a gyorsított hálózatkezelés sablonkonfigurációkat telepítse újra a virtuális Gépet. Ellenőrizze, hogy támogatja-e az nva-t a gyorsított hálózati funkció [segítségért forduljon az NVA szállítói](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), igény szerint.

## <a name="advanced-network-administrator-troubleshooting"></a>A speciális hálózati rendszergazda hibáinak elhárítása

### <a name="capture-network-trace"></a>Hálózati nyomkövetés rögzítése
A forrásoldali virtuális gép egyidejű hálózati nyomkövetést, az nva-n és a cél virtuális gép futtatása közben rögzítése **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** vagy **Nmap**, majd állítsa le a nyomkövetést.

1. Egyidejű hálózati nyomkövetést rögzítéséhez, futtassa a következő parancsot:

    Windows esetén:

        netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

    Linux esetén:

        sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Használat **PsPing** vagy **Nmap** a forrás virtuális Gépet a cél virtuális gép (például: `PsPing 10.0.0.4:80` vagy `Nmap -p 80 10.0.0.4`).

3. Nyissa meg a hálózati nyomkövetést a cél virtuális gép használatával [Hálózatfigyelő](https://www.microsoft.com/download/details.aspx?id=4865) vagy a tcpdump. Alkalmazzon megjelenítési szűrőt futtatta, a forrás virtuális gép IP-címhez **PsPing** vagy **Nmap** , mint például `IPv4.address==10.0.0.4 (Windows netmon)` vagy `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Nyomok elemzése

Ha nem érkeznek bejövő csomagok a háttérbeli virtuális gép nyomkövetési látja, valószínűleg egy NSG-t, vagy UDR zavarja a folyamatot, vagy az NVA-útválasztási táblázatok helytelenek.

Ha a csomagok beérkeznek, de nincs válasz, akkor a virtuálisgép-alkalmazáshoz vagy a tűzfalhoz kapcsolódó problémáról lehet szó. Ezeket a problémákat a [forduljon segítségért az NVA szállító, igény szerint](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

