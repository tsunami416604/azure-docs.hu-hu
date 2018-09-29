---
title: Nem tud csatlakozni az Azure-beli Windows virtuális gép RDP-vel |} A Microsoft Docs
description: Hibák elhárításához, ha nem tud csatlakozni a Windows virtuális gép az Azure-ban a távoli asztal
keywords: Távoli asztali hiba, a távoli asztali kapcsolat hiba, nem lehet csatlakozni a virtuális gép távoli asztali hibaelhárítása
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: b73cdc7e55a60fbefc294a54f70daa9b2a11ced1
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452453"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Az Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása
A távoli asztal protokoll (RDP) kapcsolatot a Windows-alapú Azure virtuális gép (VM), és nem fér hozzá a virtuális gép különböző okok miatt meghiúsulhat. A probléma lehet a távoli asztal szolgáltatással a virtuális Gépet, a hálózati kapcsolat vagy a távoli asztali ügyfél a gazdaszámítógépen. Ez a cikk végigvezeti néhány, a leggyakrabban használt módszerek RDP-kapcsolatok problémáinak megoldásához. 

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Gyors hibaelhárítási lépéseket
Hibaelhárítási lépések, után próbáljon újra csatlakozni a virtuális Gépet:

1. Távoli asztal konfigurációjának alaphelyzetbe.
2. Ellenőrizze a hálózati biztonsági csoport szabályai / Cloud Services-végpontok.
3. Tekintse át a VM-konzol naplói.
4. A hálózati adapter a virtuális gép alaphelyzetbe állítása.
5. A VM-erőforrás állapotának ellenőrzése.
6. A virtuális gép jelszó alaphelyzetbe állítása.
7. Indítsa újra a virtuális Gépet.
8. A virtuális gép ismételt üzembe.

Olvassa tovább, ha részletes lépéseket és magyarázatok van szüksége. Győződjön meg arról, hogy helyi hálózati berendezések, mint például az útválasztók és tűzfalak nem korlátozzák-e kimenő TCP-port 3389-es, amint [hibakeresésekhez RDP részletes](detailed-troubleshoot-rdp.md).

> [!TIP]
> Ha a **Connect** gombra a virtuális gép szürkén jelenik meg a portálon, és nem csatlakozik az Azure- [Express Route](../../expressroute/expressroute-introduction.md) vagy [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) kapcsolatot szeretne létrehozni és hozzárendelése a nyilvános IP-címet a virtuális gép RDP használata előtt. További információ: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>RDP-hibáinak elhárítása módjai
Háríthatóak el a virtuális gépek létrehozása a Resource Manager üzemi modell használatával a következő módszerek egyikével:

