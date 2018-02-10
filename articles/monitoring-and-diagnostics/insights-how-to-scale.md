---
title: "Méretezni a példányok száma, manuálisan vagy automatikusan skálázva Azure portálon |} Microsoft Docs"
description: "További tudnivalók az Azure szolgáltatások méretezése."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.openlocfilehash: 9ff52bd29644c7c585d2a85fcbe49e8800f6a035
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="scale-instance-count-manually-or-automatically"></a>Méretezhető példányszám manuális vagy automatikus
Az a [Azure Portal](https://portal.azure.com/), manuálisan állíthatja be a példányok száma a szolgáltatás, vagy paramétert adhat meg, hogy automatikusan méretezési igény szerint. Ez általában nevezzük *horizontális felskálázás* vagy *méretezése*.

Előtt példányok száma alapján skálázás, vegye figyelembe, hogy skálázás érintett **tarifacsomag** példányok száma kívül. Különböző tarifacsomagjainak lehet eltérő számú maggal és a memória, és így rendelkeznek jobb teljesítményt nyújt a példányok azonos számú (amely *vertikális felskálázás* vagy *csökkentheti*). Ez a cikk foglalkozik kifejezetten *méretezése* és *kimenő*.

Méretezheti a portálon, és használhatja a [REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx) vagy [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) méretezési úgy, hogy automatikusan vagy manuálisan.

## <a name="scaling-manually"></a>Manuális skálázás
1. A a [Azure Portal](https://portal.azure.com/), kattintson a **Tallózás**, majd keresse meg az erőforrás méretezésére, például egy **App Service-csomag**.
2. Kattintson a **beállítások > kibővítési (App Service-csomag).**
3. Felső részén a **méretezési** panelen, a a **futtatási előzményei** lapon láthatja, hogy a szolgáltatás automatikus skálázási műveletek előzményeit.
   
    ![Skála panel](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > Csak az automatikus skálázás által végrehajtott műveletek jelennek meg ezen a diagramon. Ha manuálisan módosítja a példányok száma, a módosítás nem jelenik meg ezen a diagramon.
   > 
   > 
4. Manuálisan módosíthatja a szám **példányok** a a **konfigurálása** fülre.
5. Kattintson a **mentése** parancsot, és akkor lesz méretezve, hogy a példányok száma szinte azonnal.

## <a name="scaling-based-on-a-pre-set-metric"></a>Egy előre meghatározott metrika skálázás alapján
Ha azt szeretné, hogy automatikusan beálljon-példányok száma alapján metrika, válassza ki a kívánt a **szerint** legördülő menüből. Például egy **App Service-csomag** szerint méretezheti **processzor**.

1. Amikor kiválaszt egy metrika kap egy csúszkát, illetve közötti méretezésére példányainak számát adja meg a beviteli mezők:
   
    ![Processzor méretezési panelről](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    Automatikus skálázási soha nem kerül a szolgáltatás alatt vagy felett a határok beállított, függetlenül a terhelését.
2. Második akkor jelölje ki a cél a metrika. Például, ha úgy döntött, hogy **processzor**, adhatja meg a cél az átlagos CPU összes példánya a szolgáltatásban. A bővített kapacitású történik, ha a átlagos CPU meghaladja a maximális adhat meg, ehhez hasonlóan a skála történjen, amikor az átlagos CPU minimális alá süllyed.
3. Kattintson a **mentése** parancsot. Automatikus skálázás győződjön meg arról, hogy biztosan megfeleljen az a példány tartomány és a cél a mérőszám néhány percenként ellenőrzi. A szolgáltatás további forgalom érkezik, amikor elérhetővé válik több példány bármi nélkül.

## <a name="scale-based-on-other-metrics"></a>A skála más metrikákkal alapján
A készletek megjelenő eltérő alapján metrikák méretezheti a **bővítse a** legördülő menüből, és is még kibővítési álló összetett készlettel rendelkezik, a méretezés szabályokban.

### <a name="adding-or-changing-a-rule"></a>Hozzáadása vagy módosítása egy szabály
1. Válassza ki a **ütemezés és a teljesítmény szabályok** a a **szerint** legördülő: ![teljesítményszabályok](./media/insights-how-to-scale/Insights_PerformanceRules.png)
2. Ha korábban már volt az automatikus skálázás, a megjelennek a pontos szabályok volt nézetét.
3. Egy másik metrika kattintson alapján méretezése a **szabály hozzáadása** sor. A metrika korábban volt szükség a metrika szerint szeretné módosítani a létező sorok egyikét is kattinthat.
   ![Szabály hozzáadása](./media/insights-how-to-scale/Insights_AddRule.png)
4. Most kell kiválasztania, amely metrika szerint szeretné. Ha egy metrika néhány dolgot figyelembe kell venni:
   
   * A *erőforrás* metrika származik. Általában ez lesz ugyanaz, mint a forrás van folyamatban. Azonban ha azt szeretné, bővítse a tároló várólista mélységét, az erőforrás a várólista bővítse a kívánt.
   * A *metrika neve* magát.
   * A *összesítési idő* mérőszám. Ez a hogyan az adatok egyesítése során a *időtartam*.
5. A metrika kiválasztása után válassza ki a mérték, és az üzemeltető küszöbértékét. Tegyük fel például, **nagyobb, mint** **80 %**.
6. Válassza ki a végrehajtandó műveletet Ön szeretné átvenni. Van néhány más típusú műveleteket:
   
   * Növelheti vagy csökkentheti - ezzel hozzáadása vagy eltávolítása a **érték** adhat meg a példányok száma
   * Növeléséhez vagy csökkentéséhez százalék - egy százalékkal Ezzel módosítja a példányok száma. Például sikerült helyezett 25 a **érték** mező, és ha jelenleg 8 példányok, akkor adható hozzá a 2.
   * Növeli vagy csökkenti a – ez állítja be a példányok száma a **érték** adhat meg.
7. Végül választható ritkán le - mennyi ideig Ez a szabály a korábbi műveletet újra méretezési után megvárja-e.
8. A szabály konfigurálása után kattintson a **OK**.
9. Miután konfigurálta az összes kívánt szabályt, ügyeljen arra, hogy elérte a **mentése** parancsot.

### <a name="scaling-with-multiple-steps"></a>Méretezést, több lépés
A fenti példák közérthető alapvető. Azonban ha szigorúbb kapcsolatos felfelé skálázással (vagy le), még akkor is hozzáadhat azonos metrika több skálázási szabály. Például a processzor két skálázási szabályok adhatók meg:

1. A horizontális felskálázáshoz 1 példány 60 % feletti processzor esetén
2. A horizontális felskálázáshoz 3 példányok 85 % feletti processzor esetén

![Több skálázási szabályok](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

A további szabálynak Ha a terhelés meghaladja a 85 % tartozó skálázási műveletek előtt elérhetővé válik egy helyett két további példányt.

## <a name="scale-based-on-a-schedule"></a>A skála a ütemezés szerint
Alapértelmezés szerint a skálázási szabály létrehozásakor mindig alkalmazzák. Láthatja, hogy a profil fejléc kattintva:

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Azonban érdemes lehet szigorúbb méretezésének során a napot vagy a hét, mint a hétvégi rendelkezik. A szolgáltatás teljesen ki munkaidőn is leállíthatja.

1. Ehhez a profilhoz van válassza **ismétlődési** ahelyett, hogy **mindig,** , és válassza ki, hogy meg szeretné alkalmazni a profilt.
2. Ahhoz például, hogy a hét folyamán érvényes profilt a **nap** legördülő menüből, törölje a jelet **szombat** és **vasárnap**.
3. Ahhoz, hogy a profilt, amely során a nappali, állítsa be a **kezdési időpont** a indítsa el a kívánt időpontot az.
   
    ![Alapértelmezett ismétlődési](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. Kattintson az **OK** gombra.
5. A következő szüksége lesz a többi időszakban alkalmazni kívánt profil hozzáadásához. Kattintson a **profil hozzáadása** sor.
    ![Munkába](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. Az új, második, a profil neve, például hívhatja azt **munkahelyi kikapcsolva**.
7. Válassza ki **ismétlődési** újra, és jelölje ki a példányok száma azt szeretné, ebben az időszakban.
8. Az alapértelmezett profil, majd kattintson a **nap** ezt a profilt alkalmazhat, és a **kezdési időpont** a nap folyamán.
   
   > [!NOTE]
   > Automatikus skálázás fogja használni a nyári időszámításra vonatkozó szabályai attól **időzóna** választja. Azonban az UTC eltolás megjeleníti az alap időzóna-eltolódás, nem a nyári megtakarítások UTC eltolás nyári időszámításra során.
   > 
   > 
9. Kattintson az **OK** gombra.
10. Most akkor adjon hozzá bármilyen során a második profil alkalmazni kívánt szabályokat. Kattintson a **szabály hozzáadása**, és majd során az alapértelmezett profil van ugyanaz a szabály lehetett összeállítani.
    
    ![Munkahelyi kikapcsolva szabály hozzáadása](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. Ügyeljen arra, hogy mindkét kibővítési szabály létrehozásához és a skála a – során a profil a példányok száma csak méretének növelése (vagy csökkentése).
12. Végezetül kattintson **mentése**.

## <a name="next-steps"></a>További lépések
* [Szolgáltatás metrikát](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
* [Figyelés engedélyezésekor és diagnosztikai](insights-how-to-use-diagnostics.md) nagyon gyakori gyűjtéséhez részletes a a szolgáltatásban.
* [Riasztási értesítéseket kaphat](insights-receive-alert-notifications.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* [Az alkalmazások teljesítményének figyelésére](../application-insights/app-insights-azure-web-apps.md) Ha azt szeretné tudni, pontosan hogyan a kód működik-e a felhőben.
* [Események és tevékenységnapló](insights-debugging-with-events.md) további minden, ami következett be a szolgáltatásban.
* [Figyelése rendelkezésre állásának és bármilyen weblap válaszképességének](../application-insights/app-insights-monitor-web-app-availability.md) az Application insights szolgáltatással, így azt is megtudhatja, ha az oldala nem működik.

