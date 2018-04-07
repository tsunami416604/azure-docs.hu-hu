---
title: Nem lehet kapcsolódni a Windows Azure-ban való RDP-|} Microsoft Docs
description: Ha nem tud kapcsolódni a távoli asztali kapcsolat segítségével Windows rendszerű virtuális gép problémák elhárítása
keywords: Távoli asztali hiba, a távoli asztali kapcsolat hiba, nem lehet csatlakozni a virtuális gép, távoli asztal – hibaelhárítás
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: danis
ms.openlocfilehash: 60c54850c1ca5de0e9bda4b48688ba297874e48e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Egy Azure virtuális géphez a távoli asztali kapcsolatok hibáinak elhárítása
A távoli asztal protokoll (RDP) kapcsolatot a Windows-alapú Azure virtuális gép (VM) különböző okokból, így nem érhető el a virtuális gép sikertelen lehet. A probléma lehet a távoli asztali szolgáltatás a virtuális Gépet, a hálózati kapcsolat vagy a távoli asztali ügyfél a gazdaszámítógépen. Ez a cikk végigvezeti Önt egy RDP-kapcsolati problémák megoldása a leggyakrabban használt módszerek. 

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Első hibaelhárítási lépések
Hibaelhárítási lépések, után próbáljon újra csatlakozni a virtuális Gépet:

1. Visszaállítja a távoli asztal-konfigurációt.
2. Ellenőrizze a hálózati biztonsági csoport szabályainak / felhőalapú szolgáltatások végpontok.
3. Tekintse át a virtuális gép konzol naplókat.
4. A hálózati adapter alaphelyzetbe a virtuális gép számára.
5. Ellenőrizze a VM erőforrás állapotát.
6. A virtuális gép jelszavát.
7. Indítsa újra a virtuális Gépet.
8. Telepítse újra a virtuális Gépet.

