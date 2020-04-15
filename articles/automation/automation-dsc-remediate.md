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
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383159"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Nem megfelelő DSC-kiszolgálók kiújítása

Amikor a kiszolgálók regisztrálva vannak az Azure `ApplyOnly`Automation `ApplyandMonitor`állapotkonfigurációjában, a konfigurációs mód a , vagy `ApplyAndAutoCorrect`a. Ha a mód nincs `ApplyAndAutoCorrect`beállítva, a megfelelő állapotból bármilyen okból elsodródó kiszolgálók nem megfelelőek maradnak, amíg manuálisan nem javítják őket.

Az Azure compute egy Run Command nevű szolgáltatást kínál, amely lehetővé teszi az ügyfelek számára, hogy parancsfájlokat futtassanak a virtuális gépeken belül.
Ez a dokumentum a konfigurációs eltolódás manuális javításakor példaparancsfájlokat tartalmaz ehhez a szolgáltatáshoz.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>A Windows virtuális gépek helyes eltolódása a PowerShell használatával

A Windows virtuális gépeken a Parancs futtatása szolgáltatással kapcsolatos részletes útmutatásért tekintse meg a [PowerShell-parancsfájlok futtatása](/azure/virtual-machines/windows/run-command)a Windows virtuális gép ben a Parancs futtatásával című dokumentációs lapon.

Ha egy Azure Automation-állapot konfigurációs csomópontot a legújabb konfiguráció letöltésére és alkalmazására kényszeríthet, használja az [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) parancsmagát.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>A Linux virtuális gépek helyes eltolódása

Hasonló funkciók jelenleg nem érhetők el linuxos kiszolgálókon.
Az egyetlen lehetőség a regisztrációs folyamat megismétlése.
Az Azure-csomópontok, javíthatja a drift az Azure Portalon, vagy az Az modul parancsmagok használatával. A folyamat részleteit az [Azure Automation állapotkonfigurációja által felügyelt bevezetési gépek dokumentálják.](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal)
A hibrid csomópontok, a mellékelt Python-parancsfájlok segítségével javíthatja a drift.
Lásd: [PowerShell DSC linuxos tárház.](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)

## <a name="next-steps"></a>További lépések

- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Az Azure Automation state configuration használatával kapcsolatos példa folyamatos üzembe helyezési folyamatban: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és a Csokoládé használata című témakörben.](automation-dsc-cd-chocolatey.md)