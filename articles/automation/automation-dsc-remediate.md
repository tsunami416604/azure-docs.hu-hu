---
title: Nem megfelelő Azure Automation-állapotkonfigurációs kiszolgálók kiújítása
description: A konfigurációk újraalkalmazása igény szerint olyan kiszolgálókra, amelyeken a konfigurációs állapot elsodródott?
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585505"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Nem megfelelő DSC-kiszolgálók szervizelése

Amikor a kiszolgálók regisztrálva vannak az Azure Automation állapotkonfigurációjában, a "Konfigurációs mód" applyonly, ApplyandMonitor vagy ApplyAndAutoCorrect lesz beállítva.
Ha a mód nincs automatikus javításra állítva, a megfelelő állapotból bármilyen okból elsodródó kiszolgálók nem megfelelőek maradnak, amíg manuálisan nem javítják ki őket.

Az Azure compute egy Run Command nevű szolgáltatást kínál, amely lehetővé teszi az ügyfelek számára, hogy parancsfájlokat futtassanak a virtuális gépeken belül.
Ez a dokumentum a konfigurációs eltolódás manuális javításakor példaparancsfájlokat tartalmaz ehhez a szolgáltatáshoz.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>A Windows virtuális gépek helyes eltolódása a PowerShell használatával

A Windows virtuális gépeken a Parancs futtatása szolgáltatás sal kapcsolatos lépésekkel kapcsolatban a [PowerShell-parancsfájlok futtatása a Windows virtuális gép futtatása parancssal](/azure/virtual-machines/windows/run-command)című dokumentációs lapon olvashat.

Ha egy Azure Automation-állapot konfigurációs csomópontot a legújabb `Update-DscConfiguration` konfiguráció letöltésére és alkalmazására kényszeríthet, használja a parancsmaszt.
További információt az [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)parancsmag dokumentációjában talál.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>A Linux virtuális gépek helyes eltolódása

Hasonló funkciók jelenleg nem érhetők el linuxos kiszolgálókon.
Az egyetlen lehetőség a regisztrációs folyamat megismétlése.
Az Azure-csomópontok, drift-korrekció elvégezhető a portálról, vagy az Az Automation-parancsmagok használatával.
A folyamat részleteit az Azure [Automation állapotkonfigurációja által felügyelt bevezetési gépek oldalon dokumentálja.](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal)
Hibrid csomópontok esetén a drift-korrekció a mellékelt Python-parancsfájlok használatával végezhető el.
Tekintse meg a dokumentációt a [PowerShell DSC Linux-tárház.](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)

## <a name="next-steps"></a>További lépések

- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai](/powershell/module/azurerm.automation/#automation)
- Az Azure Automation állapotkonfigurációjának folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládés használatával](automation-dsc-cd-chocolatey.md)
