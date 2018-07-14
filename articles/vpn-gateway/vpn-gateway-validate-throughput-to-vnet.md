---
title: A Microsoft Azure Virtual Network VPN teljesítményének érvényesítése |} A Microsoft Docs
description: Ez a dokumentum az a célja, hogy segítséget nyújtson a felhasználó, ellenőrizze a hálózati átviteli sebesség a helyszíni erőforrások Azure virtuális gépeken.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 7e6b3e7496c4a063156ff3b8feae1f5096efe55f
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035618"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Hogyan lehet egy virtuális hálózati VPN teljesítményének érvényesítése

VPN gateway-kapcsolat lehetővé teszi biztonságos kapcsolat, létesítmények közötti kapcsolatokat a virtuális hálózat Azure-ban és a helyszíni informatikai infrastruktúrát.

Ez a cikk bemutatja, hogyan érvényesítheti a hálózati átviteli sebesség a helyszíni erőforrások Azure virtuális gépeken (VM). Azt is nyújt hibaelhárítási útmutatót.

>[!NOTE]
>Ebből a cikkből gyakori hibák azonosítása és kijavítása érdekében. Ha nem sikerül a probléma megoldásához az alábbi információk alapján [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Áttekintés

A VPN gateway-kapcsolat a következő összetevőkből áll:

- A helyszíni VPN-eszköz (listájának megtekintéséhez [ellenőrzött VPN-eszközök)](vpn-gateway-about-vpn-devices.md#devicetable).
- Nyilvános interneten
- Az Azure VPN gateway
- Azure VM

Az alábbi ábrán a logikai kapcsolat a helyszíni hálózat egy Azure virtuális hálózathoz VPN-kapcsolaton keresztül.

![Kapcsolat az ügyfél logikaihálózat MSFT hálózathoz VPN használatával](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Kiszámítása a maximális várt bejövő/kimenő forgalom

1.  Az alkalmazás eredeti átviteli követelmények meghatározása.
2.  Az Azure VPN gateway átviteli korlátainak határozza meg. Segítségre van szüksége, a "Termékváltozat és a VPN-típus szerint az összesített átviteli" című szakaszában talál [tervezéssel és kialakítással VPN-átjáró](vpn-gateway-plan-design.md).
3.  Határozza meg a [Azure virtuális gép átviteli sebességéről szóló útmutatót](../virtual-machines/virtual-machines-windows-sizes.md) a Virtuálisgép-mérethez.
4.  Határozza meg, hogy az internetszolgáltató (ISP) sávszélességet.
5.  A várt – minimális sávszélesség (virtuális gép, átjáró, ISP) az átviteli sebességet számítja ki * 0,8 értéket.

Ha a számított átviteli sebesség nem felel meg az alkalmazás eredeti átviteli sebességet megkövetelő, növelheti a sávszélességet, hogy azonosította az eseményt szűk erőforrás szeretné. Tekintse meg az Azure VPN Gateway átméretezéséhez [átjárók Termékváltozatainak módosítása](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Virtuális gép átméretezése, lásd: [virtuális gép átméretezése](../virtual-machines/virtual-machines-windows-resize-vm.md). Ha nem várt internetes sávszélességet, akkor is érdemes lépjen kapcsolatba az Internetszolgáltatóval.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Teljesítménynövelő eszközök használatával hálózati átviteli sebesség ellenőrzéséhez

Ellenőrzés során nem csúcsidőre hajtható végre, mint a VPN-alagút átviteli színtelítettség tesztelés során nem biztosít pontos eredményeket.

Ebben a tesztben használjuk eszköze iPerf, amely Windows és Linux rendszereken is működik, és ügyfél- és módot támogat. 3 GB/s a Windows virtuális gépek korlátozva.

Ez az eszköz nem végez lemezre írási/olvasási műveleteket. Kizárólag az önállóan létrehozott TCP-forgalom az egyik hoz létre. Statisztika, amely méri az ügyfél és kiszolgáló-csomópont között rendelkezésre álló sávszélesség Kísérletezési alapján létrehozott. Ha két csomópont közötti teszteli, egy, a kiszolgáló és a többi ügyfél működik. Ha ez a vizsgálat befejeződött, azt javasoljuk, hogy megfordította a szerepkörök mindkét feltöltési teszteléséhez, és töltse le a két csomópontra átviteli sebesség.

### <a name="download-iperf"></a>Töltse le a iPerf
Töltse le [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). További információkért lásd: [iPerf dokumentáció](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >A külső termékekhez, hogy ez a cikk ismerteti a Microsoft független a Microsofttól. A Microsoft nem vállal, vélelmezett vagy más módon, a teljesítményére és megbízhatóságára.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Futtatási iPerf (iperf3.exe)
1. Engedélyezze egy NSG/ACL-szabály engedélyezi a forgalmat (a nyilvános IP-cím tesztelése az Azure virtuális gépen).

2. A két csomópontra a tűzfal-kivétel az port 5001 engedélyezése.

    **Windows:** rendszergazdaként a következő parancsot:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Eltávolítja a szabályt, ha teszteli az elkészült, futtassa a következő parancsot:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Azure-beli Linuxos:** Azure Linux-rendszerképeket megengedő tűzfalak vannak. Ha egy alkalmazás figyeljen egy portot, keresztül engedélyezi a forgalmat. Egyéni rendszerképek védett explicit módon megnyitott portok szükség lehet. Gyakori Linux operációsrendszer-réteg tűzfalak tartalmaznak `iptables`, `ufw`, vagy `firewalld`.

3. A kiszolgáló-csomóponton módosítsa a könyvtárat, ahol iperf3.exe ki kell olvasni. Ezután iPerf kiszolgáló módban fut, és beállíthatja úgy, hogy a port 5001, az alábbi parancsok figyelése:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Az ügyfél-csomóponton módosítsa a könyvtárat, ahol iperf eszközt ki kell olvasni, és ezután futtassa a következő parancsot:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Az ügyfél a kiszolgálóval 5001-port forgalmát van hogy 30 másodpercig. A jelző "-P", amely azt jelzi, hogy a kiszolgáló-csomópontot az egyidejű kapcsolatok 32 használjuk.

    Az alábbi képernyőn látható a jelen példa kimenetében:

    ![Kimenet](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (NEM KÖTELEZŐ) Ha szeretné megőrizni a vizsgálati eredmények, a következő parancs futtatásával:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Az előző lépések végrehajtását követően hajtható végre ugyanazokat a lépéseket, a fordított irányú, szerepkörökhöz, hogy a kiszolgáló-csomópont lesz az ügyfél és fordítva.

## <a name="address-slow-file-copy-issues"></a>Lassú fájl másolási hibáinak megoldásához
Lassú fájl kiugrások, amikor a Windows Explorer használatával vagy áthúzását keresztül egy RDP-munkamenetet tapasztalhat. Ez a probléma általában egyikét vagy mindkettőt, az alábbi tényezők miatt van:

- Fájl másolása alkalmazások, például a Windows Explorerben és RDP-t, ne használjon több szálon, amikor fájlokat másol. A jobb teljesítmény érdekében használjon egy több szálon futó fájl másolása alkalmazás például [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) 16 vagy 32 szálak használatával a fájlok másolásához. A szál száma fájlmásolási funkciója Richcopy módosításához kattintson **művelet** > **beállítások másolása** > **fájlmásolás**.<br><br>
![Lassú fájl másolása kapcsolatos problémák](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Nincs elegendő VM lemez olvasási/írási sebessége. További információkért lásd: [Azure Storage hibaelhárítási](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Helyszíni eszköz külső irányuló felületén
Ha a helyszíni VPN-eszköznek az Internet felé néző IP-cím szerepel a [helyi hálózati](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definíciót az Azure-ban, tapasztalhat meggátoló ahhoz, hogy meg bontja a kapcsolatot a VPN-, időnként fel, vagy a teljesítménnyel kapcsolatos problémák.

## <a name="checking-latency"></a>Késés ellenőrzése
A Microsoft Azure Edge-eszközön nyomkövetési tracert használatával határozza meg, hogy vannak-e bármilyen meghaladja a 100 ms közötti útválasztók ugrásainak késések.

A helyszíni hálózatról futtatni *tracert* , a virtuális IP-cím az Azure-átjáró vagy a virtuális gép. Ha csak * ad vissza, tudja elérte az Azure Edge-ben. Amikor megjelenik, amely tartalmazza az adott vissza "MSN" DNS-nevek, ismernie elérte a Microsoft gerinchálózatán keresztül.<br><br>
![Késés ellenőrzése](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>További lépések
További információ vagy a Súgó tekintse meg az alábbi hivatkozásokat:

- [Azure-beli virtuális gépek hálózati teljesítmény optimalizálása](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [A Microsoft ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
