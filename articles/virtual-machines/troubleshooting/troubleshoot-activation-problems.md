---
title: A Windows virtuális gépek aktiválási problémáinak elhárítása az Azure-ban| Microsoft dokumentumok
description: A Windows virtuálisgépek aktiválásával kapcsolatos problémák Azure-beli javításának elhárítási lépései
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
ms.openlocfilehash: 5c84588290ce769b556002469b6a11c6950bb878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476552"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Windows rendszerű virtuális gépek Azure-ban való aktiválási problémáinak hibaelhárítása

Ha az egyéni lemezképből létrehozott Azure Windows virtuális gép (VM) aktiválásakor problémája van, a jelen dokumentumban megadott információk segítségével elháríthatja a problémát. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Az Azure KMS-végpontok működésének megismerése az Azure-beli virtuális gépek Windows termékaktiválása kapcsán

Az Azure különböző végpontokat használ a KMS (Key Management Services) aktiválásához attól függően, hogy a virtuális gép a felhőrégióban található-e. A hibaelhárítási útmutató használatakor használja a megfelelő KMS-végpontot, amely a régióra vonatkozik.

* Az Azure nyilvános felhőrégiói: kms.core.windows.net:1688
* Az Azure China 21Vianet nemzeti felhőrégiói: kms.core.chinacloudapi.cn:1688
* Az Azure Germany nemzeti felhőrégiói: kms.core.cloudapi.de:1688
* Az Azure US Gov nemzeti felhőrégiói: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Hibajelenség

Amikor megpróbál aktiválni egy Azure Windows virtuális gép, hibaüzenet jelenik meg hasonlít a következő minta:

**Hiba: 0xC004F074 A Software LicensingService arról számolt be, hogy a számítógépet nem lehetett aktiválni. Nem lehet kapcsolatba lépni a kulcskezelő szolgáltatással (KMS). További információt az alkalmazás eseménynaplójában talál.**

## <a name="cause"></a>Ok

Az Azure-beli virtuális gépek aktiválási problémái akkor jelentkeznek, ha a Windows rendszerű virtuális gép nem a megfelelő KMS-ügyféltelepítési kulccsal lett konfigurálva, vagy a Windows rendszerű virtuális gép nem tud csatlakozni az Azure KMS-szolgáltatáshoz (kms.core.windows.net, 1688-as port). 

## <a name="solution"></a>Megoldás

