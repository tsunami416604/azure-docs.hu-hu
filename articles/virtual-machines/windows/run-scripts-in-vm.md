---
title: Parancsfájlok futtatása Azure-beli Windows rendszerű virtuális gépen
description: Ez a témakör azt ismerteti, hogyan futtathat parancsfájlokat Windows rendszerű virtuális gépeken belül
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: c2999d5bd6d780d5815f22d18e6e8cdd580987af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87020168"
---
# <a name="run-scripts-in-your-windows-vm"></a>Parancsfájlok futtatása a Windows rendszerű virtuális gépen

A feladatok automatizálásához vagy a hibák elhárításához szükség lehet a virtuális gépeken futtatott parancsok futtatására. A következő cikk rövid áttekintést nyújt a virtuális gépeken belüli parancsfájlok és parancsok futtatásához elérhető funkciókról.

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az [Egyéni szkriptek bővítménye](../extensions/custom-script-windows.md) elsődlegesen a telepítés utáni konfiguráció és a Szoftvertelepítés során használatos.

* Parancsfájlok letöltése és futtatása az Azure Virtual Machines szolgáltatásban.
* Futtatható Azure Resource Manager sablonok, Azure CLI, REST API, PowerShell vagy Azure Portal használatával.
* A parancsfájlok az Azure Storage-ból vagy a GitHubról tölthetők le, illetve a SZÁMÍTÓGÉPRŐL is elérhetők, ha a Azure Portal fut.
* PowerShell-parancsfájl futtatása Windows rendszerű gépeken és bash-parancsfájlban Linux-gépeken.
* Hasznos lehet a telepítés utáni konfiguráció, a Szoftvertelepítés, valamint az egyéb konfigurációs vagy felügyeleti feladatok elvégzéséhez.

## <a name="run-command"></a>Parancs futtatása

A [Futtatás parancs](run-command.md) funkció lehetővé teszi a virtuális gépek és az alkalmazások kezelését és a hibaelhárítást parancsfájlok használatával, és akkor is elérhető, ha a számítógép nem érhető el, például ha a vendég tűzfal nem rendelkezik a megnyitott RDP-vagy SSH-porttal.

* Parancsfájlok futtatása az Azure Virtual Machines szolgáltatásban.
* Futtatható [Azure Portal](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)vagy [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) használatával
* Futtasson gyorsan egy parancsfájlt, és tekintse meg a kimenetet, és szükség szerint ismételje meg a Azure Portal.
* A szkript közvetlenül is beírható, vagy futtathatja a beépített parancsfájlok egyikét.
* PowerShell-parancsfájl futtatása Windows rendszerű gépeken és bash-parancsfájlban Linux-gépeken.
* A virtuális gépek és az alkalmazások kezeléséhez, valamint a nem elérhető virtuális gépek parancsfájljainak futtatásához hasznos.

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

A [hibrid Runbook](../../automation/automation-hybrid-runbook-worker.md) -feldolgozó általános gépi, alkalmazás-és környezeti kezelést biztosít az Automation-fiókban tárolt egyéni parancsfájlokkal.

* Parancsfájlok futtatása az Azure-ban és a nem Azure-beli gépeken.
* Futtatható a Azure Portal, az Azure CLI, a REST API, a PowerShell és a webhook használatával.
* Automation-fiókban tárolt és kezelt parancsfájlok.
* A PowerShell, a PowerShell-munkafolyamat, a Python vagy a grafikus runbookok futtatása
* Nincs időkorlát a parancsfájl futási idején.
* Egyidejűleg több parancsfájl is futtatható.
* A rendszer a teljes parancsfájl kimenetét adja vissza és tárolja.
* A feladatok előzményei 90 napig érhetők el.
* A parancsfájlok helyi rendszerként vagy felhasználó által megadott hitelesítő adatokkal is futtathatók.
* [Manuális telepítést](../../automation/automation-windows-hrw-install.md) igényel

## <a name="serial-console"></a>Soros konzol

A [Serial Console](../troubleshooting/serial-console-windows.md) közvetlen hozzáférést biztosít egy virtuális géphez, hasonlóan ahhoz, hogy a billentyűzet csatlakoztatva legyen a virtuális géphez.

* Parancsok futtatása az Azure Virtual Machines szolgáltatásban.
* Egy szöveges konzol használatával futtatható a Azure Portal számítógépén.
* Jelentkezzen be a gépre egy helyi felhasználói fiókkal.
* Akkor hasznos, ha a virtuális géphez való hozzáférésre a gép hálózati vagy operációsrendszer-állapota nélkül van szükség.

## <a name="next-steps"></a>További lépések

További információ a virtuális gépeken belüli parancsfájlok és parancsok futtatásához elérhető különböző funkciókról.

* [Egyéni szkriptbővítmény](../extensions/custom-script-windows.md)
* [Parancs futtatása](run-command.md)
* [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md)
* [Serial console](../troubleshooting/serial-console-windows.md)
