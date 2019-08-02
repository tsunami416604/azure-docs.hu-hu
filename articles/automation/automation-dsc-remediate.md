---
title: Nem megfelelő Azure Automation állapot-konfigurációs kiszolgálók szervizelése
description: Igény szerinti konfigurációk újraalkalmazása olyan kiszolgálókra, amelyeken a konfiguráció állapota sodródik
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614497"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Nem megfelelő DSC-kiszolgálók szervizelése

Ha a kiszolgálók Azure Automation állapot-konfigurációval vannak regisztrálva, a "konfigurációs mód" ApplyOnly, ApplyandMonitor vagy ApplyAndAutoCorrect értékre van beállítva.
Ha a mód nem automatikus javításra van beállítva, akkor a megfelelő állapotú kiszolgálók bármilyen okból nem megfelelőek maradnak, amíg azokat manuálisan nem javították.

Az Azure-beli számítási funkció a Run parancs nevű funkciót kínálja, amely lehetővé teszi az ügyfeleknek a parancsfájlok futtatását a virtuális gépeken belül.
Ez a dokumentum példákat tartalmaz a szolgáltatáshoz, amikor manuálisan kijavította a konfiguráció eltolódását.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Windows rendszerű virtuális gépek helyes elsodródása a PowerShell használatával

A Windows rendszerű virtuális gépeken futtatott parancs futtatásával kapcsolatos részletes utasításokért tekintse meg a következőt: a dokumentációs lapon futtassa a [PowerShell-parancsfájlokat a Windows rendszerű](/azure/virtual-machines/windows/run-command)virtuális gépen a Run paranccsal.

Ha kényszeríteni szeretné a Azure Automation állapot-konfigurációs csomópontot a legújabb konfiguráció letöltésére és alkalmazására, használja a `Update-DscConfiguration` parancsmagot.
Részletekért lásd: parancsmag dokumentációjának [frissítése – DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek helyes elsodródása

Hasonló funkciók jelenleg nem érhetők el Linux-kiszolgálókon.
Az egyetlen lehetőség a regisztrációs folyamat megismétlése.
Az Azure-csomópontok esetében az drift-Correction a portálról vagy az az Automation parancsmagok használatával végezhető el.
A folyamat részleteit a [Azure Automation állapot](/azure/automation/automation-dsc-onboarding#azure-portal)-konfigurációval való felügyeletre szolgáló lapok bevezetésével foglalkozó lapon dokumentáljuk.
Hibrid csomópontok esetén a befoglalt Python-szkriptek segítségével elvégezhető a drift-Hibakiigazitás.
Tekintse meg a [Linux rendszerhez készült POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)dokumentációját.

## <a name="next-steps"></a>További lépések

- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
