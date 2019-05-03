---
title: Beállítások konfigurálása
titleSuffix: Azure Cognitive Services
description: Szolgáltatás konfigurációja magában foglalja, hogyan kezeli az a szolgáltatás a felhőtechnológia, milyen gyakran a szolgáltatást ismerteti, milyen gyakran van retrained a modell és tárolt adatok mennyiségétől.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 0cd08e1191c68c57975d3e68648134925155e7f2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027221"
---
# <a name="personalizer-settings"></a>Personalizer beállításai

Szolgáltatás konfigurációja magában foglalja, hogyan kezeli az a szolgáltatás a felhőtechnológia, milyen gyakran a szolgáltatást ismerteti, milyen gyakran van retrained a modell és tárolt adatok mennyiségétől.

For-each visszajelzési ciklus Personalizer erőforrás létrehozása. 

## <a name="configure-service-settings-in-the-azure-portal"></a>Szolgáltatás beállításainak konfigurálása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Personalizer erőforrás található. 
1. Az a **erőforrás-kezelés** szakaszban jelölje be **beállítások**.

    ![Jelentkezzen be az Azure portálra. A Personalizer erőforrás található. A Resource management területen válassza ki a beállításokat.](media/settings/how-to-configure.png)

### <a name="reward-settings-for-the-feedback-loop"></a>A visszajelzési ciklus ellenszolgáltatás beállításai

A visszajelzési ciklus használata jutalmakat a szolgáltatás beállításainak konfigurálása. A következő beállítások módosításait alaphelyzetbe állítása az aktuális Personalizer modell, és a szoftveres átképezése, és az utolsó 2 nap adatait:

![A visszajelzési ciklus ellenszolgáltatás beállításainak konfigurálása](media/settings/configure-model-reward-settings.png)

|Beállítás|Cél|
|--|--|
|Ellenszolgáltatás várakozási idő|Mennyi ideig során melyik Personalizer gyűjt ellenszolgáltatás értékek rangsorolják hívás csoportok, attól a pillanattól kezdve indítása a rangsorolt hívás történik. Ez az érték azzal, hogy van beállítva: "Hogy mennyi ideig Personalizer várjon a felhőtechnológia hívásokat?" Bármely után ezt az ablakot érkező ellenszolgáltatás lesz naplózva, de nem használt tanulási.|
|Alapértelmezett ellenszolgáltatás|Ha nincs ellenszolgáltatás hívás nem érkezett hívás által Personalizer a társított egy rang ellenszolgáltatás várakozási idő időszak alatt, Personalizer fogja hozzárendelni az alapértelmezett fejében. Alapértelmezés szerint, és a legtöbb esetben az alapértelmezett ellenszolgáltatás értéke nulla.|
|Ellenszolgáltatás összesítés|Ha több jutalmakat ugyanaz a rangsor API meghívása, használja az összesítési módszer: **sum** vagy **legkorábbi**. Legkorábbi választja ki a legkorábbi pontszám, és elveti a többi. Ez akkor hasznos, ha azt szeretné, hogy egyedi ellenszolgáltatás valószínűleg ismétlődő hívások között. |

Ezek a beállítások módosítása után győződjön meg arról, jelölje be **mentése**.

### <a name="exploration-setting"></a>Feltárás beállítás 

Személyre szabás is képes felderíteni új mintákat, és tudjon alkalmazkodni felhasználói viselkedés idővel alternatívák áttekintésével. A **feltárás** beállítás azt határozza meg, hány százaléka rangsorolják hívások feltárása a válaszok. 

A beállítások módosítása az aktuális Personalizer modell alaphelyzetbe, és a újratanítása, az adatok az elmúlt 2 nap.

![A feltárás beállítás azt határozza meg, hány százaléka rangsorolják hívások feltárása a válaszok](media/settings/configure-exploration-setting.png)

A beállítás módosítása után győződjön meg arról, jelölje be **mentése**.

### <a name="model-update-frequency"></a>Modell frissítési gyakoriság

**Frissítési gyakoriság modell** állítja be, hogy milyen gyakran egy új Personalizer modell retrained van. 

![Modell frissítési gyakoriság beállítása, milyen gyakran egy új Personalizer modell retrained van.](media/settings/configure-model-update-frequency-settings.png)

A beállítás módosítása után győződjön meg arról, jelölje be **mentése**.

### <a name="data-retention"></a>Adatmegőrzés

**Adatmegőrzés időtartama** beállítja, hogy hány nap Personalizer tartja adatait tartalmazó naplófájlok. Múltbeli adatok naplók végrehajtásához szükség [offline értékelések](concepts-offline-evaluation.md), Personalizer hatékonyságának mérése és optimalizálása Learning házirend használt.

A beállítás módosítása után győződjön meg arról, jelölje be **mentése**.

## <a name="export-the-personalizer-model"></a>A Personalizer modell exportálása

A szakaszban az erőforrás-kezelés **modell és a házirend**, tekintse át a modell létrehozásához és az utolsó frissítés dátuma, és exportálja a jelenlegi modell.

![Aktuális Personalizer modell exportálása](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importálás és exportálás learning házirend

A szakaszban az erőforrás-kezelés **modell és a házirend**, egy új tanulási házirend importálása vagy exportálása az aktuális learning házirend.

## <a name="next-steps"></a>További lépések

[Megerősítő tanulást](concepts-reinforcement-learning.md) 
