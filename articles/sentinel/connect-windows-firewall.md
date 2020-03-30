---
title: Windows tűzfaladatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Windows tűzfaladatait az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588059"
---
# <a name="connect-windows-firewall"></a>A Windows tűzfal csatlakoztatása



A Windows tűzfal-összekötő lehetővé teszi a Windows-tűzfalak naplóinak egyszerű csatlakoztatását, ha azok az Azure Sentinel-munkaterülethez csatlakoznak. Ez a kapcsolat lehetővé teszi az irányítópultok megtekintését, egyéni riasztások létrehozását és a vizsgálat javítását. Ez több betekintést nyújt a szervezet hálózatába, és javítja a biztonsági műveleti képességeket. A megoldás a Windows tűzfal eseményeit azon Windows-gépekről gyűjti, amelyeken a Log Analytics-ügynök telepítve van. 


> [!NOTE]
> - Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.
> - Ha az Azure Sentinel és az Azure Security Center ugyanabba a munkaterületre gyűjti, nincs szükség a Windows tűzfal megoldás engedélyezésére ezen az összekötőn keresztül. Ha mégis engedélyezte, az nem okoz duplikált adatokat. 

## <a name="enable-the-connector"></a>Az összekötő engedélyezése 

1. Az Azure Sentinel portálon válassza **az Adatösszekötők** lehetőséget, majd kattintson a **Windows tűzfal** csempéjére. 
1.  Ha windowsos gépei az Azure-ban vannak:
    1. Kattintson **az Ügynök telepítése az Azure Windows virtuális gépen**elemre.
    1. A **virtuális gépek** listájában válassza ki az Azure Sentinelbe streamelni kívánt Windows-gépet. Győződjön meg arról, hogy ez egy Windows virtuális gép.
    1. A virtuális gép számára megnyíló ablakban kattintson a **Csatlakozás gombra.**  
    1. Kattintson az **Engedélyezés gombra** a **Windows tűzfal-összekötő** ablakában. 

2. Ha a Windows-gép nem Azure virtuális gép:
    1. Kattintson **az Ügynök telepítése nem Azure-alapú gépekre**elemre.
    1. A **Közvetlen ügynök** ablakban válassza a **Windows-ügynök letöltése (64 bites)** vagy **a Windows-ügynök letöltése (32 bites) lehetőséget.**
    1. Telepítse az ügynököt a Windows rendszerű számítógépre. Másolja a **munkaterület-azonosítót**, **az elsődleges kulcsot**és a Másodlagos **kulcsot,** és használja őket, amikor a telepítés során a rendszer kéri.

4. Válassza ki, hogy mely adattípusokat szeretné streamelni.
5. Kattintson **a Megoldás telepítése gombra.**
6. Ha a Windows tűzfal log analytics szolgáltatásában a megfelelő sémát szeretné használni, keresse meg a **SecurityEvent (Biztonság) kifejezést.**

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathat Windows tűzfalat az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)

