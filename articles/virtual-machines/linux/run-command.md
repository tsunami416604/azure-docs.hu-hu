---
title: Rendszerhéj-parancsfájlok futtatása egy Linux virtuális gép az Azure-on
description: Ez a témakör ismerteti az Azure Linux virtuális gépekről futtatása paranccsal parancsfájlok futtatása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6f21452ddc6c8a48392d24615e8dbcbba8b996c8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660950"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Futtassa a Linux virtuális gép PowerShell parancsfájloknál ad-parancs futtatása

Futtatási parancs lehetővé teszi, hogy az Azure Linux virtuális gép hálózati kapcsolat függetlenül belül rendszerhéj-parancsfájlok futtatását. Ezek a parancsfájlok vagy általános számítógép-felügyelet használható, és gyorsan diagnosztizálhatja és virtuális gép hozzáférés és a hálózati problémák megoldásához és a virtuális gép egy jó állapotra is használható.

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez használható több lehetőség áll rendelkezésre. Futtatási parancs parancsfájlok futtathatók a virtuális gépeken, függetlenül a hálózati kapcsolatot, és (nem kötelező telepítés) alapértelmezés szerint mindig elérhető. Futtatási parancs is használható az Azure portálon keresztül [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), vagy [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Ez a funkció akkor hasznos, az összes olyan esetek, amikor szeretné futtatni a parancsprogram witin a virtuális gépek, így egyik hibaelhárítása, és javíthatja a virtuális gép, amely nem csatlakozik a hálózat nem megfelelő hálózati vagy a rendszergazda felhasználó miatt egyetlen módja konfiguráció.

## <a name="restrictions"></a>Korlátozások

Az alábbiakban található parancs futtatása használatakor korlátozásainak listáját.

* Kimeneti korlátozódik utolsó 4096 bájtos
* A parancsfájl futtatása körülbelül 20 másodperc minimális időtartam
* Emelt szintű felhasználói parancsfájlok futtatása Linux rendszeren
* Egyszerre csak egy parancsprogram futtathatnak.
* Nem lehet megszakítani a parancsfájl futtatását
* A maximális egy parancsfájlt ideje 90 perc, azt követően a rendszer túllépi az időkorlátot

## <a name="azure-cli"></a>Azure CLI

Az alábbiakban egy példa segítségével a [az vm Futtatás-parancs](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) héjparancsfájlt egy Azure Linux virtuális gépen futtatandó parancsot.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

## <a name="azure-portal"></a>Azure Portal

Keresse meg a virtuális gép [Azure](https://portal.azure.com) válassza **paranccsal** alatt **műveletek**. A virtuális Gépen futtatandó elérhető parancsok listája jelenik meg.

![A parancs futtatásához](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancs. A parancsok némelyike rendelkezhet opcionális vagy kötelező bemeneti paraméter. Ezeknek a parancsoknak a paraméterek ahhoz, hogy adja meg a bemeneti szöveg mezői jelenjenek meg. Minden egyes parancsnál megtekintheti a kibontásával futtatott parancsfájl **parancsfájl megtekintése**. **RunPowerShellScript** eltér a más parancsok, lehetővé teszi a saját egyéni parancsfájl. 

> [!NOTE]
> A beépített parancsok, amelyek nem szerkeszthető.

Ha a parancsot választja, kattintson **futtatása** a parancsfájl futtatásához. A parancsfájl futtatása, és amikor végzett, és adja vissza a kimeneti ki a hibákat a kimeneti ablakban. Az alábbi képernyőfelvételen látható egy példa a kimenetre futtatását a **ifconfig** parancsot.

![Parancsfájl kimeneti parancs futtatása](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>A használható

Az alábbi táblázatban a rendelkezésre álló parancsok listájához, a Linux virtuális gépekhez. A **RunShellScript** parancs használható a kívánt egyéni parancsfájl futtatásához.

|**Name (Név)**|**Leírás**|
|---|---|
|**RunShellScript**|A Linux-parancsfájl végrehajtása.|
|**ifconfig**| Minden hálózati interfészen konfigurációjának beolvasása.|

## <a name="limiting-access-to-run-command"></a>Parancs futtatása való hozzáférés korlátozása

Futtassa a parancsokat listázása, vagy a parancs részletes adatait megjelenítő igényli a `Microsoft.Compute/locations/runCommands/read` engedéllyel, amely a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör és az annál magasabb rendelkezik.

A parancs futtatásához szükség van a `Microsoft.Compute/virtualMachines/runCommand/action` engedéllyel, amely a [közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör és az annál magasabb rendelkezik.

Egyikét használhatja a [beépített](../../role-based-access-control/built-in-roles.md) szerepköröket, vagy hozzon létre egy [egyéni](../../role-based-access-control/custom-roles.md) szerepkört használja a parancs futtatása.

## <a name="next-steps"></a>További lépések

Megtekintéséhez [a Linux virtuális gép található parancsfájlok futtatásának](run-scripts-in-vm.md) tájékozódhat az egyéb módjai parancsprogramok és parancsok a virtuális gép távoli futtatása.
