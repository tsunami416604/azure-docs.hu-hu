---
title: "Egy felhőalapú szolgáltatás figyelése |} Microsoft Docs"
description: "Ismerje meg a felhőszolgáltatások figyelése a klasszikus Azure portál használatával."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2015
ms.author: adegeo
ms.openlocfilehash: c369b22cf068a473343b006eb1b06fdd350d31db
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-monitor-cloud-services"></a>A Cloud Services monitorozása
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

Figyelheti `key` teljesítménymutatók a felhőszolgáltatások a klasszikus Azure portálon. Az ellenőrzések minimális és az egyes szerepkör-szolgáltatás részletes állíthatja be, és testre szabhatja a figyelési jelennek meg. Részletes figyelési adatait tárolja a storage-fiók, amelyhez a portál kívül is hozzáférhet. 

Figyelési jeleníti meg a klasszikus Azure portálon esetében magas konfigurálhatók. Kiválaszthatja a metrikákat a metrikák listában a figyelni kívánt a **figyelő** lapot, és kiválaszthatja, melyik metrikák megrajzolásához a metrikák a diagramok a **figyelő** lap, és az irányítópulton. 

## <a name="concepts"></a>Alapelvek
Alapértelmezés szerint a minimális figyelési biztosított a gazda operációs rendszer a szerepkör-példányok (virtuális gépek) gyűjtött teljesítményszámlálók segítségével új felhőalapú szolgáltatás. A minimális metrikák processzor, adatok, kimenő, lemez olvasási teljesítményt, és a lemez írási átviteli korlátozódnak. Úgy konfigurálja, részletes figyelését, a virtuális gépeken (szerepkörpéldányok) teljesítményadatokat alapján további metrikák fogadhat. A részletes metrikák alkalmazás műveletek során előforduló problémák szorosabb elemzésének engedélyezése.

Alapértelmezés szerint a szerepkörpéldányok teljesítményszámláló-adatokat mintát, és továbbítja a szerepkörpéldány 3 perces időközönként. Ha engedélyezi a részletes figyelését, a nyers teljesítmény számlálóadatok összesített értéket minden egyes szerepkör-példány és a szerepkörpéldányok az egyes szerepkörökhöz 5 perc, 1 óra és 12 óra időközönként. Az összesített adatok 10 nap után véglegesen törlődnek.

Miután engedélyezte a részletes figyelését, összesített figyelési adatokat a rendszer a tárfiókban lévő táblákban tárolja. Szerepkör részletes figyelésének engedélyezése, konfigurálnia kell egy diagnosztikai kapcsolati karakterláncot a tárfiók mutató. Eltérő tárfiókokból különböző szerepkörök is használhatja.

Részletes figyelési növekszik engedélyezése a tárolási költségeket kapcsolatos adatok tárolási, az adatok átvitelét és a tárolási tranzakciók. Minimális figyelési tárfiók nem szükséges. A minimális figyelési szinten a metrikák adatok nem kerülnek be a tárfiók, még akkor is, ha beállította a figyelési szintjét részletes.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>Útmutató: a felhőszolgáltatások figyelésének konfigurálása
Az alábbi eljárások segítségével konfigurálhatja a részletes vagy a minimális figyelését a klasszikus Azure portálon. 

