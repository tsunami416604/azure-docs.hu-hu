---
title: Riasztásokat hoznak létre az Azure figyelő dinamikus küszöbökkel
description: Hozzon létre riasztásokat machine learning-alapú dinamikus küszöbértékek
author: antonfrMSFT
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: mbullwin
ms.reviewer: antonfr
ms.component: alerts
ms.openlocfilehash: 01f924e0b3a2976a3f537cb5acac842eeeaccb4b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263313"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Riasztások dinamikus küszöbökkel Azure figyelőben (korlátozott nyilvános előzetes verzió)

Dinamikus küszöbökkel a riasztások jelezhetik Azure metrika riasztást Azure figyelőben, amelyek speciális Machine Learning (ML) lehetőségeket további metrikák korábbi viselkedést automatikusan kiszámítja az alapkonfigurációkat, és felhasználhatja őket riasztási küszöbértékek kihasználhatja továbbfejlesztése.

A dinamikus küszöbértékek használatának előnyei a következők:

- A figyelő automatikusan megtanulja a metrika korábbi teljesítményét, és alkalmazza az ML-algoritmusok riasztási küszöbértékek meghatározásához egy előre meghatározott kemény határ beállítását társított bajlódni mentése.
- Azonosítani tudják határozza viselkedését, és csak a várt határozza működése eltérő módon tudják a riasztást. Dinamikus küszöbértékek metrika riasztások nem indít el, ha a szolgáltatás a hétvégén rendszeresen üresjáratban, és minden hétfőn majd napra. Jelenleg támogatott: óránkénti, napi és heti szezonalitás értékének.
- Folyamatosan Tanulja meg a metrika teljesítmény és adaptív metrika változásait.

Dinamikus küszöbérték-alapú értesítések érhetők el az összes Azure figyelő-alapú felsorolt ezen metrika források [cikk](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Jelentkezzen az előzetes eléréséhez

Ezt a képességet a léptetési érvénybe [regisztráljon az előzetes](http://aka.ms/DynamicThresholdMetricAlerts). Mivel mindig, Örülnénk, ha visszajelzést, legyen hamarosan: [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Riasztások konfigurálása dinamikus küszöbökkel

Dinamikus küszöbértékek riasztások konfigurálható riasztásait az Azure-figyelő

![Riasztások megtekintése](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Dinamikus küszöbértékeket egy riasztási szabály létrehozása

1. A figyelő riasztást ablaktábla, válassza ki a **új riasztást szabály** gombra kattintva új riasztás létrehozása, az Azure-ban.

   ![Új riasztási szabály](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. Szabály létrehozása szakasz a három részből álló mellett látható: _határozza meg a riasztási feltétel_, _határozza meg a riasztás részleteinek_, és _Define művelet csoport_. Először kezdődnie a _határozza meg a riasztási feltétel_ szakasz használata a **válasszon cél** adja meg a célként megadott erőforrás kiválasztásával mutató hivatkozást. Egy szükséges erőforrás van kiválasztva, kattintson a Kész gombra.

   ![Válassza ki a cél](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. Ezután használja a **adja meg a feltételeket** gombra kattintva érhető el, az erőforrás- és a jel listából jel beállítások listájának megtekintéséhez válasszon egy megfelelő **metrika** lehetőséget. (Például CPU százaléka.)

   ![Feltétel hozzáadása](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. Konfigurálása jel logika képernyőn riasztási logika szakaszában lehetősége van a feltétel váltson át olyan típusú dinamikus, ami automatikusan fog létrehozni a dinamikus küszöbértékek (piros vonalak) mellett a mérték (kék sor).

   ![Dinamikus](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. A diagramon megjelenő küszöbértékek alapján kiszámítása az előzményadatok elmúlt hét napban, ha riasztás jön létre, a dinamikus küszöbértékek elérhető további előzményadatokat arányban fogják beszerezni és folyamatosan megtudhatja, hogy új adatok alapján a a küszöbértékek pontosabb.

6. Riasztási logika további beállítások:
   - Probléma merült fel; a riasztás az alábbi három feltétel egyikének aktiválására választhat:
       - A felső küszöbértéknél nagyobb vagy kisebb az alsó küszöbértéknél (alapértelmezett)
       - Nagyobb, mint a felső küszöbérték
       - Kisebb az alsó küszöbértéknél.
   - Összesítési idő: sum, min, max (alapértelmezett), átlag.
   - Riasztási Sensitivity:
       - Magas – további riasztások, mint a riasztás akkor is kiváltódik legkisebb eltérés.
       - Med – érzékeny magas, a magas érzékenységi (alapértelmezett)-nál kevesebb riasztások-nál kisebb
       - Alacsony – a legkevésbé bizalmas küszöbértéket.

    ![Riasztási logika beállítások](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Kiértékelése alapján:
    -  Milyen időtartamig, a riasztás kell keresni a megadott feltétel kiválasztásával a **időszak**.

    ![Értékelés alapja](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Támogatott Küszöbidőszakok értékeit: 5 perc, a 10 perc, 30 perc és 1 óra.

   Átmeneti igényeiben jelentkező által előállított riasztások zaj csökkentése érdekében azt javasoljuk, a "Number megsértését jelenti a riasztás aktiválásához" beállításokat. Ez a funkció lehetővé teszi, hogy a rendszer riasztást küld Önnek, csak akkor, ha valaki megsértette a küszöbérték X egymást követő alkalommal vagy Y alkalommal utolsó Z kívül. Példa:

    Riasztást vált ki, ha a probléma folyamatos 15 percig, egy adott időszakban, 5 perces 3 egymást követő alkalommal használja a következő beállításokat:

   ![Értékelés alapja](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    Riasztást vált ki, ha az elmúlt 30 perc, 5 perces idővel kívül 15 percen belül a dinamikus küszöb megsértése történt, használja a következő beállításokat:

   ![Értékelés alapja](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. Jelenleg felhasználók lehetnek dinamikus küszöb feltételekkel riasztások feltétel.

   ![Szabály létrehozása](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Kérdések és válaszok

- K:, ha a mérték lassan idővel változik, Ez elindítja a dinamikus küszöbökkel riasztást?

- V: valószínűleg nem. Dinamikus küszöbértékek jelentős eltérések észlelése, nem pedig lassan fejlődnek problémák jól használhatók.

- K: beállítani a dinamikus küszöbértékeket egy API-n keresztül?

- V: dolgozunk rajta.
