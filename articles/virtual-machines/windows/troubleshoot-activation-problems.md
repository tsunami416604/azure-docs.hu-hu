---
title: "Az Azure-ban a Windows virtuális gép aktiválással kapcsolatos problémák elhárítása |} Microsoft Docs"
description: "A hibaelhárítás javaslatot tesz, ha a Windows virtuális gép aktiválási problémák elhárítása az Azure-ban"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: a6e0750e28c2d71721450d6ba84a1caa362c638b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Azure Windows virtuális gép aktiválással kapcsolatos problémák elhárítása

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ha gondja támad, amikor aktiválja a Azure Windows virtuális gép (VM), amely egy egyéni lemezképből jön létre, a jelen dokumentum információkat használhatja a probléma elhárítása érdekében. 

## <a name="symptom"></a>Jelenség

Az Azure Windows virtuális gép aktiválása megkísérlésekor hibaüzenetet kap üzenet a következő mintához hasonló:

**Hiba: 0xC004F074 a szoftver LicensingService jelentette, hogy a számítógép nem lehet aktiválni. Érhető el. nem kulcs ManagementService (KMS). További információt az alkalmazás eseménynaplóban talál.**

## <a name="cause"></a>Ok
Azure virtuális gép aktiválási problémák általában fordulhat elő, ha a Windows virtuális gép nem konfigurálható a megfelelő KMS-ügyfél telepítési kulcsot, vagy a Windows virtuális Gépet az Azure KMS szolgáltatáshoz (kms.core.windows.net, port 1668) kapcsolódási problémát észlelt. 

## <a name="solution"></a>Megoldás

>[!NOTE]
>Ha a telephelyek közötti VPN használ, és a kényszerített bújtatás, lásd: [használata Azure egyéni útvonalak KMS-aktiválás engedélyezéséhez a kényszerített bújtatás](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Ha ExpressRoute használ, és rendelkezik az alapértelmezett útvonal közzétett című [Azure virtuális gép esetleg nem indulnak majd keresztüli ExpressRoute](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>1. lépés konfigurálása a megfelelő KMS-ügyfél telepítési kulcsot (a Windows Server 2016 és a Windows Server 2012 R2)

A virtuális Gépet, amely a Windows Server 2016 vagy a Windows Server 2012 R2 egyéni lemezkép jön létre konfigurálnia kell a megfelelő KMS-ügyfél telepítési kulcsot a virtuális gép számára.

Ez a lépés nem vonatkozik a Windows 2012 vagy Windows 2008 R2. Az automatizálási virtuális gép aktiválása (AVMA) funkció, amely csak a Windows Server 2012 R2 és Windows Server 2016 által támogatott használ.

1. Futtatás **slmgr.vbs/dlv** egy rendszergazda jogú parancssorba. Ellenőrizze a kimenet a leírás értékét, és ellenőrizze, hogy létrehozták, kereskedelmi (KISKERESKEDELMI csatorna) vagy (VOLUME_KMSCLIENT) mennyiségi licencelési adathordozóról:
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Ha **slmgr.vbs/dlv** jeleníti meg a KISKERESKEDELMI csatorna, futtassa a következő parancsokat a [KMS-ügyfél telepítési kulcsának](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) használja a Windows Server-verzió, és annak kikényszerítéséhez, hogy meg újra az aktiválást: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Például Windows Server 2016 Datacenter, hogy futnak a következő parancsot:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2. lépés a virtuális gép és az Azure KMS szolgáltatás közötti kapcsolat ellenőrzése

1. Töltse le, és bontsa ki a [Psping](http:/technet.microsoft.com/en-us/sysinternals/jj729731.aspx) eszköz egy helyi mappába a virtuális gépen, amely nem aktiválható. 

2. Ugrás a Start, keresse meg a Windows PowerShell, kattintson a jobb gombbal a Windows PowerShell, és válassza a Futtatás rendszergazdaként.

3. Győződjön meg arról, hogy a virtuális gép a megfelelő Azure KMS-kiszolgáló használatára van konfigurálva. Ehhez futtassa a következő parancsot:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    A parancs visszaadja-e: kulcskezelő szolgáltatás számítógépnév sikeresen beállítva kms.core.windows.net:1688.

4. Győződjön meg arról, hogy rendelkezik-e a KMS-kiszolgálóhoz való kapcsolódás Psping használatával. Váltson át a mappát, amelyikbe kibontotta a Pstools.zip letöltés, és futtassa a következő:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  A kimenet utolsó előtti második sorában, győződjön meg arról, hogy látni: küldött = 4, fogadott = 4, elveszett = 0 (0 % veszteség).

  Ha elveszett nagyobb, mint 0 (nulla), a virtuális gép nem rendelkezik kapcsolattal a KMS-kiszolgálóhoz. Ebben a helyzetben Ha a virtuális gép virtuális hálózatban, és van egy egyéni DNS-kiszolgáló megadva, meg kell győződnie arról, hogy a DNS-kiszolgáló van kms.core.windows.net megoldható. Vagy módosítsa a DNS-kiszolgáló, amely kms.core.windows.net oldható fel.

  Figyelje meg, hogy ha eltávolítja az összes DNS-kiszolgáló egy virtuális hálózatot, virtuális gépek Azure belső DNS-szolgáltatás használatára. Ez a szolgáltatás kms.core.windows.net tudja oldani.
  
Győződjön meg arról is, hogy a Vendég tűzfal olyan módon, hogy megakadályozza a aktiválási kísérletek nem konfiguráltak.

5. A sikeres kapcsolat kms.core.windows.net ellenőrzése után futtassa a következő parancsot, hogy emelt szintű Windows PowerShell parancssorába. Ez a parancs megpróbál aktiválási több alkalommal.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

A sikeres aktiválási adja vissza adatokat, a következőhöz:

**Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678) aktiválása... A termék aktiválása sikeres.**

## <a name="faq"></a>GYIK 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>A Windows Server 2016 létrehozott Azure piactérről. Konfigurálja a KMS-kulcsot a Windows Server 2016 aktiválásához kell? 
 
Nem. A lemezkép az Azure piactér rendelkezik a megfelelő KMS-ügyfél telepítő kulcsát már be van állítva. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Windows-aktiválás működik ugyanúgy attól függetlenül történik, ha a virtuális gép által használt Azure hibrid használata juttatás (HUB), vagy nem? 
 
Igen. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Mi történik, ha a Windows aktiválási időszak lejár? 
 
Ha a türelmi időszak lejárt, és még nincs aktiválva a Windows, Windows Server 2008 R2 és a Windows újabb verzióiban kattintás aktiválásával kapcsolatos további értesítések. A tapéta fekete marad, és a Windows Update biztonsági és csak a kritikus frissítéseket, de nem választható frissítéseket telepíti. Az értesítések című alján a [licencelési feltételek](http://technet.microsoft.com/en-us/library/ff793403.aspx) lap.   

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.