* [Az Azure portal](#using-the-azure-portal) – nagyszerű, ha szeretne gyorsan az RDP vagy felhasználói hitelesítő adatok alaphelyzetbe állítása, és nem rendelkezik Azure-eszközök telepítve.
* [Az Azure PowerShell](#using-azure-powershell) – Ha egy PowerShell-parancssort teljesebb gyorsan az RDP vagy felhasználói hitelesítő adatok alaphelyzetbe állítása az Azure PowerShell-parancsmagok használatával.

A használatával létrehozott virtuális gépek hibaelhárítási lépéseket is megkeresheti a [klasszikus üzemi modellt](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Hibaelhárítás az Azure portal használatával
Hibaelhárítási lépések, után próbáljon csatlakozni a virtuális gép újra. Ha még nem sikerül, próbálja meg a következő lépéssel.

1. **Az RDP-kapcsolat alaphelyzetbe állítása**. Ebben a lépésben hibaelhárítási távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfal-szabályok például forgalomszűrők blokkolják a RDP, ha alaphelyzetbe állítja a RDP-konfigurációja.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Görgessen lefelé a beállítások ablaktáblában a **támogatás + hibaelhárítás** a lista alján találgató szakaszban. Kattintson a **jelszó alaphelyzetbe állítása** gombra. Állítsa be a **mód** való **csak a konfiguráció alaphelyzetbe állítása** és kattintson a **frissítés** gombra:
   
    ![Az Azure Portalon az RDP-konfiguráció visszaállítása](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Ellenőrizze a hálózati biztonsági csoport szabályai**. Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. Emellett áttekintheti az érvényben lévő biztonsági csoport szabályai annak biztosítása érdekében a bejövő "Engedélyezés" NSG-t a szabály létezik, és van priorizálva (alapértelmezés: 3389) RDP-portra. További információkért lásd: [használatával érvényes biztonsági szabályokat a virtuális gép forgalom áramlása](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Tekintse át a virtuális gép rendszerindítási diagnosztika**. Ez a hibaelhárítási lépés áttekinti a VM-konzol naplói meghatározni, ha a virtuális Gépet jelent problémát. Nem minden virtuális gép rendszerindítási diagnosztika engedélyezve van, rendelkezik, ezért lehet, hogy ez a hibaelhárítási lépés nem kötelező.
   
    Bizonyos hibaelhárítási lépések e cikk kereteit, de van hatással az RDP-kapcsolatot, szélesebb körű problémájára utalhat. A konzol naplói és a virtuális gép képernyőfelvételén áttekintésével további információkért lásd: [a rendszerindítási diagnosztika a virtuális gépek](boot-diagnostics.md).

4. **A hálózati adapter alaphelyzetbe állítása a virtuális gép**. További információkért lásd: [Azure Windows virtuális gép alaphelyzetbe állítása a hálózati adapter](../windows/reset-network-interface.md).
5. **A VM-erőforrás állapotának ellenőrzése**. Hibaelhárítási ebben a lépésben ellenőrzi, hogy nincsenek ismert problémák és az Azure platform, amely hatással lehet a virtuális gép kapcsolatának.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Görgessen lefelé a beállítások ablaktáblában a **támogatás + hibaelhárítás** a lista alján találgató szakaszban. Kattintson a **a Resource health** gombra. Kifogástalan állapotú virtuális gép jelenti, hogy **elérhető**:
   
    ![Az Azure Portalon a virtuális gép erőforrás állapotának ellenőrzése](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés egy helyi rendszergazdai fiók jelszavát alaphelyzetbe állítása, ha nem biztos benne, vagy elfelejtette a hitelesítő adatokat.  Miután bejelentkezett a virtuális gép, vissza kell állítania, hogy a felhasználó jelszavát.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Görgessen lefelé a beállítások ablaktáblában a **támogatás + hibaelhárítás** a lista alján találgató szakaszban. Kattintson a **jelszó alaphelyzetbe állítása** gombra. Győződjön meg arról, hogy a **mód** értékre van állítva **jelszó alaphelyzetbe állítása** és adja meg a felhasználónevet és a egy új jelszót. Végül kattintson a **frissítés** gombra:
   
    ![Az Azure Portalon a felhasználói hitelesítő adatok alaphelyzetbe állítása](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés javíthatja ki minden olyan alapul szolgáló problémák merülnek fel a virtuális gépre.
   
    Válassza ki a virtuális gép az Azure Portalon, és kattintson a **áttekintése** fülre. Kattintson a **indítsa újra a** gombra:
   
    ![Indítsa újra a virtuális gép az Azure Portalon](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **A virtuális gép ismételt üzembe**. Ez a hibaelhárítási lépés újbóli üzembe helyezése bármely alapul szolgáló platform vagy hálózati problémák megoldására az Azure-on belül egy másik gazdagépre a virtuális gép.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Görgessen lefelé a beállítások ablaktáblában a **támogatás + hibaelhárítás** a lista alján találgató szakaszban. Kattintson a **ismételt üzembe helyezése** gombra, majd **ismételt üzembe helyezése**:
   
    ![Az Azure Portalon a virtuális gép újratelepítése](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Ez a művelet befejezését követően elveszik az ideiglenes lemez adatait, és dinamikus IP-címek a virtuális Géphez társított frissülnek.

9. **Ellenőrizze az útválasztást**. Network Watcher használatát [a következő Ugrás](../../network-watcher/network-watcher-check-next-hop-portal.md) teszi, hogy erősítse meg, hogy egy útvonal forgalom nem megakadályozza, hogy vagy a virtuális gépről. Érvényes útvonalak a hálózati adapter érvényes útvonalai tekintse át is. További információkért lásd: [érvényes útvonalak használata virtuális gépek forgalom áramlása](../../virtual-network/diagnose-network-routing-problem.md).

10. Győződjön meg arról, hogy bármilyen helyszíni tűzfalon, vagy a számítógép tűzfala engedélyezi-e a kimenő 3389-es TCP-forgalom az Azure-bA.

Ha továbbra is problémái vannak az RDP, [nyisson egy támogatási kérést](https://azure.microsoft.com/support/options/) vagy olvasása [fogalmakkal és lépésekkel hibaelhárítás az RDP részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Hibaelhárítás az Azure PowerShell-lel
Ha még nem tette, [a legújabb Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

Az alábbi példák a változókkal például `myResourceGroup`, `myVM`, és `myVMAccessExtension`. A változó nevét és helyét. cserélje le a saját értékeire.

> [!NOTE]
> A felhasználói hitelesítő adatok és az RDP-konfigurációjának visszaállítása használatával a [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-parancsmagot. A következő példákban `myVMAccessExtension` a folyamat részeként megadott név. Ha előzőleg dolgozott a VMAccessAgent, a meglévő bővítmény nevét beszerezheti a `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` a virtuális gép tulajdonságainak ellenőrzése. Tekintse meg a nevet, tekintse meg a kimeneti "Bővítmények" szakaszában.

Hibaelhárítási lépések, után próbáljon csatlakozni a virtuális gép újra. Ha még nem sikerül, próbálja meg a következő lépéssel.

1. **Az RDP-kapcsolat alaphelyzetbe állítása**. Ebben a lépésben hibaelhárítási távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfal-szabályok például forgalomszűrők blokkolják a RDP, ha alaphelyzetbe állítja a RDP-konfigurációja.
   
    Az alábbi példa alaphelyzetbe állítja az RDP-kapcsolat a virtuális gép nevű `myVM` a a `WestUS` helye és nevű erőforráscsoportban `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Ellenőrizze a hálózati biztonsági csoport szabályai**. Hibaelhárítási ebben a lépésben ellenőrzi, hogy rendelkezik-e a szabály a hálózati biztonsági csoportot, hogy engedélyezze az RDP-forgalmat a. Az alapértelmezett port, RDP a 3389-es TCP-port az. Egy szabályt, amely engedélyezi az RDP-forgalmat előfordulhat, hogy nem hozhatók létre automatikusan a virtuális gép létrehozásakor.
   
    Első lépésként rendelje hozzá a konfigurációs adatokat a hálózati biztonsági csoport a `$rules` változó. Az alábbi példa a hálózati biztonsági csoport nevű információt szerez `myNetworkSecurityGroup` az erőforráscsoport neve `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Most megtekintheti a szabályokat, amelyek a hálózati biztonsági csoporthoz vannak konfigurálva. Gondoskodjon arról, hogy a szabály a 3389-es TCP-port bejövő kapcsolatok engedélyezése a következő:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Az alábbi példa bemutatja egy érvényes biztonsági szabályt, amely lehetővé teszi az RDP-forgalmat. Látható `Protocol`, `DestinationPortRange`, `Access`, és `Direction` megfelelően vannak konfigurálva:
   
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
   
    Ha nem rendelkezik egy RDP-forgalmat engedélyező szabállyal [hozzon létre egy hálózati biztonsági csoport szabálya](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Engedélyezi a 3389-es TCP-porton.
3. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés a helyi rendszergazdai fiókot, ha biztos abban, vagy elfelejtette a hitelesítő adatokat adja meg a jelszó alaphelyzetbe állítása.
   
    Először adja meg a felhasználónevet és a egy új jelszót rendelhet a hitelesítő adatokat a `$cred` változót az alábbiak szerint:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Most frissítse a hitelesítő adatokat a virtuális Gépen. Az alábbi példa frissíti a hitelesítő adatokat a virtuális gép nevű `myVM` a a `WestUS` helye és nevű erőforráscsoportban `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés javíthatja ki minden olyan alapul szolgáló problémák merülnek fel a virtuális gépre.
   
    A következő példa újraindítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **A virtuális gép ismételt üzembe**. Ez a hibaelhárítási lépés újbóli üzembe helyezése bármely alapul szolgáló platform vagy hálózati problémák megoldására az Azure-on belül egy másik gazdagépre a virtuális gép.
   
    Az alábbi példa ismét üzembe helyezi a virtuális gép nevű `myVM` a a `WestUS` helye és nevű erőforráscsoportban `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Ellenőrizze az útválasztást**. Network Watcher használatát [a következő Ugrás](../../network-watcher/network-watcher-check-next-hop-portal.md) teszi, hogy erősítse meg, hogy egy útvonal forgalom nem megakadályozza, hogy vagy a virtuális gépről. Érvényes útvonalak a hálózati adapter érvényes útvonalai tekintse át is. További információkért lásd: [érvényes útvonalak használata virtuális gépek forgalom áramlása](../../virtual-network/diagnose-network-routing-problem.md).

7. Győződjön meg arról, hogy bármilyen helyszíni tűzfalon, vagy a számítógép tűzfala engedélyezi-e a kimenő 3389-es TCP-forgalom az Azure-bA.

Ha továbbra is problémái vannak az RDP, [nyisson egy támogatási kérést](https://azure.microsoft.com/support/options/) vagy olvasása [fogalmakkal és lépésekkel hibaelhárítás az RDP részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>A klasszikus üzemi modellel létrehozott virtuális gépek hibaelhárítása
Hibaelhárítási lépések, után próbáljon újra csatlakozni a virtuális Gépet.

1. **Az RDP-kapcsolat alaphelyzetbe állítása**. Ebben a lépésben hibaelhárítási távoli kapcsolatok le vannak tiltva, vagy a Windows tűzfal-szabályok például forgalomszűrők blokkolják a RDP, ha alaphelyzetbe állítja a RDP-konfigurációja.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Kattintson a **... További** gombra, majd kattintson a **távelérés visszaállítása**:
   
    ![Az Azure Portalon az RDP-konfiguráció visszaállítása](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Ellenőrizze a Cloud Services végpontjainak**. Hibaelhárítási ebben a lépésben ellenőrzi, hogy rendelkezik-e végpontok az felhőszolgáltatásokat, hogy engedélyezze az RDP-forgalmat. Az alapértelmezett port, RDP a 3389-es TCP-port az. Egy szabályt, amely engedélyezi az RDP-forgalmat előfordulhat, hogy nem hozhatók létre automatikusan a virtuális gép létrehozásakor.
   
   Válassza ki a virtuális Gépet az Azure Portalon. Kattintson a **végpontok** gombra a virtuális gép jelenleg konfigurált végpontok megtekintéséhez. Győződjön meg arról, hogy a végpontok léteznek, amelyek lehetővé teszik az RDP-forgalmat a 3389-es TCP-porton.
   
   Az alábbi példa érvényes végpontok, amelyek lehetővé teszik az RDP-forgalmat jeleníti meg:
   
   ![Ellenőrizze a Cloud Services végpontok az Azure Portalon](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Ha nem rendelkezik egy végpontot, amely lehetővé teszi, hogy RDP-forgalmat, [hozzon létre egy Cloud Services végpontot](../windows/classic/setup-endpoints.md). Magánhálózati port 3389-es TCP engedélyezése.
3. **Tekintse át a virtuális gép rendszerindítási diagnosztika**. Ez a hibaelhárítási lépés áttekinti a VM-konzol naplói meghatározni, ha a virtuális Gépet jelent problémát. Nem minden virtuális gép rendszerindítási diagnosztika engedélyezve van, rendelkezik, ezért lehet, hogy ez a hibaelhárítási lépés nem kötelező.
   
    Bizonyos hibaelhárítási lépések e cikk kereteit, de van hatással az RDP-kapcsolatot, szélesebb körű problémájára utalhat. A konzol naplói és a virtuális gép képernyőfelvételén áttekintésével további információkért lásd: [a rendszerindítási diagnosztika a virtuális gépek](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **A VM-erőforrás állapotának ellenőrzése**. Hibaelhárítási ebben a lépésben ellenőrzi, hogy nincsenek ismert problémák és az Azure platform, amely hatással lehet a virtuális gép kapcsolatának.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Görgessen lefelé a beállítások ablaktáblában a **támogatás + hibaelhárítás** a lista alján találgató szakaszban. Kattintson a **Resource Health** gombra. Kifogástalan állapotú virtuális gép jelenti, hogy **elérhető**:
   
    ![Az Azure Portalon a virtuális gép erőforrás állapotának ellenőrzése](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Felhasználói hitelesítő adatok alaphelyzetbe állítása**. Ez a hibaelhárítási lépés állítja vissza, ha nem biztos benne, vagy elfelejtette a hitelesítő adatokat adja meg a helyi rendszergazdai fiók jelszavát.  Miután bejelentkezett a virtuális gép, vissza kell állítania, hogy a felhasználó jelszavát.
   
    Válassza ki a virtuális Gépet az Azure Portalon. Görgessen lefelé a beállítások ablaktáblában a **támogatás + hibaelhárítás** a lista alján találgató szakaszban. Kattintson a **jelszó alaphelyzetbe állítása** gombra. Adja meg a felhasználónevet és a egy új jelszót. Végül kattintson a **mentése** gombra:
   
    ![Az Azure Portalon a felhasználói hitelesítő adatok alaphelyzetbe állítása](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Indítsa újra a virtuális gép**. Ez a hibaelhárítási lépés javíthatja ki minden olyan alapul szolgáló problémák merülnek fel a virtuális gépre.
   
    Válassza ki a virtuális gép az Azure Portalon, és kattintson a **áttekintése** fülre. Kattintson a **indítsa újra a** gombra:
   
    ![Indítsa újra a virtuális gép az Azure Portalon](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Győződjön meg arról, hogy bármilyen helyszíni tűzfalon, vagy a számítógép tűzfala engedélyezi-e a kimenő 3389-es TCP-forgalom az Azure-bA.

Ha továbbra is problémái vannak az RDP, [nyisson egy támogatási kérést](https://azure.microsoft.com/support/options/) vagy olvasása [fogalmakkal és lépésekkel hibaelhárítás az RDP részletes](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Konkrét RDP-hibáinak elhárítása
Egy adott hibaüzenetet kaphatja meg, a virtuális gép RDP-n keresztül való kapcsolódás során. A leggyakoribb hibaüzenetek a következők:

* [A távoli munkamenet megszakadt, mert nem találhatók távoli asztali licenckiszolgáló kiszolgálók érhető el, amely licencet biztosíthatna](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Távoli asztal nem találja a számítógép "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows biztonsági hiba: nem működött a hitelesítő adatok](troubleshoot-specific-rdp-errors.md#wincred).
* [A számítógép nem lehet kapcsolódni a távoli számítógép](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>További források
Ha ezek a hibák egyike sem történt, és még mindig nem tud csatlakozni a virtuális gép távoli asztalon keresztül, olvassa el a részletes [hibaelhárítási útmutató a távoli asztal](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Című témakörben leírt lépéseket egy virtuális gépen futó alkalmazásokhoz való hozzáférésüket [-beli virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ha problémái vannak az Azure-beli Linuxos virtuális gép kapcsolódni, lásd: Secure Shell (SSH) használatával [hibaelhárítása SSH kapcsolatok az Azure-beli Linuxos VM](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


