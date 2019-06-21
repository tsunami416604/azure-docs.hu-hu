---
title: Windows tűzfal adatok csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Windows tűzfal adatok Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 09e63612d6e0e70b1bb21c23b158f650d4c34080
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190600"
---
# <a name="connect-windows-firewall"></a>A Windows tűzfal csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows tűzfal-összekötő lehetővé teszi, hogy könnyedén csatlakoztathatók a Windows tűzfal naplók, ha csatlakoznak az Azure Sentinel-munkaterülethez. Ez a kapcsolat lehetővé teszi megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat és vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket. A megoldás a Windows-gépeken, amelyen telepítve van egy Log Analytics-ügynök Windows tűzfal eseményeket gyűjti. 


> [!NOTE]
> Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="enable-the-connector"></a>Az összekötő engedélyezése 

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** majd kattintson a a **Windows tűzfal** csempére. 
1.  Ha a Windows-gép az Azure-ban:
    1. Kattintson a **ügynök telepítése az Azure Windows virtuális gép**.
    1. Az a **virtuális gépek** listájához, válassza ki a Windows-gép streamelése az Azure-Sentinel szeretné. Ellenőrizze, hogy ez egy Windows virtuális Gépet.
    1. A virtuális gép megnyíló ablakban kattintson a **Connect**.  
    1. Kattintson a **engedélyezése** a a **Windows tűzfal-összekötő** ablak. 

2. Ha a Windows-gépen nem egy Azure virtuális Gépen:
    1. Kattintson a **ügynök telepítése nem Azure-beli gépek**.
    1. Az a **közvetlen ügynök** ablakában válassza **töltse le Windows-ügynök (64 bites)** vagy **töltse le Windows-ügynök (32 bites)** .
    1. Telepítse az ügynököt a Windows-gépen. Másolás a **munkaterület-Azonosítót**, **elsődleges kulcs**, és **másodlagos kulcs** , és használja őket, amikor a rendszer kéri, a telepítés során.

4. Válassza ki a kívánt adatfolyam adattípusokat.
5. Kattintson a **megoldás telepítése**.
6. A megfelelő sémát a Log Analytics használata a Windows tűzfal, keresse meg **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Windows tűzfal Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

