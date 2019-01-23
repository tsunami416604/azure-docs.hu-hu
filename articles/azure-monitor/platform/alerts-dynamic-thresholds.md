---
title: Riasztások létrehozása az Azure monitorban dinamikus küszöbértékekkel
description: Riasztások létrehozása a machine learning-alapú dinamikus küszöbértékekkel
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449010"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Metrikákhoz kapcsolódó riasztások dinamikus küszöbértékekkel az Azure monitorban (nyilvános előzetes verzió)

Metrikariasztás dinamikus küszöbértékek észlelése a fejlett gépi tanulási (ML) további metrikák általi korábbi viselkedés, mintákat és rendellenességeket, amelyek jelzik a lehetséges szolgáltatással kapcsolatos problémák azonosítására használ. Egy egyszerű felhasználói felület és a méretezett operations támogatást biztosít a felhasználók olyan teljesen automatizált módon konfigurálhatja a riasztási szabályok az Azure Resource Manager API-n keresztül.

Riasztási szabály létrehozása után fog aktiválódni csak amikor a figyelt metrika nem viselkednek, várt, a testre szabott küszöbértékek alapján.

Örömmel vennénk visszajelzéseit, legyen hamarosan azurealertsfeedback@microsoft.com.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Miért és mikor ajánlott a dinamikus feltétel típusa használ?

1. **Méretezhető riasztási** – riasztások szabályokat hozhat létre dinamikus küszöbértékek metrika sorozat több száz küszöbértékek szabott egyszerre. Riasztási szabály meghatározása csak egyetlen mértéket a ugyanolyan könnyű még biztosítása. Használata kezelheti a felhasználói felületen vagy az Azure Resource Manager API-eredmények kevesebb riasztási szabályok az. A méretezhető módszer különösen hasznos, metrika méretek esetén, vagy több erőforrásokhoz, például az összes előfizetés erőforrások alkalmazása esetén. Amely fordítja le a felügyeleti és a riasztásokat szabályok létrehozását mentése jelentős mennyiségű időt. [További tudnivalók a metrika riasztások dinamikus küszöbértékekkel sablonokkal konfigurálása](alerts-metric-create-templates.md).

1. **A metrika mintafelismerést intelligens** – az egyedi gépi Tanulási technológiával képesek vagyunk metrikamintákhoz észlelése és tudjon alkalmazkodni metrika idővel, amely gyakran szezonalitás (óránként / naponta / hetente). Idő és a riasztási gyakorlattól eltérő minta alapján a metrikák általi viselkedéséhez gyakorlatainak mentesíti az, hogy mindegyik metrikát "megfelelő" küszöbértéke terhe. A dinamikus küszöbértékek használt gépi Tanulási algoritmus úgy tervezték, hogy megakadályozza a zajos (kis pontosságú) és az adott várt mintának nem rendelkező wide (alacsony visszaírási) küszöbértékeket.

1. **Intuitív konfigurációs** – dinamikus küszöbértékek engedélyezése kell rendelkeznie a metrika tartalmazó tartományhoz ismerete enyhítése fogalmait, használatával metrikákhoz kapcsolódó riasztások beállítását.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Hogyan konfigurálhatók a riasztásokat szabályok dinamikus küszöbértékekkel?

