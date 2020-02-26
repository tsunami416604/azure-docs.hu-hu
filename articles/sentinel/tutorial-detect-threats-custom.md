---
title: Egyéni analitikus szabályok létrehozása a gyanús fenyegetések észleléséhez az Azure Sentinel használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni analitikai szabályokat a gyanús fenyegetések észleléséhez az Azure Sentinel használatával.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585107"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Oktatóanyag: egyéni analitikus szabályok létrehozása a gyanús fenyegetések észleléséhez

Miután az [adatforrásokat](quickstart-onboard.md) az Azure sentinelhez csatlakoztatta, létrehozhat olyan egyéni szabályokat, amelyek meghatározott feltételeket kereshetnek a környezetében, és incidenseket hozhatnak létre a feltételek teljesülése esetén, így kivizsgálhatja azokat. Ez az oktatóanyag segít egyéni szabályok létrehozásában az Azure Sentinel használatával észlelt fenyegetések észleléséhez.

Ez az oktatóanyag segítséget nyújt az Azure Sentinel-fenyegetések észlelésében.
> [!div class="checklist"]
> * Analitikus szabályok létrehozása
> * Fenyegetési válaszok automatizálása

## <a name="create-custom-analytic-rules"></a>Egyéni analitikus szabályok létrehozása

Egyéni analitikai szabályokat hozhat létre, amelyek segítségével megkeresheti a környezetben gyanús fenyegetések és rendellenességek típusát. A szabály ellenőrzi, hogy azonnal értesítést kap-e, hogy a fenyegetések osztályozását, kivizsgálását és szervizelését el tudja végezni.

1. Az Azure Sentinel alatti Azure Portal válassza az **elemzés**lehetőséget.

