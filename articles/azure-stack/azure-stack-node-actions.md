---
title: Méretezési egység csomópont műveletek Azure verem |} Microsoft Docs
description: Útmutató csomópont állapotának megtekintése és a bekapcsolási használata, kapcsolja ki, a kiürítési, és az integrált Azure verem rendszeren csomópont műveletek folytatása.
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
ms.date: 06/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 3ecc8885a30a11472fe93bbda60c39131c6b3bd7
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801415"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skálázási egység csomópont műveletek Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk ismerteti a méretezési egység és a kapcsolódó csomópontjainak állapotának megtekintése és használata a rendelkezésre álló csomópont műveleteket. Csomópont műveletek bekapcsolás power: kikapcsolva, kiürítésére, folytatása és javítása. Általában akkor használják a csomópont műveletek során a mező helyettesítő részből áll, vagy a csomópont helyreállítási forgatókönyvek esetén.

> [!Important]  
> A cikkben minden csomópont műveletek egyszerre csak egy célcsomóponttal kell.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>A skálázási egység és csomópontjainak állapotának megtekintése

A felügyeleti portálon a méretezési egység és a kapcsolódó csomópontjainak állapotának egyszerűen megtekintheti.

A skálázási egység állapotának megjelenítése:

1. Az a **régió felügyeleti** csempére, válassza ki azt a régiót.
2. A bal oldali alatt **infrastruktúrához kapcsolódó erőforrások**, jelölje be **skálázási egységek**.
3. Válassza ki a skálázási egység az eredményeket.
 
Itt tekintheti meg a következő információkat:

- A régió neve. A régió nevét a hivatkozott **-hely** a PowerShell modul.
- rendszer típusa
- teljes logikai magok
- memória összesen
- a listában az egyes csomópontok és állapotuk; vagy **futtató** vagy **leállt**.

![Skálázási egység csempe futó állapot az egyes csomópontok megjelenítése](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>A skálázási egység csomópont adatainak megtekintése

Ha egy adott csomópont, a következő információkat tekintheti meg:

- régió neve
- kiszolgáló-modell
- Az alaplapi felügyeleti vezérlővel (BMC) IP-címe
- működési állapot
- Magok száma összesen
- teljes memóriamennyiség
 
![Skálázási egység csempe futó állapot az egyes csomópontok megjelenítése](media/azure-stack-node-actions/NodeActions.PNG)

Itt skálázási egység csomópont műveleteket is végrehajthat.

## <a name="scale-unit-node-actions"></a>Skálázási egység csomópont műveletek

A skálázási egység csomópont adatainak megtekintésekor is csomópont műveletek végezhetők, mint:

- bekapcsolása és kikapcsolása
- kiürítési és folytatása
- Javítás

A csomópont működési állapotát határozza meg, milyen beállítások érhetők el.

### <a name="power-off"></a>Kikapcsolás

A **kikapcsolásához** művelet kikapcsolja a csomópont. Ugyanaz, mintha a főkapcsoló. Létezik **nem** leállítási jelet küld az operációs rendszer. Műveletek tervezett kikapcsolására győződjön meg arról, először üríti ki a skálázási egység csomópont.

Ez a művelet jellemzően akkor van a csomópont lefagyott állapotban van, és már nem válaszol a kérésekre.

> [!Important] 
> Ez a funkció csak az Powershellen keresztül érhető el. Lesz elérhető a verem Azure felügyeleti portálon újra később.


A kikapcsolási művelet Powershellen keresztül futtatása:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

Az valószínű esetében, amelyek a kikapcsolási művelet nem működik használja helyette a BMC webes felületet.

### <a name="power-on"></a>Bekapcsolás

A **Bekapcsolással** művelet bekapcsolja a csomópont. Ugyanaz, mintha a főkapcsoló. 

> [!Important] 
> Ez a funkció csak az Powershellen keresztül érhető el. Lesz elérhető a verem Azure felügyeleti portálon újra később.

A power futtatásához a műveletei Powershellen keresztül:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

A valószínűtlen eset, amely a bekapcsolási művelet nem működik használja helyette a BMC webes felületet.

### <a name="drain"></a>Kiürítési

A **kiürítésére** művelet az összes aktív munkaterhelések evacuates adott méretezési egységhez fennmaradó csomópontjai közötti elosztásával.

Ez a művelet általában a kijelzők, például egy teljes csomópont váltja fel a mező helyettesítő során használatos.

> [!IMPORTANT]  
> Győződjön meg arról, hogy csak tervezett karbantartási időszak alatt, ahol felhasználók értesítést üríthetik a csomópontokat. Bizonyos körülmények között az aktív munkaterhelések fedezheti megszakítás mellett folytathatja.

A PowerShell segítségével a kiürítési művelet futtatása:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Folytatás

A **folytatása** művelet folytatja lecsepegtetett csomópontot, és a munkaterhelés-elhelyezés aktív jelöli meg. A korábbi a csomóponton futó munkaterhelések nem adja vissza. (Ha egy csomópont, és a kikapcsolás, amikor a csomópont kapcsolja vissza a üríti ki, nincs megjelölve aktívként munkaterhelés-elhelyezés. Ha készen áll, kell használnia a művelet folytatása a csomópont aktívként megjelölni.)

A művelet folytatása a Powershellen keresztül futtatása:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Javítás

A **javítási** művelet kijavítja a csomópont. Használatát csak a következő esetekben:

- Teljes csomópont csere (vagy anélkül új adatlemezek)
- Után hardverhiba összetevő és a cseredimenzió (Ha a mező cserélhető Cisco egységet (FRU) dokumentációjában).

> [!IMPORTANT]  
> A dokumentációban a OEM hardver gyártója által biztosított FRU pontos lépések során ki kell cserélni a kívánt csomópontra vagy különálló. FRU dokumentációjában adja meg, hogy szükséges-e a javítási művelet egy hardverösszetevő cseréje után.  

A javítási művelet futtatásakor meg kell adnia a BMC IP-címet. 

A PowerShell segítségével a javítási művelet futtatása:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>További lépések

Az Azure verem háló rendszergazdája modul kapcsolatos további információkért lásd: [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.3.0).