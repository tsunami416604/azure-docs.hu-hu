---
title: Az Azure-Sentinel-előzetes verzióban érhető el a Windows biztonsági események adatainak összegyűjtése |} A Microsoft Docs
description: Útmutató az Azure-Sentinel a Windows biztonsági események adatainak összegyűjtése.
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 90bff4cd98046a42fa31d98ff00e76d7fc9ac307
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576088"
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

1. Az Azure-Sentinel-portálon válassza **adatgyűjtés** majd kattintson a a **Windows biztonsági eseményeinek** csempére. 
1. Válassza ki a kívánt adatfolyam adattípusokat.
1. Kattintson az **Update** (Frissítés) elemre.
6. A megfelelő sémát a Log Analytics használata a Windows biztonsági eseményeket, keresse meg **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók elindítani, megjelenik a Log Analytics körülbelül 20 percet igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Windows biztonsági eseményeinek Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

