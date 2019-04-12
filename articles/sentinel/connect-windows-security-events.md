---
title: Windows biztonsági események adatainak csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Windows biztonsági események adatainak Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d131d39a9d9770046fa3f9368250204e4d0b0162
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491952"
---
# <a name="connect-windows-security-events"></a>A Windows biztonsági eseményeinek csatlakoztatása 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows-kiszolgálók az Azure Sentinel-munkaterülethez kapcsolódó összes biztonsági eseményt streamelheti. Ez a kapcsolat lehetővé teszi megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat és vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket.  Események streamelése választhatja ki:

- **Az összes esemény** – az összes Windows biztonsági és AppLocker-eseményeket.
- **Közös** -eseményeinek naplózási célokra szabványos készletét.
- **Minimális** -eseményeket, amelyek esetleg jelzik a potenciális fenyegetések egy kis készletét. Ha engedélyezi ezt a beállítást, akkor tudja a teljes auditnaplót.
- **Nincs** – nincs biztonsági és AppLocker-eseményeket.

> [!NOTE]
> 
> - Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="set-up-the-windows-security-events-connector"></a>A Windows biztonsági eseményeinek connector telepítése

Teljes mértékben a Windows biztonsági események integrálása az Azure-Sentinel:

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** majd kattintson a a **Windows biztonsági eseményeinek** csempére. 
1. Válassza ki a kívánt adatfolyam adattípusokat.
1. Kattintson az **Update** (Frissítés) elemre.
6. A megfelelő sémát a Log Analytics használata a Windows biztonsági eseményeket, keresse meg **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók elindítani, megjelenik a Log Analytics körülbelül 20 percet igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Windows biztonsági eseményeinek Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

