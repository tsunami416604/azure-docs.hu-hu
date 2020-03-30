---
title: Hozzon létre egyéni analitikus szabályokat a gyanús fenyegetések észleléséhez az Azure Sentinel segítségével| Microsoft dokumentumok
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni analitikus szabályokat az Azure Sentinel gyanús fenyegetései észleléséhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585107"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Oktatóanyag: Hozzon létre egyéni analitikus szabályokat a gyanús fenyegetések észlelésére

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure Sentinelhez, létrehozhat egyéni szabályokat, amelyek megkereshetik a környezetben az adott feltételeket, és incidenseket generálhatnak, amikor a feltételek egyeznek, így kivizsgálhatja őket. Ez az oktatóanyag segít egyéni szabályok létrehozásában az Azure Sentinel használatával a fenyegetések észleléséhez.

Ez az oktatóanyag segít a fenyegetések észlelésének az Azure Sentinel használatával.
> [!div class="checklist"]
> * Analitikus szabályok létrehozása
> * A fenyegetésekre adott válaszok automatizálása

## <a name="create-custom-analytic-rules"></a>Egyéni analitikus szabályok létrehozása

Egyéni analitikus szabályokat hozhat létre, amelyek segítségével megkeresheti a környezetében gyanús fenyegetések és anomáliák típusait. A szabály gondoskodik arról, hogy azonnal értesítést kapjon, így triage, vizsgálja ki, és a fenyegetések elhárítása.

1. Az Azure Sentinel alatti Azure Portalon válassza az **Analytics**lehetőséget.