>[!NOTE]
>Ha helyek közötti VPN-t és kényszerített bújtatást használ, olvassa el az [Azure egyéni útvonalak használata a KMS-aktiválás kötelező bújtatással történő engedélyezéséhez.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>Ha ExpressRoute-ot használ, és közzétett egy alapértelmezett útvonalat, olvassa el a [Letiltható internetkapcsolat expressroute-áramkörökhöz kapcsolódó virtuális hálózatokkal való blokkolása?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>1. lépés A megfelelő KMS-ügyfélbeállítási kulcs konfigurálása

Az egyéni lemezképből létrehozott virtuális gép esetében konfigurálnia kell a megfelelő KMS-ügyfélbeállítási kulcsot a virtuális géphez.

1. Futtassa **az slmgr.vbs /dlv** parancsot egy rendszergazda jogú parancssorban. Ellenőrizze a Leírás értéket a kimenetben, majd állapítsa meg, hogy kiskereskedelmi (kiskereskedelmi csatorna) vagy mennyiségi (VOLUME_KMSCLIENT) licencadathordozóról hozták-e létre:
  

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

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2. lépés A virtuális gép és az Azure KMS szolgáltatás közötti kapcsolat ellenőrzése

1. Töltse le és bontsa ki a [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) eszközt a virtuális gép egy helyi mappájába, amely nem aktiválható. 

2. Nyissa meg a kezdőképernyőt, keressen a Windows PowerShellen, kattintson a jobb gombbal a Windows PowerShell elemre, és válassza a Futtatás rendszergazdaként parancsot.

3. Győződjön meg arról, hogy a virtuális gép úgy van konfigurálva, hogy a megfelelő Azure KMS-kiszolgálót használja. Ehhez futtassa az alábbi parancsot:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    A parancsnak a következőt kell visszaadnia: A kulcskezelő szolgáltatás számítógépének neve kms.core.windows.net:1688 sikeresen.

4. A Psping használatával ellenőrizze, hogy csatlakozik-e a KMS-kiszolgálóhoz. Lépjen abba a mappába, amelybe kibontotta a letöltött Pstools.zip fájlt, majd futtassa a következőt:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   A kimenet utolsó előtti sorában győződjön meg arról, hogy a következőket látja: Elküldve = 4, Beérkezett = 4, Elveszett = 0 (0% veszteség).

   Ha az Elveszett érték nagyobb, mint 0 (nulla), a virtuális gép nem rendelkezik kapcsolattal a KMS-kiszolgálóval. Ebben az esetben, ha a virtuális gép egy virtuális hálózatban van, és egyéni DNS-kiszolgáló van megadva, meg kell győződnie arról, hogy a DNS-kiszolgáló képes feloldani kms.core.windows.net. Vagy módosítsa a DNS-kiszolgálót olyanra, amely megoldja kms.core.windows.net.

   Figyelje meg, hogy ha eltávolítja az összes DNS-kiszolgálók egy virtuális hálózatról, a virtuális gépek az Azure belső DNS-szolgáltatás. Ez a szolgáltatás feloldhatja kms.core.windows.net.
  
    Győződjön meg arról is, hogy a kimenő hálózati forgalmat a KMS-végpont 1688 port nem blokkolja a tűzfal a virtuális gép.

5. Ellenőrizze a [Network Watcher Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) használatával, hogy a kérdéses virtuális gép következő ugrástípusa a cél IP 23.102.135.246 (kms.core.windows.net) vagy a megfelelő KMS-végpont IP-címe, amely a régióra vonatkozik, az **internet**.  Ha az eredmény VirtualAppliance vagy VirtualNetworkGateway, akkor valószínű, hogy létezik alapértelmezett útvonal.  Lépjen kapcsolatba a hálózati rendszergazdával, és működjön együtt velük a helyes lépések meghatározásához.  Ez lehet egy [egyéni útvonal,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) ha a megoldás összhangban van a szervezet szabályzataival.

6. Miután ellenőrizte, hogy sikeresen csatlakozik a kms.core.windows.net helyhez, futtassa a következő parancsot a rendszergazda jogú Windows PowerShell-parancssorban. Ez a parancs több alkalommal megpróbálja elvégezni az aktiválást.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    A sikeres aktiválás az alábbihoz hasonló információkat ad vissza:
    
    **Windows(R), ServerDatacenter kiadás aktiválása (12345678-1234-1234-1234-12345678) ...  A termék sikeresen aktiválva.**

## <a name="faq"></a>GYIK 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>A Windows Server 2016-ot az Azure Piactérről hoztam létre. Be kell konfigurálnom a KMS-kulcsot a Windows Server 2016 aktiválásához? 

 
Nem. Az Azure Marketplace-en a rendszerkép rendelkezik a megfelelő KMS-ügyfélbeállítási kulcs már konfigurálva van. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>A Windows aktiválása ugyanúgy működik, függetlenül attól, hogy a virtuális gép az Azure Hybrid Use Benefit (HUB) (HUB) vagy sem? 

 
Igen. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Mi történik, ha a Windows aktiválási időszaka lejár? 

 
Ha a türelmi időszak lejárt, és a Windows még mindig nincs aktiválva, a Windows Server 2008 R2 és a Windows újabb verziói további értesítéseket jelenítenek meg az aktiválásról. Az asztal háttérképe fekete marad, és a Windows Update csak biztonsági és kritikus frissítéseket telepít, a választható frissítéseket azonban nem. Lásd az Értesítések szakaszt a [Licencelési feltételek](https://technet.microsoft.com/library/ff793403.aspx) lap alján.   

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
