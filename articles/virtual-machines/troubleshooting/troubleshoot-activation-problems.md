---
title: A Windows rendszerű virtuális gépek aktiválásával kapcsolatos problémák elhárítása az Azure-ban | Microsoft Docs
description: Az Azure-beli Windows rendszerű virtuális gépek aktiválási problémáinak kijavításához szükséges hibaelhárítási lépéseket ismerteti
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 820fe016d630e63660368c9065acbcda9b514921
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858608"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Windows rendszerű virtuális gépek Azure-ban való aktiválási problémáinak hibaelhárítása

Ha az Azure Windows rendszerű virtuális gép (VM) egyéni rendszerképből való aktiválása során problémát tapasztal, a probléma elhárításához használhatja a dokumentumban található információkat. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Az Azure KMS-végpontok működésének megismerése az Azure-beli virtuális gépek Windows termékaktiválása kapcsán

Az Azure különböző végpontokat használ a KMS (kulcskezelő szolgáltatások) aktiválásához attól függően, hogy melyik felhő-régión belül található a virtuális gép. A hibaelhárítási útmutató használatakor használja az adott régióra érvényes KMS-végpontot.

* Azure nyilvános felhő régiói: kms.core.windows.net:1688
* Azure China 21Vianet nemzeti Felhőbeli régiók: kms.core.chinacloudapi.cn:1688
* Azure Germany – nemzeti Felhőbeli régiók: kms.core.cloudapi.de:1688
* Azure US Gov nemzeti Felhőbeli régiók: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Hibajelenség

Amikor egy Azure Windows rendszerű virtuális gépet próbál aktiválni, a következőhöz hasonló hibaüzenet jelenik meg:

**Hiba: a 0xC004F074 LicensingService jelentett, hogy a számítógép nem aktiválható. Nem lehet kapcsolatot létesíteni a kulcs ManagementService (KMS). További információért tekintse meg az alkalmazás eseménynaplóját.**

## <a name="cause"></a>Ok

Az Azure-beli virtuális gépek aktiválási problémái akkor jelentkeznek, ha a Windows rendszerű virtuális gép nem a megfelelő KMS-ügyféltelepítési kulccsal lett konfigurálva, vagy a Windows rendszerű virtuális gép nem tud csatlakozni az Azure KMS-szolgáltatáshoz (kms.core.windows.net, 1688-as port). 

## <a name="solution"></a>Megoldás

>[!NOTE]
>Ha helyek közötti VPN-t és kényszerített bújtatást használ, tekintse meg az [Egyéni Azure-útvonalak használata a KMS-Aktiválás kényszerített bújtatással való engedélyezését](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling)ismertető témakört. 
>
>Ha a ExpressRoute-t használja, és rendelkezik a közzétett alapértelmezett útvonallal, tekintse meg a [ExpressRoute-áramkörökhöz csatlakozó virtuális hálózatok internetkapcsolatának blokkolása](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)című témakört.

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>1. lépés a megfelelő KMS-ügyfél telepítési kulcsának konfigurálása

Az egyéni rendszerképből létrehozott virtuális gép esetében konfigurálnia kell a megfelelő KMS-ügyfél telepítési kulcsát a virtuális géphez.

1. Futtassa az **slmgr. vbs/dlv** egy rendszergazda jogú parancssorban. Tekintse meg a Leírás értékét a kimenetben, majd döntse el, hogy a kiskereskedelmi (kereskedelmi csatornán) vagy a Volume (VOLUME_KMSCLIENT) licenc adathordozóján lett létrehozva:
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Ha az **slmgr.vbs /dlv** a KISKERESKEDELMI csatornát jeleníti meg, futtassa az alábbi parancsokat a [KMS-ügyféltelepítési kulcs](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) a használt Windows Server-verzióhoz való beállításához, majd kényszerítse az újbóli aktiválást: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    A Windows Server 2016 Datacenter esetében például a következő parancsot kell futtatnia:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2. lépés a virtuális gép és az Azure KMS szolgáltatás közötti kapcsolat ellenőrzése

