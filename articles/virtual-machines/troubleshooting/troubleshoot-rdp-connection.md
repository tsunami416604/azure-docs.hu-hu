---
title: Az Azure-beli Windows rendszerű virtuális gépekhez nem lehet RDP-kapcsolatot létesíteni | Microsoft Docs
description: Hibák elhárítása, amikor nem tud csatlakozni az Azure-beli Windows rendszerű virtuális géphez a Távoli asztal használatával
keywords: Távoli asztali hiba, távoli asztali kapcsolati hiba, nem lehet csatlakozni a virtuális géphez, távoli asztal hibaelhárítása
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: d3ad0e6d88ed849074989dc36698c01209921449
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749684"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Azure-beli virtuális géphez Távoli asztal kapcsolatok hibáinak megoldása
A Windows-alapú Azure-beli virtuális gépek (VM) RDP protokollon alapuló kapcsolata több okból is meghiúsulhat. A kapcsolat hiányában nem tudja elérni a virtuális gépet. A probléma forrása lehet a virtuális gép távoli asztali szolgáltatása, a hálózati kapcsolat vagy a gazdaszámítógép távoli asztali ügyfele. Ez a cikk végigvezeti Önt az RDP-kapcsolatok problémáinak megoldásához leggyakrabban használt módszereken. 

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géppel:

1. Távoli asztal konfiguráció alaphelyzetbe állítása.
2. A hálózati biztonsági csoportra vonatkozó szabályok/Cloud Services végpontok keresése.
3. Tekintse át a virtuális gép konzoljának naplóit.
4. Állítsa alaphelyzetbe a virtuális gép hálózati adapterét.
5. Győződjön meg arról, hogy a virtuális gép Resource Health.
6. A virtuális gép jelszavának alaphelyzetbe állítása.
7. Indítsa újra a virtuális gépet.
8. Telepítse újra a virtuális gépet.

Ha részletesebb lépésekre és magyarázatokra van szüksége, folytassa az olvasást. Ellenőrizze, hogy a helyi hálózati eszközök, például az útválasztók és a tűzfalak nem blokkolja-e a 3389-es kimenő TCP-portot, ahogy azt a [részletes RDP hibaelhárítási forgatókönyvek](detailed-troubleshoot-rdp.md)ismertetik

> [!TIP]
> Ha a virtuális gép **Connect** gombja szürkén jelenik meg a portálon, és nem csatlakozik az Azure-hoz egy [expressz útvonalon](../../expressroute/expressroute-introduction.md) vagy [helyek közötti VPN-](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) kapcsolaton keresztül, akkor az RDP használata előtt létre kell hoznia és hozzá kell rendelnie egy nyilvános IP-címet a virtuális géphez. További információ: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Az RDP-problémák hibaelhárításának módjai
A Resource Manager-alapú üzemi modell használatával létrehozott virtuális gépeket a következő módszerek egyikével lehet elhárítani:

* Azure Portal – nagyszerű, ha gyorsan alaphelyzetbe kell állítania az RDP-konfigurációt vagy a felhasználói hitelesítő adatokat, és nincs telepítve az Azure-eszközök.
* Azure PowerShell – ha egy PowerShell-parancssorral rendelkezik, gyorsan visszaállíthatja az RDP-konfigurációt vagy a felhasználói hitelesítő adatokat az Azure PowerShell-parancsmagok használatával.

