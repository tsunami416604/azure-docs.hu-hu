---
title: Windows tűzfal adatgyűjtést az Azure-Sentinel-Előzetesében |} A Microsoft Docs
description: Ismerje meg az Azure Sentinel-a Windows tűzfal adatainak gyűjtéséről.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3839d81f70b8bc6dcb1da3c4dd77f52443294707
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574840"
---
# <a name="connect-windows-firewall"></a>A Windows tűzfal csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows tűzfal-összekötő lehetővé teszi, hogy könnyedén csatlakoztathatók a Windows tűzfal naplók, ha csatlakoznak az Azure Sentinel-munkaterülethez. Ez a kapcsolat lehetővé teszi megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat és vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket.  


> [!NOTE]
> 
> - Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="enable-the-connector"></a>Az összekötő engedélyezése 

1. Az Azure-Sentinel-portálon válassza **adatgyűjtés** majd kattintson a a **Windows tűzfal** csempére. 
1. Válassza ki a kívánt adatfolyam adattípusokat.
1. Kattintson az **Install** (Telepítés) gombra.
6. A megfelelő sémát a Log Analytics használata a Windows tűzfal, keresse meg **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Windows tűzfal Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

