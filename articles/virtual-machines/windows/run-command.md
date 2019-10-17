---
title: PowerShell-parancsfájlok futtatása Azure-beli Windows rendszerű virtuális gépen
description: Ez a témakör azt ismerteti, hogyan futtathat PowerShell-parancsfájlokat egy Azure Windows rendszerű virtuális gépen a Run paranccsal
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0a9a5e465e160da34a21f66fd7176a8fea5d1aac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376250"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Run paranccsal

A Run parancs a virtuálisgép-ügynök használatával futtat PowerShell-parancsfájlokat egy Azure Windows-alapú virtuális gépen belül. Ezek a parancsfájlok használhatók a gépek és az alkalmazások felügyeletére, valamint a virtuális gép hozzáférési és hálózati problémáinak gyors diagnosztizálására és szervizelésére, valamint a virtuális gép megfelelő állapotba való visszaállítására is.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez több lehetőség is használható. A Run paranccsal távolról futtathat parancsfájlokat a virtuális gépeken a virtuálisgép-ügynök használatával. A Run parancs a Windows rendszerű virtuális gépek Azure Portalon, [Rest APIon](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)vagy [powershellen](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) keresztül is használható.

Ez a képesség minden olyan forgatókönyv esetében hasznos, ahol parancsfájlt szeretne futtatni egy virtuális gépen belül, és az egyik az egyetlen módja annak, hogy az RDP-vagy SSH-porttal nem rendelkező virtuális gépeket nem lehet megnyitni, mert nem megfelelő hálózati vagy rendszergazda felhasználó Configuration.

## <a name="restrictions"></a>Korlátozások

A futtatási parancs használatakor a következő korlátozások érvényesek:

* A kimenet az utolsó 4096 bájtra korlátozódik
* A parancsfájl futtatásának minimális ideje körülbelül 20 másodperc
* Parancsfájlok futtató rendszer Windows rendszeren
* Egyszerre csak egy parancsfájl futhat
* Az adatokat kérő parancsfájlok (interaktív mód) nem támogatottak.
* Futó parancsfájlt nem lehet megszakítani
* A parancsfájlok futtatásának maximális ideje 90 perc, ami után időtúllépés történik
* A parancsfájl eredményeinek visszaküldéséhez a virtuális gépről kimenő kapcsolat szükséges.

> [!NOTE]
> A megfelelő működéshez a futtatási parancshoz kapcsolat szükséges (443-es port) az Azure nyilvános IP-címeihez. Ha a bővítmény nem fér hozzá ezekhez a végpontokhoz, a parancsfájlok sikeresen futhatnak, de nem adhatják vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, a [szolgáltatási címkék](../../virtual-network/security-overview.md#service-tags) használatával engedélyezheti az Azure-beli nyilvános IP-címekre irányuló forgalmat az `AzureCloud` címkével.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Windows rendszerű virtuális gépekhez elérhető parancsok listáját jeleníti meg. A **RunPowerShellScript** parancs a kívánt egyéni szkriptek futtatására is használható. Ha az Azure CLI vagy a PowerShell használatával futtat egy parancsot, a `--command-id` vagy `-CommandId` paraméterhez megadott értéknek az alábbi értékek egyikének kell lennie. Ha olyan értéket ad meg, amely nem érhető el, akkor a rendszer hibaüzenetet küld.

```error
The entity was not found in this Azure location
```

|**Name (Név)**|**Leírás**|
|---|---|
|**RunPowerShellScript**|PowerShell-parancsfájl végrehajtása|
|**EnableRemotePS**|A távoli PowerShell engedélyezését konfigurálja a gépen.|
|**EnableAdminAccount**|Ellenőrzi, hogy a helyi rendszergazdai fiók le van-e tiltva, és ha igen, akkor.|
|**IPConfig**| Az IP-cím, az alhálózati maszk és az alapértelmezett átjáró részletes információit jeleníti meg a TCP/IP-hez kötött egyes adapterek esetében.|
|**RDPSettings**|Ellenőrzi a beállításjegyzék beállításait és a tartományi házirend beállításait. Házirend-műveleteket javasol, ha a számítógép egy tartomány része, vagy módosítja a beállításokat az alapértelmezett értékekre.|
|**ResetRDPCert**|Az RDP-figyelőhöz kötött SSL-tanúsítvány eltávolítása és az RDP-figyelő biztonsági mentése az alapértelmezett értékre. Akkor használja ezt a parancsfájlt, ha a tanúsítvánnyal kapcsolatos problémákat tapasztal.|
|**SetRDPPort**|Beállítja Távoli asztal kapcsolatok alapértelmezett vagy felhasználó által megadott portszámát. Engedélyezi a tűzfalszabályok bejövő hozzáférését a porthoz.|

## <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az alábbi példa az az [VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) paranccsal futtat egy rendszerhéj-parancsfájlt egy Azure Linux rendszerű virtuális gépen.

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

## <a name="azure-portal"></a>Azure Portal

Navigáljon egy virtuális gépre az [Azure](https://portal.azure.com) -ban, és válassza a **Futtatás parancsot** a **műveletek**területen. Megjelenik a virtuális gépen futtatandó elérhető parancsok listája.

![Parancsok futtatása](./media/run-command/run-command-list.png)

Válassza ki a futtatandó parancsot. Egyes parancsok opcionális vagy kötelező bemeneti paramétereket tartalmazhatnak. Ezen parancsok esetében a paraméterek szövegmezőként jelennek meg a bemeneti értékek megadásához. Minden parancsnál megtekintheti a Futtatás alatt álló parancsfájlt a **megtekintési parancsfájl**kibontásával. A **RunPowerShellScript** eltér a többi parancstól, mivel lehetővé teszi a saját egyéni parancsfájl megadását.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

A parancs kiválasztása után kattintson a **Futtatás** gombra a szkript futtatásához. A szkript lefut, és ha elkészült, a kimenetet és az esetleges hibákat adja vissza a kimeneti ablakban. Az alábbi képernyőképen egy példa látható a **RDPSettings** parancs futtatásának eredményéről.

![Parancsfájl kimenetének futtatása](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Az alábbi példa egy Azure-beli virtuális gépen futtatott PowerShell-szkript futtatását szemlélteti a [meghívó-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával. A parancsmag elvárja, hogy a `-ScriptPath` paraméterben hivatkozott parancsfájl helyi legyen a parancsmag futtatásának helyétől.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>A futtatási parancshoz való hozzáférés korlátozása

A futtatási parancsok listázása vagy a parancs részleteinek megjelenítéséhez a `Microsoft.Compute/locations/runCommands/read` engedélyre van szükség az előfizetés szintjén, amely a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör és a magasabb.

A parancs futtatásához a `Microsoft.Compute/virtualMachines/runCommand/action` engedélyre van szükség az előfizetés szintjén, amely a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepköre, és magasabb.

Használhatja a [beépített](../../role-based-access-control/built-in-roles.md) szerepkörök egyikét, vagy létrehozhat egy [Egyéni](../../role-based-access-control/custom-roles.md) szerepkört a futtatási parancs használatához.

## <a name="next-steps"></a>Következő lépések

A szkriptek és parancsok távoli virtuális gépen való futtatásának más módjairól a [parancsfájlok futtatása a Windows rendszerű virtuális gépen](run-scripts-in-vm.md) című témakörben talál további információt.
