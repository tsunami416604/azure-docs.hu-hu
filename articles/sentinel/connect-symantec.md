---
title: Csatlakozás Azure-on Előzetesben Sentinel-Symantec ICDX adatok |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Symantec ICDX adatokat az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502083"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Csatlakozás a Symantec ICDX berendezés 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDX összekötő segítségével kapcsolódhat egyszerűen azokhoz az összes a Symantec biztonsági megoldásainak naplóit az Azure Sentinel, megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat, és a vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket. Symantec ICDX és Sentinel-Azure közötti integráció lehetővé teszi a REST API-t használja.


> [!NOTE]
> Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="configure-and-connect-symantec-icdx"></a>Beállítása és csatlakozása a Symantec ICDX 

Symantec ICDX is integrálhatja, és közvetlenül az Azure Sentinel-naplók exportálása.

1. Nyissa meg a ICDX felügyeleti konzolt.
2. A bal oldali navigációs menüben válassza ki a **konfigurációs** , majd a **továbbítók** fülre.
3. A Microsoft Azure Log Analytics sorban kattintson **további**, utána pedig **szerkesztése**. 
4. Az a **a Microsoft Azure Log Analytics továbbító** ablakban állítsa be a következőket:
    - Egyéni napló neve hagyja az alapértelmezett, SymantecICDX.
    - A munkaterület Azonosítóját másolja és illessze be azt a **ügyfél-azonosító** mező. Másolás a **elsődleges kulcs** , és illessze be a megosztott kulcs mezőt. Másolhatja ezeket az értékeket Sentinel-Azure portal kiválasztásával **adatösszekötők** , majd **Symantec ICDX**.
6. A Symantec ICDX események Log Analytics használja a megfelelő sémát, keresse meg **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Symantec ICDX Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

