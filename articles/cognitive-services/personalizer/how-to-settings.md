---
title: A Personalizer konfigurálása
titleSuffix: Azure Cognitive Services
description: A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 4ab1dcf4f3554c941107ec653f717b3680543da2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490734"
---
# <a name="configure-personalizer"></a>A Personalizer konfigurálása

A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.

## <a name="create-personalizer-resource"></a>Személyre szabott erőforrás létrehozása

Hozzon létre egy személyre szabott erőforrást minden visszajelzési hurokhoz. 

1. Jelentkezzen be az [Azure portálra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Az előző hivatkozás a személyre szabási szolgáltatás **Létrehozás** lapjára lép. 
1. Adja meg a szolgáltatás nevét, válassza ki az előfizetést, a helyet, az árképzési szintet és az erőforráscsoportot.
1. Válassza ki a megerősítést, és válassza a **Létrehozás**lehetőséget.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Szolgáltatás konfigurálása a Azure Portalban

1. Bejelentkezés az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. A személyre szabott erőforrás megkeresése. 
1. Az **Erőforrás-kezelés** szakaszban válassza a **konfiguráció**elemet.

    A Azure Portal elhagyása előtt másolja át az egyik erőforrás-kulcsot a **kulcsok** lapról. Erre a [személyre szabott SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)használatára lesz szükség.

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>A visszajelzési hurok megtérülésének beállítása használati eset alapján

Konfigurálja a szolgáltatást a visszajelzési hurok használatára. A következő értékek módosításai visszaállítják a jelenlegi személyre szabott modellt, majd az utolsó 2 nap adatait áttanítják:

![Adja meg a visszajelzési hurok jutalmazási értékeit](media/settings/configure-model-reward-settings.png)

|Érték|Cél|
|--|--|
|jutalom várakozási ideje|Meghatározza azt az időtartamot, ameddig a személynek a rangsorolási híváshoz tartozó jutalmazási értékeket kell gyűjtenie a rangsor meghívásának pillanatától kezdve. Ezt az értéket úgy állítja be a rendszer, hogy megkérdezi, hogy mennyi ideig kell megvárnia a személyre szabási hívásokat? Az ablak beérkezését követően bekövetkező minden jutalom bekerül, de nem használható a tanuláshoz.|
|Alapértelmezett jutalom|Ha nem fogadja el a személyre szabott jutalmat a rangsorolási várakozási idő időszaka alatt, a személyre szabási híváshoz rendelt jutalmazási időablakban az alapértelmezett jutalom lesz hozzárendelve. Alapértelmezés szerint a legtöbb esetben az alapértelmezett jutalom nulla.|
|Jutalom összesítése|Ha ugyanahhoz a Range API-híváshoz több jutalom érkezik, a rendszer ezt az összesítési módszert használja: **Sum** vagy **legkorábbi**. A legkorábbi pontszámot kapott, és elveti a többit. Ez akkor hasznos, ha egyedi jutalmat szeretne használni a lehetséges duplikált hívások között. |

Az értékek módosítása után válassza a **Mentés**lehetőséget.

### <a name="configure-exploration"></a>Feltárás konfigurálása 

A személyre szabás lehetővé teszik az új minták észlelését és a felhasználói viselkedés változásainak időbeli változását az alternatívák feltárásával. A **feltárási** érték határozza meg, hogy a rangsorolási hívások hány százalékát kell megválaszolni a feltárással. 

Az érték módosításakor a rendszer alaphelyzetbe állítja az aktuális személyre szabott modellt, majd az utolsó 2 nap adatainak megfelelően áttanítja azt.

![A feltárási érték határozza meg, hogy a rangsorolási hívások hány százalékát kell megválaszolni a feltárással](media/settings/configure-exploration-setting.png)

Az érték módosítása után válassza a **Mentés**lehetőséget.

### <a name="model-update-frequency"></a>Modell frissítési gyakorisága

A minden aktív eseményből származó jutalmazási API-hívásokból betanított legújabb modellt nem használja automatikusan a személyre szabási sorrend hívása. A **modell frissítési gyakorisága** határozza meg, hogy a rangsor milyen gyakran használja a Range. 

A magas modell-frissítési gyakoriságok olyan helyzetekben hasznosak, amikor szorosan nyomon szeretné követni a felhasználói viselkedések változásait. Ilyenek például az élő Hírek, a vírusos tartalmak vagy az élő termékek ajánlattételi helyei. Ezeket a forgatókönyveket 15 perces gyakorisággal használhatja. A legtöbb felhasználási eset esetében az alacsonyabb frissítési gyakoriság érvényes. Az egyperces frissítési gyakoriságok akkor hasznosak, ha az alkalmazás kódját a személyre szabás, a demók vagy a gépi tanulási szempontok interaktív tesztelése során végzi.

![A modell frissítési gyakorisága határozza meg, hogy milyen gyakran van új személyre szabott modell újratanítása.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Az érték módosítása után válassza a **Mentés**lehetőséget.

### <a name="data-retention"></a>Adatmegőrzés

**Az adatmegőrzési időszak** azt állítja be, hogy hány nap személy tartja az adatnaplókat. Az [Offline értékelések](concepts-offline-evaluation.md)végrehajtásához múltbeli adatnaplókra van szükség, amelyek a személyre szabott és a tanulási szabályzatok optimalizálására szolgálnak.

Az érték módosítása után válassza a **Mentés**lehetőséget.

## <a name="export-the-personalizer-model"></a>A személyre szabott modell exportálása

A **modell-és tanulási beállítások**erőforrás-kezelés szakaszában tekintse át a modell létrehozásának és utolsó frissítésének dátumát, és exportálja az aktuális modellt. A Azure Portal vagy a személyre szabható API-k használatával archiválhatja a modelleket archiválás céljából. 

![Aktuális személyre szabott modell exportálása](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Képzési szabályzat importálása és exportálása

A **modell-és tanulási beállítások**erőforrás-kezelés szakaszában importáljon egy új képzési szabályzatot, vagy exportálja az aktuális tanulási szabályzatot.
A korábbi exportálásokból lekérheti a tanulási szabályzatok fájljait, vagy letöltheti az offline értékelések során felderített optimalizált szabályzatokat. Ezeknek a fájloknak a manuális módosítása hatással lesz a gépi tanulási teljesítményre és az offline értékelések pontosságára, és a Microsoft nem tudja tanúsítani a gépi tanulás és az értékelések pontosságát, vagy a manuálisan szerkesztett házirendek miatti szolgáltatási kivételeket.

## <a name="clear-data-for-your-learning-loop"></a>Az adatgyűjtési hurokhoz tartozó adattörlés

1. A Azure Portal a személyre szabott erőforráshoz a **modell és tanulás beállításai** lapon válassza az **adattörlés**lehetőséget.
1. Az összes érték törléséhez és a tanulási hurok eredeti állapotba való visszaállításához jelölje be a 3 jelölőnégyzetet.

    ![A Azure Portalban törölje a jelet az adatok személyre szabása erőforrásból elemre.](./media/settings/clear-data-from-personalizer-resource.png)

    |Érték|Cél|
    |--|--|
    |Naplózott személyre szabás és jutalmazási érték.|A naplózási adatai kapcsolat nélküli értékelésekben használatosak. Törölje az adatait, ha alaphelyzetbe állítja az erőforrást.|
    |A személyre szabott modell alaphelyzetbe állítása.|Ez a modell minden átképzésen megváltozik. A betanítás gyakorisága a konfiguráció lapon a **feltöltési modell gyakorisága** **beállításban** van megadva. |
    |Állítsa be a képzési szabályzatot alapértelmezett értékre.|Ha az offline kiértékelés részeként módosította a képzési szabályzatot, a rendszer visszaállítja az eredeti tanulási szabályzatot.|

1. A törlési folyamat elindításához válassza a **kijelölt adatelemek törlése** lehetőséget. Az állapot az Azure-értesítésekben szerepel, a jobb felső navigációs sávon. 

## <a name="next-steps"></a>További lépések


[A régió elérhetőségének megismerése](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