Továbbra is olvasási, ha a részletes lépéseket és magyarázata van szükség. Győződjön meg arról, hogy helyi hálózati eszközökre, például az útválasztók és tűzfalak nem blokkolják a kimenő 3389-es TCP-port leírtaknak megfelelően [hibakeresésekhez RDP részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Ha a **Connect** gombra kattint, a virtuális Gépet ki használhatók a portálon, és nem csatlakozik Azure keresztül egy [Express Route](../../expressroute/expressroute-introduction.md) vagy [telephelyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) kapcsolat létrehozásához szükséges és rendelje a nyilvános IP-címnek a virtuális gép RDP használata előtt. További információ: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>RDP elhárítása módjai
A Resource Manager üzembe helyezési modellel használatával az alábbi módszerek egyikének használatával létrehozott virtuális gépek hibaelhárítását hajthatja végre:

* [Azure-portálon](#using-the-azure-portal) - nagyszerű, ha szüksége gyorsan alaphelyzetbe állítani a RDP vagy felhasználói hitelesítő adatokat, és nem kell az Azure-eszközök telepítve.
* [Az Azure PowerShell](#using-azure-powershell) – Ha Ön egy PowerShell-parancssorba, a Feladatkezelő gyorsan az RDP vagy felhasználói hitelesítő adatok alaphelyzetbe állítása az Azure PowerShell-parancsmagok használatával.

Használatával létrehozott virtuális gépek hibaelhárítási lépéseket is megkeresheti a [klasszikus telepítési modell](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Hibaelhárítás az Azure portál használatával
Minden hibaelhárítási lépés után ismét kapcsolódni a virtuális Gépre próbálja. Ha továbbra sem sikerül kapcsolódni, próbálja meg a következő lépéssel.

1. **Az RDP-kapcsolat alaphelyzetbe állítására**. Ez a hibaelhárítási lépés visszaállítja az RDP-konfigurációt, távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfalszabályok blokkolják RDP, például.
   
    Válassza ki a virtuális Gépet az Azure portálon. Görgessen lefelé a beállítások panelen a **támogatási + hibaelhárítás** szakasz közelében tétellista aljához. Kattintson a **jelszó-átállítási** gombra. Állítsa be a **mód** való **alaphelyzetbe állítása konfigurációs csak** , majd a **frissítés** gombra:
   
    ![Visszaállítja az RDP-konfigurációt az Azure-portálon](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Ellenőrizze hálózati biztonsági csoportszabályok**. Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. Hatékony biztonsági csoport szabályokat, hogy biztosítsa a bejövő "Engedélyezés" NSG-t is felhasználhatja a szabály létezik-e, és a rendszer előrébb RDP-porthoz (3389-es alapértelmezett). További információkért lásd: [használatával hatékony biztonsági szabályokat hibáinak elhárítása a virtuális gép forgalom bonyolódjon](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

3. **Tekintse át a virtuális gép rendszerindítási diagnosztika**. Ez a hibaelhárítási lépés ellenőrzi, hogy a virtuális gép konzolnaplófájlokban meghatározni, ha a virtuális gép hibát jelez. Nem minden virtuális gép rendelkezik engedélyezve van, a rendszerindítási diagnosztika, ezért lehet, hogy ez a hibaelhárítási lépés nem kötelező.
   
    Adott hibaelhárítási lépéseket a cikk túlmutató, de, amely érinti az RDP-kapcsolat szélesebb problémájára utalhat. A konzol naplói és a virtuális gép képernyőkép áttekintésével további információkért lásd: [rendszerindítási diagnosztika a virtuális gépek](boot-diagnostics.md).

4. **A virtuális gép számára a hálózati adapter alaphelyzetbe**. További információkért lásd: [NIC visszaállítása az Azure virtuális gépekhez Windows](reset-network-interface.md).
5. **Ellenőrizze a virtuális gép Resource Health**. Ez a hibaelhárítási lépés ellenőrzi, hogy nincsenek ismert problémák a Azure platformon, amely hatással lehet a kapcsolatot a virtuális géphez.
   
    Válassza ki a virtuális Gépet az Azure portálon. Görgessen lefelé a beállítások panelen a **támogatási + hibaelhárítás** szakasz közelében tétellista aljához. Kattintson a **erőforrás állapota** gombra. A megfelelő virtuális gépek jelenti, hogy **elérhető**:
   
    ![Ellenőrizze a VM erőforrás állapota az Azure-portálon](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés egy helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása, ha bizonytalan, vagy elfelejtette a hitelesítő adatokat.  Miután jelentkezett be a virtuális Gépet, alaphelyzetbe kell, hogy a felhasználó jelszavát.
   
    Válassza ki a virtuális Gépet az Azure portálon. Görgessen lefelé a beállítások panelen a **támogatási + hibaelhárítás** szakasz közelében tétellista aljához. Kattintson a **jelszó-átállítási** gombra. Győződjön meg arról, hogy a **mód** értéke **jelszó-átállítási** és írja be a felhasználónevet és egy új jelszót. Végül kattintson a **frissítés** gombra:
   
    ![Az Azure-portálon a felhasználói hitelesítő adatok alaphelyzetbe állítása](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés kijavíthatja a mögöttes probléma merül fel a virtuális gépért nehézségekkel.
   
    Válassza ki a virtuális Gépet az Azure portálon, és kattintson a **áttekintése** fülre. Kattintson a **indítsa újra a** gombra:
   
    ![Indítsa újra a virtuális Gépet az Azure-portálon](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Telepítse újra a virtuális gép**. Ez a hibaelhárítási lépés redeploys bármely alapul szolgáló platform vagy hálózati problémák megoldására Azure belül egy másik gazdagépre a virtuális Gépet.
   
    Válassza ki a virtuális Gépet az Azure portálon. Görgessen lefelé a beállítások panelen a **támogatási + hibaelhárítás** szakasz közelében tétellista aljához. Kattintson a **újratelepíteni** gombra, majd **újratelepíteni**:
   
    ![Telepítse újra a virtuális Gépet az Azure-portálon](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Ez a művelet befejezése után rövid élettartamú lemez adatok nem vesztek el, és dinamikus IP-címek a virtuális Géphez társított frissülnek.

9. **Ellenőrizze az útválasztást**. Használja a hálózati figyelőt [a következő Ugrás](../../network-watcher/network-watcher-check-next-hop-portal.md) képességet, hogy ellenőrizze, hogy egy útvonal nem akadályozza meg, hogy forgalom való átirányítását, vagy egy virtuális gépről. Egy adott hálózati csatoló összes hatékony útvonalak hatékony útvonalakat is felhasználhatja. További információkért lásd: [hatékony használata az útvonalakat hibáinak elhárítása a virtuális gép forgalom bonyolódjon](../../virtual-network/virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow).

10. Győződjön meg arról, hogy azokat a helyi tűzfal, illetve a számítógép tűzfala lehetővé teszi az Azure kimenő 3389-es TCP-forgalom.

Ha RDP problémák továbbra is találkozik, akkor [támogatási kérést nyithat](https://azure.microsoft.com/support/options/) vagy olvasási [RDP hibaelhárítással kapcsolatos fogalmak és a lépések részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Hibaelhárítás az Azure PowerShell használatával
Ha még nem tette, [a legújabb Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

Az alábbi példák a változókkal például `myResourceGroup`, `myVM`, és `myVMAccessExtension`. A változó nevét és helyét. cserélje le a saját értékeit.

> [!NOTE]
> A felhasználói hitelesítő adatok és az RDP-konfigurációt alaphelyzetbe állítását a [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-parancsmagot. A következő példákban `myVMAccessExtension` a folyamat részeként megadott név. Ha előzőleg dolgozott a VMAccessAgent, a meglévő bővítmény nevét beszerezheti a `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` a virtuális gép tulajdonságainak ellenőrzéséhez. Az nevének megtekintéséhez keresse meg a kimeneti "Extensions" részében található.

Minden hibaelhárítási lépés után ismét kapcsolódni a virtuális Gépre próbálja. Ha továbbra sem sikerül kapcsolódni, próbálja meg a következő lépéssel.

1. **Az RDP-kapcsolat alaphelyzetbe állítására**. Ez a hibaelhárítási lépés visszaállítja az RDP-konfigurációt, távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfalszabályok blokkolják RDP, például.
   
    Az alábbi példában visszaállítja az RDP-csatlakozás a virtuális gép nevű `myVM` a a `WestUS` helyét és nevű erőforráscsoportban `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Ellenőrizze hálózati biztonsági csoportszabályok**. Ez a hibaelhárítási lépés ellenőrzi, hogy rendelkezik-e a szabály a hálózati biztonsági csoport RDP-forgalmát engedélyezi. Az alapértelmezett port az RDP a 3389-es TCP-port. Egy szabályt, amely engedélyezi az RDP-forgalmát előfordulhat, hogy nem automatikusan létrejön a virtuális gép létrehozásakor.
   
    Először, rendelje hozzá a konfigurációs adatokat a hálózati biztonsági csoport a `$rules` változó. A következő példa a hálózati biztonsági csoport nevű információt szerez `myNetworkSecurityGroup` az erőforráscsoport neve `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Mostantól megtekintheti a szabályokat, amelyek a hálózati biztonsági csoporthoz vannak beállítva. Győződjön meg arról, hogy létezik-e szabály engedélyezi a 3389-es TCP-portot a bejövő kapcsolatok az alábbiak szerint:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    A következő példában egy érvényes biztonsági szabályt, amely lehetővé teszi az RDP-forgalmát. Látható `Protocol`, `DestinationPortRange`, `Access`, és `Direction` megfelelően vannak konfigurálva:
   
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
   
    Ha nem rendelkezik olyan szabály, amely lehetővé teszi, hogy RDP-forgalmát, [hozzon létre egy hálózati biztonsági szabály](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Engedélyezi a 3389-es TCP-port.
3. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés, ha biztos abban, vagy elfelejtette a hitelesítő adatok helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása.
   
    Először adja meg a felhasználónevet és egy új jelszót rendelhet a hitelesítő adatokat a `$cred` változót az alábbiak szerint:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Most frissítse a hitelesítő adatokat a virtuális gépen. Az alábbi példa frissíti a virtuális gép nevű hitelesítő adatok `myVM` a a `WestUS` helyét és nevű erőforráscsoportban `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés kijavíthatja a mögöttes probléma merül fel a virtuális gépért nehézségekkel.
   
    Az alábbi példa nevű virtuális gép újraindul `myVM` az erőforráscsoport neve `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Telepítse újra a virtuális gép**. Ez a hibaelhárítási lépés redeploys bármely alapul szolgáló platform vagy hálózati problémák megoldására Azure belül egy másik gazdagépre a virtuális Gépet.
   
    Az alábbi példa redeploys nevű virtuális gép `myVM` a a `WestUS` helyét és nevű erőforráscsoportban `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Ellenőrizze az útválasztást**. Használja a hálózati figyelőt [a következő Ugrás](../../network-watcher/network-watcher-check-next-hop-portal.md) képességet, hogy ellenőrizze, hogy egy útvonal nem akadályozza meg, hogy forgalom való átirányítását, vagy egy virtuális gépről. Egy adott hálózati csatoló összes hatékony útvonalak hatékony útvonalakat is felhasználhatja. További információkért lásd: [hatékony használata az útvonalakat hibáinak elhárítása a virtuális gép forgalom bonyolódjon](../../virtual-network/virtual-network-routes-troubleshoot-powershell.md#using-effective-routes-to-troubleshoot-vm-traffic-flow).

7. Győződjön meg arról, hogy azokat a helyi tűzfal, illetve a számítógép tűzfala lehetővé teszi az Azure kimenő 3389-es TCP-forgalom.

Ha RDP problémák továbbra is találkozik, akkor [támogatási kérést nyithat](https://azure.microsoft.com/support/options/) vagy olvasási [RDP hibaelhárítással kapcsolatos fogalmak és a lépések részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>A klasszikus telepítési modell használatával létrehozott virtuális gépek hibaelhárítása
Hibaelhárítási lépések, után próbáljon újra csatlakozni a virtuális Gépet.

1. **Az RDP-kapcsolat alaphelyzetbe állítására**. Ez a hibaelhárítási lépés visszaállítja az RDP-konfigurációt, távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfalszabályok blokkolják RDP, például.
   
    Válassza ki a virtuális Gépet az Azure portálon. Kattintson a **... További** gombra, majd kattintson az **távelérés alaphelyzetbe**:
   
    ![Visszaállítja az RDP-konfigurációt az Azure-portálon](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Cloud Services végpontok ellenőrzése**. Ez a hibaelhárítási lépés ellenőrzi, hogy rendelkezik-e végpontok RDP-forgalmát engedélyezi a felhőszolgáltatásokban. Az alapértelmezett port az RDP a 3389-es TCP-port. Egy szabályt, amely engedélyezi az RDP-forgalmát előfordulhat, hogy nem automatikusan létrejön a virtuális gép létrehozásakor.
   
   Válassza ki a virtuális Gépet az Azure portálon. Kattintson a **végpontok** gombra kattintva megtekintheti a jelenleg konfigurált a virtuális gép számára. Győződjön meg arról, hogy a végpontok léteznek, amely engedélyezi az RDP a 3389-es TCP-porton.
   
   Az alábbi példa érvényes végpontok, amelyek lehetővé teszik az RDP-forgalmát jeleníti meg:
   
   ![Az Azure portálon Felhőszolgáltatások végpontok ellenőrzése](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Ha nem rendelkezik olyan végponttal, amely lehetővé teszi, hogy RDP-forgalmát, [Felhőszolgáltatások-végpont létrehozása](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Személyes 3389-es port TCP engedélyezéséhez.
3. **Tekintse át a virtuális gép rendszerindítási diagnosztika**. Ez a hibaelhárítási lépés ellenőrzi, hogy a virtuális gép konzolnaplófájlokban meghatározni, ha a virtuális gép hibát jelez. Nem minden virtuális gép rendelkezik engedélyezve van, a rendszerindítási diagnosztika, ezért lehet, hogy ez a hibaelhárítási lépés nem kötelező.
   
    Adott hibaelhárítási lépéseket a cikk túlmutató, de, amely érinti az RDP-kapcsolat szélesebb problémájára utalhat. A konzol naplói és a virtuális gép képernyőkép áttekintésével további információkért lásd: [rendszerindítási diagnosztika a virtuális gépek](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Ellenőrizze a virtuális gép Resource Health**. Ez a hibaelhárítási lépés ellenőrzi, hogy nincsenek ismert problémák a Azure platformon, amely hatással lehet a kapcsolatot a virtuális géphez.
   
    Válassza ki a virtuális Gépet az Azure portálon. Görgessen lefelé a beállítások panelen a **támogatási + hibaelhárítás** szakasz közelében tétellista aljához. Kattintson a **Resource Health** gombra. A megfelelő virtuális gépek jelenti, hogy **elérhető**:
   
    ![Ellenőrizze a VM erőforrás állapota az Azure-portálon](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés, ha nem ismeri, vagy elfelejtette a hitelesítő adatok helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása.  Miután jelentkezett be a virtuális Gépet, alaphelyzetbe kell, hogy a felhasználó jelszavát.
   
    Válassza ki a virtuális Gépet az Azure portálon. Görgessen lefelé a beállítások panelen a **támogatási + hibaelhárítás** szakasz közelében tétellista aljához. Kattintson a **jelszó-átállítási** gombra. Adja meg a felhasználónevet és egy új jelszót. Végül kattintson a **mentése** gombra:
   
    ![Az Azure-portálon a felhasználói hitelesítő adatok alaphelyzetbe állítása](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés kijavíthatja a mögöttes probléma merül fel a virtuális gépért nehézségekkel.
   
    Válassza ki a virtuális Gépet az Azure portálon, és kattintson a **áttekintése** fülre. Kattintson a **indítsa újra a** gombra:
   
    ![Indítsa újra a virtuális Gépet az Azure-portálon](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Győződjön meg arról, hogy azokat a helyi tűzfal, illetve a számítógép tűzfala lehetővé teszi az Azure kimenő 3389-es TCP-forgalom.

Ha RDP problémák továbbra is találkozik, akkor [támogatási kérést nyithat](https://azure.microsoft.com/support/options/) vagy olvasási [RDP hibaelhárítással kapcsolatos fogalmak és a lépések részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Adott RDP-hibák elhárítása
A virtuális gép RDP-kapcsolaton keresztül csatlakozni próbáló során felmerülő specifikus hibaüzenet. A leggyakoribb hibaüzenetek a következők:

* [A távoli munkamenet meg lett szakítva, mert nincs távoli asztali licenckiszolgálókat kínálnak az licenc](troubleshoot-specific-rdp-errors.md#rdplicense).
* [A távoli asztal nem találja a számítógépen "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows biztonsági hiba: A hitelesítő adatok nem működött](troubleshoot-specific-rdp-errors.md#wincred).
* [Ez a számítógép nem tud kapcsolódni a távoli számítógép](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>További források
Ha ezek a hibák egyike sem történt, és Ön továbbra sem sikerül csatlakozni a virtuális gép távoli asztalon keresztül, olvassa el a részletes [hibaelhárítási útmutatója a távoli asztal](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Című témakörben leírt lépéseket a virtuális gép futó alkalmazásokhoz való hozzáférés [egy Azure virtuális gépen futó alkalmazáshoz való hozzáférés hibáinak elhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ha a Secure Shell (SSH) használatával szeretne csatlakozni egy Linux virtuális Gépet az Azure-ban, olvassa el a problémákat [hibaelhárítása SSH kapcsolatok egy Linux virtuális Gépet az Azure-ban](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

