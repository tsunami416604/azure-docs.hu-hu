---
title: 'Oktatóanyag: Előzetes verziós környezet beállítása – Azure Time Series Insights | Microsoft dokumentumok'
description: 'Oktatóanyag: Ismerje meg, hogyan állíthat be egy környezetet az Azure Time Series Insights előzetes verziójában.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: af15a7366fd07cecb376ff76ad383f784202a887
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77526814"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Azure Time Series Insights előzetes verziójú környezet beállítása

Ez az oktatóanyag végigvezeti az Azure Time Series Insights előzetes *verziós fizetéses* (PAYG) környezet létrehozásának folyamatán.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure Time Series Insights előzetes környezetben.
> * Csatlakoztassa az Azure Time Series Insights előzetes környezetet egy IoT Hubhoz.
> * Az Azure Time Series Insights előzetes környezetébe való adatfolyamadatok futtatásához futtasson megoldásgyorsítómintát.
> * Végezze el az adatok alapvető elemzését.
> * Definiáljon egy idősorozat-modell típust és hierarchiát, és társítsa azt a példányokkal.
> * Használja a Power BI-összekötőt, és jelenítse meg az adatokat a Power BI-ban.

>[!TIP]
> [Az IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators) nagyvállalati szintű, előre konfigurált megoldásokat kínálnak, amelyek segítségével felgyorsíthatja az egyéni IoT-megoldások fejlesztését.

