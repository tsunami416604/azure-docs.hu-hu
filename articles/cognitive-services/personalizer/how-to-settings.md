---
title: A Personalizer konfigurálása
description: A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran van újrabetanítva, és mennyi adatot tárolnak.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219356"
---
# <a name="configure-personalizer-learning-loop"></a>A Personalizer tanulási ciklusának konfigurálása

A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran van újrabetanítva, és mennyi adatot tárolnak.

Konfigurálja a tanulási hurkot a **konfigurációs** lapon, az Azure Portalon az adott Personalizer erőforráshoz.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Jutalmak konfigurálása a visszacsatolási ciklushoz

Konfigurálja a szolgáltatást a tanulási hurok jutalmak használatára. A következő értékek módosításai alaphelyzetbe állítják az aktuális Personalizer modellt, és újrabetanítják azt az adatok utolsó 2 napjával.

> [!div class="mx-imgBorder"]
> ![A visszacsatolási ciklus jutalomértékeinek konfigurálása](media/settings/configure-model-reward-settings.png)

|Érték|Cél|
|--|--|
|Jutalom várakozási idő|Beállítja, hogy a Personalizer mennyi ideig gyűjti a Ranghívás jutalomértékeit, attól a pillanattól kezdve, hogy a Rang hívás megtörténik. Ezt az értéket a következő kérdés állítja be: "Mennyi ideig várjon a Personalizer a jutalomhívásokra?" Az ezen ablak után érkező jutalmakat naplózzuk, de nem használjuk fel a tanuláshoz.|
|Alapértelmezett jutalom|Ha a Personalize nem fogad jutalomhívást a Ranghíváshoz társított Jutalom várakozási idő ablak ban, a Personalizer hozzárendeli az alapértelmezett jutalmat. Alapértelmezés szerint, és a legtöbb esetben az alapértelmezett jutalom nulla (0).|
|Jutalom összesítése|Ha ugyanahhoz a Rank API-híváshoz több jutalom érkezik, a program ezt az összesítési módszert használja: **sum** vagy **earliest .** Legkorábbi csákány a legkorábbi pontszámot kapott, és eldobja a többit. Ez akkor hasznos, ha egyedi jutalmat szeretne az esetlegesen ismétlődő hívások között. |

Az értékek módosítása után válassza a **Mentés**lehetőséget.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>A feltárás konfigurálása úgy, hogy a tanulási hurok alkalmazkodjon

A személyre szabás képes új mintákat felfedezni, és alkalmazkodni a felhasználói viselkedés változásaihoz az idő múlásával az alternatívák feltárásával a betanított modell előrejelzésének használata helyett. A **feltárási** érték határozza meg, hogy a rangsorolási hívások hány százaléka válaszol a feltárással.

Az érték módosításai alaphelyzetbe állítják az aktuális Personalizer modellt, és újrabetanítják azt az adatok utolsó 2 napjával.

![A feltárási érték határozza meg, hogy a Rangsorolási hívások hány százaléka válaszol a feltárással](media/settings/configure-exploration-setting.png)

Az érték módosítása után válassza a **Mentés**lehetőséget.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Modellfrissítési gyakoriság konfigurálása a modellbetanításhoz

A **modell frissítési gyakorisága beállítja,** hogy milyen gyakran a modell betanítása.

|Frekvencia beállítás|Cél|
|--|--|
|1 perc|Az egyperces frissítési gyakoriságok akkor hasznosak, ha az alkalmazás **kódját a** Personalizer használatával, demókkal vagy interaktívan teszteli a gépi tanulási szempontokat.|
|15 perc|A magas modellfrissítési gyakoriságok olyan helyzetekben hasznosak, amikor **szorosan nyomon** szeretné követni a felhasználói viselkedések változásait. Ilyenek például az élő híreken, vírusos tartalmakon vagy élő termékajánlattételen futó webhelyek. Ezekben a forgatókönyvekben 15 perces gyakoriságot használhat. |
|1 óra|A legtöbb használati esetben az alacsonyabb frissítési gyakoriság hatékony.|

![A modellfrissítés gyakorisága beállítja, hogy milyen gyakran van újrabetanítva egy új Personalizer modell.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Az érték módosítása után válassza a **Mentés**lehetőséget.

## <a name="data-retention"></a>Adatmegőrzés

**Az adatmegőrzési időszak** határozza meg, hogy a Personalizer hány napig tart adatnaplókat. A korábbi adatnaplók szükségesek [az offline értékelések](concepts-offline-evaluation.md)elvégzéséhez, amelyek a Personalizer hatékonyságának mérésére és a tanulási szabályzat optimalizálására szolgálnak.

Az érték módosítása után válassza a **Mentés**lehetőséget.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>A modell alaphelyzetbe állítását tartalmazó beállítások

A következő műveletek közé tartozik a modell azonnali átképzése az adatok utolsó 2 napja.

* Jutalom
* Feltárás

Az összes adat [törléséhez](how-to-manage-model.md) használja a **Model and learning settings ** oldalt.

## <a name="next-steps"></a>További lépések

[További információ a modell kezeléséről](how-to-manage-model.md)
