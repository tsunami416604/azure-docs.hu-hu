---
title: PowerShell-parancsfájlok futtatása Windows virtuális gépen az Azure-ban
description: Ez a témakör azt ismerteti, hogyan futtatható a PowerShell-parancsfájlok egy Azure Windows virtuális gépen a Parancs futtatása szolgáltatás sal
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749228"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>PowerShell-parancsfájlok futtatása a Windows virtuális gépben a Run Command paranccsal

A Parancs futtatása szolgáltatás a virtuális gép (VM) ügynök e PowerShell-parancsfájlok futtatásához egy Azure Windows virtuális gépen. Ezeket a parancsfájlokat használhatja az általános gép- vagy alkalmazáskezeléshez. Segítségével gyorsan diagnosztizálhatja és orvosolhatja a virtuális gép hozzáférését és a hálózati problémákat, és visszajuttathatja a virtuális gép jó állapotba.

 

## <a name="benefits"></a>Előnyök

A virtuális gépek többféleképpen érhetők el. A Run Command parancsfájlokat távolról futtathat a virtuális gépeken a virtuális gép ügynökhasználatával. A Parancs futtatása az Azure Portalon, a [REST API-n](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)vagy a Windows-alapú [Virtuális gépekhez való PowerShell-en](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) keresztül használható.

Ez a funkció minden olyan esetben hasznos, amikor egy virtuális gépen belül parancsfájlt szeretne futtatni. Ez az egyetlen módja a hibaelhárításnak és a virtuális gépek kiújulásának, amelyek nem rendelkeznek az RDP vagy Az SSH port nem megfelelő hálózati vagy felügyeleti felhasználói konfiguráció miatt meg nyitva.

## <a name="restrictions"></a>Korlátozások

A Futtatás parancs használatakor a következő korlátozások érvényesek:

* A kimenet az utolsó 4096 bájtra korlátozódik.
* A parancsfájl futtatásához szükséges minimális idő körülbelül 20 másodperc.
* A parancsfájlok Rendszerként futnak a Windows rendszeren.
* Egyszerre csak egy parancsfájl futhat.
* Az információt (interaktív mód) felhasználó parancsfájlok nem támogatottak.
* Futó parancsfájl nem szakítható meg.
* A parancsfájl oka legfeljebb 90 perc. Azután, akkor időtúl.
* Kimenő kapcsolat a virtuális gép szükséges a parancsfájl eredményének visszaadása.

> [!NOTE]
> A megfelelő működéshez a Parancs futtatása szükséges kapcsolatot (443-as port) az Azure nyilvános IP-címek. Ha a bővítmény nem fér hozzá ezekhez a végpontokhoz, a parancsfájlok futtatása sikereslehet, de nem adja vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, [a szolgáltatás címkék](../../virtual-network/security-overview.md#service-tags) használatával engedélyezheti a `AzureCloud` forgalmat az Azure nyilvános IP-címek a címke használatával.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Windows virtuális gépekhez elérhető parancsok listáját tartalmazza. A **RunPowerShellScript** paranccsal tetszőleges egyéni parancsfájlfuttatásához használhatja. Ha az Azure CLI vagy a PowerShell használatával fut egy parancs, `--command-id` `-CommandId` a megadott értéket a vagy paraméter nek az alábbi értékek egyikének kell lennie. Ha olyan értéket ad meg, amely nem elérhető parancs, a következő hibaüzenet jelenik meg:

```error
The entity was not found in this Azure location
```

|**Név**|**Leírás**|
|---|---|
|**RunPowerShellScript**|Egy PowerShell-parancsfájlt futtat.|
|**EnableRemotePS**|A távoli PowerShell engedélyezéséhez konfigurálja a számítógépet.|
|**EnableAdminAccount**|Ellenőrzi, hogy a helyi rendszergazdai fiók le van-e tiltva, és ha igen, engedélyezi-e.|
|**Ipconfig**| A TCP/IP-hez kötött adapterek IP-címére, alhálózati maszkjára és alapértelmezett átjárójára vonatkozó részletes információkat jeleníti meg.|
|**RDP-beállítások**|Ellenőrzi a beállításjegyzék- és tartományi házirend-beállításokat. Házirend-műveleteket javasol, ha a gép egy tartomány része, vagy módosítja a beállításokat az alapértelmezett értékekre.|
|**ResetRDPCert**|Eltávolítja az RDP-figyelőhöz kötött SSL-tanúsítványt, és visszaállítja az RDP-figyelő biztonságát az alapértelmezettre. Akkor használja ezt a parancsfájlt, ha bármilyen problémát lát a tanúsítvánnyal kapcsolatban.|
|**SetRDPPort**|Beállítja a Távoli asztali kapcsolatok alapértelmezett vagy felhasználó által megadott portszámát. Tűzfalszabályok engedélyezése a porthoz való bejövő hozzáféréshez.|

## <a name="azure-cli"></a>Azure CLI

A következő példa az [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) parapara ncit használ egy rendszerhéj-parancsfájl futtatásához egy Azure Windows virtuális gép.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure portál

Lépjen egy virtuális gépre az [Azure Portalon,](https://portal.azure.com) és válassza a **Futtatás parancsot** **az OPERATIONS csoportban.** A virtuális gépen futtatandó elérhető parancsok listája jelenik meg.

![Parancsok listája](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancsot. Előfordulhat, hogy egyes parancsok nem kötelező vagy kötelező bemeneti paramétereket tartalmaznak. Ezeknél a parancsoknál a paraméterek szövegmezőkként jelennek meg a bemeneti értékek megadásához. Az egyes parancsokhoz a Nézet parancsfájl kibontásával tekintheti meg a futtatott **parancsfájlt.** **RunPowerShellScript** eltér a többi parancs, mert lehetővé teszi, hogy saját egyéni parancsfájlt.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

Miután kiválasztotta a parancsot, a Parancsfájl futtatásához válassza a **Futtatás** lehetőséget. A parancsfájl befejezése után visszaadja a kimenetet és a kimeneti ablakban előforduló hibákat. A következő képernyőképen az **RDPSettings** parancs futtatásának egy példakimenete látható.

![Parancsparancsfájl-kimenet futtatása](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

A következő példa az [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával egy PowerShell-parancsfájl futtatásához egy Azure virtuális gép. A parancsmag arra számít, hogy `-ScriptPath` a paraméterben hivatkozott parancsfájl helyi legyen a parancsmag futtatásának helyéhez.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Hozzáférés korlátozása a Run Command parancshoz

A futtatási parancsok listázása vagy `Microsoft.Compute/locations/runCommands/read` a parancs részleteinek megjelenítése az előfizetés szintjén szükséges engedélyszükséges. A beépített [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör és a magasabb szintek rendelkeznek ezzel az engedéllyel.

A parancs futtatásához az `Microsoft.Compute/virtualMachines/runCommand/action` előfizetés szintjén szükséges az engedély. A [Virtuálisgép közreműködőszerepkör](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és a magasabb szintű rendelkezik ezzel az engedéllyel.

Használhatja a [beépített szerepkörök egyikét,](../../role-based-access-control/built-in-roles.md) vagy létrehozhat egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md) a Run Command használatához.

## <a name="next-steps"></a>További lépések

A parancsfájlok és parancsok távoli futtatásának egyéb módjairól a virtuális gépben [parancsfájlok futtatása](run-scripts-in-vm.md)című témakörben olvashat.
