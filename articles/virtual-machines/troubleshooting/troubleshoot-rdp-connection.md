---
title: Nem lehet csatlakozni az RDP szolgáltatáshoz Windows virtuális géphez az Azure-ban | Microsoft dokumentumok
description: Elháríthatja azokat a problémákat, amelyek akkor nem tudcsatlakozni a Windows virtuális gépéhez az Azure-ban a Távoli asztal használatával
keywords: Távoli asztal hiba,távoli asztali kapcsolathiba,nem lehet csatlakozni a virtuális géphez,távoli asztali hibaelhárítás
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
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916608"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Távoli asztali kapcsolatok – Azure virtuális gép – problémamegoldás
A Windows-alapú Azure-beli virtuális gépek (VM) RDP protokollon alapuló kapcsolata több okból is meghiúsulhat. A kapcsolat hiányában nem tudja elérni a virtuális gépet. A probléma forrása lehet a virtuális gép távoli asztali szolgáltatása, a hálózati kapcsolat vagy a gazdaszámítógép távoli asztali ügyfele. Ez a cikk bemutatja az RDP-kapcsolatok hibáinak megoldására szolgáló leggyakoribb módszereket. 

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépések
Minden hibaelhárítási lépés után próbáljon meg újra csatlakozni a virtuális géphez:

1. Távoli asztal konfigurációjának alaphelyzetbe állítása.
2. Ellenőrizze a hálózati biztonsági csoport szabályait / Felhőszolgáltatások végpontok.
3. Tekintse át a virtuális gép konzoljának naplóit.
4. Állítsa alaphelyzetbe a virtuális gép hálózati adapterét.
5. Ellenőrizze a virtuális gép erőforrás ának állapotát.
6. Állítsa alaphelyzetbe a virtuális gép jelszavát.
7. Indítsa újra a virtuális gép.
8. Telepítse újra a virtuális gép.

Folytassa az olvasást, ha részletesebb lépésekre és magyarázatokra van szüksége. Ellenőrizze, hogy a helyi hálózati berendezések, például az útválasztók és a tűzfalak nem blokkolják-e a 3389-es kimenő TCP-portot, amint azt a [részletes RDP hibaelhárítási forgatókönyvek is megjegyezték.](detailed-troubleshoot-rdp.md)

> [!TIP]
> Ha a **virtuális gép Csatlakozás** gombja szürkén jelenik meg a portálon, és nem csatlakozik az Azure-hoz expressz [útvonalon](../../expressroute/expressroute-introduction.md) vagy helyek [közötti VPN-kapcsolaton](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) keresztül, létre kell hoznia és hozzá kell rendelnie a virtuális géphez egy nyilvános IP-címet, mielőtt rdp-t használhatna. További információ: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Az RDP-vel kapcsolatos problémák elhárításának módjai
Az Erőforrás-kezelő telepítési modelljével létrehozott virtuális gépek hibaelhárítása az alábbi módszerek egyikével:

* Azure Portal – nagyszerű, ha gyorsan alaphelyzetbe kell állítania az RDP-konfigurációt vagy a felhasználói hitelesítő adatokat, és nincs telepítve az Azure-eszközök.
* Azure PowerShell – ha egy PowerShell-parancshasználatával elégedett, gyorsan alaphelyzetbe állíthatja az RDP-konfigurációt vagy a felhasználói hitelesítő adatokat az Azure PowerShell-parancsmagok használatával.

