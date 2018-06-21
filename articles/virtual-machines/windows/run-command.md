---
title: Egy Windows Azure-ban a PowerShell-parancsfájlok futtatása
description: Ez a témakör ismerteti egy parancs futtatása Azure Windows virtuális gépeken PowerShell-parancsfájlok futtatása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3feed9c1c8903db66a0506f09161982dadaa79ba
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284964"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>PowerShell-parancsfájlok futtathatók a Windows virtuális gép a parancs futtatása

Futtassa a parancsot használja egy Windows Azure VM rendszerhéj PowerShell parancsfájlok futtatásához a Virtuálisgép-ügynök. Ezek a parancsfájlok vagy általános számítógép-felügyelet használható, és gyorsan diagnosztizálhatja és virtuális gép hozzáférés és a hálózati problémák megoldásához és a virtuális gép egy jó állapotra is használható.

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez használható több lehetőség áll rendelkezésre. A parancs futtatása távolról használata az ügynököt a virtuális gépek parancsfájlok is futtathatók. Futtatási parancs is használható az Azure portálon keresztül [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), vagy [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Ez a lehetőség akkor hasznos, ha szeretné, hogy egy parancsfájl witin egy virtuális gép futtatásához, és egyik hibaelhárítása, és javíthatja az RDP nem rendelkező virtuális gép egyetlen módja vagy SSH-port nyissa meg a nem megfelelő hálózati vagy a rendszergazda felhasználó miatt az összes forgatókönyv konfiguráció.

## <a name="restrictions"></a>Korlátozások

A következő korlátozások vonatkoznak a parancs futtatása használatakor:

* Kimeneti bájtra korlátozva utolsó 4096
* Parancsfájl futtatása a minimális ideje pedig körülbelül 20 másodperc
* A Windows rendszert futtató parancsfájlok
* Egyszerre csak egy parancsprogram futtathatnak.
* Parancsfájlok (interaktív módban) információt kérő nem támogatottak.
* Nem lehet megszakítani a parancsfájl futtatását
* A maximális egy parancsfájlt ideje 90 perc, azt követően a rendszer túllépi az időkorlátot

## <a name="run-a-command"></a>Parancs futtatása

Keresse meg a virtuális gép [Azure](https://portal.azure.com) válassza **paranccsal** alatt **műveletek**. A virtuális Gépen futtatandó elérhető parancsok listája jelenik meg.

![A parancs futtatásához](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancs. A parancsok némelyike rendelkezhet opcionális vagy kötelező bemeneti paraméter. Ezeknek a parancsoknak a paraméterek ahhoz, hogy adja meg a bemeneti szöveg mezői jelenjenek meg. Minden egyes parancsnál megtekintheti a kibontásával futtatott parancsfájl **parancsfájl megtekintése**. **RunPowerShellScript** eltér a más parancsok, lehetővé teszi a saját egyéni parancsfájl.

> [!NOTE]
> A beépített parancsok, amelyek nem szerkeszthető.

Ha a parancsot választja, kattintson **futtatása** a parancsfájl futtatásához. A parancsfájl futtatása, és amikor végzett, és adja vissza a kimeneti ki a hibákat a kimeneti ablakban. Az alábbi képernyőfelvételen látható egy példa a kimenetre futtatását a **RDPSettings** parancsot.

![Parancsfájl kimeneti parancs futtatása](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Parancsok

Az alábbi táblázatban a rendelkezésre álló parancsok listájához, a Windows virtuális gépek esetén. A **RunPowerShellScript** parancs használható a kívánt egyéni parancsfájl futtatásához.

|**Name (Név)**|**Leírás**|
|---|---|
|**RunPowerShellScript**|Egy PowerShell-parancsprogram végrehajtása|
|**EnableRemotePS**|Konfigurálja a számítógépet, hogy engedélyezze a távoli PowerShell.|
|**EnableAdminAccount**|Ha a helyi rendszergazdai fiók le van tiltva, és ha igen lehetővé teszi, hogy ellenőrzi.|
|**IPConfig**| Részletes információk megjelenítése az IP-cím, alhálózati maszk és alapértelmezett átjáró mindegyik adapterhez kötött TCP/IP.|
|**RDPSettings**|Ellenőrzi a beállításjegyzék-beállítások és a tartományi házirend-beállításokat. Házirend műveletek javasol, ha a számítógép egy tartomány része, vagy módosítja a beállításokat az alapértelmezett értékekre.|
|**ResetAccountPassword**| Alaphelyzetbe állítja a beépített Rendszergazda fiók jelszavát.|
|**ResetRDPCert**|Eltávolítja az RDP-figyelőjének társítva SSL-tanúsítványt, és visszaállítja az RDP listerner biztonsági alapértelmezett. Használja ezt a parancsfájlt, ha problémák merülnek fel a tanúsítvány látható.|
|**SetRDPPort**|Beállítja az alapértelmezett vagy felhasználó megadott portszám a távoli asztali kapcsolatok. Lehetővé teszi, hogy a port hozzáférése bejövő tűzfalszabályt.|

## <a name="powershell"></a>PowerShell

Az alábbiakban egy példa segítségével a [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) parancsmag PowerShell parancsfájl futtatása egy Azure virtuális gépen.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Parancs futtatása való hozzáférés korlátozása

Futtassa a parancsokat listázása, vagy a parancs részletes adatait megjelenítő igényli a `Microsoft.Compute/locations/runCommands/read` engedéllyel, amely a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör és az annál magasabb rendelkezik.

A parancs futtatásához szükség van a `Microsoft.Compute/virtualMachines/runCommand/action` engedéllyel, amely a [közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör és az annál magasabb rendelkezik.

Egyikét használhatja a [beépített](../../role-based-access-control/built-in-roles.md) szerepköröket, vagy hozzon létre egy [egyéni](../../role-based-access-control/custom-roles.md) szerepkört használja a parancs futtatása.

## <a name="next-steps"></a>További lépések

Megtekintéséhez [a Windows virtuális gép található parancsfájlok futtatásának](run-scripts-in-vm.md) tájékozódhat az egyéb módjai parancsprogramok és parancsok a virtuális gép távoli futtatása.