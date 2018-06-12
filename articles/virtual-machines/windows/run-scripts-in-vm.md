---
title: Az Azure Windows virtuális gép található parancsfájlok futtatásának
description: Ez a témakör ismerteti a Windows rendszerű virtuális gépeken parancsfájlok futtatása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6d8df5e4992cd8d51f1b5259947139d3b180ec82
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267046"
---
# <a name="run-scripts-in-your-windows-vm"></a>A Windows virtuális gép található parancsfájlok futtatásának

Feladatok automatizálásához, vagy a problémák elhárításához, szükség lehet a virtuális gépek parancsok futtatását. A következő cikk rövid áttekintést funkcióját, parancsprogramok és parancsok a virtuális gépeken futtathatók.

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

A [egyéni parancsprogramok futtatására szolgáló bővítmény](../extensions/custom-script-windows.md) elsősorban a feladás egy vagy több központi telepítési konfiguráció és a szoftver telepítéséhez.

* Töltse le és parancsfájlok futtatása az Azure virtuális gépeken.
* Futtatható Azure Resource Manager-sablonok, az Azure parancssori felület, REST API-t, PowerShell vagy az Azure portál használatával.
* Parancsfájlok le: az Azure storage vagy a Githubon, vagy a számítógépről, az Azure-portálon való futtatáskor megadott.
* Windows-alapú gépek PowerShell-parancsprogrammal, és a Linux-gépek parancsfájl Bash.
* Feladás egy vagy több központi telepítési konfigurációs, Szoftvertelepítés és egyéb konfigurációs és felügyeleti feladatok hasznos.

## <a name="run-command"></a>A parancs futtatása

A [parancs futtatása](run-command.md) a szolgáltatás lehetővé teszi, hogy a virtuális gép és az alkalmazások kezelése és a hibaelhárítás parancsfájlok segítségével, és elérhető még ha a gép nem érhető el, például ha a Vendég tűzfal nem rendelkezik az RDP és az SSH-port Nyissa meg.

* Azure virtuális gépeken található parancsfájlok futtatásának.
* Segítségével [Azure-portálon](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), vagy [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)
* Gyorsan futtatni egy parancsfájlt, és tekintse meg a kimeneti, és az Azure portálon szükség szerint ismételje meg.
* Parancsfájl közvetlenül írható, vagy a beépített parancsfájlok futtatása.
* Windows-alapú gépek PowerShell-parancsprogrammal, és a Linux-gépek parancsfájl Bash.
* Akkor hasznos, a virtuális gépek és a felügyelet és a parancsfájlok futtatásához a virtuális gépek, amelyek nem érhető el.

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

A [hibrid forgatókönyv-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) gép, az alkalmazás és a környezet általános felügyeleti biztosít a felhasználó Automation-fiókban tárolt egyéni parancsfájlokat.

* Azure-ban és az Azure-gépek parancsfájlok futtatása.
* Futtathatja az Azure-portált használja, az Azure parancssori felület, a REST API-t, a PowerShell, a webhook.
* Automation-fiók a tárolt és kezelt parancsfájlok.
* Futtatás a PowerShell, a PowerShell munkafolyamat, a Python vagy a grafikus forgatókönyvek
* A parancsfájl nem korlátozott futási időt.
* Több parancsfájl egyidejűleg is futtathatók.
* Teljes körű parancsfájl kimenetében, visszaadott és a rendszer.
* A feladatelőzmények 90 napig érhető el.
* Parancsfájlok futtathatók a helyi rendszerfiókként vagy felhasználó által megadott hitelesítő adatokkal.
* Szükséges [manuális telepítés](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Soros konzol

A [soros konzol](serial-console.md) közvetlen hozzáférést biztosít egy virtuális géphez, hasonló, hogy a virtuális Géphez kapcsolódó billentyűzet.

* Futtassa a parancsokat az Azure virtuális gépeken.
* Futtathatja-konzollal egy szöveges a gépre az Azure portálon.
* A számítógép helyi felhasználói fiókkal bejelentkezni.
* Akkor hasznos, ha a virtuális géphez való hozzáférés van szükség a virtuálisgép-hálózat vagy operációs rendszer állapotának függetlenül.

## <a name="next-steps"></a>További lépések

További információ az egyes szolgáltatásokhoz, amelyek parancsfájlokat és a virtuális gépeken parancsok futtatásához.

* [Egyéni szkriptbővítmény](../extensions/custom-script-windows.md)
* [A parancs futtatása](run-command.md)
* [Hibrid forgatókönyv-feldolgozó](../../automation/automation-hybrid-runbook-worker.md)
* [Soros konzol](serial-console.md)
