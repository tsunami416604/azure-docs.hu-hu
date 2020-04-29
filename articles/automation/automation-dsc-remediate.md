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
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406086"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Nem megfelelő DSC-kiszolgálók szervizelése

Ha a kiszolgálók Azure Automation állapot-konfigurációval vannak regisztrálva, a konfigurációs mód `ApplyOnly`a `ApplyandMonitor`következőre `ApplyAndAutoCorrect`van beállítva:, vagy. Ha a mód nem értékre `ApplyAndAutoCorrect`van állítva, akkor a megfelelő állapotú kiszolgálók bármilyen okból nem megfelelők maradnak, amíg azokat manuálisan nem javították.

Az Azure-beli számítási funkció a Run parancs nevű funkciót kínálja, amely lehetővé teszi az ügyfeleknek a parancsfájlok futtatását a virtuális gépeken belül.
Ez a dokumentum példákat tartalmaz a szolgáltatáshoz, amikor manuálisan kijavította a konfiguráció eltolódását.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Windows rendszerű virtuális gépek helyes elsodródása a PowerShell használatával

A Windows rendszerű virtuális gépeken futtatott parancs futtatásával kapcsolatos részletes utasításokért tekintse meg a következő témakört: a dokumentációs lapon futtassa a [PowerShell-parancsfájlokat a Windows rendszerű](/azure/virtual-machines/windows/run-command)virtuális gépen a Run paranccsal.

Egy Azure Automation állapot-konfigurációs csomópont kényszerítéséhez a legújabb konfiguráció letöltéséhez és alkalmazásához használja az [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) parancsmagot.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek helyes elsodródása

Hasonló funkciók jelenleg nem érhetők el Linux-kiszolgálókon.
Az egyetlen lehetőség a regisztrációs folyamat megismétlése.
Az Azure-csomópontok esetében kiválaszthatja a Azure Portal vagy az az Module parancsmagok használatával történő eltolódást. A folyamat részleteit dokumentálja a [bevezetési gépeken, Azure Automation állapot konfigurációjának](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal)megfelelően.
A hibrid csomópontok esetében a mellékelt Python-szkriptek használatával is kiválaszthatja a driftt.
Lásd: [POWERSHELL DSC Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)-tárházhoz.

## <a name="next-steps"></a>További lépések

- A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfigurációval és a csokoládéval](automation-dsc-cd-chocolatey.md).