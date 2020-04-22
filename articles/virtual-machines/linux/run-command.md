---
title: Shell szkriptek futtatása Linux os virtuális gépen az Azure-ban
description: Ez a témakör azt ismerteti, hogyan futtathat parancsfájlokat egy Azure Linux-alapú virtuális gépen a Parancs futtatása szolgáltatás használatával
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758608"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Parancs futtatásához shell szkriptek a Linux virtuális gép

A Parancs futtatása szolgáltatás a virtuális gép (VM) ügynök egy Azure Linux-alapú virtuális gépen belüli rendszerhéj-parancsfájlok futtatásához. Ezeket a parancsfájlokat használhatja az általános gép- vagy alkalmazáskezeléshez. Segítségével gyorsan diagnosztizálhatja és orvosolhatja a virtuális gép hozzáférését és a hálózati problémákat, és visszajuttathatja a virtuális gép jó állapotba.

## <a name="benefits"></a>Előnyök

A virtuális gépek többféleképpen érhetők el. A Run Command parancsfájlokat távolról futtathat a virtuális gépeken a virtuális gép ügynökhasználatával. A Run Command az Azure Portalon, [a REST API-n](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)vagy az [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) Linuxos virtuális gépeken keresztül használható.

Ez a funkció minden olyan esetben hasznos, amikor egy virtuális gépen belül parancsfájlt szeretne futtatni. Ez az egyetlen módja a hibaelhárításnak és a virtuális gépek kiújulásának, amelyek nem rendelkeznek az RDP vagy Az SSH port nem megfelelő hálózati vagy felügyeleti felhasználói konfiguráció miatt meg nyitva.

## <a name="restrictions"></a>Korlátozások

A Futtatás parancs használatakor a következő korlátozások érvényesek:

* A kimenet az utolsó 4096 bájtra korlátozódik.
* A parancsfájl futtatásához szükséges minimális idő körülbelül 20 másodperc.
* A parancsfájlok alapértelmezés szerint emelt szintű felhasználóként futnak Linuxon.
* Egyszerre csak egy parancsfájlt futtathat.
* Az információt (interaktív mód) felhasználó parancsfájlok nem támogatottak.
* Futó parancsfájl nem szakítható meg.
* A parancsfájl oka legfeljebb 90 perc. Ezután a forgatókönyv időbeli marad.
* Kimenő kapcsolat a virtuális gép szükséges a parancsfájl eredményének visszaadása.

> [!NOTE]
> A megfelelő működéshez a Parancs futtatása szükséges kapcsolatot (443-as port) az Azure nyilvános IP-címek. Ha a bővítmény nem fér hozzá ezekhez a végpontokhoz, a parancsfájlok futtatása sikereslehet, de nem adja vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, [a szolgáltatás címkék](../../virtual-network/security-overview.md#service-tags) használatával engedélyezheti a `AzureCloud` forgalmat az Azure nyilvános IP-címek a címke használatával.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Linux virtuális gépekhez elérhető parancsok listáját tartalmazza. A **RunShellScript** paranccsal tetszőleges egyéni parancsfájlfuttatásához használhatja. Ha az Azure CLI vagy a PowerShell használatával fut egy parancs, `--command-id` `-CommandId` a megadott értéket a vagy paraméter nek az alábbi értékek egyikének kell lennie. Ha olyan értéket ad meg, amely nem elérhető parancs, a következő hibaüzenet jelenik meg:

```error
The entity was not found in this Azure location
```

|**Név**|**Leírás**|
|---|---|
|**RunShellScript**|Linux rendszerhéj-parancsfájlt futtat.|
|**ifconfig között**| Leveszi az összes hálózati adapter konfigurációját.|

## <a name="azure-cli"></a>Azure CLI

A következő példa az [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) parapara használatával futtategy rendszerhéj-parancsfájlt egy Azure Linux-alapú virtuális gépen.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Ha a parancsokat másik `sudo -u` felhasználóként szeretné futtatni, írja be a felhasználói fiók megadását.

## <a name="azure-portal"></a>Azure Portal

Lépjen egy virtuális gépre az [Azure Portalon,](https://portal.azure.com) és válassza a **Futtatás parancsot** **az OPERATIONS csoportban.** A virtuális gépen futtatandó elérhető parancsok listája jelenik meg.

![Parancsok listája](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancsot. Előfordulhat, hogy egyes parancsok nem kötelező vagy kötelező bemeneti paramétereket tartalmaznak. Ezeknél a parancsoknál a paraméterek szövegmezőkként jelennek meg a bemeneti értékek megadásához. Az egyes parancsokhoz a Nézet parancsfájl kibontásával tekintheti meg a futtatott **parancsfájlt.** **RunShellScript** eltér a többi parancs, mert lehetővé teszi, hogy saját egyéni parancsfájlt.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

Miután kiválasztotta a parancsot, a Parancsfájl futtatásához válassza a **Futtatás** lehetőséget. A parancsfájl befejezése után visszaadja a kimenetet és a kimeneti ablakban előforduló hibákat. A következő képernyőképen egy példa kimenetfut az **ifconfig** parancsot.

![Parancsparancsfájl-kimenet futtatása](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

A következő példa az [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával egy PowerShell-parancsfájl futtatásához egy Azure virtuális gép. A parancsmag arra számít, hogy `-ScriptPath` a paraméterben hivatkozott parancsfájl helyi legyen a parancsmag futtatásának helyéhez.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Hozzáférés korlátozása a Run Command parancshoz

A futtatási parancsok listázása vagy `Microsoft.Compute/locations/runCommands/read` a parancs részleteinek megjelenítése az előfizetés szintjén szükséges engedélyszükséges. A beépített [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör és a magasabb szintek rendelkeznek ezzel az engedéllyel.

A parancs futtatásához az `Microsoft.Compute/virtualMachines/runCommand/action` előfizetés szintjén szükséges az engedély. A [Virtuálisgép közreműködőszerepkör](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és a magasabb szintű rendelkezik ezzel az engedéllyel.

Használhatja a [beépített szerepkörök egyikét,](../../role-based-access-control/built-in-roles.md) vagy létrehozhat egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md) a Run Command használatához.

## <a name="next-steps"></a>További lépések

A parancsfájlok és parancsok távoli futtatásának egyéb módjairól a virtuális gépben a [Parancsfájlok futtatása a Linux virtuális gépben](run-scripts-in-vm.md)című témakörben olvashat.