1. Töltse le és csomagolja ki a [PSping](https://docs.microsoft.com/sysinternals/downloads/psping) eszközt a virtuális gép egy olyan helyi mappájába, amely nem aktiválódik. 

2. Lépjen a Start menüre, keresse meg a Windows PowerShellt, kattintson a jobb gombbal a Windows PowerShell elemre, majd válassza a Futtatás rendszergazdaként parancsot.

3. Győződjön meg arról, hogy a virtuális gép úgy van konfigurálva, hogy a megfelelő Azure KMS-kiszolgálót használja. Ehhez futtassa az alábbi parancsot:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    A parancsnak vissza kell térnie: a kulcskezelő szolgáltatás kms.core.windows.net:1688 beállítása sikeres.

4. A Psping használatával ellenőrizze, hogy csatlakozik-e a KMS-kiszolgálóhoz. Lépjen abba a mappába, amelybe kibontotta a letöltött Pstools.zip fájlt, majd futtassa a következőt:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   A kimenet második – utolsó sorában ellenőrizze, hogy látható-e: elküldött = 4, fogadott = 4, elveszett = 0 (0% Loss).

   Ha az elveszett érték nagyobb nullánál (nulla), a virtuális gépnek nincs kapcsolata a KMS-kiszolgálóval. Ebben az esetben, ha a virtuális gép egy virtuális hálózaton található, és van megadva egyéni DNS-kiszolgáló, meg kell győződnie arról, hogy a DNS-kiszolgáló képes a kms.core.windows.net feloldására. Vagy módosítsa a DNS-kiszolgálót úgy, hogy az a kms.core.windows.net oldja fel.

   Figyelje meg, hogy ha eltávolít egy virtuális hálózatról az összes DNS-kiszolgálót, a virtuális gépek az Azure belső DNS-szolgáltatását használják. Ez a szolgáltatás képes megoldani a kms.core.windows.net.
  
    Győződjön meg arról is, hogy a virtuális gépen a tűzfal nem blokkolja a kimenő hálózati forgalmat a KMS-végpontra, a 1688-as portot.

5. Ellenőrizze, hogy a következő [ugrást Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) használja-e, hogy az adott virtuális gépről jövő ugrás típusa a KMS.Core.Windows.net cél IP-23.102.135.246 (a esetében) vagy a megfelelő KMS-végpont IP-címére vonatkozik-e az **Internet**.  Ha az eredmény VirtualAppliance vagy VirtualNetworkGateway, akkor valószínű, hogy egy alapértelmezett útvonal létezik.  Forduljon a hálózati rendszergazdához, és működjön együtt velük a megfelelő műveletek meghatározásához.  Ez lehet egy [Egyéni útvonal](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) , ha a megoldás konzisztens a szervezet házirendjeivel.

6. Miután ellenőrizte, hogy sikeresen csatlakozik a kms.core.windows.net helyhez, futtassa a következő parancsot a rendszergazda jogú Windows PowerShell-parancssorban. Ez a parancs több alkalommal megpróbálja elvégezni az aktiválást.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    A sikeres aktiválás az alábbihoz hasonló információkat ad vissza:
    
    **Windows (R), pl. serverdatacenter Edition aktiválása (12345678-1234-1234-1234-12345678)...  A termék sikeresen aktiválva.**

## <a name="faq"></a>GYIK 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Létrehoztam a Windows Server 2016-et az Azure Marketplace-ről. Be kell állítania a KMS-kulcsot a Windows Server 2016 aktiválásához? 

 
Nem. Az Azure Marketplace-en található rendszerkép már konfigurálva van a megfelelő KMS-ügyfél telepítési kulcsával. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>A Windows-aktiválás ugyanúgy működik, függetlenül attól, hogy a virtuális gép Azure Hybrid Use Benefitt (HUB) használ-e? 

 
Igen. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Mi történik, ha a Windows-aktiválási időszak lejár? 

 
Ha a türelmi időszak lejárt, és a Windows még nem aktiválódik, a Windows Server 2008 R2 és a Windows újabb verziói további értesítéseket jelenítenek meg az aktiválással kapcsolatban. Az asztali háttérkép feketén marad, és a Windows Update csak a biztonsági és kritikus frissítéseket telepíti, de nem kötelező frissítéseket is. Tekintse meg az értesítések szakaszt a [licencelési feltételek](https://technet.microsoft.com/library/ff793403.aspx) lap alján.   

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
