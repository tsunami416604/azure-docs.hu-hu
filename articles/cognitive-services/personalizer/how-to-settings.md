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
ms.openlocfilehash: 976830232453eee0993e64ac445c2e6a2f7e20ef
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478577"
---
# <a name="personalizer-settings"></a>Personalizer beállításai

Szolgáltatás konfigurációja magában foglalja, hogyan kezeli az a szolgáltatás a felhőtechnológia, milyen gyakran a szolgáltatást ismerteti, milyen gyakran van retrained a modell és tárolt adatok mennyiségétől.

## <a name="create-personalizer-resource"></a>Personalizer erőforrás létrehozása

For-each visszajelzési ciklus Personalizer erőforrás létrehozása. 

1. Jelentkezzen be az [Azure portálra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Az előző kapcsolat veszi, hogy a **létrehozás** Personalizer szolgáltatás lapján. 
1. Adja meg a szolgáltatás nevét, és válasszon ki egy előfizetést, hely, tarifacsomag, és erőforráscsoport.
1. Válassza ki a megerősítés, majd **létrehozás**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Szolgáltatás beállításainak konfigurálása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. A Personalizer erőforrás található. 
1. Az a **erőforrás-kezelés** szakaszban jelölje be **beállítások**.

    Mielőtt elhagyja az Azure Portalon, másolja ki az egyik az erőforrás kulcsainak az a **kulcsok** lapot. Szüksége lesz használni a [Personalizer SDK](https://go.microsoft.com/fwlink/?linkid=2092353).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>A visszajelzési ciklus használati eset alapján ellenszolgáltatás beállításainak konfigurálása

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

A legújabb modellre, minden aktív esemény ellenszolgáltatás API-hívások betanított automatikusan Personalizer rangsorolják hívás nem használják. A **modell frissítési gyakoriság** beállítja, hogy milyen gyakran a rangsorolt hívás által használt modell frissítése. 

Olyan helyzetben, amikor szorosan követi a változásokat a felhasználói viselkedés magas modell frissítési gyakoriságot hasznosak. Ilyenek például a webhelyek, amelyek az élő hírek, vírusos tartalmat, vagy élő termék ajánlattételi folyamat során. Ezekben az esetekben használható 15 perces gyakorisággal. Használati esetek többségében egy alacsonyabb frissítési gyakoriság érvényben. Egyperces frissítési gyakoriságot hasznosak, ha egy alkalmazás kód használatával Personalizer, bemutatók végrehajtásakor.%n vagy interaktív módon tesztelése a machine learning szempontok hibakereséséhez.

![Modell frissítési gyakoriság beállítása, milyen gyakran egy új Personalizer modell retrained van.](media/settings/configure-model-update-frequency-settings.png)

A beállítás módosítása után győződjön meg arról, jelölje be **mentése**.

### <a name="data-retention"></a>Adatmegőrzés

**Adatmegőrzés időtartama** beállítja, hogy hány nap Personalizer tartja adatait tartalmazó naplófájlok. Múltbeli adatok naplók végrehajtásához szükség [offline értékelések](concepts-offline-evaluation.md), Personalizer hatékonyságának mérése és optimalizálása Learning házirend használt.

A beállítás módosítása után győződjön meg arról, jelölje be **mentése**.

## <a name="export-the-personalizer-model"></a>A Personalizer modell exportálása

A szakaszban az erőforrás-kezelés **modell és a házirend**, tekintse át a modell létrehozásához és az utolsó frissítés dátuma, és exportálja a jelenlegi modell. Az Azure portal vagy a Personalizer API-k segítségével exportálja egy modellfájl archiválási célokból. 

![Aktuális Personalizer modell exportálása](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importálás és exportálás learning házirend

A szakaszban az erőforrás-kezelés **modell és a házirend**, egy új tanulási házirend importálása vagy exportálása az aktuális learning házirend.

## <a name="next-steps"></a>További lépések

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[További információ a régiók rendelkezésre állása](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