A [klasszikus üzemi modellel](#troubleshoot-vms-created-using-the-classic-deployment-model)létrehozott virtuális gépek hibaelhárítására vonatkozó lépéseket is megtalálhatja.

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Hibakeresés a Azure Portal használatával
Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géphez való csatlakozással. Ha továbbra sem tud kapcsolatot létesíteni, próbálkozzon a következő lépéssel.

1. **Az RDP-kapcsolat alaphelyzetbe állítása**. Ez a hibaelhárítási lépés alaphelyzetbe állítja az RDP-konfigurációt, ha a távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfal szabályai blokkolja az RDP-t, például.
   
    Válassza ki a virtuális gépet a Azure Portal. Görgessen le a beállítások ablaktáblán a lista alján található **támogatás + hibaelhárítás** szakaszhoz. Kattintson a **jelszó alaphelyzetbe állítása** gombra. Állítsa a **módot** a **konfiguráció alaphelyzetbe állításához** , majd kattintson a **frissítés** gombra:
   
    ![Az RDP-konfiguráció alaphelyzetbe állítása a Azure Portalban](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Ellenőrizze a hálózati biztonsági csoport szabályait**. Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. A hatályos biztonsági csoportok szabályait is áttekintheti, így biztosítva, hogy a bejövő "engedélyezés" NSG szabály létezik, és az RDP-portra van rangsorolva (alapértelmezett 3389). További információ: [hatékony biztonsági szabályok használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Tekintse át a virtuális gép rendszerindítási diagnosztikát**. Ez a hibaelhárítási lépés a virtuálisgép-konzol naplófájljainak áttekintésével megállapítja, hogy a virtuális gép jelentett-e problémát. Nem minden virtuális gépre van engedélyezve a rendszerindítási diagnosztika, így ez a hibaelhárítási lépés opcionális lehet.
   
    Az adott hibaelhárítási lépések a jelen cikk hatókörén kívül esnek, de az RDP-kapcsolatot érintő szélesebb körű problémát jelezhetnek. A konzol naplófájljainak és a virtuális gép képernyőképének áttekintésével kapcsolatos további információkért lásd: [virtuális gépek rendszerindítási diagnosztika](boot-diagnostics.md).

4. **Állítsa alaphelyzetbe a virtuális gép hálózati adapterét**. További információ: [a NIC for Azure Windows VM alaphelyzetbe állítása](../windows/reset-network-interface.md).
5. Győződjön meg arról, **hogy a virtuális gép Resource Health**. Ez a hibaelhárítási lépés ellenőrzi, hogy nincsenek-e olyan ismert problémák az Azure platformon, amelyek hatással lehetnek a virtuális géphez való kapcsolódásra.
   
    Válassza ki a virtuális gépet a Azure Portal. Görgessen le a beállítások ablaktáblán a lista alján található **támogatás + hibaelhárítás** szakaszhoz. Kattintson az **erőforrás állapota** gombra. Az egészséges virtuális gépekről készült jelentések **elérhetők**:
   
    ![A VM-erőforrás állapotának keresése a Azure Portalban](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés alaphelyzetbe állítja a jelszót egy helyi rendszergazdai fiókban, ha nem biztos abban, hogy elfelejtette a hitelesítő adatokat.  Ha bejelentkezett a virtuális gépre, állítsa alaphelyzetbe az adott felhasználó jelszavát.
   
    Válassza ki a virtuális gépet a Azure Portal. Görgessen le a beállítások ablaktáblán a lista alján található **támogatás + hibaelhárítás** szakaszhoz. Kattintson a **jelszó alaphelyzetbe állítása** gombra. Győződjön meg arról, hogy a **mód** a **jelszó alaphelyzetbe állítására** van beállítva, majd adja meg a felhasználónevét és egy új jelszót. Végül kattintson a **frissítés** gombra:
   
    ![A felhasználó hitelesítő adatainak alaphelyzetbe állítása a Azure Portalban](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Indítsa újra a virtuális gépet**. Ez a hibaelhárítási lépés bármilyen mögöttes problémát kijavítani tud, amely maga a virtuális gép.
   
    Válassza ki a virtuális gépet a Azure Portalon, majd kattintson az **Áttekintés** fülre. kattintson az **Újraindítás** gombra:
   
    ![Indítsa újra a virtuális gépet a Azure Portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Telepítse újra a virtuális gépet**. Ez a hibaelhárítási lépés újratelepíti a virtuális gépet egy másik gazdagépre az Azure-ban, hogy javítsa az összes mögöttes platformot vagy hálózati problémát.
   
    Válassza ki a virtuális gépet a Azure Portal. Görgessen le a beállítások ablaktáblán a lista alján található **támogatás + hibaelhárítás** szakaszhoz. Kattintson az **újratelepítés** gombra, majd az újbóli **üzembe helyezés**elemre:
   
    ![Telepítse újra a virtuális gépet a Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    A művelet befejezése után a rendszer elveszíti az ideiglenes lemezeket, és frissíti a virtuális géppel társított dinamikus IP-címeket.

9. **Ellenőrizze az útválasztást**. A [következő ugrási](../../network-watcher/network-watcher-check-next-hop-portal.md) lehetőséggel ellenőrizheti, hogy egy adott útvonal nem akadályozza-e a forgalmat a virtuális gépekről vagy rendszerről. Network Watcher Az érvényes útvonalakat is áttekintheti a hálózati adapterek összes érvényes útvonalának megtekintéséhez. További információ: [hatékony útvonalak használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-routing-problem.md).

10. Győződjön meg arról, hogy a helyszíni tűzfal vagy tűzfal a számítógépén engedélyezi a kimenő TCP 3389-forgalom Azure-ba való továbbítását.

Ha továbbra is RDP-problémákba ütközik, [Nyisson meg egy támogatási kérést](https://azure.microsoft.com/support/options/) , vagy olvassa el [részletesebben az RDP-hibaelhárítási fogalmakat és lépéseket](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Hibakeresés a Azure PowerShell használatával
Ha még nem tette meg, [telepítse és konfigurálja a legújabb Azure PowerShell](/powershell/azure/overview).

Az alábbi példák olyan változókat használnak, mint például a `myResourceGroup`, a `myVM`és a `myVMAccessExtension`. Cserélje le ezeket a változók nevét és helyét a saját értékeire.

> [!NOTE]
> A felhasználói hitelesítő adatok és az RDP-konfiguráció alaphelyzetbe állítása a [set-AzVMAccessExtension PowerShell-](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) parancsmag használatával végezhető el. Az alábbi példákban `myVMAccessExtension` a folyamat részeként megadott nevet. Ha korábban már dolgozott a VMAccessAgent, a meglévő bővítmény nevét a `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` használatával szerezheti be a virtuális gép tulajdonságainak ellenőrzéséhez. A név megtekintéséhez tekintse meg a kimenet "Extensions" (bővítmények) szakaszát.

Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géphez való csatlakozással. Ha továbbra sem tud kapcsolatot létesíteni, próbálkozzon a következő lépéssel.

1. **Az RDP-kapcsolat alaphelyzetbe állítása**. Ez a hibaelhárítási lépés alaphelyzetbe állítja az RDP-konfigurációt, ha a távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfal szabályai blokkolja az RDP-t, például.
   
    A következő példa visszaállítja az RDP-kapcsolatokat egy `myVM` nevű virtuális gépen a `WestUS` helyen és az `myResourceGroup`nevű erőforráscsoporthoz:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Ellenőrizze a hálózati biztonsági csoport szabályait**. Ez a hibaelhárítási lépés azt ellenőrzi, hogy a hálózati biztonsági csoportban van-e egy olyan szabály, amely engedélyezi az RDP-forgalmat. Az RDP-hez tartozó alapértelmezett port a 3389-es TCP-port. A virtuális gép létrehozásakor nem lehet automatikusan létrehozni egy olyan szabályt, amely engedélyezi az RDP-forgalmat.
   
    Először rendelje hozzá a hálózati biztonsági csoport összes konfigurációs adatait a `$rules` változóhoz. Az alábbi példa az `myResourceGroup`nevű erőforráscsoport `myNetworkSecurityGroup` nevű hálózati biztonsági csoport adatait kérdezi le:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Most tekintse meg a hálózati biztonsági csoporthoz konfigurált szabályokat. Ellenőrizze, hogy létezik-e olyan szabály, amely engedélyezi a 3389-as TCP-portot a bejövő kapcsolatokhoz a következő módon:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Az alábbi példa egy érvényes biztonsági szabályt mutat be, amely engedélyezi az RDP-forgalmat. Megtekintheti a `Protocol`, `DestinationPortRange`, `Access`és `Direction` konfigurálását:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Ha nem rendelkezik olyan szabállyal, amely engedélyezi az RDP-forgalmat, [hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Engedélyezze a 3389-es TCP-portot.
3. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés alaphelyzetbe állítja a jelszót a helyi rendszergazdai fiókon, amelyet akkor ad meg, ha nem tudja, vagy elfelejtette a hitelesítő adatokat.
   
    Először adja meg a felhasználónevet és az új jelszót úgy, hogy a következő módon rendeli hozzá a hitelesítő adatokat a `$cred` változóhoz:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Most frissítse a hitelesítő adatokat a virtuális gépen. A következő példa egy `myVM` nevű virtuális gép hitelesítő adatait frissíti a `WestUS` helyen és az `myResourceGroup`nevű erőforráscsoport-csoporton:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Indítsa újra a virtuális gépet**. Ez a hibaelhárítási lépés bármilyen mögöttes problémát kijavítani tud, amely maga a virtuális gép.
   
    A következő példa újraindítja `myVM` nevű virtuális gépet az `myResourceGroup`nevű erőforráscsoporthoz:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Telepítse újra a virtuális gépet**. Ez a hibaelhárítási lépés újratelepíti a virtuális gépet egy másik gazdagépre az Azure-ban, hogy javítsa az összes mögöttes platformot vagy hálózati problémát.
   
    A következő példa újratelepíti a `myVM` nevű virtuális gépet a `WestUS` helyen és az `myResourceGroup`nevű erőforráscsoport-csoportba:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Ellenőrizze az útválasztást**. A [következő ugrási](../../network-watcher/network-watcher-check-next-hop-portal.md) lehetőséggel ellenőrizheti, hogy egy adott útvonal nem akadályozza-e a forgalmat a virtuális gépekről vagy rendszerről. Network Watcher Az érvényes útvonalakat is áttekintheti a hálózati adapterek összes érvényes útvonalának megtekintéséhez. További információ: [hatékony útvonalak használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-routing-problem.md).

7. Győződjön meg arról, hogy a helyszíni tűzfal vagy tűzfal a számítógépén engedélyezi a kimenő TCP 3389-forgalom Azure-ba való továbbítását.

Ha továbbra is RDP-problémákba ütközik, [Nyisson meg egy támogatási kérést](https://azure.microsoft.com/support/options/) , vagy olvassa el [részletesebben az RDP-hibaelhárítási fogalmakat és lépéseket](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>A klasszikus üzemi modell használatával létrehozott virtuális gépek hibáinak megoldása
Az egyes hibaelhárítási lépések után próbálkozzon újra a virtuális géppel.

1. **Az RDP-kapcsolat alaphelyzetbe állítása**. Ez a hibaelhárítási lépés alaphelyzetbe állítja az RDP-konfigurációt, ha a távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfal szabályai blokkolja az RDP-t, például.
   
    Válassza ki a virtuális gépet a Azure Portal. Kattintson a **... További** gomb, majd kattintson a **távelérés alaphelyzetbe állítása**elemre:
   
    ![Az RDP-konfiguráció alaphelyzetbe állítása a Azure Portalban](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Ellenőrizze Cloud Services végpontokat**. Ez a hibaelhárítási lépés ellenőrzi, hogy vannak-e végpontok a Cloud Services az RDP-forgalom engedélyezéséhez. Az RDP-hez tartozó alapértelmezett port a 3389-es TCP-port. A virtuális gép létrehozásakor nem lehet automatikusan létrehozni egy olyan szabályt, amely engedélyezi az RDP-forgalmat.
   
   Válassza ki a virtuális gépet a Azure Portal. Kattintson a **végpontok** gombra a virtuális géphez jelenleg konfigurált végpontok megtekintéséhez. Ellenőrizze, hogy létezik-e olyan végpont, amely engedélyezi az RDP-forgalmat az 3389-as TCP-porton.
   
   Az alábbi példa olyan érvényes végpontokat mutat be, amelyek engedélyezik az RDP-forgalmat:
   
   ![Cloud Services végpontok ellenőrzése a Azure Portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Ha nem rendelkezik olyan végponttal, amely engedélyezi az RDP-forgalmat, [hozzon létre egy Cloud Services végpontot](../windows/classic/setup-endpoints.md). Engedélyezze a TCP-t a 3389-es magánhálózati porton.
3. **Tekintse át a virtuális gép rendszerindítási diagnosztikát**. Ez a hibaelhárítási lépés a virtuálisgép-konzol naplófájljainak áttekintésével megállapítja, hogy a virtuális gép jelentett-e problémát. Nem minden virtuális gépre van engedélyezve a rendszerindítási diagnosztika, így ez a hibaelhárítási lépés opcionális lehet.
   
    Az adott hibaelhárítási lépések a jelen cikk hatókörén kívül esnek, de az RDP-kapcsolatot érintő szélesebb körű problémát jelezhetnek. A konzol naplófájljainak és a virtuális gép képernyőképének áttekintésével kapcsolatos további információkért lásd: [virtuális gépek rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. Győződjön meg arról, **hogy a virtuális gép Resource Health**. Ez a hibaelhárítási lépés ellenőrzi, hogy nincsenek-e olyan ismert problémák az Azure platformon, amelyek hatással lehetnek a virtuális géphez való kapcsolódásra.
   
    Válassza ki a virtuális gépet a Azure Portal. Görgessen le a beállítások ablaktáblán a lista alján található **támogatás + hibaelhárítás** szakaszhoz. Kattintson a **Resource Health** gombra. Az egészséges virtuális gépekről készült jelentések **elérhetők**:
   
    ![A VM-erőforrás állapotának keresése a Azure Portalban](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés alaphelyzetbe állítja a jelszót a megadott helyi rendszergazdai fiókon, ha nem biztos abban, hogy elfelejtette a hitelesítő adatokat.  Ha bejelentkezett a virtuális gépre, állítsa alaphelyzetbe az adott felhasználó jelszavát.
   
    Válassza ki a virtuális gépet a Azure Portal. Görgessen le a beállítások ablaktáblán a lista alján található **támogatás + hibaelhárítás** szakaszhoz. Kattintson a **jelszó alaphelyzetbe állítása** gombra. Adja meg a felhasználónevét és egy új jelszót. Végül kattintson a **Save (Mentés** ) gombra:
   
    ![A felhasználó hitelesítő adatainak alaphelyzetbe állítása a Azure Portalban](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Indítsa újra a virtuális gépet**. Ez a hibaelhárítási lépés bármilyen mögöttes problémát kijavítani tud, amely maga a virtuális gép.
   
    Válassza ki a virtuális gépet a Azure Portalon, majd kattintson az **Áttekintés** fülre. kattintson az **Újraindítás** gombra:
   
    ![Indítsa újra a virtuális gépet a Azure Portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Győződjön meg arról, hogy a helyszíni tűzfal vagy tűzfal a számítógépén engedélyezi a kimenő TCP 3389-forgalom Azure-ba való továbbítását.

Ha továbbra is RDP-problémákba ütközik, [Nyisson meg egy támogatási kérést](https://azure.microsoft.com/support/options/) , vagy olvassa el [részletesebben az RDP-hibaelhárítási fogalmakat és lépéseket](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Adott RDP-hibák elhárítása
Előfordulhat, hogy egy adott hibaüzenet jelenik meg, amikor RDP-kapcsolaton keresztül próbál csatlakozni a virtuális géphez. A leggyakoribb hibaüzenetek a következők:

* [A távoli munkamenet le lett választva, mert nincs elérhető távoli asztal licenckiszolgálóra a licenc biztosításához](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Távoli asztal nem találja a "Name" nevű számítógépet](troubleshoot-specific-rdp-errors.md#rdpname).
* [Hitelesítési hiba történt. Nem lehet felvenni a kapcsolatot a helyi biztonsági szolgáltatóval](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows biztonsági hiba: a hitelesítő adatai nem működnek](troubleshoot-specific-rdp-errors.md#wincred).
* [Ez a számítógép nem tud kapcsolódni a távoli számítógéphez](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>További források
Ha ezen hibák egyike sem történt meg, és továbbra sem tud csatlakozni a virtuális géphez Távoli asztalon keresztül, olvassa el a [Távoli asztal részletes hibaelhárítási útmutatóját](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* A virtuális gépen futó alkalmazások elérésével kapcsolatos hibaelhárítási lépéseket lásd: az Azure-beli [virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ha a Secure Shell (SSH) segítségével csatlakozik egy Linux rendszerű virtuális géphez az Azure-ban, tekintse meg az [SSH-kapcsolatok hibaelhárítása Az Azure-ban linuxos virtuális géppel](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakört.


