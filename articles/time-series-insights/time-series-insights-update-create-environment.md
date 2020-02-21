---
title: 'Oktatóanyag: előzetes verziójú környezet beállítása – Azure Time Series Insights | Microsoft Docs'
description: 'Oktatóanyag: környezet beállítása Azure Time Series Insights előzetes verzióban.'
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
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526814"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Azure Time Series Insights – előzetes környezet beállításával.

Ez az oktatóanyag végigvezeti a Azure Time Series Insights előzetes *utólagos* elszámolású (TB) környezet létrehozásának folyamatán.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Azure Time Series Insights előnézeti környezet létrehozása.
> * Az Azure Time Series Insights előnézeti környezetének összekötése egy IoT Hub.
> * Egy megoldás-gyorsító minta futtatásával továbbíthatja az adatátvitelt a Azure Time Series Insights előzetes verziójának környezetében.
> * Alapvető adatok elemzését, a.
> * Definiáljon egy idősorozat-modell típusát és hierarchiáját, és társítsa azt a példányokhoz.
> * Használja az Power BI-összekötőt, és jelenítse meg az adatPower BIokban.

>[!TIP]
> A [IoT megoldás-gyorsítók](https://www.azureiotsolutions.com/Accelerators) olyan nagyvállalati szintű előre konfigurált megoldásokat biztosítanak, amelyek segítségével felgyorsíthatja az egyéni IoT-megoldások fejlesztését.

Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Legalább a **közreműködő** szerepkörrel kell rendelkeznie az Azure-előfizetéshez. További információért olvassa el [a hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)című témakört.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban három szimulált eszközt fog létrehozni, amelyek az Azure IoT Hub-példányra küldenek adatküldést.

1. Nyissa meg az [Azure IoT megoldás-gyorsítók lapot](https://www.azureiotsolutions.com/Accelerators). Az oldalon számos előre összeépített példa látható. Jelentkezzen be az Azure-fiókjával. Ezután válassza az **eszköz szimulálása**elemet.

   [![Azure IoT megoldás-gyorssegédek lapja.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. A következő lapon válassza a **kipróbálás most**lehetőséget. Ezután adja meg a szükséges paramétereket az **eszköz-szimulációs megoldás létrehozása** lapon.

   Paraméter|Leírás
   ---|---
   **Központi telepítés neve** | Ez az egyedi érték egy új erőforráscsoport létrehozásához használatos. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Azure-előfizetés** | Adja meg ugyanazt az előfizetést, amelyet az előző szakaszban Time Series Insights környezet létrehozásához használt.
   **Üzembe helyezési beállítások** | Válassza az **új IoT hub kiépítése** lehetőséget az oktatóanyaghoz tartozó új IoT hub létrehozásához.
   **Azure-beli hely** | Adja meg ugyanazt a régiót, amelyet az előző szakaszban Time Series Insights környezet létrehozásához használt.

   Ha elkészült, a **Létrehozás** gombra kattintva kiépítheti a megoldás Azure-erőforrásait. A folyamat elvégzése akár 20 percet is igénybe vehet.

   [![kiépíteni az eszköz szimulációs megoldását.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. A kiépítés befejezése után két értesítés jelenik meg, amely azt jelenti **, hogy a**központi telepítési állapot a **kiépítés** befejezését követően lett áthelyezve. 

   >[!IMPORTANT]
   > Még ne adja meg a megoldás-gyorssegédet! Tartsa meg ezt a weblapot, mert később vissza fog térni.

   [a ![Device szimulációs megoldás kiépítési folyamata befejeződött.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Most vizsgálja meg az újonnan létrehozott erőforrásokat a Azure Portal. Figyelje meg, hogy az **erőforráscsoportok** lapon az utolsó lépésben megadott **megoldás neve** alapján létrehozott egy új erőforráscsoportot. Jegyezze fel az eszköz szimulálásához létrehozott erőforrásokat.

   [![eszköz szimulációs erőforrásai.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Előzetes verziójú TB-környezet létrehozása

Ez a szakasz azt ismerteti, hogyan hozhat létre Azure Time Series Insights előnézeti környezetet, és hogyan csatlakoztathatja a IoT-megoldás-gyorsító által létrehozott IoT hub-hoz a [Azure Portal](https://portal.azure.com/)használatával.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) az Azure-előfizetési fiók használatával. 
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban. 
1. Válassza ki a **eszközök internetes hálózata** kategóriát, majd válassza a **Time Series Insights**lehetőséget. 

   [![válassza ki a Time Series Insights környezeti erőforrást.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Az **Time Series Insights környezet létrehozása** ablaktábla **alapok** lapján adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Környezet neve** | Adjon egyedi nevet a Azure Time Series Insights előnézeti környezetnek. |
    | **Előfizetés** | Adja meg azt az előfizetést, amelyben létre szeretné hozni a Azure Time Series Insights előnézeti környezetet. Az ajánlott eljárás az, hogy ugyanazt az előfizetést használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot a Azure Time Series Insights előnézet környezeti erőforráshoz. Az erőforráscsoport az Azure-erőforrások tárolója. Az ajánlott eljárás az, hogy ugyanazt az erőforráscsoportot használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Hely** | Válasszon ki egy adatközpont-régiót Azure Time Series Insights előnézeti környezetéhez. A további késés elkerülése érdekében érdemes létrehozni a Azure Time Series Insights előnézeti környezetet ugyanabban a régióban, mint az IoT hub, amelyet az eszköz-szimulátor hozott létre. |
    | **Tier** |  Válassza*a* **TB** (utólagos elszámolású) lehetőséget. Ez a Azure Time Series Insights előzetes termékhez tartozó SKU. |
    | **Tulajdonság neve** | Adjon meg egy értéket, amely egyedileg azonosítja a Time Series-példányt. A **tulajdonság azonosítója** mezőben megadott érték később nem módosítható. Ebben az oktatóanyagban adja meg a ***iothub--kapcsolatok-Device-ID***értéket. Ha többet szeretne megtudni az idősorozat-AZONOSÍTÓról, olvassa el az [ajánlott eljárásokat az idősorozat-azonosító kiválasztásához](./time-series-insights-update-how-to-id.md). |
    | **Storage-fiók neve** | Adjon meg egy globálisan egyedi nevet egy új Storage-fiókhoz.|
    |**Meleg tároló engedélyezése**|Válassza az **Igen** lehetőséget a meleg tároló engedélyezéséhez. Később is visszatérhet, és engedélyezheti ezt a beállítást. |
    |**Adatmegőrzés (nap)**|Válassza az alapértelmezett beállítást 7 nap. |

    Válassza a Next (tovább) lehetőséget **: eseményforrás**.

   [![új Time Series Insights környezeti konfiguráció.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![konfigurálja a környezet idősorozat-AZONOSÍTÓját.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Az **eseményforrás** lapon adja meg a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | --- |
   | **Létrehoz egy eseményforrás?** | Válassza az **Igen** lehetőséget.|
   | **Name (Név)** | Adjon meg egyedi értéket az eseményforrás neveként. |
   | **Forrás típusa** | Válassza a **IoT hub**lehetőséget. |
   | **Válasszon hubot** | Válassza a **meglévő kiválasztása**lehetőséget. |
   | **Előfizetés** | Válassza ki az eszköz-szimulátorhoz használt előfizetést. |
   | **IoT Hub neve** | Válassza ki az eszköz-szimulátorhoz létrehozott IoT hub-nevet. |
   | **Hozzáférési szabályzat IoT Hub** | Válassza a **iothubowner**lehetőséget. |
   | **IoT Hub fogyasztói csoport** | Válassza az **új**lehetőséget, adjon meg egy egyedi nevet, majd válassza a **+ Hozzáadás**lehetőséget. A fogyasztói csoportnak egyedi értéknek kell lennie Azure Time Series Insights előzetes verzióban. |
   | **Timestamp tulajdonság** | Ezzel az értékkel azonosítható a bejövő telemetria adataiban található **timestamp** tulajdonság. Ebben az oktatóanyagban hagyja üresen ezt a mezőt. Ez a szimulátor a IoT Hub bejövő időbélyegét használja, amely Time Series Insights alapértelmezett értéke. |

   Válassza a **felülvizsgálat + létrehozás**lehetőséget.

   [![konfigurálja a létrehozott IoT hubot eseményforrásként.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Kattintson a **Létrehozás** gombra.

    [![felülvizsgálat + létrehozás oldalon, a létrehozás gombbal.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Tekintse át az üzemelő példány állapotát:

    [![értesítés arról, hogy a telepítés befejeződött.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Alapértelmezés szerint az Azure-előfizetés tulajdonosaként férhet hozzá Azure Time Series Insights előnézeti környezetéhez. Ellenőrizze, hogy rendelkezik-e hozzáféréssel:

   1. Keresse meg az erőforráscsoportot, majd válassza ki az újonnan létrehozott Azure Time Series Insights előnézeti környezetet. 

      [![kiválaszthatja és megtekintheti a környezetét.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. A Azure Time Series Insights előnézet oldalon válassza az **adatelérési házirendek**lehetőséget:

      [![az adatelérési házirendek ellenőrzése.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Ellenőrizze, hogy a hitelesítő adatok szerepelnek-e a listáján:

      Ha a hitelesítő adatai nem szerepelnek a felsorolásban, akkor engedélyt kell adnia a környezet elérésére a hitelesítő adatok hozzáadása és keresése lehetőség kiválasztásával. Ha többet szeretne megtudni az engedélyek beállításáról, olvassa el az [adathozzáférés engedélyezése](./time-series-insights-data-access.md)című témakört.

## <a name="stream-data"></a>Adatok streamelése

Most, hogy üzembe helyezte a Time Series Insights környezetét, megkezdheti az adatátvitelt az elemzéshez.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Ha szükséges, jelentkezzen be újra ugyanezen az oktatóanyagban használt Azure-fiók használatával. Válassza ki az "eszköz megoldás" lehetőséget, majd **lépjen a megoldás-gyorsító** elemre, és indítsa el a telepített megoldást.

   [![megoldás-gyorssegédek irányítópultja.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Az eszköz-szimulációs webalkalmazás megkezdi, hogy megadja a webalkalmazásnak a **bejelentkezést, és beolvassa a profiljának** engedélyét. Ez az engedély lehetővé teszi az alkalmazás számára, hogy lekérje az alkalmazás működésének támogatásához szükséges felhasználói profil adatait.

   [![eszköz-szimulációs webalkalmazás-engedély.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Válassza az **+ új szimuláció**lehetőséget. A **szimulációs beállítások** oldal betöltése után adja meg a szükséges paramétereket.

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adjon egyedi nevet a szimulátornak. |
    | **Leírás** | Adjon meg egy definíciót. |
    | **Szimuláció időtartama** | **Határozatlan ideig történő futtatásra**van beállítva. |
    | **Eszközmodell** | Kattintson **az + eszköz típusának hozzáadása** elemre. <br />**Név**: adjon meg egy **liftet**. <br />**Mennyiség**: írja be a **3**értéket. <br /> Hagyja meg a fennmaradó alapértelmezett értékeket |
    | **Cél IoT Hub** | Az **előre kiépített IoT hub használatára**van beállítva. |

    [![konfigurálja a paramétereket és az indítást.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Válassza a **Szimuláció indítása**lehetőséget.

    Az eszköz szimulációjának irányítópultján megjelenik az **aktív eszközök** és az **összes üzenet** .

    [![Azure IoT szimulációs irányítópult.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Adatok elemzése

Ebben a szakaszban az idősorozat-adatokra vonatkozó alapszintű elemzéseket a [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)használatával hajtja végre.

1. A [Azure Portal](https://portal.azure.com/)erőforrás oldaláról válassza ki az URL-címet a Azure Time Series Insights előnézeti Explorerben.

    [![a Time Series Insights Preview Explorer URL-címét.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. A Time Series Insights Explorerben megjelenik a képernyő felső részén átívelő sáv. Ez a rendelkezésre állási választó. Győződjön meg arról, hogy legalább két 2 m van kiválasztva, és ha szükséges, bontsa ki az időkeretet úgy, hogy kiválasztja és húzza a választót a bal és a jobb oldalra.

1. Az **Idősorozat-példányok** a bal oldalon lesznek megjelenítve.

    [a nem szülő példányok ![listája.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Válassza ki az első idejű adatsorozat-példányt. Ezután válassza a **hőmérséklet megjelenítése**lehetőséget.

    [az átlagos hőmérséklet megjelenítéséhez ![kiválasztott Time Series-példányt a Menu paranccsal.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Ekkor megjelenik egy idősorozat-diagram. Módosítsa az **intervallumot** a **30-as**értékre.

1. Ismételje meg az előző lépést a másik két idősorozat-példánnyal, hogy mindhárom megtekinthető legyen, ahogy az a következő ábrán is látható:

    [![diagram az összes idősorozathoz.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Válassza ki az időtartomány-választót a jobb felső sarokban. Itt kiválaszthatja az adott kezdési és befejezési időpontot az ezredmásodpercre, vagy az előre konfigurált beállítások közül választhat, például az **elmúlt 30 percben**. Módosíthatja az alapértelmezett időzónát is.

    [![az időtartomány beállítása az elmúlt 30 percre.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    A megoldás-gyorsító előrehaladása az **elmúlt 30 percben** már megjelenik a Time Series Insights Explorerben.

## <a name="define-and-apply-a-model"></a>Modell meghatározása és alkalmazása

Ebben a szakaszban egy modellt alkalmaz az adatai struktúrájára. A modell elvégzéséhez a típusokat, hierarchiákat és példányokat kell meghatároznia. Ha többet szeretne megtudni az adatmodellezésről, olvassa el a [Time Series modellt](./time-series-insights-update-tsm.md).

1. A Explorerben válassza a **modell** fület:

   [![tekintse meg a modell fület a Explorerben.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   A **típusok** lapon válassza a **+ Hozzáadás**lehetőséget.

1. Adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Name (Név)** | **Lift** megadása |
    | **Leírás** | Adja meg a **lift típus definícióját** |

1. Ezután válassza a **változók** lapot. 

   Válassza a **+ változó hozzáadása** lehetőséget, és adja meg a következő értékeket a felvonó első változójának. Összesen három változót fog létrehozni.

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adja meg az **AVG hőmérsékletet**. |
    | **Típusú** | **Numerikus** elem kiválasztása |
    | **Érték** | Válasszon az előre beállított listából: válassza a **hőmérséklet (Double)** lehetőséget. <br /> Megjegyzés: Előfordulhat, hogy a rendszer automatikusan kitölti az **értéket** , miután Azure Time Series Insights előnézet elkezdi fogadni az eseményeket.|
    | **Összesítési művelet** | Bontsa ki a **Speciális beállítások elemet**. <br /> Válassza az **AVG**elemet. |

    Kattintson az **Alkalmaz** gombra. Ezután adja meg ismét a **változót** , és állítsa be a következő értékeket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adja meg az **AVG Vibration**értéket. |
    | **Típusú** | **Numerikus** elem kiválasztása |
    | **Érték** | Válasszon az előre beállított listából: válassza a **vibráció (dupla)** lehetőséget. <br /> Megjegyzés: Előfordulhat, hogy a rendszer automatikusan kitölti az **értéket** , miután Azure Time Series Insights előnézet elkezdi fogadni az eseményeket.|
    | **Összesítési művelet** | Bontsa ki a **Speciális beállítások elemet**. <br /> Válassza az **AVG**elemet. |

    Kattintson az **Alkalmaz** gombra. Ezután adja meg ismét a **változót** , és állítsa be a következő értékeket a harmadik és a végső változóhoz:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adja meg a **padlót**. |
    | **Típusú** | **Kategorikus** kijelölése |
    | **Érték** | Válasszon az előre beállított lehetőség közül: válassza a **padló (Double)** lehetőséget. <br /> Megjegyzés: Előfordulhat, hogy a rendszer automatikusan kitölti az **értéket** , miután Azure Time Series Insights előnézet elkezdi fogadni az eseményeket.|
    | **Kategóriák** | <span style="text-decoration: underline">Címke</span>  - <span style="text-decoration: underline">értékek</span> <br /> Alsó: 1, 2, 3, 4 <br /> Középső: 5, 6, 7, 8, 9 <br /> Felső: 10, 11, 12, 13, 14, 15 |
    | **Alapértelmezett kategória** | **Ismeretlen** megadása |

    [![adja meg a típus változóit.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

1. Kattintson a **Mentés** gombra. A rendszer három változót hoz létre és jelenít meg.

    [![a típus hozzáadása után tekintse át a modellt a modell nézetben.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Válassza a **hierarchiák** fület. Ezután válassza a **+ Hozzáadás**lehetőséget.
   
   A **hierarchia szerkesztése** panelen állítsa be a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | ---|
   | **Name (Név)** | Adja meg a **hely hierarchiáját**. |
   |**Szinten**| Adja meg az **országot** az első szint neveként <br> Válassza a **+ szint hozzáadása** lehetőséget <br> Adja meg a második szint **városát** , majd válassza a **+ szint hozzáadása** elemet. <br> A harmadik és utolsó szint neveként adja meg a **Building** nevet |

   Kattintson a **Mentés** gombra.

   [![az új hierarchia megjelenítése a modell nézetben.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigáljon a **példányokhoz**. A jobb szélen lévő **műveletek** területen kattintson a ceruza ikonra az első példány szerkesztéséhez a következő értékekkel:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **lift**lehetőséget. |
    | **Name (Név)** | **1. felvonó** megadása|
    | **Leírás** | Adja meg **az 1. felvonó példányát** |

    Lépjen a **példány mezőire** , és adja meg a következő értékeket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **Város** | **Seattle** megadása |
    | **Létrehozása** | Adja meg a **térközt** |

    Kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépést a másik két példánnyal a következő értékek használatakor:

    **2. lift esetén:**

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **lift**lehetőséget. |
    | **Name (Név)** | **2. felvonó** megadása|
    | **Leírás** | **A 2. felvonóhoz tartozó példány** megadása |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **Város** | **Seattle** megadása |
    | **Létrehozása** | A **Pacific Science Center** megadása |

    **3. felvonó esetén:**

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **lift**lehetőséget. |
    | **Name (Név)** | **3. felvonó** megadása|
    | **Leírás** | **A 3. felvonóhoz tartozó példány** megadása |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **Város** | Adja meg a **New York** -i |
    | **Létrehozása** | Adja meg a **Birodalom állapotának kiépítése** |

    [![tekintse meg a frissített példányokat.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Váltson vissza az **elemzés** lapra a diagram ablaktábla megtekintéséhez. Az idősorozat-példányok megjelenítéséhez a **hely hierarchiája**alatt bontsa ki az összes hierarchia szintet:

    [![az összes hierarchiát megtekintheti a diagram nézetben.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. A **Pacific Science Center**alatt válassza ki az idősorozat-példány **2. liftjét**, majd válassza az **átlaghőmérséklet megjelenítése**lehetőséget.

1. Ugyanannak a példánynak a **2. liftben**válassza a **padló megjelenítése**lehetőséget.

    A kategorikus változóval meghatározhatja, hogy a lift mennyi idő után kerüljön a felső, alsó és középső emeleten.

    [a 2. ![a hierarchiát és az adathalmazt ábrázolja.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**lehetőséget, keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

* Eszköz-szimulációs gyorssegéd létrehozása és használata.
* Hozzon létre egy Azure Time Series Insights előzetes verziójú TB-környezetet.
* Csatlakoztathatja a Azure Time Series Insights előnézeti környezetet egy IOT hubhoz.
* Egy megoldás-gyorsító minta futtatásával továbbíthatja az adatátvitelt a Azure Time Series Insights előnézeti környezetbe.
* Az adatok egy alapszintű elemzéseket végezhet.
* Definiáljon egy idősorozat-modell típusát és hierarchiáját, majd társítsa azokat a példányokhoz.

Most, hogy már tudja, hogyan hozhat létre saját Azure Time Series Insights előnézeti környezetet, további információt olvashat a Azure Time Series Insights főbb fogalmakról.

További információ a Azure Time Series Insights Storage-konfigurációról:

> [!div class="nextstepaction"]
> [Azure Time Series Insights előnézet tároló és bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Azure Time Series Insights előzetes verziójú adatmodellezés](./time-series-insights-update-tsm.md)
