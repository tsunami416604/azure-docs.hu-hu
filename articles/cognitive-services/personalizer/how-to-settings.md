---
title: Beállítások konfigurálása – személyre szabás
titleSuffix: Azure Cognitive Services
description: A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: cd67f435ff390267e01acd99594b9063db4c4ee1
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559086"
---
# <a name="personalizer-settings"></a>Személyre szabott beállítások

A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.

## <a name="create-personalizer-resource"></a>Személyre szabott erőforrás létrehozása

Hozzon létre egy személyre szabott erőforrást minden visszajelzési hurokhoz. 

1. Jelentkezzen be az [Azure portálra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Az előző hivatkozás a személyre szabási szolgáltatás **Létrehozás** lapjára lép. 
1. Adja meg a szolgáltatás nevét, válassza ki az előfizetést, a helyet, az árképzési szintet és az erőforráscsoportot.
1. Válassza ki a megerősítést, és válassza a **Létrehozás**lehetőséget.

## <a name="configure-service-settings-in-the-azure-portal"></a>A szolgáltatás beállításainak konfigurálása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. A személyre szabott erőforrás megkeresése. 
1. Az **Erőforrás-kezelés** szakaszban válassza a **Beállítások**lehetőséget.

    A Azure Portal elhagyása előtt másolja át az egyik erőforrás-kulcsot a **kulcsok** lapról. Erre a [személyre szabott SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)használatára lesz szükség.

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>A visszajelzési hurok jutalmazási beállításainak konfigurálása használati eset alapján

Adja meg a szolgáltatás beállításait a visszajelzési hurokhoz a jutalmak használatára. A következő beállítások módosításai alaphelyzetbe állítja a jelenlegi személyre szabott modellt, és áttanítja az utolsó 2 nap után:

![A visszajelzési hurok jutalmazási beállításainak konfigurálása](media/settings/configure-model-reward-settings.png)

|Beállítás|Cél|
|--|--|
|Jutalom várakozási ideje|Meghatározza azt az időtartamot, ameddig a személynek a rangsorolási híváshoz tartozó jutalmazási értékeket kell gyűjtenie a rangsor meghívásának pillanatától kezdve. Ezt az értéket a következő kéréssel állíthatja be: "Mennyi ideig legyen a személyre szabott várakozás a jutalmak meghívására?" Az ablak beérkezését követően bekövetkező minden jutalom bekerül, de nem használható a tanuláshoz.|
|Alapértelmezett jutalom|Ha nem fogadja el a személyre szabott jutalmat a rangsorolási várakozási idő időszaka alatt, a személyre szabási híváshoz rendelt jutalmazási időablakban az alapértelmezett jutalom lesz hozzárendelve. Alapértelmezés szerint a legtöbb esetben az alapértelmezett jutalom nulla.|
|Jutalom összesítése|Ha ugyanahhoz a Range API-híváshoz több jutalom érkezik, a rendszer ezt az összesítési módszert használja: **Sum** vagy legkorábbi. A legkorábbi pontszámot kapott, és elveti a többit. Ez akkor hasznos, ha egyedi jutalmat szeretne használni a lehetséges duplikált hívások között. |

A beállítások módosítása után válassza a **Mentés**lehetőséget.

### <a name="exploration-setting"></a>Feltárási beállítás 

A személyre szabás lehetővé teszik az új minták észlelését és a felhasználói viselkedés változásainak időbeli változását az alternatívák feltárásával. A **feltárási** beállítás határozza meg, hogy a rangsorolási hívások hány százalékát kell megválaszolni a feltárással. 

A beállítás módosításai alaphelyzetbe állítja a jelenlegi személyre szabott modellt, és áttanítja azt az utolsó 2 nap során.

![A feltárási beállítás határozza meg, hogy a rangsorolási hívások hány százalékát kell megválaszolni a feltárással](media/settings/configure-exploration-setting.png)

A beállítás módosítása után válassza a **Mentés**lehetőséget.

### <a name="model-update-frequency"></a>Modell frissítési gyakorisága

A minden aktív eseményből származó jutalmazási API-hívásokból betanított legújabb modellt nem használja automatikusan a személyre szabási sorrend hívása. A **modell frissítési gyakorisága** határozza meg, hogy a rangsor milyen gyakran használja a Range. 

A magas modell-frissítési gyakoriságok olyan helyzetekben hasznosak, amikor szorosan nyomon szeretné követni a felhasználói viselkedések változásait. Ilyenek például az élő Hírek, a vírusos tartalmak vagy az élő termékek ajánlattételi helyei. Ezeket a forgatókönyveket 15 perces gyakorisággal használhatja. A legtöbb felhasználási eset esetében az alacsonyabb frissítési gyakoriság érvényes. Az egyperces frissítési gyakoriságok akkor hasznosak, ha az alkalmazás kódját a személyre szabás, a demók vagy a gépi tanulási szempontok interaktív tesztelése során végzi.

![A modell frissítési gyakorisága határozza meg, hogy milyen gyakran van új személyre szabott modell újratanítása.](media/settings/configure-model-update-frequency-settings.png)

A beállítás módosítása után válassza a **Mentés**lehetőséget.

### <a name="data-retention"></a>Adatmegőrzés

**Az adatmegőrzési időszak** azt állítja be, hogy hány nap személy tartja az adatnaplókat. Az [Offline értékelések](concepts-offline-evaluation.md)végrehajtásához múltbeli adatnaplókra van szükség, amelyek a személyre szabott és a tanulási szabályzatok optimalizálására szolgálnak.

A beállítás módosítása után válassza a **Mentés**lehetőséget.

## <a name="export-the-personalizer-model"></a>A személyre szabott modell exportálása

A **modell és a házirend**erőforrás-kezelés szakaszában tekintse át a modell létrehozásának és utolsó frissítésének dátumát, és exportálja az aktuális modellt. A Azure Portal vagy a személyre szabható API-k használatával archiválhatja a modelleket archiválás céljából. 

![Aktuális személyre szabott modell exportálása](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Képzési szabályzat importálása és exportálása

A **modell és a házirend erőforrás-** kezelés szakaszában importáljon egy új képzési szabályzatot, vagy exportálja az aktuális képzési szabályzatot.
A korábbi exportálásokból lekérheti a tanulási szabályzatok fájljait, vagy letöltheti az offline értékelések során felderített optimalizált szabályzatokat. Ezeknek a fájloknak a manuális módosítása hatással lesz a gépi tanulási teljesítményre és az offline értékelések pontosságára, és a Microsoft nem tudja tanúsítani a gépi tanulás és az értékelések pontosságát, vagy a manuálisan szerkesztett házirendek miatti szolgáltatási kivételeket.

## <a name="next-steps"></a>További lépések

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[A régió elérhetőségének megismerése](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
