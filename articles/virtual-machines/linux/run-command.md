---
title: Rendszerhéj-parancsfájlok futtatása egy Linux virtuális gép az Azure-on
description: Ez a témakör ismerteti az Azure Linux virtuális gépekről futtatása paranccsal parancsfájlok futtatása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a7e828aa79d3a7fba53c0ef9f683ed16afc9a3e6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267458"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Futtassa a Linux virtuális gép PowerShell parancsfájloknál ad-parancs futtatása

Futtassa a parancsot használja a Virtuálisgép-ügynök az Azure Linux virtuális gép belül rendszerhéj-parancsfájlok futtatása. Ezek a parancsfájlok vagy általános számítógép-felügyelet használható, és gyorsan diagnosztizálhatja és virtuális gép hozzáférés és a hálózati problémák megoldásához és a virtuális gép egy jó állapotra is használható.

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez használható több lehetőség áll rendelkezésre. A parancs futtatása távolról használata az ügynököt a virtuális gépek parancsfájlok is futtathatók. Futtatási parancs is használható az Azure portálon keresztül [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), vagy [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Ez a lehetőség akkor hasznos, ha szeretné, hogy egy parancsfájl witin egy virtuális gép futtatásához, és egyik hibaelhárítása, és javíthatja az RDP nem rendelkező virtuális gép egyetlen módja vagy SSH-port nyissa meg a nem megfelelő hálózati vagy a rendszergazda felhasználó miatt az összes forgatókönyv konfiguráció.

## <a name="restrictions"></a>Korlátozások

Az alábbiakban található parancs futtatása használatakor korlátozásainak listáját.

* Kimeneti korlátozódik utolsó 4096 bájtos
* A parancsfájl futtatása körülbelül 20 másodperc minimális időtartam
* Parancsfájlok futtatása alapértelmezés szerint emelt szintű felhasználói Linux rendszeren
* Egyszerre csak egy parancsprogram futtathatnak.
* Nem lehet megszakítani a parancsfájl futtatását
* A maximális egy parancsfájlt ideje 90 perc, azt követően a rendszer túllépi az időkorlátot

## <a name="azure-cli"></a>Azure CLI

Az alábbiakban egy példa segítségével a [az vm Futtatás-parancs](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) héjparancsfájlt egy Azure Linux virtuális gépen futtatandó parancsot.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Egy másik felhasználóként parancsok futtatásához használhatja `sudo -u` használandó felhasználói fiókot szeretne megadni.

## <a name="azure-portal"></a>Azure Portal

Keresse meg a virtuális gép [Azure](https://portal.azure.com) válassza **paranccsal** alatt **műveletek**. A virtuális Gépen futtatandó elérhető parancsok listája jelenik meg.

![A parancs futtatásához](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancs. A parancsok némelyike rendelkezhet opcionális vagy kötelező bemeneti paraméter. Ezeknek a parancsoknak a paraméterek ahhoz, hogy adja meg a bemeneti szöveg mezői jelenjenek meg. Minden egyes parancsnál megtekintheti a kibontásával futtatott parancsfájl **parancsfájl megtekintése**. **RunShellScript** eltér a más parancsok, lehetővé teszi a saját egyéni parancsfájl. 

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