1. A felső menüsorban válassza az **+Létrehozás** és **az Ütemezett lekérdezési szabály**lehetőséget. Ezzel megnyitja az **Analytics szabály varázslót**.

    ![Ütemezett lekérdezés létrehozása](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Az **Általános** lapon adjon meg egy egyedi **nevet**és egy **Leírást.** A **Taktika** mezőben a támadások azon kategóriái közül választhat, amelyek szerint osztályozni szeretné a szabályt. Állítsa be a riasztás **súlyossága** szükség szerint. Amikor létrehozza a szabályt, **állapota** alapértelmezés szerint **engedélyezve** van, ami azt jelenti, hogy közvetlenül a létrehozás befejezése után fog futni. Ha nem szeretné, hogy azonnal fusson, válassza a **Letiltva**lehetőséget, és a szabály hozzáadódik az **Aktív szabályok** laphoz, és akkor engedélyezheti, amikor szüksége van rá.

    ![Egyéni analitikus szabály létrehozásának megkezdése](media/tutorial-detect-threats-custom/general-tab.png)

1. A **Szabály logikabeállítása** lapon közvetlenül a Szabály **lekérdezési** mezőbe írhat lekérdezést, vagy létrehozhatja a lekérdezést a Log Analytics szolgáltatásban, majd másolhatja és beillesztheti azt.
 
   ![Lekérdezés létrehozása az Azure Sentinelben](media/tutorial-detect-threats-custom/settings-tab.png)

   - Tekintse meg az **eredmények előnézeti** területen a jobb oldalon, ahol az Azure Sentinel megjeleníti az eredmények száma (naplóesemények) a lekérdezés generál, módosítása menet közben, alekérdezés írása és konfigurálása során. A grafikon a megadott időszak eredményeinek számát mutatja, amelyet a **Lekérdezés ütemezése** szakasz beállításai határoznak meg.
    - Ha azt látja, hogy a lekérdezés túl sok vagy túl gyakori riasztást vált ki, beállíthatja az alaptervet a **Riasztási küszöbérték** szakaszban.

      Az alábbiakban egy mintalekérdezést, amely figyelmezteti Önt, ha rendellenes számú erőforrás jön létre az Azure Activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > A lekérdezés hosszának 1 és 10 000 karakter \*között kell \*lennie, és nem tartalmazhat "keresés" vagy "egyesítő" karaktereket.

    1. Az **entitások leképezése** szakasz segítségével összekapcsolhatja a lekérdezés eredményeinek paramétereit az Azure Sentinel által felismert entitásokkal. Ezek az entitások képezik a további elemzés alapját, beleértve a riasztások csoportosítását az incidensek besorolásához az **Incidens beállításai** lapon.
    1. A **Lekérdezés ütemezése** szakaszban állítsa be a következő paramétereket:

       1. Állítsa be **a Lekérdezés futtatása minden** szabályozni, hogy milyen gyakran fut a lekérdezés - olyan gyakran, mint 5 percenként, vagy olyan ritkán, mint naponta egyszer.

       1. Állítsa be a lekérdezés által lefedett adatok aktuális időszakát **az utolsó ból származó lekérdezési adatok** beállításához – például lekérdezheti az elmúlt 10 perc adatot, vagy az elmúlt 6 órányi adatot.

       > [!NOTE]
       > Ez a két beállítás egymástól független, egy bizonyos pontig. A lekérdezést rövid időközönként futtathatja, amely az intervallumnál hosszabb időtartamot fedle le (valójában egymást átfedő lekérdezésekkel), de nem futtathat lekérdezést olyan időközönként, amely meghaladja a fedezeti időszakot, ellenkező esetben a lekérdezés teljes lefedettségében hiányosságok lesznek.

    1. A **Riasztási küszöbérték** szakasz használatával definiálja az alaptervet. Állítsa be például a **Riasztás generálása lehetőséget, ha a lekérdezési eredmények száma** **nagyobb, mint a** 1000-es szám, és adja meg az 1000 számot, ha azt szeretné, hogy a szabály csak akkor hozzon létre riasztást, ha a lekérdezés minden futtatáskor több mint 1000 eredményt hoz létre. Mivel ez egy kötelező mező, ha nem szeretne alaptervet beállítani – azaz ha azt szeretné, hogy a riasztás minden eseményt regisztráljon – írja be a 0 értéket a szám mezőbe.

    1. Az **Elnyomás szakaszban** a **Riasztás létrehozása után** a Futás leállítása **beállítást** bekapcsolhatja, ha a riasztás tkövetően a lekérdezési időközt meghaladó időtartamra fel szeretné függeszteni a szabály működését. Ha ezt bekapcsolja, a **Leállítási lekérdezést** legfeljebb 24 órára kell beállítania.

1. Az **Incidensbeállítások** lapon megadhatja, hogy az Azure Sentinel a riasztásokat végrehajtható incidensekké alakítja-e, és ha azokat. Ha ez a lap egyedül marad, az Azure Sentinel egyetlen, különálló incidenst hoz létre minden egyes riasztástól. Az ezen a lapon található beállítások módosításával dönthet úgy, hogy nem hoz létre incidenseket, vagy több riasztást csoportosít egyetlen incidensbe.

    1. Az **Incidensbeállítások** szakaszban az **elemzési szabály által aktivált riasztásokból incidensek létrehozása** alapértelmezés szerint **engedélyezve**van, ami azt jelenti, hogy az Azure Sentinel egyetlen, külön incidenst hoz létre a szabály által aktivált minden egyes riasztástól.<br></br>Ha nem szeretné, hogy ez a szabály incidensek létrehozását eredményezje (például ha ez a szabály csak további elemzéshez szükséges adatok gyűjtésére szolgál), állítsa ezt **letiltva**értékre.

    1. A **Riasztáscsoportosítás** szakaszban, ha azt szeretné, hogy egy incidens hasonló vagy ismétlődő riasztások egy csoportjából jön létre, állítsa be **a csoporthoz kapcsolódó riasztásokat, amelyeket ez az elemzési szabály vált ki, az incidensek** értékre, és állítsa be a következő paramétereket. **Enabled**

    1. **A csoport korlátozása a kiválasztott időkereten belül létrehozott riasztásokra:**<br></br> Határozza meg azt az időkeretet, amelyen belül a hasonló vagy ismétlődő riasztások csoportosítva lesznek. Az összes megfelelő riasztások ezen időkereten belül együttesen generál egy incidens vagy egy eseménycsoport (attól függően, hogy az alábbi csoportosítási beállításokat). Az ezen időkereten kívüli riasztások külön incidenst vagy incidenseket hoznak létre.

    2. **Az elemzési szabály által kiváltott csoportriasztások egyetlen incidensbe kerülnek:** Válassza ki a riasztások csoportosításának alapját:

        - **A riasztásokat egyetlen incidensbe csoportosítja, ha az összes entitás megfelel:** <br></br>A riasztások akkor vannak csoportosítva, ha azonos értékekkel rendelkeznek az egyes leképezett entitásokhoz (a fenti Szabálylogika beállítása lapon definiálva). Ez az ajánlott beállítás.

        - **A szabály által kiváltott összes riasztást egyetlen incidensbe csoportosítsa:** <br></br>A szabály által létrehozott összes riasztás csoportosítva van, még akkor is, ha nem azonos értékűek.

        - **A riasztásokat egyetlen incidensbe csoportosítja, ha a kiválasztott entitások egyeznek:** <br></br>A riasztások akkor vannak csoportosítva, ha azonos értékekkel rendelkeznek néhány leképezett entitáshoz (amelyek közül választhat a legördülő listából). Ezt a beállítást akkor érdemes használnia, ha például külön incidenseket szeretne létrehozni a forrás- vagy a cél IP-címek alapján.

    3. **Újra megnyitja a lezárt egyeztetési incidenseket:** Ha egy incidenst lezártak (ami azt jelenti, hogy az alapul szolgáló probléma megoldódott), és ezt követően egy másik riasztás jön létre, amely az adott incidensbe lett volna csoportosítva, állítsa ezt a beállítást **Engedélyezve,** ha azt szeretné, hogy a lezárt incidens újra megnyíljon, és **hagyja letiltva,** ha azt szeretné, hogy a riasztás új incidenst hozzon létre.

1. Az **Automatikus válaszok** lapon jelölje ki azokat a forgatókönyveket, amelyeket automatikusan szeretne futtatni, amikor az egyéni szabály riasztást hoz létre. A forgatókönyvek létrehozásáról és automatizálásáról a [Fenyegetések reválaszolása](tutorial-respond-threats-playbook.md)című témakörben talál további információt.

1. Válassza a **Véleményezés és létrehozás** lehetőséget az új riasztási szabály összes beállításának áttekintéséhez, majd a **Létrehozás gombra a riasztási szabály inicializálásához.**
  
1. A riasztás létrehozása után az **Aktív szabályok**csoportban egy egyéni szabály kerül a táblába. Ebből a listából engedélyezheti, letilthatja vagy törölheti az egyes szabályokat.

1. A létrehozott riasztási szabályok eredményeinek megtekintéséhez nyissa meg az **Incidensek** lapot, ahol osztályozást, [incidensek kivizsgálását](tutorial-investigate-cases.md)és a fenyegetések elhárítását.


> [!NOTE]
> Az Azure Sentinelben létrehozott riasztások a [Microsoft Graph Security](https://aka.ms/securitygraphdocs)szolgáltatáson keresztül érhetők el. További információt a [Microsoft Graph Biztonsági riasztások dokumentációjában](https://aka.ms/graphsecurityreferencebetadocs)talál.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan kezdheti el észlelni a fenyegetéseket az Azure Sentinel használatával.

A fenyegetésekre adott válaszok automatizálásáról [az Automatikus fenyegetésre adott válaszok beállítása az Azure Sentinelben című dokumentumban.](tutorial-respond-threats-playbook.md)

