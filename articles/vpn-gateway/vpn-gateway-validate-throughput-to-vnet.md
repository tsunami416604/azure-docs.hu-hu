---
title: A Microsoft Azure virtuális hálózat VPN átviteli ellenőrzése |} Microsoft Docs
description: Ez a dokumentum célja a felhasználó a hálózati teljesítményt a helyszíni erőforrások az Azure virtuális gép ellenőrzése érdekében.
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
ms.openlocfilehash: 38ff1ee4c525d41e2a7446d5adc792c746504491
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754507"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Hogyan VPN átviteli virtuális hálózat ellenőrzése

VPN gateway-kapcsolattal lehetővé teszi a biztonságos létrehozásához, létesítmények közötti kapcsolat között a virtuális hálózat az Azure és a helyszíni informatikai infrastruktúrát.

Ez a cikk bemutatja, hogyan ellenőrizze a hálózati átviteli a helyszíni erőforrások az Azure virtuális gép (VM). Azt is nyújt hibaelhárítási útmutatót.

>[!NOTE]
>Ez a cikk célja gyakran előforduló problémák megoldásában. Ha nem sikerül, a probléma megoldásához a következő információk segítségével [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Áttekintés

A VPN gateway-kapcsolatot a következő összetevőket foglalja magában:

- A helyszíni VPN-eszköz (listájának megtekintéséhez [érvényesíteni a VPN-eszközök)](vpn-gateway-about-vpn-devices.md#devicetable).
- Nyilvános Internet
- Az Azure VPN gateway
- Azure VM

Az alábbi ábrán a logikai kapcsolat a helyszíni hálózat az Azure virtuális hálózat VPN-en keresztül.

![Logikai kapcsolat az ügyfél hálózati MSFT hálózathoz VPN-nel](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>A maximális várt belépés és kilépés kiszámítása

1.  Az alkalmazás eredeti átviteli követelmények meghatározása.
2.  Az Azure VPN gateway átviteli sebességének korlátai határozza meg. Az "SKU- és VPN-típus szerint összesítő átviteli" részében talál segítséget, [tervezése és kialakítása VPN-átjáró](vpn-gateway-plan-design.md).
3.  Határozza meg a [Azure virtuális gép átviteli útmutatást](../virtual-machines/virtual-machines-windows-sizes.md) a Virtuálisgép-mérethez.
4.  Határozza meg, hogy az internetszolgáltató (ISP) sávszélességet.
5.  A várt átviteli - (VM-átjárón internetszolgáltató) minimális sávszélesség kiszámításához * 0,8 értéket.

A számított teljesítményt nem felel meg az alkalmazás eredeti átviteli követelmények, ha a sávszélességet az azonosított a szűk erőforrás növelnie kell. Tekintse meg az Azure VPN Gateway átméretezéséhez [módosítása egy átjáró-Termékváltozat](vpn-gateway-about-vpn-gateway-settings.md#gwsku). A virtuális gépek átméretezéséhez lásd: [méretezze át a virtuális gépek](../virtual-machines/virtual-machines-windows-resize-vm.md). Ha nem várt internetes sávszélességet, is érdemes lehet lépjen kapcsolatba az Internetszolgáltatóval.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Hálózati átviteli teljesítmény eszközökkel ellenőrzése

Az ellenőrzés során nem csúcsidőre kell hajtható végre, mert VPN alagút átviteli telítettségét tesztelés során nem ad pontos eredményeket.

Az eszközt a teszteléshez használjuk iPerf, amely Windows és Linux rendszeren működik és ügyfél- és módok. 3 GB/s a Windows virtuális gépek korlátozva.

Ez az eszköz nem végez semmilyen lemez olvasási/írási műveletek. Kizárólag a saját TCP-forgalom több olyan end egyéb hoz létre. Akkor jönnek létre, kísérletezhet, és mérheti az ügyfél és kiszolgáló-csomópont között rendelkezésre álló sávszélesség statisztikát. Ha két csomópont között, egy működik, a kiszolgáló és az egyéb ügyfélként. Ha ez a vizsgálat befejeződött, azt javasoljuk, hogy megfordította a szerepkörök mindkét feltöltés teszteléséhez, és töltse le mindkét csomópont átviteli sebességet.

### <a name="download-iperf"></a>Töltse le a iPerf
Töltse le [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). További információkért lásd: [iPerf dokumentáció](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >A külső cikkben említett termékeket független, a Microsoft által. A Microsoft nem vállal, vélelmezett vagy a teljesítmény és megbízhatóságára.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Futtassa a iPerf (iperf3.exe)
1. Engedélyezze az NSG/ACL-szabályának, lehetővé téve a forgalom (a nyilvános IP-cím tesztelés Azure virtuális gépen).

2. Mindkét csomópontján olyan érvényes tűzfalkivétel port 5001 engedélyezése.

    **Windows:** rendszergazdaként futtassa a következő parancsot:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    A szabály eltávolításához tesztelése során befejeződött, a parancs futtatásához:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Az Azure Linux:** Azure Linux képek megengedő tűzfalak vannak. Ha egy alkalmazás egy portot figyel, keresztül engedélyezi a forgalmat. Egyéni képek, védett esetleg explicit módon megnyitott portok. Közös Linux operációsrendszer-réteg tűzfalak tartalmaznak `iptables`, `ufw`, vagy `firewalld`.

3. A kiszolgáló-csomóponton nyissa meg azt a könyvtárat, ahol iperf3.exe ki kell olvasni. Ezután iPerf kiszolgáló módban fut, és állítsa be úgy a 5001, az alábbi parancsok port figyelésére:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Az ügyfél-csomópontnak módosítsa a könyvtárat, ahol iperf eszköz kibontott, és futtassa a következő parancsot:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Az ügyfél 30 másodpercig van hogy forgalmat porton 5001 a kiszolgálóra. A jelző "-P" azt jelzi, hogy a kiszolgáló-csomópontra 32 egyidejűleg létesíthető kapcsolatok használjuk.

    A következő képernyő Ez a példa mutatja:

    ![Kimenet](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (VÁLASZTHATÓ) A vizsgálati eredmények megőrzése érdekében futtassa ezt a parancsot:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Az előző lépések végrehajtását követően hajtható végre a lépéseket a fordított irányú, szerepkörök, hogy a kiszolgáló-csomópont lesz az ügyfél és fordítva.

## <a name="address-slow-file-copy-issues"></a>Lassú fájl másolása problémák megoldása
Problémákat tapasztalhat a másolás, ha a Windows Intézővel vagy áthúzza egy RDP-kapcsolaton keresztül lassú fájlt. Ez a probléma általában nem két, a következő tényezőket:

- Fájl másolása alkalmazások, például a Windows Intézőt, és RDP, ne használja egyszerre használható szálak, amikor a fájlok másolása. A jobb teljesítmény érdekében használjon egy többszálas fájl másolása alkalmazás például [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) 16 vagy 32 szálat használ a fájlok másolása. A szál száma Richcopy fájlmásolási módosításához kattintson **művelet** > **beállításai** > **fájlmásolás**.<br><br>
![Lassú fájl másolása problémák](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Nincs elegendő méretű lemez olvasási/írási sebessége. További információkért lásd: [Azure Storage hibaelhárítása](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>A helyszíni eszközök külső irányuló felület
Ha a helyszíni VPN-eszköz Internet felé néző IP-cím szerepel a [helyi hálózati](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) -definícióban az Azure-ban problémákat tapasztalhat, nem tudja telepíteni a tegye fel a VPN-és szórványos bontja a kapcsolatot, vagy a teljesítményproblémák.

## <a name="checking-latency"></a>Késés ellenőrzése
A nyomkövetést a Microsoft Azure peremhálózati eszköz tracert segítségével határozhatja meg, ha bármely meghaladja a 100 ms közötti ugrások késések fordulnak.

A helyi hálózatról futtatni *tracert* a VIP az Azure-átjáró vagy a virtuális gép. Miután látja csak * lett visszaadva, akkor tudja az Azure biztonsági elérte. Amikor megjelenik, amely tartalmazza az adott vissza "MSN" DNS-nevek, ismernie elérte a Microsoft gerincét.<br><br>
![Késés ellenőrzése](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>További lépések
További információt vagy a help tekintse meg a következőket:

- [Az Azure virtuális gépek hálózati teljesítmény optimalizálása](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [A Microsoft támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
