---
title: Csomópont kapcsolatos műveletek méretezése az Azure Stackben |} A Microsoft Docs
description: Útmutató megtekintése a csomópont állapota, és használja a kiürítési leállítása a, és ha megszakad a csomópont műveleteket az Azure Stackkel integrált rendszereknél.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a792bc083c3a2c78b24d5895c34420b86b0863bb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959767"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skálázási egység csomópont műveletek az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a méretezési egység és a kapcsolódó csomópontokat állapotának megtekintése és használata a rendelkezésre álló csomópont műveleteket. Csomópont műveletek: a kapacitást power kikapcsolva, kiürítési, folytatásához és javítása. A csomópont műveletek általában a mező helyettesítő részből áll, vagy a csomópont-helyreállítási helyzetekben során használ.

> [!Important]  
> Az összes csomópont műveletek ebben a cikkben leírt egyszerre csak egy célcsomóponttal kell.


## <a name="view-the-node-status"></a>A csomópont állapotának megtekintése

A felügyeleti portálon egyszerűen megtekintheti a skálázási egység és a kapcsolódó csomópontjainak állapotát.

A skálázási egység állapotának megtekintése:

1. Az a **régiók kezelése** csempére, válassza ki a régiót.
2. A bal oldali alatt **infrastruktúra-erőforrások**válassza **egységig**.
3. Az eredmények közül válassza ki a skálázási egység.
 
Itt tekintheti meg a következő információkat:

- régió neve. A régió neve útvonalában **-hely** a PowerShell-modul.
- rendszer típusa
- logikai magok száma összesen
- memória összesen
- Az egyes csomópontok és azok állapotát; listája mindkét **futó** vagy **leállt**

![Az egyes csomópontokon futó állapotát megjelenítő skálázási egység csempe](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-node-information"></a>Csomópont-információk megtekintése

Ha az egyes csomópontok, a következő információkat tekintheti meg:

- Régió neve
- kiszolgáló-modell
- Az alaplapi felügyeleti vezérlőnek (BMC) IP-címe
- működési állapot
- Magok teljes száma
- teljes memóriamennyiség
 
![Az egyes csomópontokon futó állapotát megjelenítő skálázási egység csempe](media/azure-stack-node-actions/NodeActions.PNG)

Skálázási egység csomópont műveletek itt is elvégezheti.

## <a name="scale-unit-node-actions"></a>Skálázási egység csomópont műveletek

Megtekintheti a skálázási egység csomópont kapcsolatos információkat, például is elvégezheti csomópont műveletek:

- Kiürítési és folytatás
- Javítás

A csomópont a működési állapota határozza meg, milyen beállítások érhetők el.

### <a name="power-off"></a>Kikapcsolás

A **kikapcsolásához** művelet kikapcsolja a csomópontot. Legyen ugyanaz, mintha a főkapcsoló. Ugyanúgy **nem** rendszerleállítási jelzés küldeni az operációs rendszer. Műveletek tervezett kikapcsolás győződjön meg arról, ürítheti skálázási egység először.

Ez a művelet általában akkor használatos, amikor egy csomópont lefagyott állapotban van, és már nem válaszol a kérelmekre.

> [!Important] 
> Ez a funkció csak Powershellen keresztül érhető el. Lesz elérhető az Azure Stack rendszergazdai portálon újra egy későbbi időpontban.


A PowerShell-lel művelet kikapcsolás futtatása:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

A valószínűtlen eset, hogy a művelet kikapcsolás nem működik használja helyette a BMC webes felületén.

### <a name="power-on"></a>Bekapcsolás

A **bekapcsolási** művelet bekapcsolja a csomóponton. Legyen ugyanaz, mintha a főkapcsoló. 

> [!Important] 
> Ez a funkció csak Powershellen keresztül érhető el. Lesz elérhető az Azure Stack rendszergazdai portálon újra egy későbbi időpontban.

A power futtatása a Powershellen keresztül művelet:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

A valószínűtlen eset, hogy a teljesítmény, a művelet nem működik használja helyette a BMC webes felületén.

### <a name="drain"></a>Kiürítési

A **kiürítési** művelet az összes aktív munkafeladatokat evacuates osztja meg őket, hogy adott skálázási egységben a többi csomópont között.

Ez a művelet általában a mező helyettesítő részből áll, például egy egész csomóponttal cseréjén során használatos.

> [!IMPORTANT]  
> Győződjön meg arról, hogy Ön ürítheti csak tervezett karbantartási időszakban, ahol felhasználói értesítést kapott. Bizonyos körülmények között az aktív munkafeladatokat tapasztalhatnak megszakítások.

A PowerShell-lel kiürítési művelet futtatása:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Folytatás

A **folytatása** művelet folytatja lecsepegtetett csomópontot, és munkaterhelés-elhelyezés aktív jelöli meg. A csomóponton futó korábbi számítási feladatok nem feladat-visszavételt. (Ön kiürítési csomópontot, majd kapcsolja ki, amikor a csomópont power lépjen vissza, ha nincs ellátva munkaterhelés-elhelyezés aktívként. Ha elkészült, kell használnia a művelet folytatása jelölje meg aktívként a csomópontra.)

A PowerShell-lel folytatása művelet futtatása:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Javítás

A **javítási** művelet helyreállít egy csomópontot. Használja azt csak az alábbi esetek valamelyike:

- Teljes csomópont helyettesítő (vagy anélkül új adatlemezek)
- Miután összetevő hardverhiba, valamint helyettesítő (Ha a mező helyettesítő egységet (FRU) dokumentációjában javasolt).

> [!IMPORTANT]  
> Pontos lépéseit a OEM hardver szállítójával FRU dokumentációjában talál, amikor szüksége van egy csomópontot vagy egyes hardverösszetevők. A FRU dokumentáció fogja megadni, hogy kell-e a javítási műveletet, hardverkomponensek cseréje után futtassa.  

A javítási művelet futtatásakor adja meg a BMC IP-címet kell. 

PowerShell-lel a javítási művelet futtatása:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>További lépések

Az Azure Stack-Fabric rendszergazdai modul kapcsolatos további információkért lásd: [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.5.0).
