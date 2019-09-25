---
title: Windows tűzfal-adatkapcsolatok összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatók a Windows tűzfal az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 840e8b3bc86281a8c42689b1cb68917741ef2bd9
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240625"
---
# <a name="connect-windows-firewall"></a>A Windows tűzfal csatlakoztatása



A Windows tűzfal-összekötő segítségével könnyedén csatlakoztathatja a Windows tűzfal naplóit, ha azok az Azure Sentinel-munkaterülethez csatlakoznak. Ez a kapcsolat lehetővé teszi az irányítópultok megtekintését, az egyéni riasztások létrehozását és a vizsgálat javítását. Ez nagyobb betekintést nyújt a szervezet hálózatára, és javítja a biztonsági műveletek képességeit. A megoldás Windows tűzfalbeli eseményeket gyűjt azokról a Windows-gépekről, amelyeken Log Analytics ügynök van telepítve. 


> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="enable-the-connector"></a>Az összekötő engedélyezése 

1. Az Azure Sentinel portálon válassza az **adatösszekötők** lehetőséget, majd kattintson a **Windows tűzfal** csempére. 
1.  Ha a Windows rendszerű gépek az Azure-ban vannak:
    1. Kattintson **az ügynök telepítése az Azure Windows rendszerű virtuális gépen**elemre.
    1. A **virtuális gépek** listájában válassza ki azt a Windows-gépet, amelyet az Azure sentinelbe szeretne továbbítani. Győződjön meg arról, hogy ez egy Windows rendszerű virtuális gép.
    1. A virtuális gép megnyíló ablakában kattintson a **kapcsolat**elemre.  
    1. A **Windows tűzfal-összekötő** ablakban kattintson az **Engedélyezés** gombra. 

2. Ha a Windows rendszerű számítógép nem Azure-beli virtuális gép:
    1. Kattintson **az ügynök telepítése nem Azure-beli gépekre**elemre.
    1. A **közvetlen ügynök** ablakban válassza a Windows- **ügynök letöltése (64 bites)** vagy a **windows-ügynök letöltése (32 bit)** lehetőséget.
    1. Telepítse az ügynököt a Windows rendszerű gépre. Másolja a **munkaterület-azonosítót**, az **elsődleges kulcsot**és a **másodlagos kulcsot** , és használja őket, ha a telepítés során a rendszer kéri.

4. Válassza ki, hogy mely adattípusokat kívánja továbbítani.
5. Kattintson a **megoldás telepítése**gombra.
6. A Windows tűzfal Log Analytics vonatkozó sémájának használatához keresse meg a **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 



## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Windows tűzfal az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