### <a name="before-you-begin"></a>Előkészületek
* Hozzon létre egy *klasszikus* tárfiók a figyelési adatok tárolására. Eltérő tárfiókokból különböző szerepkörök is használhatja. További információkért lásd: [a storage-fiók létrehozása](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* A felhőszolgáltatás szerepköreit Azure Diagnostics engedélyezése. Lásd: [diagnosztika konfigurálása Felhőszolgáltatások](cloud-services-dotnet-diagnostics.md).

Győződjön meg arról, hogy a diagnosztika kapcsolati karakterlánc megtalálható a szerepkör konfigurációja. A részletes megfigyelésre, amíg nem engedélyezi az Azure Diagnostics és diagnosztika kapcsolati karakterláncot szerepeljenek a szerepkör-konfigurációjának nem kapcsolható.   

> [!NOTE]
> Azure SDK 2.5 célzó projektek nem automatikusan tartalmaztak a diagnosztika kapcsolati karakterláncot a projekt sablonban. Ezek a projektek kell manuálisan a diagnosztika kapcsolati karakterlánc hozzáadása a szerepkör konfigurációja.
> 
> 

**Manuális diagnosztika kapcsolati karakterlánc hozzáadása szerepkör konfigurálása**

1. Nyissa meg a Felhőszolgáltatás-projektet a Visual Studio
2. Kattintson duplán arra a a **szerepkör** Tervező, és válassza ki a szerepkör megnyitása a **beállítások** lap
3. Keresse meg a nevű beállítás **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Ha ez a beállítás nincs megadva, a **beállítás hozzáadása** gombra kattintva adja hozzá a konfigurációt, és módosítani az új beállítások az típusát **ConnectionString**
5. A kapcsolati karakterlánc értékét a parancsával a **...**  gombra. Ekkor megnyílik egy párbeszédpanel, lehetővé téve, válasszon egy tárfiókot.
   
    ![A Visual Studio-beállítások](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Részletes vagy a minimális figyelési szintjének módosítása
1. Az a [a klasszikus Azure portálon](https://manage.windowsazure.com/), nyissa meg a **konfigurálása** a felhőalapú szolgáltatás telepítési oldal.
2. A **szint**, kattintson a **részletes** vagy **minimális**. 
3. Kattintson a **Save** (Mentés) gombra.

Miután bekapcsolja a részletes figyelését, jelenítse meg a klasszikus Azure portálon figyelési adatokat órán belül.

A nyers teljesítmény számlálóadatok és összesített figyelési adatokat a storage-fiókot a telepítési azonosító, a szerepkörök minősíteni táblák vannak tárolva. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Hogyan: cloud service metrikák kapcsolatos riasztások fogadása
A felhőalapú szolgáltatás figyelési mérőszámok alapján riasztásokat fogadhat. Az a **szolgáltatások** a klasszikus Azure portál oldalán portálon létrehozhat egy szabályt, amely riasztást vált ki, ha úgy dönt, a metrika eléri a megadott értéket. Választhatja azt is, hogy az e-mail küldése a riasztás aktiválása. További információkért lásd: [hogyan: riasztási értesítések fogadása és a riasztási szabályok kezelése az Azure-ban](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Útmutató: a metrikák tábla metrikák hozzáadása
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), nyissa meg a **figyelő** lap a felhőalapú szolgáltatáshoz.
   
    Alapértelmezés szerint a metrikák tábla jeleníti meg az elérhető mérőszámok egy részét. Az ábrán egy felhőalapú szolgáltatás, amely a szerepkör szinten összesített adatok a Memória\Rendelkezésre álló memória (MB) teljesítményszámláló korlátozódik alapértelmezett részletes mutatóit. Használjon **metrikák hozzáadása** figyelése a klasszikus Azure portálon összesítő és szerepkörszintű további metrikák kiválasztásához.
   
    ![Részletes megjelenítése](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. A metrikák tábla metrikák hozzáadása:
   
   1. Kattintson a **metrikák hozzáadása** megnyitásához **válasszon metrikák**, az alább látható módon.
      
       Az első elérhető metrika kiterjesztettük lehetőségek állnak rendelkezésre. A felső beállítás mindegyik metrikát összes szerepkör összesített figyelési adatait jeleníti meg. Emellett lehetősége van az adatok megjelenítése egyéni szerepkörök.
      
       ![Metrikák hozzáadása](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. Jelölje be a megjelenítendő metrikák
      
      * Kattintson a lefelé mutató nyílra által a metrikát bontsa ki a figyelési beállításokat.
      * Válassza ki a megjeleníteni kívánt megfigyelési lehetőségek jelölőnégyzetet.
        
        Legfeljebb 50 metrikákat a metrikák tábla jelenítheti meg.
        
        > [!TIP]
        > A metrikák lista részletes figyelési metrikákat több tucatnyi tartalmazhat. A görgetősáv megjelenítéséhez vigye a párbeszédpanel jobb oldalán. A lista szűréséhez kattintson a keresés ikonra, és adja meg a szöveget a keresőmezőbe, a lent látható módon.
        > 
        > 
        
        ![Adja hozzá a metrikák keresése](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. Metrikák kiválasztása után kattintson az OK (jelölő).
   
    A kijelölt metrikák kerülnek a metrikák tábla alább látható módon.
   
    ![a figyelő metrikák](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. A metrikák táblából metrika törléséhez kattintson a metrika jelölje ki, majd kattintson a **törölje metrikát**. (Csak akkor jelenik meg **törölje metrikát** Ha rendelkezik a kijelölt metrikával.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Egyéni metrikák hozzáadása a metrikák tábla
A **részletes** a portál figyelési szint alapértelmezett metrikák megfigyeléséhez listáját tartalmazza. Ezek bármely egyéni metrikák vagy teljesítményszámlálók határozzák meg az alkalmazás a portálon keresztül figyelheti.

A következő lépések azt feltételezik, hogy be van-e kapcsolva **részletes** szint figyelése és konfigurálta-e az alkalmazás számára gyűjt, és egyéni teljesítményszámlálóit át. 

Az egyéni teljesítményszámlálóit a portálon megjelenítendő módosítania a konfigurációt a üvegvatta-vezérlő-tároló:

1. Nyissa meg a üvegvatta-vezérlő-tároló blob a diagnosztikai tárfiók. Használhatja a Visual Studio vagy bármely más Tártallózó ehhez.
   
    ![A Visual Studio Server Explorer](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Keresse meg a minta használatával blob elérési **DeploymentId/RoleName/RoleInstance** a konfiguráció a szerepkör-példány található. 
   
    ![A Visual Studio Tártallózó](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. A szerepkör példánya konfigurációs fájljának letöltése, és frissítse úgy, hogy minden egyéni teljesítményszámlálót tartalmaznak. Például a figyelni kívánt *az írási bájtok/s* a a *C meghajtó* adja hozzá a következő **PerformanceCounters\Subscriptions** csomópont
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. A módosítások mentéséhez, és töltse fel a konfigurációs fájlt vissza ugyanazon a helyen található meglévő fájlok felülírása.
5. A klasszikus Azure portál konfigurálása a részletes módot kapcsolót. Ha a kapcsoló már akkor minimális és a részletes váltáshoz.
6. Az egyéni teljesítményszámláló elérhető lesz a **metrikák hozzáadása** párbeszédpanel megnyitásához. 

## <a name="how-to-customize-the-metrics-chart"></a>Útmutató: a metrikák diagram testreszabása
1. A metrikák kijelölése a metrikák diagram megrajzolásához legfeljebb 6 metrikákat. Jelöljön ki egy metrikát, kattintson a bal oldalon a jelölőnégyzetet. Egy metrika a metrikák táblázathoz, vagy törölje a metrikák táblázatban a megfelelő jelölőnégyzet bejelölését.
   
    A metrikák tábla kiválaszthatja a metrikákat, mivel a metrikák a metrikák diagram kerülnek. A szűk képernyőjén egy **további n** legördülő lista tartalmazza-e, amelyek nem felelnek meg a megjelenített metrika fejlécek.
2. Váltás megjelenítése relatív (végső értéke csak mindegyik metrikát) és abszolút értékek (Y-tengely jelenik meg), válassza a relatív vagy abszolút a diagram tetején.
   
    ![Relatív vagy abszolút](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. Az időtartományt a metrikák diagram jelennek meg, jelölje ki 1 óra, 24 óra vagy 7 nap a diagram tetején.
   
    ![A figyelő megjelenítési időszak](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    Az irányítópult metrikák diagram metrikák ábrázolásához metódus nem azonos. Metrikák szabványos készletét, és metrikákat hozzáadásakor vagy eltávolításakor a metrika fejléc kiválasztásával.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Az irányítópult a metrikák diagram testreszabásához
1. Nyissa meg a felhőszolgáltatás irányítópultján.
2. Adja hozzá, vagy távolítsa el a diagram metrikák:
   
   * Egy új mérőszám megrajzolásához, jelölje be a metrika a diagram fejlécekben. A keskeny képernyő, kattintson a lefelé mutató nyílra által  ***n* ?? metrikák** megrajzolásához egy metrika a diagramterület fejléc nem jeleníthető meg.
   * Egy metrikát a diagram ábrázolja törléséhez törölje a jelölőnégyzet jelölését a fejléc.
   
3. Váltás a **relatív** és **abszolút** jeleníti meg.
4. Válassza a 1 óra, 24 óra, vagy az adatok megjelenítéséhez 7 nap.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Útmutató: a részletes nyomon követési adatok a klasszikus Azure portálon kívül hozzáférés
A storage-fiókok az egyes szerepkörökhöz megadott táblák részletes figyelési adatait tárolja. Az egyes felhőalapú szolgáltatás központi telepítések a szerepkörhöz tartozó hat táblázatok jönnek létre. Két táblát hoz létre az egyes (5 perc, 1 óra és 12 óra). Ezek a táblázatok egyik tárolja, szerepkörszintű összesítések; a másik táblában szerepkörpéldányokat összesítéseinek tárolja. 

A tábla nevének formátuma a következő:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

Ahol:

* *deploymentID* a felhőalapú szolgáltatás üzemelő példányhoz társítva GUID-azonosítója
* *aggregation_interval* 5 M, 1 óra vagy 12 H =
* szerepkörszintű összesítések = R
* a szerepkörpéldányokért összesítések = RI

Például az alábbi táblázatok címkéket 1 órás időközönként összesítve részletes figyelési adatokat:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
