---
title: Szkriptek futtatása az Azure Linux VM
description: Ez a témakör ismerteti, hogyan lehet virtuális gépen parancsfájlok futtatása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3881a6d0ee1139fa481908f09b6e96efda24a5cd
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979003"
---
# <a name="run-scripts-in-your-linux-vm"></a>Szkriptek futtatása a Linux rendszerű virtuális gép

Automatizálhatja a feladatokat, vagy hibák elhárítása, szükség lehet egy virtuális gépen futtassa a parancsokat. A következő cikk rövid áttekintést nyújt, amelyek futtathatók a parancsprogramok és parancsok belül a virtuális gépek funkcióit.

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

A [egyéni szkriptek futtatására szolgáló bővítmény](../extensions/custom-script-linux.md) elsősorban a post üzembe helyezési konfiguráció és a szoftverek telepítéséhez.

* Töltse le és parancsfájlok futtatása az Azure-beli virtuális gépeken.
* Futtatható az Azure Resource Manager-sablonok, az Azure CLI, REST API, PowerShell vagy az Azure portal használatával.
* Parancsfájlok az Azure storage- és GitHub-től letöltött, vagy a számítógépről, az Azure Portalról futtatásakor megadott.
* PowerShell-parancsprogram futtatásával a Windows-gépeken, és a Bash-szkript a Linux rendszerű gépeken.
* Hasznos üzembe helyezést követő konfiguráció, Szoftvertelepítés, és egyéb konfigurációs vagy a felügyeleti feladatokat.

## <a name="run-command"></a>Parancs futtatása

A [futtatása paranccsal](run-command.md) funkció lehetővé teszi, hogy a virtuális gép és a felügyelet és a hibaelhárítás parancsfájlok segítségével, és elérhető még ha a gép nem érhető el, például ha a Vendég tűzfala nem rendelkezik az RDP vagy SSH-port Nyissa meg.

* Szkriptek futtatása az Azure-beli virtuális gépeken.
* Felhasználásával [az Azure portal](run-command.md), [REST API-val](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI-vel](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), vagy [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Gyorsan egy parancsfájl és nézet kimeneti futtassa, és ismételje meg az Azure Portalon igény szerint.
* Parancsfájl közvetlenül írható, vagy futtassa a beépített parancsfájlok.
* PowerShell-parancsprogram futtatásával a Windows-gépeken, és a Bash-szkript a Linux rendszerű gépeken.
* Akkor hasznos, a virtuális gépek és alkalmazások kezelése, valamint a parancsfájlok futtatásához a virtuális gépeket, amelyek nem érhetők el.

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

A [hibrid Runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) általános gép, az alkalmazás és környezet kezelést biztosít az Automation-fiókban tárolt felhasználó egyéni szkriptekkel.

* Szkriptek futtatása az Azure és a nem Azure-beli gépek.
* Futtatható az Azure portal, az Azure CLI-vel, a REST API, PowerShell, a webhook használatával.
* Az Automation-fiók a tárolt és kezelt parancsfájlokat.
* PowerShell, a PowerShell-munkafolyamat, a Python vagy a grafikus runbookok futtatása
* A szkript futtatási idő nincs időkorlátja.
* Több parancsfájl egyidejűleg is futtathatók.
* Teljes szkript kimeneti adja vissza, és tárolja.
* A feladatelőzmények 90 napig érhető el.
* Parancsfájlok futtathatók a helyi rendszerként vagy felhasználó által megadott hitelesítő adatokkal.
* Szükséges [manuális telepítés](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Soros konzol

A [soros konzol](serial-console.md) közvetlen hozzáférést biztosít egy virtuális géphez, hasonlóan a virtuális géphez csatlakoztatott billentyűzet kellene.

* A parancsok futtatása Azure-beli virtuális gépeken.
* Futtatható az Azure Portalon a gép egy szöveges alapú konzol használatával.
* Jelentkezzen be a számítógép egy helyi felhasználói fiók.
* Akkor hasznos, amikor a gép, hálózati vagy az operációs rendszer állapottól függetlenül a virtuális géphez való hozzáférés szükséges.

## <a name="next-steps"></a>További lépések

További információ a különböző funkciókat, amelyek futtathatók a parancsprogramok és parancsok belül a virtuális gépek.

* [Egyéni szkriptbővítmény](../extensions/custom-script-linux.md)
* [A parancs futtatása](run-command.md)
* [Hibrid Runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md)
* [Soros konzol](serial-console.md)