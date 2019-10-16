---
title: Rendszerhéj-parancsfájlok futtatása Linux rendszerű virtuális gépen az Azure-ban
description: Ez a témakör azt ismerteti, hogyan futtathat parancsfájlokat egy Azure-beli linuxos virtuális gépen a Run paranccsal
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6550b6e3f59ff7e6bac39dfc1abcf829256122d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376352"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Rendszerhéj-parancsfájlok futtatása a Linux rendszerű virtuális gépen a Run paranccsal

A Run parancs a virtuálisgép-ügynök használatával futtatja a rendszerhéj-parancsfájlokat egy Azure Linux rendszerű virtuális gépen. Ezek a parancsfájlok használhatók a gépek és az alkalmazások felügyeletére, valamint a virtuális gép hozzáférési és hálózati problémáinak gyors diagnosztizálására és szervizelésére, valamint a virtuális gép megfelelő állapotba való visszaállítására is.

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez több lehetőség is használható. A Run paranccsal távolról futtathat parancsfájlokat a virtuális gépeken a virtuálisgép-ügynök használatával. A Run parancs a Linux rendszerű virtuális gépekhez készült Azure Portalon, [Rest APIon](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)vagy [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) -n keresztül is használható.

Ez a képesség minden olyan forgatókönyv esetében hasznos, ahol parancsfájlt szeretne futtatni egy virtuális gépen belül, és az egyik az egyetlen módja annak, hogy az RDP-vagy SSH-porttal nem rendelkező virtuális gépeket nem lehet megnyitni, mert nem megfelelő hálózati vagy rendszergazda felhasználó Configuration.

## <a name="restrictions"></a>Korlátozások

Az alábbi lista a Run parancs használatakor megjelenő korlátozásokat sorolja fel.

* A kimenet az utolsó 4096 bájtra korlátozódik
* A parancsfájl futtatásának minimális ideje 20 másodpercnél
* Alapértelmezés szerint a parancsfájlok a Linuxon emelt szintű felhasználóként futnak
* Egyszerre csak egy parancsfájl futhat
* Az adatokat kérő parancsfájlok (interaktív mód) nem támogatottak.
* Futó parancsfájlt nem lehet megszakítani
* A parancsfájlok futtatásának maximális ideje 90 perc, ami után időtúllépés történik
* A parancsfájl eredményeinek visszaküldéséhez a virtuális gépről kimenő kapcsolat szükséges.

> [!NOTE]
> A megfelelő működéshez a futtatási parancshoz kapcsolat szükséges (443-es port) az Azure nyilvános IP-címeihez. Ha a bővítmény nem fér hozzá ezekhez a végpontokhoz, a parancsfájlok sikeresen futhatnak, de nem adhatják vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, a [szolgáltatási címkék](../../virtual-network/security-overview.md#service-tags) használatával engedélyezheti az Azure-beli nyilvános IP-címekre irányuló forgalmat az `AzureCloud` címkével.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Linux rendszerű virtuális gépekhez elérhető parancsok listáját jeleníti meg. A **RunShellScript** parancs a kívánt egyéni szkriptek futtatására is használható. Ha az Azure CLI vagy a PowerShell használatával futtat egy parancsot, a `--command-id` vagy `-CommandId` paraméterhez megadott értéknek az alábbi értékek egyikének kell lennie. Ha olyan értéket ad meg, amely nem érhető el, akkor a rendszer hibaüzenetet küld.

```error
The entity was not found in this Azure location
```

|**Name (Név)**|**Leírás**|
|---|---|
|**RunShellScript**|Végrehajt egy linuxos rendszerhéj-parancsfájlt.|
|**ifconfig**| Az összes hálózati adapter konfigurációjának beolvasása.|

## <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az alábbi példa az az [VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) paranccsal futtat egy rendszerhéj-parancsfájlt egy Azure Linux rendszerű virtuális gépen.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Ha más felhasználóként szeretné futtatni a parancsokat, a `sudo -u` használatával megadhatja a használni kívánt felhasználói fiókot.

## <a name="azure-portal"></a>Azure Portal

Navigáljon egy virtuális gépre az [Azure](https://portal.azure.com) -ban, és válassza a **Futtatás parancsot** a **műveletek**területen. Megjelenik a virtuális gépen futtatandó elérhető parancsok listája.

![Parancsok futtatása](./media/run-command/run-command-list.png)

Válassza ki a futtatandó parancsot. Egyes parancsok opcionális vagy kötelező bemeneti paramétereket tartalmazhatnak. Ezen parancsok esetében a paraméterek szövegmezőként jelennek meg a bemeneti értékek megadásához. Minden parancsnál megtekintheti a Futtatás alatt álló parancsfájlt a **megtekintési parancsfájl**kibontásával. A **RunShellScript** eltér a többi parancstól, mivel lehetővé teszi a saját egyéni parancsfájl megadását.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

A parancs kiválasztása után kattintson a **Futtatás** gombra a szkript futtatásához. A szkript lefut, és ha elkészült, a kimenetet és az esetleges hibákat adja vissza a kimeneti ablakban. Az alábbi képernyőfelvételen az **ifconfig** parancs futtatásának kimenete látható.

![Parancsfájl kimenetének futtatása](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Az alábbi példa egy Azure-beli virtuális gépen futtatott PowerShell-szkript futtatását szemlélteti a [meghívó-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával. A parancsmag elvárja, hogy a `-ScriptPath` paraméterben hivatkozott parancsfájl helyi legyen a parancsmag futtatásának helyétől.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>A futtatási parancshoz való hozzáférés korlátozása

A futtatási parancsok listázása vagy a parancs részleteinek megjelenítéséhez a `Microsoft.Compute/locations/runCommands/read` engedélyre van szükség az előfizetés szintjén, amely a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör és a magasabb.

A parancs futtatásához a `Microsoft.Compute/virtualMachines/runCommand/action` engedélyre van szükség az előfizetés szintjén, amely a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepköre, és magasabb.

Használhatja a [beépített](../../role-based-access-control/built-in-roles.md) szerepkörök egyikét, vagy létrehozhat egy [Egyéni](../../role-based-access-control/custom-roles.md) szerepkört a futtatási parancs használatához.

## <a name="next-steps"></a>Következő lépések

Lásd: [parancsfájlok futtatása a Linux rendszerű virtuális gépen](run-scripts-in-vm.md) a parancsfájlok és parancsok távoli futtatásának más módjairól a virtuális gépen.