1. A felső menüsorban válassza a **+ Létrehozás** lehetőséget, és válassza az **ütemezett lekérdezési szabályt**. Ekkor megnyílik az **elemzési szabály varázsló**.

    ![Ütemezett lekérdezés létrehozása](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Az **általános** lapon adjon meg egy egyedi **nevet**és egy **leírást**. A **taktikák** mezőben választhat a támadási kategóriák közül, amelyek alapján osztályozni tudja a szabályt. Szükség szerint állítsa be a riasztás **súlyosságát** . A szabály létrehozásakor az **állapota** alapértelmezés szerint **engedélyezve** van, ami azt jelenti, hogy a létrehozás befejezését követően azonnal elindul. Ha nem szeretné, hogy azonnal fusson, válassza a **Letiltva**lehetőséget, és a szabály hozzá lesz adva az **aktív szabályok** laphoz, és szükség esetén is engedélyezheti.

    ![Egyéni analitikus szabály létrehozásának megkezdése](media/tutorial-detect-threats-custom/general-tab.png)

1. A **szabály logikájának beállítása** lapon közvetlenül is írhat egy lekérdezést a **szabály lekérdezése** mezőbe, vagy létrehozhatja a lekérdezést log Analytics, majd onnan másolhatja és beillesztheti.
 
   ![Lekérdezés létrehozása az Azure Sentinelben](media/tutorial-detect-threats-custom/settings-tab.png)

   - Tekintse meg az **eredmények előnézetének** területét a jobb oldalon, ahol az Azure Sentinel megjeleníti a lekérdezés által generált eredmények (log Events) számát, és a lekérdezés írásakor és konfigurálásakor menet közben változik. A diagram a megadott időszakon belüli eredmények számát jeleníti meg, amelyet a **lekérdezés ütemezése** szakaszban megadott beállítások határoznak meg.
    - Ha úgy látja, hogy a lekérdezés túl sok vagy túl gyakori riasztást vált ki, beállíthatja az alapkonfigurációt a **riasztási küszöbérték** szakaszban.

      Íme egy példa a lekérdezésre, amely riasztást küld, ha rendellenes számú erőforrás jön létre az Azure-tevékenységben.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > A lekérdezés hosszának 1 és 10 000 karakter közöttinek kell lennie, és nem tartalmazhat "Search \*" vagy "Union \*" karaktert.

    1. A **leképezési entitások** szakasz segítségével a lekérdezési eredményekből származó paramétereket kapcsolhat az Azure Sentinel által elismert entitásokhoz. Ezek az entitások a további elemzés alapjául szolgálnak, beleértve az incidensek **beállításai** lapon lévő riasztások csoportosítását.
    1. A **lekérdezés ütemezése** szakaszban adja meg a következő paramétereket:

       1. Az **összes lekérdezés futtatása** beállítással beállíthatja, hogy a lekérdezés milyen gyakran fusson – akár 5 percenként, akár ritkábban, akár naponta egyszer.

       1. A **legutóbbi keresési adatok** beállítása a lekérdezés által jelzett adatok időtartamának meghatározásához – például lekérdezheti az elmúlt 10 perc adatait, vagy az elmúlt 6 óra adatait.

       > [!NOTE]
       > Ez a két beállítás egymástól független, egy pontra. A lekérdezéseket rövid idő alatt futtathatja, amely hosszabb időt is igénybe vehet az intervallumnál (egymást átfedő lekérdezésekkel), de nem futtathat lekérdezést olyan intervallumban, amely meghaladja a lefedettségi időt, ellenkező esetben a teljes lekérdezési lefedettség hiányában marad.

    1. Az alapkonfiguráció meghatározásához használja a **riasztási küszöbérték** szakaszt. Például állítsa be a **riasztást, ha a lekérdezés eredményeinek száma** **nagyobb, mint** , és adja meg a 1000 számot, ha azt szeretné, hogy a szabály csak akkor hozzon létre riasztást, ha a lekérdezés több, mint 1000 eredményt hoz létre minden alkalommal, amikor fut. Mivel ez egy kötelező mező, ha nem szeretne beállítani egy alapkonfigurációt – azaz ha azt szeretné, hogy a riasztás minden eseményt regisztráljon – a 0 értéket adja meg a szám mezőben.

    1. A **letiltási** szakaszban bekapcsolhatja a **futó lekérdezés leállítása a riasztás létrehozása után** beállítást, ha a riasztást követően felfüggeszti a szabály működését egy olyan időszakra, amely meghaladja a lekérdezési időközt. Ha bekapcsolja ezt a beállítást, be kell állítania a **lekérdezés leállítása leállítását** azon időtartamra, ameddig a lekérdezésnek futnia kell, akár 24 óráig.

1. Az **incidens beállításai** lapon megadhatja, hogy az Azure Sentinel hogyan kapcsolja be a riasztásokat a gyakorlatban előforduló incidensekre. Ha ez a lap egyedül marad, az Azure Sentinel egyetlen, külön incidenst hoz létre minden riasztásból. Dönthet úgy, hogy nem hozott létre incidenseket, vagy egyetlen incidensbe csoportosítja a több riasztást. ehhez módosítsa az ezen a lapon található beállításokat.

    1. Az **incidens beállításai** szakaszban az **ezen elemzési szabály által aktivált riasztásokból származó incidensek létrehozása** alapértelmezés szerint **engedélyezve**értékre van állítva, ami azt jelenti, hogy az Azure Sentinel egyetlen, külön incidenst hoz létre minden egyes, a szabály által aktivált riasztás alapján.<br></br>Ha nem szeretné, hogy ez a szabály bármilyen incidens létrehozását eredményezje (például ha ez a szabály csak a további elemzéshez szükséges adatok gyűjtésére szolgál), állítsa ezt a lehetőséget **Letiltva**értékre.

    1. Ha a **riasztások csoportosítása** szakaszban egyetlen incidenst szeretne létrehozni hasonló vagy ismétlődő riasztások csoportjából, állítsa be a **csoporttal kapcsolatos riasztásokat, amelyeket ez az elemzési szabály aktivál** , és **engedélyezze**az incidenseket, és állítsa be a következő paramétereket.

    1. **A csoport korlátozása a kijelölt időkereten belül létrehozott riasztásokra**:<br></br> Határozza meg azt az időkeretet, amelyen belül a hasonló vagy ismétlődő riasztások együtt lesznek csoportosítva. Az adott időkereten belül az összes vonatkozó riasztás együttesen létrehoz egy incidenst vagy incidensek készletét (az alábbi csoportosítási beállításoktól függően). Az ebben az időkereten kívüli riasztások külön incidenst vagy incidenseket hoznak létre.

    2. Az **elemzési szabály által aktivált riasztások csoportosítása egyetlen incidensre a következő módon**: válassza ki, hogy melyik alapján csoportosítsa a rendszer a riasztásokat:

        - **Riasztások csoportosítása egyetlen incidensbe, ha az összes entitás egyezik**: <br></br>A riasztások együtt vannak csoportosítva, ha azonos értékekkel rendelkeznek a leképezett entitások mindegyikéhez (a fenti szabály logikája lapon van meghatározva). Ez a javasolt beállítás.

        - A **szabály által aktivált riasztások csoportosítása egyetlen incidensbe**: <br></br>A szabály által létrehozott összes riasztás együtt van csoportosítva, még akkor is, ha azok nem azonos értékeket használnak.

        - **Riasztások csoportosítása egyetlen incidensbe, ha a kiválasztott entitások egyeznek**: <br></br>A riasztások akkor vannak csoportosítva, ha azonos értékekkel rendelkeznek a leképezett entitások némelyikéhez (a legördülő listából választhat). Érdemes lehet ezt a beállítást használni, ha például különálló incidenseket szeretne létrehozni a forrás vagy a cél IP-címei alapján.

    3. A **lezárt egyeztetési incidensek újbóli megnyitása**: Ha egy incidens **le** lett zárva (ami azt jelenti, hogy az alapul szolgáló probléma megoldódott), és ezt követően egy másik riasztás jön létre, amely az adott incidensbe lett csoportosítva. Ha azt szeretné, hogy a riasztás új incidenst hozzon létre, állítsa be **ezt a beállítást** .

1. Az **automatikus válaszok** lapon válassza ki azokat a forgatókönyveket, amelyeket automatikusan szeretne futtatni, ha az egyéni szabály létrehoz egy riasztást. A forgatókönyvek létrehozásával és automatizálásával kapcsolatos további információkért lásd: [válaszadás a fenyegetésekre](tutorial-respond-threats-playbook.md).

1. Válassza a **felülvizsgálat és létrehozás** lehetőséget az új riasztási szabály összes beállításának áttekintéséhez, majd válassza a létrehozás lehetőséget a **riasztási szabály inicializálásához**.
  
1. A riasztás létrehozása után a rendszer egy egyéni szabályt ad hozzá a táblához az **aktív szabályok**területen. Ebből a listából engedélyezheti, letilthatja vagy törölheti az egyes szabályokat.

1. A létrehozott riasztási szabályok eredményeinek megtekintéséhez nyissa meg az **incidensek** lapot, ahol osztályozást készíthet, [kivizsgálhatja az incidenseket](tutorial-investigate-cases.md), és elháríthatja a fenyegetéseket.


> [!NOTE]
> Az Azure Sentinelben létrehozott riasztások [Microsoft Graph biztonságon](https://aka.ms/securitygraphdocs)keresztül érhetők el. További információ: [Microsoft Graph Security riasztások dokumentációja](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával.

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [állítson be automatizált fenyegetési válaszokat az Azure sentinelben](tutorial-respond-threats-playbook.md).

