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
ms.openlocfilehash: df75ff9a359620781743732f4f12a6d3e7ec51c6
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331674"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-private-preview"></a>Riasztások dinamikus küszöbértékekkel az Azure monitorban (korlátozott privát előzetes verzió)

Riasztások dinamikus küszöbértékekkel újdonságként jelentek meg az Azure metrika riasztások az Azure monitorban, amely további metrikák általi korábbi viselkedés automatikusan kiszámítja az alapkonfigurációkat, és felhasználhatja őket a riasztástípusok küszöbértékét, speciális Machine Learning (gépi tanulás) lehetőségeit kihasználva.

Dinamikus küszöbérték használatának előnyei a következők:

- Egy előre meghatározott merev határ beállítás szerint a figyelő automatikusan megtanulja a metrika korábbi teljesítményét, és alkalmazza a gépi Tanulási algoritmus segítségével határozza meg a riasztási küszöbértékek társított nem kell menteni.
- Azonosítani tudják szezonális viselkedését, és csak a várt szezonális működésének gyakorlattól riasztás. Metrikákhoz kapcsolódó riasztások dinamikus küszöbértékekkel nem vált, ha a szolgáltatás a hétvégeken rendszeresen üresjáratban, és ezután hirtelen megugró kihasználtság minden hétfőn. Jelenleg támogatott: óránkénti, napi és heti szezonalitás.
- Folyamatosan tanul metrika teljesítményének és adaptív metrika módosításához.

Dinamikus küszöbalapú riasztások állnak rendelkezésre az összes az Azure monitor-alapú ezen metrika források [cikk](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Az előzetes verzió eléréséhez regisztráljon

Ezt a képességet egy léptetéses érvénybe [regisztrálni az előzetes verzióra](https://aka.ms/DynamicThresholdMetricAlerts). Mint mindig, örömmel vennénk, ha visszajelzést adna, tartsa azt hamarosan elérhető: [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Riasztások dinamikus küszöbértékekkel konfigurálása

Riasztások dinamikus küszöbértékekkel konfigurálható az Azure monitorban riasztásokat

![Riasztások megtekintése](media/alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Riasztási szabályok létrehozása dinamikus küszöbértékekkel

1. A figyelő a riasztások panelen válassza ki a **Új riasztási szabály** gombra kattintva hozzon létre egy új értesítés az Azure-ban.

   ![Új riasztási szabály](media/alerts-dynamic-thresholds/002.png)

2. Szabály létrehozása szakaszban mellett a három részből álló látható: _Riasztási feltétel megadása_, _riasztás részleteinek megadása_, és _definiálása műveletcsoport_. Először kezdődik a _riasztási feltétel megadása_ használjuk a **válassza ki a cél** adja meg a cél erőforrás kijelölésével mutató hivatkozást. Miután egy megfelelő erőforrás van kiválasztva, kattintson a Kész gombra.

   ![Cél kiválasztása](media/alerts-dynamic-thresholds/0003.png)

3. Következő az a **adja meg a feltételeket** gomb elérhető, az erőforrás és a jel listából jel beállítások listájának megtekintéséhez válasszon egy megfelelő **metrika** lehetőséget. (Például százalékos Processzorhasználat.)

   ![Feltétel hozzáadása](media/alerts-dynamic-thresholds/004.png)

4. A konfigurálás jel logikai képernyőn az Alert logic szakaszban van váltására szolgáló beállítás a feltételnek megfelelő dinamikus, amely automatikusan létrejön a dinamikus küszöbértékek (piros vonalak) mellett a metrika (kék vonal).

   ![Dinamikus](media/alerts-dynamic-thresholds/005.png)

5. A küszöbértékeket a diagramon megjelenő kiszámítása alapján az előzményadatok elmúlt hét nap, a riasztás létrehozása után a dinamikus küszöbértékek elérhető további előzményadatok arányban fogják beszerezni és folyamatosan tekintjük át, győződjön meg arról, hogy új adatok alapján a a küszöbértékek pontosabb.

6. Riasztási logika további beállítások:
   - Feltétel – a riasztás a következő három feltétel egyikének aktiválását választhat:
       - A felső küszöbértéknél nagyobb vagy kisebb, mint az alsó küszöbértéket (alapértelmezett)
       - Nagyobb, mint a felső küszöbérték
       - Az alsó küszöbérték alatti.
   - Idő összesítése: ÁTLAG (alapértelmezett), sum, min, max.
   - Riasztási érzékenység:
       - Magas – a további riasztások, mivel a riasztás akkor aktiválódik, a legkisebb értékét.
       - Med – kevésbé érzékeny, mint a nagy, kevesebb riasztást, mint a magas érzékenységi (alapértelmezett)
       - Alacsony – a legkevésbé bizalmas küszöbértéket.

    ![Riasztási logika beállításai](media/alerts-dynamic-thresholds/00007.png)

7. Értékelt alapján:
    -  Milyen időtartam, a riasztás derítheti fel a megadott feltétel tárházából választva a **időszak**.

    ![Értékelés alapja](media/alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Támogatott Küszöbidőszakok értékeit: 5 perc, a 10 perc, 30 perc és 1 óra.

   Átmeneti által létrehozott riasztási zaj csökkentéséhez javasoljuk az "A riasztást kiváltó szabálysértések száma" beállításokkal. Ez a funkció lehetővé teszi, hogy is kaphat a riasztás csak akkor, ha a küszöb megsértettek X egymást követő alkalommal sem, vagy ki az utolsó Z időszakok Y alkalommal. Példa:

    Riasztást kiváltó, ha a probléma folyamatos 15 perc, 5 perc elteltével az adott időszakban 3 egymást követő alkalommal használja a következő beállításokat:

   ![Értékelés alapja](media/alerts-dynamic-thresholds/0008.png)

    Riasztást kiváltó, amikor a dinamikus küszöbértéket a 15 perc alatt az elmúlt 30 perc, 5 perces időtartammal kívül megsértését, használja a következő beállításokat:

   ![Értékelés alapja](media/alerts-dynamic-thresholds/0009.png)

8. Felhasználók jelenleg a dinamikus küszöbértéket feltételekkel riasztások egyetlen feltételként rendelkezhet.

   ![Szabály létrehozása](media/alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Kérdések és válaszok

- KÉRDÉS: A metrika lassan idővel változik, ha ez egy riasztást aktivál dinamikus küszöbértékekkel?

- V: Valószínűleg nem. Dinamikus küszöbérték jók jelentős eltérések észlelése lassan fejlődő problémák helyett.

- KÉRDÉS: Konfigurálhatja a dinamikus küszöbértékeket egy API-n keresztül?

- V: Dolgozunk rajta.
