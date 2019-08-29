---
title: A Windows rendszerű virtuális gépek aktiválásával kapcsolatos problémák elhárítása az Azure-ban | Microsoft Docs
description: Az Azure-beli Windows rendszerű virtuális gépek aktiválási problémáinak kijavításához szükséges hibaelhárítási lépéseket ismerteti
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: d403292a7f7ab1080f4270a420c23353eda5fd71
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090043"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Az Azure Windows rendszerű virtuális gépek aktiválási problémáinak elhárítása

Ha az Azure Windows rendszerű virtuális gép (VM) egyéni rendszerképből való aktiválása során problémát tapasztal, a probléma elhárításához használhatja a dokumentumban található információkat. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Azure KMS-végpontok ismertetése az Azure-beli Windows termékaktiválás Virtual Machines

Az Azure különböző végpontokat használ a KMS-aktiváláshoz attól függően, hogy melyik Felhőbeli régióban található a virtuális gép. A hibaelhárítási útmutató használatakor használja az adott régióra érvényes KMS-végpontot.

* Azure public cloud regions: kms.core.windows.net:1688
* Azure China 21Vianet national cloud regions: kms.core.chinacloudapi.cn:1688
* Azure Germany – nemzeti Felhőbeli régiók: kms.core.cloudapi.de:1688
* Azure US Gov nemzeti Felhőbeli régiók: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Jelenség

Amikor egy Azure Windows rendszerű virtuális gépet próbál aktiválni, a következőhöz hasonló hibaüzenet jelenik meg:

**Hiba: 0xC004F074 a szoftver LicensingService jelentett, hogy a számítógép nem aktiválható. Nem lehet kapcsolatot létesíteni a kulcs ManagementService (KMS). További információért tekintse meg az alkalmazás eseménynaplóját.**

## <a name="cause"></a>Ok

Az Azure-beli virtuális gépek aktiválásával kapcsolatos problémák általában akkor fordulnak elő, ha a Windows rendszerű virtuális gép nincs konfigurálva a megfelelő KMS-ügyfél telepítési kulcsával, vagy a Windows rendszerű virtuális gép kapcsolódási problémája van az Azure KMS szolgáltatással (kms.core.windows.net, 1688-as port 

## <a name="solution"></a>Megoldás

>[!NOTE]
>Ha helyek közötti VPN-t és kényszerített bújtatást használ, tekintse meg az [Egyéni Azure-útvonalak használata a KMS-Aktiválás kényszerített bújtatással](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx)való engedélyezését ismertető témakört. 
>
>Ha a ExpressRoute-t használja, és van egy alapértelmezett útvonala, akkor [Az Azure-beli virtuális gép nem tud aktiválni a ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx)-et.

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>1\. lépés a megfelelő KMS-ügyfél telepítési kulcsának konfigurálása

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

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2\. lépés a virtuális gép és az Azure KMS szolgáltatás közötti kapcsolat ellenőrzése

1. Töltse le és csomagolja ki a [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) eszközt a virtuális gép egy olyan helyi mappájába, amely nem aktiválódik. 

2. Lépjen a Start menüre, keresse meg a Windows PowerShellt, kattintson a jobb gombbal a Windows PowerShell elemre, majd válassza a Futtatás rendszergazdaként parancsot.

3. Győződjön meg arról, hogy a virtuális gép úgy van konfigurálva, hogy a megfelelő Azure KMS-kiszolgálót használja. Ehhez futtassa a következő parancsot:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    A parancsnak ehhez hasonlókat kell visszaadnia: A kulcskezelő szolgáltatás számítógépnév beállítása kms.core.windows.net:1688 sikeres.

4. A Psping használatával ellenőrizze, hogy van-e kapcsolat a KMS-kiszolgálóval. Lépjen abba a mappába, amelybe kibontotta a letöltött Pstools.zip fájlt, majd futtassa a következőt:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Győződjön meg arról, hogy a kimenet utolsó előtti sorában a következők láthatók: Elküldött = 4, fogadott = 4, elveszett = 0 (0% veszteség).

   Ha az elveszett érték nagyobb nullánál (nulla), a virtuális gépnek nincs kapcsolata a KMS-kiszolgálóval. Ebben az esetben, ha a virtuális gép egy virtuális hálózaton található, és van megadva egyéni DNS-kiszolgáló, meg kell győződnie arról, hogy a DNS-kiszolgáló képes a kms.core.windows.net feloldására. Vagy módosítsa a DNS-kiszolgálót úgy, hogy az a kms.core.windows.net oldja fel.

   Figyelje meg, hogy ha eltávolít egy virtuális hálózatról az összes DNS-kiszolgálót, a virtuális gépek az Azure belső DNS-szolgáltatását használják. Ez a szolgáltatás képes megoldani a kms.core.windows.net.
  
    Győződjön meg arról is, hogy a virtuális gépen a tűzfal nem blokkolja a kimenő hálózati forgalmat a KMS-végpontra, a 1688-as portot.

5. Miután meggyőződött a kms.core.windows.net való sikeres csatlakozásról, futtassa a következő parancsot az emelt szintű Windows PowerShell-parancssorban. Ez a parancs több alkalommal megpróbálja elvégezni az aktiválást.

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

 
Ha a türelmi időszak lejárt, és a Windows még nem aktiválódik, a Windows Server 2008 R2 és a Windows újabb verziói további értesítéseket jelenítenek meg az aktiválással kapcsolatban. Az asztali háttérkép feketén marad, és a Windows Update csak a biztonsági és kritikus frissítéseket telepíti, de nem kötelező frissítéseket is. Tekintse meg az értesítések szakaszt a licencelési [feltételek](https://technet.microsoft.com/library/ff793403.aspx) lap alján.   

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz a probléma gyors megoldása érdekében.
