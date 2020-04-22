---
title: Parancsfájlok futtatása Azure Linux os virtuális gépben
description: Ez a témakör a parancsfájlok virtuális gépen belüli futtatását ismerteti
services: automation
ms.service: virtual-machines-linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
ms.openlocfilehash: 9dbfb3e76fe1c0e80dd8020b79f4edd6d32a24a3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758581"
---
# <a name="run-scripts-in-your-linux-vm"></a>Parancsfájlok futtatása a Linux virtuális gépben

A feladatok automatizálásához vagy a problémák elhárításához előfordulhat, hogy parancsokat kell futtatnia egy virtuális gépen. A következő cikk rövid áttekintést nyújt a virtuális gépeken belüli parancsfájlok és parancsok futtatásához elérhető szolgáltatásokról.

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az [egyéni parancsfájl-bővítmény](../extensions/custom-script-linux.md) elsősorban a telepítés utáni konfigurációhoz és a szoftvertelepítéshez használatos.

* Parancsfájlok letöltése és futtatása az Azure virtuális gépein.
* Azure Resource Manager-sablonok, Az Azure CLI, A REST API, a PowerShell vagy az Azure Portal használatával futtatható.
* A parancsfájlfájlok letölthetők az Azure Storage-ból vagy a GitHubról, vagy a számítógépről biztosíthatók, ha az Azure Portalról futtatják.
* PowerShell-parancsfájl futtatása Windows-gépeken és Bash parancsfájl linuxos gépeken.
* A telepítés utáni konfigurációhoz, a szoftvertelepítéshez és egyéb konfigurációs vagy felügyeleti feladatokhoz hasznos.

## <a name="run-command"></a>Futtatás parancs

A [Parancs futtatása](run-command.md) szolgáltatás lehetővé teszi a virtuális gép- és alkalmazáskezelést és a hibaelhárítást parancsfájlok használatával, és akkor is elérhető, ha a gép nem érhető el, például ha a vendég tűzfal nem rendelkezik az RDP vagy SSH port nyitva.

* Parancsfájlok futtatása az Azure virtuális gépein.
* Futtatható az [Azure Portal,](run-command.md) [rest API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)vagy [PowerShell használatával](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Gyorsan futtathat egy parancsfájlt, és megtekintheti a kimenetet, és szükség szerint ismételje meg az Azure Portalon.
* Parancsfájl t közvetlenül is beírhatja, vagy futtathatja az egyik beépített parancsfájlt.
* PowerShell-parancsfájl futtatása Windows-gépeken és Bash parancsfájl linuxos gépeken.
* Hasznos a virtuális gép és az alkalmazás kezelése és a parancsfájlok futtatásához a virtuális gépek, amelyek nem érhető el.

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

A [hibrid Runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) általános gép-, alkalmazás- és környezetkezelést biztosít egy Automation-fiókban tárolt egyéni parancsfájlokkal.

* Parancsfájlokat futtataz Azure-ban és nem Azure-os gépeken.
* Futtatható az Azure Portal, az Azure CLI, a REST API, a PowerShell, a webhook használatával.
* Automation-fiókban tárolt és kezelt parancsfájlok.
* PowerShell, PowerShell-munkafolyamat, Python vagy grafikus runbookok futtatása
* Nincs időkorlát a parancsfájlok futtatásának idejére.
* Egyszerre több parancsfájl is futtatható.
* A teljes parancsfájl kimenetet a rendszer visszaadja és tárolja.
* A feladatelőzmények 90 napig állnak rendelkezésre.
* A parancsfájlok helyi rendszerként vagy a felhasználó által megadott hitelesítő adatokkal futtathatók.
* [Manuális telepítést](../../automation/automation-windows-hrw-install.md) igényel

## <a name="serial-console"></a>Soros konzol

A [soros konzol](serial-console.md) közvetlen hozzáférést biztosít a virtuális géphez, hasonlóan ahhoz, mint ha a virtuális géphez csatlakoztatott billentyűzet van csatlakoztatva.

* Parancsok futtatása az Azure virtuális gépeken.
* Futtatható egy szöveges konzol on the machine az Azure Portalon.
* Bejelentkezés a számítógépre helyi felhasználói fiókkal.
* Akkor hasznos, ha a virtuális géphez való hozzáférésre a számítógép hálózatától vagy operációs rendszerének állapotától függetlenül van szükség.

## <a name="next-steps"></a>További lépések

További információ a virtuális gépeken belüli parancsfájlok és parancsok futtatásához elérhető különböző funkciókról.

* [Egyéni szkriptbővítmény](../extensions/custom-script-linux.md)
* [Parancs futtatása](run-command.md)
* [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md)
* [Soros konzol](serial-console.md)