Riasztások dinamikus küszöbértékekkel metrika riasztások az Azure Monitor használatával konfigurálható. [További információ a metrika-riasztások konfigurálása](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hogyan számítják ki a küszöbértékek?

A dinamikus küszöbértéket folyamatosan megtanulja az adatokat a metrika sorozat próbál algoritmusok és metódusok. a modell és algoritmusok és módszerek használatával modellezheti próbál. Minták észleli az adatok, például a szezonalitás (óránként / naponta / hetente), és képesek zajos metrikákat (például a gép CPU és memória) kezelésére és metrikák alacsony diszperziós (például a rendelkezésre állás és a hiba mellett).

A küszöbértékek oly módon, hogy ezeket a küszöbértékeket eltérést jelöli a metrikákkal kapcsolatos viselkedést anomáliát ki van jelölve.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Dinamikus küszöbérték középérték "Érzékenysége" beállítás mire?

Riasztási küszöbérték érzékenységét egy magas szintű fogalom, amely szabályozza a metrikákkal kapcsolatos viselkedés riasztást kiváltó szükséges eltérés.
Ezt a beállítást nem szükséges a mérőszám, például a statikus küszöbérték kapcsolatos adatait. Az elérhető lehetőségek:

- Magas – a küszöbértékeket a rövid és a metrika sorozat minta közeli lesz. Riasztási szabály akkor aktiválódik, a legkisebb értékét, a további riasztások eredményez.
- Közepes – kevesebb szoros és több elosztott terhelésű küszöbértékek, kevesebb riasztást, mint a magas érzékenységi (alapértelmezett).
- Alacsony – a küszöbértékek lesz metrika sorozat minta további távolsága a laza. Riasztási szabály csak aktiválják a nagy eltérés, kevesebb riasztások eredményez.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Mik a dinamikus küszöbértékek "Kezelő" beállítás lehetőségei?

A szabály riasztásokat hozhat létre dinamikus küszöbértékek szabott küszöbértékek mindkét felső a metrikákkal kapcsolatos viselkedést és dolní meze használatával ugyanaz a riasztási szabály alapján.
A riasztás a következő három feltétel egyikének aktiválását közül választhat:

- A felső küszöbértéknél nagyobb vagy kisebb, mint az alsó küszöbértéket (alapértelmezett)
- Nagyobb, mint a felső küszöbérték
- Az alsó küszöbérték alatti.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Mit dinamikus küszöbértékek középérték speciális beállításait?

**Sikertelen időszakok** -dinamikus küszöbértékek is lehetővé teszi, hogy "A riasztást kiváltó szabálysértések száma", konfigurálhatja a minimálisan szükséges (az időkeret alapértelmezett értéke négy riasztást a rendszer egy adott időtartományon belül szorzataként eltérések 20 percig). A felhasználó sikertelen időszakok konfigurálhatja, és válassza ki a, a sikertelen és a időtartomány módosításával riasztást kap. A riasztási zaj átmeneti által generált csökkenti. Példa:

Riasztást kiváltó, ha a probléma folyamatos 20 perc, 5 perc elteltével egy adott időszak csoportosítása 4 egymást követő alkalommal használja a következő beállításokat:

![Folyamatos probléma időszakok beállításainak 20 perc, 5 perces időszak adott csoportja 4 egymást követő alkalommal sikertelen](media/alerts-dynamic-thresholds/0008.png)

Ha hiba történt a dinamikus küszöbérték megsértését az elmúlt 30 perc, 5 perces időtartammal 20 perce a riasztást kiváltó, használja a következő beállításokat:

![Sikertelen időszakok beállításait a probléma az elmúlt 30 perc, 5 perces időszak csoportosítása a 20 perce](media/alerts-dynamic-thresholds/0009.png)

**Időpontnál régebbi adatok mellőzése** -felhasználók is adhat meg, amelyből a rendszer kell kezdődnie kiszámítása a küszöbértékeket a kezdő dátumot. Egy tipikus használati eset akkor fordulhat elő, ha egy erőforrást egy tesztelési módban futó volt, és a egy éles környezetbeli számítási feladatokra kiszolgálása érdekében mostantól hitelesítenie kelljen magát, és ezért a tesztelési fázis során bármilyen mérőszám viselkedését kell figyelmen kívül kell hagyni.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Lassú viselkedés módosítani fogja a metrikaindító metrikanevét riasztást?

Valószínűleg nem. Dinamikus küszöbérték jók jelentős eltérések észlelése lassan fejlődő problémák helyett.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Mennyi adatot szolgál, és ezután kiszámíthatja a küszöbértékeket?

A diagramon jelennek meg a mérőszám a riasztási szabály létrehozása előtt a küszöbértékek alapján számítjuk az előzményadatok utolsó 10 nap, a riasztási szabály létrehozása után a dinamikus küszöbértékek fogja beszerezni elérhető további előzményadatokat és lesz Ismerje meg, hogy a küszöbértékek pontosabb új adatok alapján folyamatosan.