Ha még nem rendelkezik [ingyenes Azure-előfizetéssel,](https://azure.microsoft.com/free/) regisztráljon.

## <a name="prerequisites"></a>Előfeltételek

* Legalább rendelkeznie kell a **közreműködői** szerepköraz Azure-előfizetés. További információért olvassa el [a Hozzáférés kezelése szerepköralapú hozzáférés-vezérlés sel és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)című olvasni.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban három szimulált eszközt hoz létre, amelyek adatokat küldenek egy Azure IoT Hub-példányba.

1. Nyissa meg az [Azure IoT-megoldásgyorsítók lapot.](https://www.azureiotsolutions.com/Accelerators) A lap számos előre összeállított példát jelenít meg. Jelentkezzen be az Azure-fiókjával. Ezután válassza **az Eszközszimuláció**lehetőséget.

   [![Az Azure IoT-megoldásgyorsítók lap.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. A következő lapon válassza a **Try Now**lehetőséget. Ezután adja meg a szükséges paramétereket az **Eszközszimuláció létrehozása megoldás** lapon.

   Paraméter|Leírás
   ---|---
   **Központi telepítés neve** | Ez az egyedi érték új erőforráscsoport létrehozásához használatos. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Azure-előfizetés** | Adja meg ugyanazt az előfizetést, amely et az előző szakaszban a Time Series Insights-környezet létrehozásához használt.
   **Telepítési lehetőségek** | Válassza **az új IoT Hub kiépítése** lehetőséget az oktatóanyagra jellemző új IoT-központ létrehozásához.
   **Az Azure helye** | Adja meg ugyanazt a régiót, amely et használt a Time Series Insights-környezet létrehozásához az előző szakaszban.

   Ha elkészült, válassza **a Create lehetőséget** a megoldás Azure-erőforrásainak kiépítéséhez. A folyamat akár 20 percet is igénybe vehet.

   [![Az eszközszimulációs megoldás kiépítése.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. A kiépítés befejezése után két értesítés jelenik meg, amelyek bejelentik, hogy a központi telepítési állapot a **kiépítésről** **a Kész állapotra**került. 

   >[!IMPORTANT]
   > Még ne adja meg a megoldásgyorsítót! Tartsa nyitva ezt a weboldalt, mert később visszatér hozzá.

   [![Eszközszimulációs megoldás kiépítése befejeződött.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Most vizsgálja meg az újonnan létrehozott erőforrásokat az Azure Portalon. Az **Erőforráscsoportok** lapon figyelje meg, hogy egy új erőforráscsoportot hoztak létre az utolsó lépésben megadott **Megoldás név** használatával. Jegyezze fel az eszközszimulációhoz létrehozott erőforrásokat.

   [![Eszközszimulációs erőforrások.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Preview PAYG környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre egy Azure Time Series Insights előzetes verziójú környezetet, és csatlakoztassa azt az IoT-megoldásgyorsító által az [Azure Portalon](https://portal.azure.com/)létrehozott IoT hubhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetési fiókjával. 
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban. 
1. Jelölje ki a **Dolgok internete kategóriát,** majd a **Time Series Insights**lehetőséget. 

   [![Válassza ki a Time Series Insights környezeti erőforrás.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. A **Time Series Insights környezet létrehozása ablaktáblán** az **Alapok** lapon állítsa be a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Környezet neve** | Adjon meg egy egyedi nevet az Azure Time Series Insights előzetes környezetben. |
    | **Előfizetés** | Adja meg az előfizetést, ahol létre szeretné hozni az Azure Time Series Insights előzetes környezetet. Ajánlott eljárás, hogy ugyanazt az előfizetést használja, mint az eszközszimulátor által létrehozott többi IoT-erőforrás. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot az Azure Time Series Insights előzetes környezeti erőforráshoz. Az erőforráscsoport az Azure-erőforrások tárolója. Ajánlott eljárás, hogy ugyanazt az erőforráscsoportot használja, mint az eszközszimulátor által létrehozott többi IoT-erőforrás. |
    | **Helyen** | Válasszon ki egy adatközpont-régiót az Azure Time Series Insights előzetes verziójához. További késés elkerülése érdekében a legjobb, ha az Azure Time Series Insights előzetes környezetben ugyanabban a régióban, mint az eszköz szimulátor által létrehozott IoT hub. |
    | **Szint** |  Válassza **a PAYG** *(pay-as-you-go) lehetőséget.* Ez az Azure Time Series Insights előzetes verziójú termékváltozata. |
    | **Tulajdonság neve** | Adjon meg egy olyan értéket, amely egyedileg azonosítja az idősorozat-példányt. A **Tulajdonságazonosító** mezőben megadott érték később nem módosítható. Ehhez az oktatóanyaghoz írja be az ***iothub-connection-device-id***. Ha többet szeretne megtudni az idősorozat-azonosítóról, olvassa el [az Idősorozat-azonosító kiválasztására vonatkozó gyakorlati tanácsok című gyakorlati tudnivalókat.](./time-series-insights-update-how-to-id.md) |
    | **Tárfiók neve** | Adjon meg egy globálisan egyedi nevet egy új tárfiókhoz.|
    |**Meleg bolt engedélyezése**|A meleg tárolás engedélyezéséhez válassza az **Igen** lehetőséget. Később is visszatérhet, és engedélyezheti ezt a beállítást. |
    |**Adatmegőrzés (napokban)**|Válassza az alapértelmezett 7 napos beállítást. |

    Válassza a **Tovább: Eseményforrás**lehetőséget.

   [![Új Time Series Insights-környezet konfigurációja.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![Állítsa be az idősorozat-azonosítót a környezethez.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Az **Esemény forrása** lapon állítsa be a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | --- |
   | **Létrehoz egy eseményforrást?** | Válassza az **Igen** lehetőséget.|
   | **Név** | Adjon meg egy egyedi értéket az esemény forrásnevéhez. |
   | **Forrás típusa** | Válassza az **IoT Hub lehetőséget.** |
   | **Hub kiválasztása** | Válassza **a Meglévő kijelölése**lehetőséget. |
   | **Előfizetés** | Válassza ki az eszközszimulátorhoz használt előfizetést. |
   | **IoT Hub neve** | Válassza ki az eszközszimulátorhoz létrehozott IoT hub nevet. |
   | **IoT Hub hozzáférési szabályzat** | Válassza az **iothubowner lehetőséget.** |
   | **IoT Hub fogyasztói csoport** | Válassza az **Új**lehetőséget, adjon meg egy egyedi nevet, majd kattintson **a + Hozzáadás gombra.** A fogyasztói csoportnak egyedi értéknek kell lennie az Azure Time Series Insights előzetes verziójában. |
   | **Időbélyeg tulajdonság** | Ez az érték a bejövő telemetriai adatok **timestamp** tulajdonságának azonosítására szolgál. Ebben az oktatóanyagban hagyja üresen ezt a mezőt. Ez a szimulátor az IoT Hub bejövő időbélyegét használja, amely a Time Series Insights alapértelmezett érték. |

   Válassza **a Véleményezés + Létrehozás lehetőséget.**

   [![Konfigurálja a létrehozott IoT hub, mint egy eseményforrás.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Kattintson a **Létrehozás** gombra.

    [![Véleményezés + Lap létrehozása a Létrehozás gombbal.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    A telepítés állapotát áttekintheti:

    [![Értesítés a telepítés befejezéséről.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Az Azure Time Series Insights előzetes környezethez alapértelmezés szerint hozzáférhet, ha ön az Azure-előfizetés tulajdonosa. Ellenőrizze, hogy van-e hozzáférése:

   1. Keresse meg az erőforráscsoportot, majd válassza ki az újonnan létrehozott Azure Time Series Insights előzetes környezetben. 

      [![Válassza ki és tekintse meg a környezetet.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Az Azure Time Series Insights előzetes verziólapján válassza az **Adatelérési szabályzatok**lehetőséget:

      [![Ellenőrizze az adatelérési házirendeket.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Ellenőrizze, hogy a hitelesítő adatok szerepelnek-e a listában:

      Ha a hitelesítő adatok nem szerepelnek a listában, meg kell adnia magának az engedélyt a környezet elérésére a Hitelesítő adatok hozzáadása és keresése lehetőség kiválasztásával. Ha többet szeretne tudni az engedélyek beállításáról, olvassa el [az Adathozzáférés megadása](./time-series-insights-data-access.md)című.

## <a name="stream-data"></a>Adatok streamelése

Most, hogy üzembe helyezte a Time Series Insights-környezetet, kezdje meg az adatok streamelését elemzésre.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Szükség esetén jelentkezzen be újra az oktatóanyagban használt Azure-fiókhasználatával. Válassza ki a "Device Solution", majd **menj a megoldásgyorsító** elindítani a telepített megoldás.

   [![Megoldásgyorsítók irányítópultja.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Az eszközszimulációs webalkalmazás azzal kezdi, hogy kéri, hogy adja meg a webalkalmazásnak a **Bejelentkezést, és olvassa el a profilengedélyét.** Ez az engedély lehetővé teszi, hogy az alkalmazás lekérje az alkalmazás működéséhez szükséges felhasználói profiladatokat.

   [![Eszközszimulációs webalkalmazás hozzájárulása.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Válassza **a + Új szimuláció lehetőséget.** A **Szimuláció beállítási** oldalának betöltése után adja meg a szükséges paramétereket.

    | Paraméter | Műveletek |
    | --- | --- |
    | **Név** | Adjon meg egy szimulátor egyedi nevét. |
    | **Leírás** | Adjon meg egy definíciót. |
    | **Szimuláció időtartama** | Állítsa **a futtatáshatározatlan időre.** |
    | **Eszközmodell** | Kattintson a + **Eszköztípus hozzáadása gombra** <br />**Név**: Adja meg **a liftet**. <br />**Összeg**: Írja be a **3**értéket. <br /> Hagyja meg a fennmaradó alapértelmezett értékeket |
    | **Cél IoT Hub** | Állítsa be **az előre kiépített IoT Hub használatára.** |

    [![Konfigurálja a paramétereket és indítsa el az indítást.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Válassza **a Szimuláció indítása**lehetőséget.

    Az eszközszimulációs irányítópulton **megjelennek az Aktív eszközök** és az **Összes üzenet.**

    [![Azure IoT szimulációs irányítópult.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Adatok elemzése

Ebben a szakaszban az [Azure Time Series Insights előzetes verziójának kezelője](./time-series-insights-update-explorer.md)segítségével alapvető elemzéseket hajthat végre az idősorozat-adatokon.

1. Nyissa meg az Azure Time Series Insights előzetes verziókezelőjét, ha kiválasztja az URL-címet az [Azure Portal](https://portal.azure.com/)erőforráslapjáról.

    [![A Time Series Insights előzetes intézőurl-címe.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. A Time Series Insights felfedezőjében megjelenik egy sáv, amely a képernyő felső részén található. Ez a rendelkezésre állás választó. Győződjön meg arról, hogy legalább két 2 m-t kijelölt, és ha szükséges, bontsa ki az időkeretet a választófogófogók balra és jobbra húzásával.

1. **A Time Series példányok** a bal oldalon jelennek meg.

    [![A nem szülő példányok listája.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Jelölje ki az első sorozatpéldányt. Ezután válassza a **Hőmérséklet megjelenítése**lehetőséget.

    [![Kijelölt idősorozat-példány a menüparanccsal az átlaghőmérséklet megjelenítéséhez.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Megjelenik egy idősorozat-diagram. Módosítsa az **intervallumot** **30-ra.**

1. Ismételje meg az előző lépést a másik két idősorozat-példával, hogy mindhárom at megtekinthesse, ahogy az a diagramon látható:

    [![Diagram az összes idősorozathoz.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Válassza ki az időtartomány-választót a jobb felső sarokban. Itt választhatja ki az adott kezdési és befejezési időket a milliszekundumig, vagy választhat az előre konfigurált beállítások közül, például **az Utolsó 30 perc**. Módosíthatja az alapértelmezett időzónát is.

    [![Állítsa az időtartományt az utolsó 30 percre.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    A megoldásgyorsító előrehaladása az **elmúlt 30 percben** most megjelenik a Time Series Insights felfedező.

## <a name="define-and-apply-a-model"></a>Modell definiálása és alkalmazása

Ebben a szakaszban egy modellt alkalmaz az adatok strukturálásához. A modell befejezéséhez típusokat, hierarchiákat és példányokat kell megadnia. Ha többet szeretne megtudni az adatmodellezésről, olvassa el [a Time Series Model című cikket.](./time-series-insights-update-tsm.md)

1. A felfedezőben válassza a **Modell** lapot:

   [![Tekintse meg a Modell lapot a kezelőben.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   A **Típusok** lapon válassza a **+ Hozzáadás**lehetőséget.

1. Adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Név** | Belépés **liftbe** |
    | **Leírás** | Írja **be: Ez a lift típusdefiníciója** |

1. Ezután válassza a **Változók** lapot. 

   Válassza a **+ Add Variable (Változó hozzáadása) lehetőséget,** és töltse ki a következő értékeket a Lift típus első változójához. Összesen három változót fog szerzőn.

    | Paraméter | Műveletek |
    | --- | --- |
    | **Név** | Adja meg **az átlagos hőmérsékletet**. |
    | **Fajta** | **Numerikus** kijelölés |
    | **Érték** | Válasszon az előre beállított: Hőmérséklet kiválasztása **(Dupla)**. <br /> Megjegyzés: Eltarthat néhány **percig,** amíg az Érték automatikusan feltöltődik, miután az Azure Time Series Insights előzetes verzió események fogadását megkezdi.|
    | **Összesítési művelet** | Bontsa ki **a Speciális beállítások csomópontot.** <br /> Válassza **az AVG**lehetőséget. |

    Kattintson az **Alkalmaz** gombra. Ezután **+ Add Variable** újra, és állítsa be a következő értékeket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Név** | Adja meg **az Átlagos rezgés t**. |
    | **Fajta** | **Numerikus** kijelölés |
    | **Érték** | Válasszon az előre beállított: Válassza **a rezgés (Dupla)**. <br /> Megjegyzés: Eltarthat néhány **percig,** amíg az Érték automatikusan feltöltődik, miután az Azure Time Series Insights előzetes verzió események fogadását megkezdi.|
    | **Összesítési művelet** | Bontsa ki **a Speciális beállítások csomópontot.** <br /> Válassza **az AVG**lehetőséget. |

    Kattintson az **Alkalmaz** gombra. Ezután **+ Add Variable** újra, és állítsa be a következő értékeket a harmadik és egyben utolsó változó:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Név** | Adja **meg a Padló**. |
    | **Fajta** | **Kategorikus** kiválasztása |
    | **Érték** | Válasszon az előre beállított: Válassza **a Padló (Dupla)** lehetőséget. <br /> Megjegyzés: Eltarthat néhány **percig,** amíg az Érték automatikusan feltöltődik, miután az Azure Time Series Insights előzetes verzió események fogadását megkezdi.|
    | **Kategóriák** | <span style="text-decoration: underline">Címke </span>   -  <span style="text-decoration: underline">értékek</span> <br /> Alsó: 1,2,3,4 <br /> Középen: 5,6,7,8,9 <br /> Felső: 10,11,12,13,14,15 |
    | **Alapértelmezett kategória** | **Ismeretlen** megadása |

    [![Típusváltozók hozzáadása.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

1. Kattintson a **Mentés** gombra. Három változó jön létre és jelenik meg.

    [![A típus hozzáadása után tekintse át a típusnézetben.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Válassza a **Hierarchiák** lapot. Ezután válassza **a + Add**lehetőséget.
   
   A **Hierarchia szerkesztése** ablaktáblán állítsa be a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | ---|
   | **Név** | Adja meg **a helyhierarchiát**. |
   |**Szintek**| Írja be **az országot** az első szint neveként. <br> + **Szint hozzáadása jelölése** <br> Írja be a Második szint **városát,** majd válassza a **+ Szint hozzáadása lehetőséget** <br> Adja meg **az épületet** a harmadik és egyben utolsó szint neveként |

   Kattintson a **Mentés** gombra.

   [![Az új hierarchia megjelenítése a Modell nézetben.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Keresse meg a Példányok ( **példányok ) .** A Jobb szélen **lévő műveletek** csoportban válassza a ceruza ikont az első példány szerkesztéséhez a következő értékekkel:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **Lift**lehetőséget. |
    | **Név** | Belépés **az 1-es liftbe**|
    | **Leírás** | Az **1-es lift példányának** megadása |

    Nyissa meg a **Példánymezők et,** és írja be a következő értékeket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Hierarchiák** | **Helyhierarchia kiválasztása** |
    | **Ország** | Adja meg **az USA-ban** |
    | **Város** | Adja meg **Seattle** |
    | **Épület** | Adja **meg a space tűt** |

    Kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépést a másik két példánnyal a következő értékek használata közben:

    **A 2-es felvonóhoz:**

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **Lift**lehetőséget. |
    | **Név** | Belépés **a 2-es liftbe**|
    | **Leírás** | A **2-es lift példájának** megadása |
    | **Hierarchiák** | **Helyhierarchia kiválasztása** |
    | **Ország** | Adja meg **az USA-ban** |
    | **Város** | Adja meg **Seattle** |
    | **Épület** | Belépés **a Csendes-óceáni Tudományos Központba** |

    **A 3-as felvonóhoz:**

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **Lift**lehetőséget. |
    | **Név** | Belépés **a 3-as liftbe**|
    | **Leírás** | A **3-as lift példányának** megadása |
    | **Hierarchiák** | **Helyhierarchia kiválasztása** |
    | **Ország** | Adja meg **az USA-ban** |
    | **Város** | Adja meg **New York** |
    | **Épület** | Adja meg **az Empire State Building** |

    [![Tekintse meg a frissített példányokat.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. A diagramablak megtekintéséhez lépjen vissza az **Elemzés** lapra. A **Helyhierarchia csoportban**bontsa ki az összes hierarchiaszintet az idősorozat-példányok megjelenítéséhez:

    [![Az összes hierarchia megtekintése diagramnézetben.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. A **Csendes-óceáni tudományos központban**válassza a Time Series Instance Elevator **2**lehetőséget, majd válassza az Átlagos **hőmérséklet megjelenítése**lehetőséget.

1. Ugyanebben az esetben válassza a **2- es lift**lehetőséget, válassza **a Padló megjelenítése**lehetőséget.

    A kategorikus változóval meghatározhatja, hogy a lift mennyi időt töltött a felső, alsó és középső emeleten.

    [![A 2-es lift megjelenítése hierarchiával és adatokkal.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy befejezte az oktatóanyagot, tisztítsa meg a létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, keresse meg az Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és a benne lévő összes erőforrást) az egyes erőforrások **törlése** vagy eltávolítása külön-külön lehetőség kiválasztásával.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

* Eszközszimuláció-gyorsító létrehozása és használata.
* Hozzon létre egy Azure Time Series Insights előzetes PAYG-környezetet.
* Csatlakoztassa az Azure Time Series Insights előzetes környezetet egy iot hubhoz.
* Az Azure Time Series Insights előzetes környezetbe való adatfolyamadat-továbbító megoldásgyorsító minta futtatása.
* Az adatok alapvető elemzésének elvégzése.
* Definiáljon egy idősorozat-modell típust és hierarchiát, és társítsa őket a példányokkal.

Most, hogy már tudja, hogyan hozhat létre saját Azure Time Series Insights előzetes verziójú környezetét, tudjon meg többet az Azure Time Series Insights legfontosabb fogalmairól.

További információ az Azure Time Series Insights tárolási konfigurációjáról:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes tárolása és be- és be- és visszaözlözése](./time-series-insights-update-storage-ingress.md)

További információ a Time Series modellekről:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes adatmodellezése](./time-series-insights-update-tsm.md)