A [klasszikus központi telepítési modell](#troubleshoot-vms-created-using-the-classic-deployment-model)használatával létrehozott virtuális gépek hibaelhárításával kapcsolatos lépéseket is megtalálhatja.

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Hibaelhárítás az Azure Portal használatával
Minden hibaelhárítási lépés után próbáljon meg újra csatlakozni a virtuális géphez. Ha továbbra sem tud csatlakozni, próbálkozzon a következő lépéssel.

1. **Állítsa alaphelyzetbe az RDP-kapcsolatot.** Ez a hibaelhárítási lépés alaphelyzetbe állítja az RDP-konfigurációt, ha a távoli kapcsolatok le vannak tiltva, vagy ha például a Windows tűzfal szabályai blokkolják az RDP-t.
   
    Válassza ki a virtuális gép az Azure Portalon. Görgessen le a beállítások ablaktáblán a lista alján található **Támogatás + Hibaelhárítás** szakaszig. Kattintson a **Jelszó alaphelyzetbe állítása** gombra. Állítsa be a **mód csak** **alaphelyzetbe állítása konfiguráció csak,** majd kattintson a **Frissítés** gombra:
   
    ![Az RDP-konfiguráció alaphelyzetbe állítása az Azure Portalon](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **A hálózati biztonsági csoport szabályainak ellenőrzése**. Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. A hatályos biztonsági csoportszabályokat is áttekintheti annak érdekében, hogy a bejövő "Engedélyezés" NSG-szabály létezik, és az RDP-port (alapértelmezett 3389) prioritása legyen. További információ: [A hatékony biztonsági szabályok használata a virtuális gép forgalomának hibáinak elhárításához.](../../virtual-network/diagnose-network-traffic-filter-problem.md)

3. **Tekintse át a virtuális gép rendszerindítási diagnosztikát.** Ez a hibaelhárítási lépés áttekinti a virtuális gép konzolnaplóit annak megállapítására, hogy a virtuális gép jelent-e problémát. Nem minden virtuális gép rendelkezik rendszerindítási diagnosztikával, ezért ez a hibaelhárítási lépés nem kötelező.
   
    A konkrét hibaelhárítási lépések túlmutatnak a jelen cikk hatókörén, de az RDP-kapcsolatot érintő szélesebb körű problémára utalhatnak. A konzolnaplók és a virtuális gép képernyőképének áttekintésével kapcsolatos további információkért lásd: [Rendszerindítási diagnosztika a virtuális gépekhez.](boot-diagnostics.md)

4. **Állítsa alaphelyzetbe a virtuális gép hálózati adapterét.** További információ: [A hálózati adapter alaphelyzetbe állítása az Azure Windows virtuális géphez.](../windows/reset-network-interface.md)
5. **Ellenőrizze a virtuális gép erőforrásának állapotát.** Ez a hibaelhárítási lépés ellenőrzi, hogy nincsenek olyan ismert problémák az Azure platformmal, amelyek hatással lehetnek a virtuális géphez való kapcsolódásra.
   
    Válassza ki a virtuális gép az Azure Portalon. Görgessen le a beállítások ablaktáblán a lista alján található **Támogatás + Hibaelhárítás** szakaszig. Kattintson az **Erőforrás állapota** gombra. A kifogástalan állapotú virtuális gép **elérhetőként jelenti:**
   
    ![Virtuálisgép-erőforrás állapotának ellenőrzése az Azure Portalon](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés visszaállítja a helyi rendszergazdai fiók jelszavát, ha bizonytalan, vagy elfelejtette a hitelesítő adatokat.  Miután bejelentkezett a virtuális gépbe, alaphelyzetbe kell állítania az adott felhasználó jelszavát.
   
    Válassza ki a virtuális gép az Azure Portalon. Görgessen le a beállítások ablaktáblán a lista alján található **Támogatás + Hibaelhárítás** szakaszig. Kattintson a **Jelszó alaphelyzetbe állítása** gombra. Győződjön meg **arról,** hogy a Mód a **Jelszó alaphelyzetbe állítása** beállításra van beállítva, majd adja meg a felhasználónevét és egy új jelszót. Végül kattintson a **Frissítés** gombra:
   
    ![A felhasználói hitelesítő adatok alaphelyzetbe állítása az Azure Portalon](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés kijavíthatja a virtuális gép által felmerülő alapvető problémákat.
   
    Válassza ki a virtuális gép az Azure Portalon, és kattintson az **Áttekintés** fülre. Kattintson az **Újraindítás** gombra:
   
    ![Indítsa újra a virtuális gép az Azure Portalon](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Telepítse újra a virtuális gép.** Ez a hibaelhárítási lépés újratelepíti a virtuális gép egy másik gazdagép az Azure-ban az alapul szolgáló platform vagy hálózati problémák kijavítása érdekében.
   
    Válassza ki a virtuális gép az Azure Portalon. Görgessen le a beállítások ablaktáblán a lista alján található **Támogatás + Hibaelhárítás** szakaszig. Kattintson az **Újratelepítés** gombra, majd az **Újratelepítés gombra:**
   
    ![A virtuális gép újratelepítése az Azure Portalon](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    A művelet befejezése után a rövid élettartamú lemezadatok elvesznek, és a virtuális géphez társított dinamikus IP-címek frissülnek.

9. **Útválasztás ellenőrzése**. A Network Watcher [Következő ugrási](../../network-watcher/network-watcher-check-next-hop-portal.md) képességével ellenőrizze, hogy egy útvonal nem akadályozza-e meg a virtuális gépre vagy a virtuális gépről történő átirányítást. A hatékony útvonalak áttekintésével megtekintheti a hálózati adapter összes hatékony útvonalát is. További információ: [Hatékony útvonalak használata a virtuális gép forgalomforgalmának elhárításához.](../../virtual-network/diagnose-network-routing-problem.md)

10. Győződjön meg arról, hogy a helyszíni tűzfal vagy tűzfal a számítógépen, lehetővé teszi a kimenő TCP 3389 forgalmat az Azure-ba.

Ha továbbra is problémákat tapasztal az RDP-vel kapcsolatban, [megnyithat egy támogatási kérelmet,](https://azure.microsoft.com/support/options/) vagy [elolvashatja az RDP hibaelhárítási fogalmait és lépéseit.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-using-azure-powershell"></a>Hibaelhárítás az Azure PowerShell használatával
Ha még nem tette meg, [telepítse és konfigurálja a legújabb Azure PowerShellt.](/powershell/azure/overview)

A következő példák olyan `myResourceGroup` `myVM`változókat használnak, mint a , és `myVMAccessExtension`a . Cserélje le ezeket a változóneveket és helyeket a saját értékeire.

> [!NOTE]
> Alaphelyzetbe állítja a felhasználói hitelesítő adatokat és az RDP-konfigurációt a [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell-parancsmag használatával. A következő példákban `myVMAccessExtension` a folyamat részeként megadott név szerepel. Ha korábban már dolgozott a VMAccessAgent, a meglévő bővítmény `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` nevét a virtuális gép tulajdonságainak ellenőrzésével kaphatja meg. A név megtekintéséhez tekintse meg a kimenet "Bővítmények" szakaszában.

Minden hibaelhárítási lépés után próbáljon meg újra csatlakozni a virtuális géphez. Ha továbbra sem tud csatlakozni, próbálkozzon a következő lépéssel.

1. **Állítsa alaphelyzetbe az RDP-kapcsolatot.** Ez a hibaelhárítási lépés alaphelyzetbe állítja az RDP-konfigurációt, ha a távoli kapcsolatok le vannak tiltva, vagy ha például a Windows tűzfal szabályai blokkolják az RDP-t.
   
    A következő példa visszaállítja az RDP-kapcsolatot `WestUS` egy a helyen `myResourceGroup`és a megnevezett erőforráscsoportban megnevezett `myVM` virtuális gépen:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **A hálózati biztonsági csoport szabályainak ellenőrzése**. Ez a hibaelhárítási lépés ellenőrzi, hogy van-e olyan szabály a hálózati biztonsági csoportban, amely engedélyezi az RDP-forgalmat. Az RDP alapértelmezett portja a 3389-es TCP-port. Az RDP-forgalom engedélyezésére vonatkozó szabály nem hozható létre automatikusan a virtuális gép létrehozásakor.
   
    Először rendelje hozzá a hálózati biztonsági csoport `$rules` összes konfigurációs adatát a változóhoz. A következő példa a névalatt megnevezett `myNetworkSecurityGroup` `myResourceGroup`hálózati biztonsági csoportról kap információt:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Most tekintse meg a hálózati biztonsági csoporthoz konfigurált szabályokat. Ellenőrizze, hogy létezik-e olyan szabály, amely engedélyezi a 3389-es TCP-portot a bejövő kapcsolatok számára az alábbiak szerint:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    A következő példa egy érvényes biztonsági szabályt mutat be, amely lehetővé teszi az RDP-forgalmat. A , `Protocol` `DestinationPortRange`, `Access`, `Direction` és megfelelően vannak konfigurálva:
   
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
   
    Ha nincs olyan szabálya, amely engedélyezi az RDP-forgalmat, [hozzon létre egy hálózati biztonsági csoport szabályt.](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) A 3389-es TCP-port engedélyezése.
3. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés visszaállítja a jelszót a helyi rendszergazdai fiók, amely akkor adja meg, ha nem biztos, vagy elfelejtette a hitelesítő adatokat.
   
    Először adja meg a felhasználónevet és az új `$cred` jelszót úgy, hogy hitelesítő adatokat rendel a változóhoz az alábbiak szerint:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Most frissítse a hitelesítő adatokat a virtuális gép. A következő példa frissíti a hitelesítő `myVM` adatokat `WestUS` a helyben és `myResourceGroup`a nevű erőforráscsoportban megnevezett virtuális gépen:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés kijavíthatja a virtuális gép által felmerülő alapvető problémákat.
   
    A következő példa újraindítja `myVM` a névvel `myResourceGroup`ellátott erőforráscsoportban megnevezett virtuális gépét:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Telepítse újra a virtuális gép.** Ez a hibaelhárítási lépés újratelepíti a virtuális gép egy másik gazdagép az Azure-ban az alapul szolgáló platform vagy hálózati problémák kijavítása érdekében.
   
    A következő példa újratelepíti `myVM` a `WestUS` helyben és a `myResourceGroup`nevű erőforráscsoportban megnevezett virtuális gépet:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Útválasztás ellenőrzése**. A Network Watcher [Következő ugrási](../../network-watcher/network-watcher-check-next-hop-portal.md) képességével ellenőrizze, hogy egy útvonal nem akadályozza-e meg a virtuális gépre vagy a virtuális gépről történő átirányítást. A hatékony útvonalak áttekintésével megtekintheti a hálózati adapter összes hatékony útvonalát is. További információ: [Hatékony útvonalak használata a virtuális gép forgalomforgalmának elhárításához.](../../virtual-network/diagnose-network-routing-problem.md)

7. Győződjön meg arról, hogy a helyszíni tűzfal vagy tűzfal a számítógépen, lehetővé teszi a kimenő TCP 3389 forgalmat az Azure-ba.

Ha továbbra is problémákat tapasztal az RDP-vel kapcsolatban, [megnyithat egy támogatási kérelmet,](https://azure.microsoft.com/support/options/) vagy [elolvashatja az RDP hibaelhárítási fogalmait és lépéseit.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>A klasszikus telepítési modell használatával létrehozott virtuális gépek hibáinak elhárítása

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Minden hibaelhárítási lépés után próbálja meg újra csatlakoztatni a virtuális géphez.

1. **Állítsa alaphelyzetbe az RDP-kapcsolatot.** Ez a hibaelhárítási lépés alaphelyzetbe állítja az RDP-konfigurációt, ha a távoli kapcsolatok le vannak tiltva, vagy ha például a Windows tűzfal szabályai blokkolják az RDP-t.
   
    Válassza ki a virtuális gép az Azure Portalon. Kattintson a **... További** gomb, majd kattintson **a Távelérés alaphelyzetbe állítása gombra:**
   
    ![Az RDP-konfiguráció alaphelyzetbe állítása az Azure Portalon](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **A Felhőszolgáltatások végpontjainak ellenőrzése.** Ez a hibaelhárítási lépés ellenőrzi, hogy a felhőszolgáltatások ban rendelkezik-e végpontokkal az RDP-forgalom engedélyezéséhez. Az RDP alapértelmezett portja a 3389-es TCP-port. Az RDP-forgalom engedélyezésére vonatkozó szabály nem hozható létre automatikusan a virtuális gép létrehozásakor.
   
   Válassza ki a virtuális gép az Azure Portalon. Kattintson a **Végpontok** gombra a virtuális géphez jelenleg konfigurált végpontok megtekintéséhez. Ellenőrizze, hogy léteznek-e olyan végpontok, amelyek engedélyezik az RDP-forgalmat a 3389-es TCP-porton.
   
   A következő példa az RDP-forgalmat engedélyező érvényes végpontokat mutatja be:
   
   ![A Felhőszolgáltatások végpontjainak ellenőrzése az Azure Portalon](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Ha nem rendelkezik olyan végpontdal, amely lehetővé teszi az RDP-forgalmat, [hozzon létre egy Felhőszolgáltatások-végpontot.](../windows/classic/setup-endpoints.md) A TCP-nek a 3389-es magánportra való engedélyezése.
3. **Tekintse át a virtuális gép rendszerindítási diagnosztikát.** Ez a hibaelhárítási lépés áttekinti a virtuális gép konzolnaplóit annak megállapítására, hogy a virtuális gép jelent-e problémát. Nem minden virtuális gép rendelkezik rendszerindítási diagnosztikával, ezért ez a hibaelhárítási lépés nem kötelező.
   
    A konkrét hibaelhárítási lépések túlmutatnak a jelen cikk hatókörén, de az RDP-kapcsolatot érintő szélesebb körű problémára utalhatnak. A konzolnaplók és a virtuális gép képernyőképének áttekintésével kapcsolatos további információkért lásd: [Rendszerindítási diagnosztika a virtuális gépekhez.](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)
4. **Ellenőrizze a virtuális gép erőforrásának állapotát.** Ez a hibaelhárítási lépés ellenőrzi, hogy nincsenek olyan ismert problémák az Azure platformmal, amelyek hatással lehetnek a virtuális géphez való kapcsolódásra.
   
    Válassza ki a virtuális gép az Azure Portalon. Görgessen le a beállítások ablaktáblán a lista alján található **Támogatás + Hibaelhárítás** szakaszig. Kattintson az **Erőforrás állapota** gombra. A kifogástalan állapotú virtuális gép **elérhetőként jelenti:**
   
    ![Virtuálisgép-erőforrás állapotának ellenőrzése az Azure Portalon](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés visszaállítja a jelszót a helyi rendszergazdai fiók, amely akkor adja meg, ha nem biztos, vagy elfelejtette a hitelesítő adatokat.  Miután bejelentkezett a virtuális gépbe, alaphelyzetbe kell állítania az adott felhasználó jelszavát.
   
    Válassza ki a virtuális gép az Azure Portalon. Görgessen le a beállítások ablaktáblán a lista alján található **Támogatás + Hibaelhárítás** szakaszig. Kattintson a **Jelszó alaphelyzetbe állítása** gombra. Adja meg felhasználónevét és új jelszavát. Végül kattintson a **Mentés** gombra:
   
    ![A felhasználói hitelesítő adatok alaphelyzetbe állítása az Azure Portalon](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés kijavíthatja a virtuális gép által felmerülő alapvető problémákat.
   
    Válassza ki a virtuális gép az Azure Portalon, és kattintson az **Áttekintés** fülre. Kattintson az **Újraindítás** gombra:
   
    ![Indítsa újra a virtuális gép az Azure Portalon](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Győződjön meg arról, hogy a helyszíni tűzfal vagy tűzfal a számítógépen, lehetővé teszi a kimenő TCP 3389 forgalmat az Azure-ba.

Ha továbbra is problémákat tapasztal az RDP-vel kapcsolatban, [megnyithat egy támogatási kérelmet,](https://azure.microsoft.com/support/options/) vagy [elolvashatja az RDP hibaelhárítási fogalmait és lépéseit.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-specific-rdp-errors"></a>Konkrét RDP-hibák elhárítása
Előfordulhat, hogy egy adott hibaüzenetet, amikor megpróbál csatlakozni a virtuális géprdp-n keresztül. A leggyakoribb hibaüzenetek a következők:

* [A távoli munkamenet le lett választva, mert nincs elérhető távoli asztali licenckiszolgáló a licenc biztosításához.](troubleshoot-specific-rdp-errors.md#rdplicense)
* [A Távoli asztal nem találja a számítógép "nevét"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Hitelesítési hiba történt. A helyi biztonsági hatósággal nem lehet kapcsolatba lépni.](troubleshoot-specific-rdp-errors.md#rdpauth)
* [Windows biztonsági hiba: A hitelesítő adatok nem működtek](troubleshoot-specific-rdp-errors.md#wincred).
* [Ez a számítógép nem tud csatlakozni a távoli számítógéphez](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>További források
Ha a hibák egyike sem történt, és továbbra sem tud csatlakozni a virtuális géphez a Távoli asztalon keresztül, olvassa el a Távoli asztal részletes [hibaelhárítási útmutatóját.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* A virtuális gépeken futó alkalmazások elérésének hibaelhárítási lépéseit az [Azure virtuális gépeken futó alkalmazások hozzáférése hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakörben található.
* Ha problémái vannak a Secure Shell (SSH) használatával egy Linux-alapú virtuális géphez való csatlakozáshoz az Azure-ban, olvassa [el az SSH-kapcsolatok hibaelhárítása linuxos virtuális géphez az Azure-ban című témakört.](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


