---
title: "Méretezési egység csomópont műveletek Azure verem |} Microsoft Docs"
description: "Útmutató csomópont állapotának megtekintése és a bekapcsolási használata, kapcsolja ki, a kiürítési, és az integrált Azure verem rendszeren csomópont műveletek folytatása."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: twooley
ms.openlocfilehash: 3696cd0da0859bebf001f7749ac8874efd574046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skálázási egység csomópont műveletek Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk ismerteti a méretezési egység és a kapcsolódó csomópontjainak állapotának megtekintése és használata a rendelkezésre álló csomópont műveleteket. Csomópont műveletek bekapcsolás power: kikapcsolva, kiürítésére, folytatása és javítása. Általában akkor használják a csomópont műveletek során a mező helyettesítő részből áll, vagy a csomópont helyreállítási forgatókönyvek esetén.

## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>A skálázási egység és csomópontjainak állapotának megtekintése

A felügyeleti portálon a méretezési egység és a kapcsolódó csomópontjainak állapotának egyszerűen megtekintheti.

A skálázási egység állapotának megjelenítése:

1. Az a **régió felügyeleti** csempére, válassza ki azt a régiót.
2. A bal oldali alatt **infrastruktúrához kapcsolódó erőforrások**, jelölje be **skálázási egységek**.
3. Válassza ki a skálázási egység az eredményeket.
 
Itt tekintheti meg a következő információkat:

- régió neve
- rendszer típusa
- teljes logikai magok
- memória összesen
- a listában az egyes csomópontok és állapotuk; Futó vagy leállt.

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
- javítás

A csomópont működési állapotát határozza meg, milyen beállítások érhetők el.

### <a name="power-off"></a>Kikapcsolás

A **kikapcsolásához** művelet kikapcsolja a csomópont. Ugyanaz, mintha a főkapcsoló. Létezik **nem** leállítási jelet küld az operációs rendszer. Műveletek tervezett kikapcsolására győződjön meg arról, először üríti ki a skálázási egység csomópont.

Ez a művelet jellemzően akkor van a csomópont lefagyott állapotban van, és már nem válaszol a kérésekre.  

A kikapcsolási művelet Powershellen keresztül futtatása:

  ````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ```` 

Az valószínű esetében, amelyek a kikapcsolási művelet nem működik használja helyette a BMC webes felületet.

### <a name="power-on"></a>Bekapcsolás

A **Bekapcsolással** művelet bekapcsolja a csomópont. Ugyanaz, mintha a főkapcsoló. 

A power futtatásához a műveletei Powershellen keresztül:

  ````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

A valószínűtlen eset, amely a bekapcsolási művelet nem működik használja helyette a BMC webes felületet.

### <a name="drain"></a>Kiürítési

A **kiürítésére** művelet az összes aktív munkaterhelések evacuates adott méretezési egységhez fennmaradó csomópontjai közötti elosztásával.

Ez a művelet általában a kijelzők, például egy teljes csomópont váltja fel a mező helyettesítő során használatos.

> [!IMPORTANT]
> Győződjön meg arról, hogy csak tervezett karbantartási időszak alatt, ahol felhasználók értesítést üríthetik a csomópontokat. Bizonyos körülmények között az aktív munkaterhelések fedezheti megszakítás mellett folytathatja.

A PowerShell segítségével a kiürítési művelet futtatása:

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Folytatás

A **folytatása** művelet folytatja lecsepegtetett csomópontot, és a munkaterhelés-elhelyezés aktív jelöli meg. A korábbi a csomóponton futó munkaterhelések nem adja vissza. (Ha egy csomópont, és a kikapcsolás, amikor a csomópont kapcsolja vissza a üríti ki, nincs megjelölve aktívként munkaterhelés-elhelyezés. Ha készen áll, kell használnia a művelet folytatása a csomópont aktívként megjelölni.)

A művelet folytatása a Powershellen keresztül futtatása:

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
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
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


