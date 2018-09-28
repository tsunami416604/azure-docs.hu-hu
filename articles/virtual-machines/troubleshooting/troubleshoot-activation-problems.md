---
title: Az Azure-beli Windows virtuális gép aktiválással kapcsolatos problémák elhárítása |} A Microsoft Docs
description: A hibaelhárítás lépéseit ismerteti az Azure-beli Windows virtuális gép aktiválási problémák megoldása
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: e8ecdf1fffb51c0b8e9ce996307595a5444a64ee
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414098"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Azure Windows virtuális gép aktiválással kapcsolatos problémák elhárítása

Ha gondja van az Azure Windows virtuális gép (VM), amely egy egyéni lemezkép alapján jön aktiválásakor, ebben a dokumentumban található információk segítségével a hiba elhárításához. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>A Windows termékaktiválás az Azure Virtual Machines Azure KMS végpontok ismertetése
Az Azure különböző végpontok használ a KMS-aktiváláshoz függően a felhő régió, ahol a virtuális gép található. Ez a hibaelhárítási útmutató használjon a megfelelő KMS-végpontot, amely az Ön régiójában érvényes.

* Az Azure nyilvános felhő-régiók: kms.core.windows.net:1688
* Az Azure China országos felhőbeli régiók: kms.core.chinacloudapi.cn:1688
* Azure Germany-országos felhőbeli régiók: kms.core.cloudapi.de:1688
* Az Azure US Gov országos felhőbeli régiók: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Jelenség

Ha megpróbálja aktiválni az Azure Windows virtuális Gépekhez, akkor megjelenik egy hibaüzenet üzenet a következő mintához hasonló:

**Chyba: 0xC004F074 a szoftver LicensingService jelentette, hogy a számítógép nem lehet aktiválni. Érhető el. nem kulcs ManagementService (KMS). Tekintse át az alkalmazások eseménynaplójában, további információt.**

## <a name="cause"></a>Ok
Általában az Azure virtuális gép aktiválási hibák lépnek fel, ha a Windows virtuális gép nincs konfigurálva a megfelelő KMS-ügyfél telepítési kulcsának használatával, vagy a Windows virtuális gépen az Azure a KMS szolgáltatást (kms.core.windows.net, port 1668) a kapcsolati probléma. 

## <a name="solution"></a>Megoldás

>[!NOTE]
>Ha a site-to-site VPN használja, és a kényszerített bújtatás, lásd: [az Azure egyéni útvonalakat a KMS-aktiválás engedélyezése kényszerített bújtatás](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Ha az ExpressRoute használ, és rendelkezik az alapértelmezett útvonal közzétett, lásd: [Azure virtuális gép esetleg nem expressroute-on keresztül aktiválhatja](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>1. lépés a megfelelő KMS-ügyfél telepítési kulcsának konfigurálása (a Windows Server 2016 és Windows Server 2012 R2)

A virtuális Gépet, amely a Windows Server 2016 vagy Windows Server 2012 R2 egyéni rendszerkép alapján hozza létre konfigurálnia kell a megfelelő KMS-ügyfél telepítési kulcsának a virtuális gép számára.

Ez a lépés nem vonatkozik a Windows 2012 vagy Windows 2008 R2. Az Automation virtuális gép aktiválása (AVMA) funkció, amely csak a Windows Server 2016 és Windows Server 2012 R2 által támogatott használ.

1. Futtatás **slmgr.vbs/dlv** egy rendszergazda jogú parancssorba. Ellenőrizze a kimenetben a leírás értékét, és ellenőrizze, hogy létrehozták a kiskereskedelmi (KISKERESKEDELMI csatorna) vagy (VOLUME_KMSCLIENT) mennyiségi licencelési adathordozóról:
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Ha **slmgr.vbs/dlv** jeleníti meg a KISKERESKEDELMI csatorna, futtassa az alábbi parancsokat a [KMS-ügyfél telepítési kulcsának](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) Windows Server verziójának használ, és hogy az aktiválási ismételje meg: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Ha például a Windows Server 2016 Datacenter futtatná a következő parancsot:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2. lépés a virtuális gép és az Azure a KMS szolgáltatás közötti kapcsolat ellenőrzése

1. Töltse le és csomagolja ki a [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) egy helyi mappába a virtuális gép, amely nem aktiválja az eszköz. 

2. Ugrás a kezdő, keressen a Windows PowerShell, kattintson a jobb gombbal a Windows PowerShell, és válassza a Futtatás rendszergazdaként.

3. Győződjön meg arról, hogy a virtuális gép a megfelelő Azure KMS-kiszolgáló használatára van konfigurálva. Ehhez futtassa a következő parancsot:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    A parancs kell visszaadnia: kulcskezelő szolgáltatás gépnév sikeresen beállítva kms.core.windows.net:1688.

4. Győződjön meg arról, hogy a KMS-kiszolgálóval való kapcsolat Psping használatával. Váltson arra a mappára, ahová kibontotta a Pstools.zip letöltése, és futtassa a következő:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Az utolsó előtti másodperc sorban a kimenet, győződjön meg arról, hogy látja: küldött = 4, a fogadott = 4, elveszett = 0 (0 %-os adatveszteség).

  Ha elveszett nagyobb, mint 0 (nulla), a virtuális gép nem rendelkezik kapcsolattal a KMS-kiszolgálóra. Ebben a helyzetben a virtuális gép egy virtuális hálózaton van, és rendelkezik egy egyéni DNS-kiszolgáló, meg kell győződnie arról, hogy a DNS-kiszolgáló el tudja kms.core.windows.net megoldásához. Vagy a DNS-kiszolgáló módosítsa, hogy kms.core.windows.net megoldásához.

  Figyelje meg, hogy ha eltávolítja az összes DNS-kiszolgáló egy virtuális hálózatot, virtuális gépek az Azure belső DNS-szolgáltatás használhat. Ez a szolgáltatás fel tudja oldani kms.core.windows.net.
  
Emellett győződjön meg arról, hogy a Vendég tűzfala nincs konfigurálva, hogy megakadályozza a aktiválási kísérletek.

5. Miután kms.core.windows.net sikeres kapcsolat ellenőrzéséhez futtassa a következő parancsot, hogy emelt szintű Windows PowerShell-parancssorba. Ez a parancs megpróbál aktiválási több alkalommal.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Sikeres aktiválás ad vissza információkat, a következőhöz:

**Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678) aktiválása... A termék aktiválása sikeres.**

## <a name="faq"></a>GYIK 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>A Windows Server 2016-ban létrehozott Azure Marketplace-ről. Kell konfigurálni a KMS-kulcs aktiválásához a Windows Server 2016? 
 
Nem. A lemezképet az Azure Marketplace-en a megfelelő KMS ügyfél telepítési kulcsának már konfigurálva van. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Nem Windows-aktiválás ugyanúgy működnek, függetlenül attól, ha a virtuális gép által használt Azure hibrid használati Benefit (HUB), vagy sem? 
 
Igen. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Mi történik, ha a Windows aktiválási időszak lejár? 
 
Amikor a türelmi időszak lejárt, és a Windows még nincs aktiválva, Windows Server 2008 R2 és a Windows újabb verziói jelennek meg aktiválásával kapcsolatos további értesítések. Az asztali háttérkép fekete marad, és a Windows Update biztonsági és kritikus frissítések csak, de nem választható frissítések telepíti. Tekintse meg az értesítések szakasz alján a [licencelési feltételek](http://technet.microsoft.com/library/ff793403.aspx) lapot.   

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
