---
title: 'Oktatóanyag: Azure Time Series Insights – előzetes környezet beállításával.'
description: 'Oktatóanyag: megtudhatja, hogyan állíthatja be a környezetet Azure Time Series Insights előzetes verzióban.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49960ae91688b97a9d38a65e1b17fd277508b996
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114583"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Azure Time Series Insights – előzetes környezet beállításával.

Ez az oktatóanyag végigvezeti a Azure Time Series Insights előzetes utólagos elszámolású (TB) környezet létrehozásának folyamatán.

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

* Legalább a **közreműködő** szerepkörrel kell rendelkeznie az Azure-előfizetéshez. További információ: [hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban három szimulált eszközt fog létrehozni, amelyek az Azure IoT Hub-példányra küldenek adatküldést.

1. Nyissa meg az [Azure IoT megoldás-gyorsítók lapot](https://www.azureiotsolutions.com/Accelerators). Az oldalon számos előre összeépített példa látható. Jelentkezzen be az Azure-fiókjával. Ezután válassza az **eszköz szimulálása**elemet.

   [![Azure IoT-megoldás gyorsító lapja](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. A következő lapon válassza a **kipróbálás most**lehetőséget.

   [![eszköz szimulációs lapja](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Az **eszköz-szimulációs megoldás létrehozása** lapon adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Központi telepítés neve** | Adjon meg egyedi értéket egy új erőforráscsoport számára. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz. |
    | **Azure-előfizetés** | Válassza ki azt az előfizetést, amelyben létre szeretné hozni a Time Series Insights-környezetét. |
    | **Azure-beli hely** | Válassza ki azt a régiót, ahol a Time Series Insights-környezetét tárolni szeretné. Vegye figyelembe, hogy az eszköz-szimulátor csak korlátozott számú régióban érhető el, így ha nem látja a kívánt régiót, akkor dönthet úgy, hogy kizárólag az oktatóanyag számára választ ki egy helyet, majd új ÁME-környezetet hoz létre, amikor készen áll a következő szakaszra való áthelyezésre:-b oarding.  |
    | **Üzembe helyezési beállítások** | Válassza az **új IoT hub kiépítése**lehetőséget. |

    1. Kattintson a **Létrehozás** gombra.
    [![eszköz szimulációs lapja](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Körülbelül 20 perc elteltével a megoldás-gyorsító készen áll.

    [![eszköz szimulációs lapja](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Előzetes verziójú TB-környezet létrehozása

Ez a szakasz azt ismerteti, hogyan hozhat létre Azure Time Series Insights előnézeti környezetet, és hogyan csatlakoztathatja a IoT-megoldás-gyorsító által létrehozott IoT hub-hoz a [Azure Portal](https://portal.azure.com/)használatával.

1. Jelentkezzen be az Azure Portalra az előfizetéses fiókba.

1. Válassza **az erőforrás létrehozása** > **eszközök internetes hálózata** > **Time Series Insights**lehetőséget.

   [![válassza a eszközök internetes hálózata lehetőséget, majd válassza a Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Az **Time Series Insights környezet létrehozása** ablaktábla **alapok** lapján adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Környezet neve** | Adjon egyedi nevet a Azure Time Series Insights előnézeti környezetnek. |
    | **Előfizetés** | Adja meg azt az előfizetést, amelyben létre szeretné hozni a Azure Time Series Insights előnézeti környezetet. Az ajánlott eljárás az, hogy ugyanazt az előfizetést használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot a Azure Time Series Insights előnézet környezeti erőforráshoz. Az erőforráscsoport az Azure-erőforrások tárolója. Az ajánlott eljárás az, hogy ugyanazt az erőforráscsoportot használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Hely** | Válasszon ki egy adatközpont-régiót Azure Time Series Insights előnézeti környezetéhez. A további késés elkerülése érdekében érdemes létrehozni a Azure Time Series Insights előnézeti környezetet ugyanabban a régióban, mint az IoT hub, amelyet az eszköz-szimulátor hozott létre. |
    | **Tier** |  Válassza*a* **TB** (utólagos elszámolású) lehetőséget. Ez a Azure Time Series Insights előzetes termékhez tartozó SKU. |
    | **Tulajdonság azonosítója** | Adjon meg egy értéket, amely egyedileg azonosítja a Time Series-példányt. A **tulajdonság azonosítója** mezőben megadott érték később nem módosítható. Ebben az oktatóanyagban adja meg a **iothub--kapcsolatok-Device-ID**értéket. Az idősorozat-AZONOSÍTÓval kapcsolatos további információkért lásd: [ajánlott eljárások idősorozat-azonosító kiválasztásához](./time-series-insights-update-how-to-id.md). |
    | **Storage-fiók neve** | Adjon meg egy globálisan egyedi nevet egy új Storage-fiókhoz.|
    |**Meleg tároló engedélyezése**|Válassza az **Igen** lehetőséget a meleg tároló engedélyezéséhez.|
    |**Adatmegőrzés (nap)**|Válassza az alapértelmezett beállítást 7 nap. |

    Válassza a Next (tovább) lehetőséget **: eseményforrás**.

   [![panel Time Series Insights környezet létrehozásához](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![ablaktábla Time Series Insights környezet létrehozásához](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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
   | **IoT Hub fogyasztói csoport** | Válassza az **új**lehetőséget, adjon meg egy egyedi nevet, majd kattintson a **Hozzáadás**gombra. A fogyasztói csoportnak egyedi értéknek kell lennie Azure Time Series Insights előzetes verzióban. |
   | **Timestamp tulajdonság** | Ezzel az értékkel azonosítható a bejövő telemetria adataiban található **timestamp** tulajdonság. Ebben az oktatóanyagban hagyja üresen ezt a mezőt. Ez a szimulátor a IoT Hub bejövő időbélyegét használja, amely Time Series Insights alapértelmezett értéke. |

   Válassza az **Áttekintés + létrehozás** lehetőséget.

   [Eseményforrás konfigurálása ![](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Kattintson a **Létrehozás** gombra.

    [![felülvizsgálat + Létrehozás lap létrehozás gombja](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Az üzemelő példány állapotát a következő helyen tekintheti meg:

    [![az üzembe helyezést elvégező értesítés](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Alapértelmezés szerint az Azure-előfizetés tulajdonosaként férhet hozzá Azure Time Series Insights előnézeti környezetéhez. Ellenőrizze, hogy rendelkezik-e hozzáféréssel:

   1. Keresse meg az erőforráscsoportot, majd válassza ki az újonnan létrehozott Azure Time Series Insights előnézeti környezetet. 
      [![kiválasztott környezet](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. A Azure Time Series Insights Előnézet lapon válassza az **adatelérési házirendek**: [![adatelérési szabályzatok](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox) elemet.

   1. Ellenőrizze, hogy a hitelesítő adatok szerepelnek-e a listáján:

      [![felsorolt hitelesítő adatok](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Ha a hitelesítő adatai nem szerepelnek a felsorolásban, akkor engedélyt kell adnia a környezet elérésére a hitelesítő adatok hozzáadása és keresése lehetőség kiválasztásával. Ha többet szeretne megtudni az engedélyek beállításáról, olvassa el az [adathozzáférés engedélyezése](./time-series-insights-data-access.md)című témakört.

## <a name="stream-data"></a>Adatok streamelése

Most, hogy üzembe helyezte a Time Series Insights környezetét, megkezdheti az adatátvitelt az elemzéshez.

1. Váltson vissza az [Azure IoT megoldás-gyorsítók lapjára](https://www.azureiotsolutions.com/Accelerators). Keresse meg a megoldást a megoldás-gyorsító irányítópultján, majd válassza az **Indítás**lehetőséget:

    [az eszköz-szimulációs megoldás ![elindítása](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Válassza **a lépjen a megoldás-gyorsító**elemre.

    [az eszköz-szimulációs megoldás ![elindítása](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. A rendszer átirányítja a **Microsoft Azure IoT-eszköz szimulációs** lapjára. Az oldal jobb felső sarkában válassza az **új szimuláció**lehetőséget.

    [![Azure IoT szimulációs oldal](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. A **Szimuláció beállítása** ablaktáblán állítsa be a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adjon egyedi nevet a szimulátornak. |
    | **Leírás** | Adjon meg egy definíciót. |
    | **Szimuláció időtartama** | **Határozatlan ideig történő futtatásra**van beállítva. |
    | **Eszközmodell** | Kattintson **az + eszköz típusának hozzáadása** elemre. <br />**Név**: adjon meg egy **liftet**. <br />**Mennyiség**: írja be a **3**értéket. <br /> Hagyja meg a fennmaradó alapértelmezett értékeket |
    | **Cél IoT Hub** | Az **előre kiépített IoT hub használatára**van beállítva. |

    [a beállítani kívánt paraméterek ![](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Válassza a **Szimuláció indítása**lehetőséget.

    Az eszköz-szimulációs irányítópulton az **aktív eszközök** és az **összes üzenet**jelenik meg.

    [![Azure IoT szimulációs irányítópult](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Adatok elemzése

Ebben a szakaszban az idősorozat-adatokra vonatkozó alapszintű elemzéseket a [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)használatával hajtja végre.

1. A [Azure Portal](https://portal.azure.com/)erőforrás oldaláról válassza ki az URL-címet a Azure Time Series Insights előnézeti Explorerben.

    [a Time Series Insights Preview Explorer URL-címének ![](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. A Time Series Insights Explorerben megjelenik egy sáv, amely a képernyő felső részén jelenik meg. Ez a rendelkezésre állási választó. Győződjön meg arról, hogy legalább két 2m van kiválasztva, és ha szükséges, bontsa ki az időkeretet a választó fogópontok balra és jobbra húzásával.

1. Ekkor megtekintheti a bal oldali **sorozatú példányokat** .


    [nem szülő példányok ![listája](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Válassza ki az első idősorozat-példányt. Ezután válassza a **nyomás megjelenítése**lehetőséget.

    [az átlagos nyomás megjelenítéséhez ![kiválasztott idősorozat-példány a Menu paranccsal](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Ekkor megjelenik egy idősorozat-diagram. Módosítsa az **intervallumot** a **30-as**értékre.

    [idősorozat-diagram ![](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ismételje meg a 3. lépést a másik két idősorozat-példánnyal, hogy mindhárom megtekinthető legyen, ahogy az a következő ábrán is látható:

    [![diagram az összes idősorozathoz](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Válassza ki az időtartomány-választót a jobb felső sarokban. Itt kiválaszthatja az adott kezdési és befejezési időpontot az ezredmásodpercre, vagy választhat az előre konfigurált beállítások közül, például az elmúlt órában. Módosíthatja az alapértelmezett időzónát is.

    [![az időtartomány beállítása egy órára](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Az alábbiakban látható a diagram ablaktábla a szimuláció futtatása után egy órára:

    [![diagram panel](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Modell meghatározása és alkalmazása

Ebben a szakaszban egy modellt alkalmaz az adatai struktúrájára. A modell elvégzéséhez a típusokat, hierarchiákat és példányokat kell meghatároznia. Az adatmodellezéssel kapcsolatos további információkért lásd: [idősorozat-modell](./time-series-insights-update-tsm.md).

1. A Explorerben válassza a **modell** fület:

   [![Model (modell) lap a Explorerben](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. A **típusok** lapon válassza a **Hozzáadás**lehetőséget.

   [a Hozzáadás gomb ![típusokhoz](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Name (Név)** | **Lift** megadása |
    | **Leírás** | Adja meg a **lift típus definícióját** |

    [a Hozzáadás gomb ![típusokhoz](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Ezután válassza a **változók** fület. [![válassza a változók lapot](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox) .

1. Válassza a **+ változó hozzáadása** lehetőséget, és adja meg a következő értékeket a felvonó első változójának. Összesen három változót fog létrehozni.

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adja meg az **AVG hőmérsékletet**. |
    | **Típusú** | **Numerikus** elem kiválasztása |
    | **Érték** | Válasszon az előre beállított listából: válassza a **hőmérséklet (Double)** lehetőséget. <br /> Megjegyzés: Előfordulhat, hogy a rendszer automatikusan kitölti az **értéket** , miután Azure Time Series Insights előnézet elkezdi fogadni az eseményeket.|
    | **Összesítési művelet** | Bontsa ki a **Speciális beállítások elemet**. <br /> Válassza az **AVG**elemet. |

    [a hőmérséklet definiálásának ![kiválasztása](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

    Válassza a **+ változó** újbóli hozzáadása lehetőséget, és állítsa be a következő értékeket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adja meg az **AVG Vibration**értéket. |
    | **Típusú** | **Numerikus** elem kiválasztása |
    | **Érték** | Válasszon az előre beállított listából: válassza a **vibráció (dupla)** lehetőséget. <br /> Megjegyzés: Előfordulhat, hogy a rendszer automatikusan kitölti az **értéket** , miután Azure Time Series Insights előnézet elkezdi fogadni az eseményeket.|
    | **Összesítési művelet** | Bontsa ki a **Speciális beállítások elemet**. <br /> Válassza az **AVG**elemet. |

    [a vibráció definiálásának ![kiválasztása](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

    Válassza a **+ változó** újbóli hozzáadása lehetőséget, és adja meg a következő értékeket a harmadik és a végső változóhoz:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adja meg a **padlót**. |
    | **Típusú** | **Kategorikus** kijelölése |
    | **Érték** | Válasszon az előre beállított lehetőség közül: válassza a **padló (Double)** lehetőséget. <br /> Megjegyzés: Előfordulhat, hogy a rendszer automatikusan kitölti az **értéket** , miután Azure Time Series Insights előnézet elkezdi fogadni az eseményeket.|
    | **Kategóriák** | <span style="text-decoration: underline">Címke</span>  - <span style="text-decoration: underline">értékek</span> <br /> Alsó: 1, 2, 3, 4 <br /> Középső: 5, 6, 7, 8, 9 <br /> Felső: 10, 11, 12, 13, 14, 15 |
    | **Alapértelmezett kategória** | **Ismeretlen** megadása |

    [a vibráció definiálásának ![kiválasztása](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Kattintson az **Alkalmaz** gombra. Három változót kell látnia:

    [a vibráció definiálásának ![kiválasztása](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Kattintson a **Mentés** gombra. Ekkor megjelenik a létrehozott **típus** :

    [a vibráció definiálásának ![kiválasztása](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Válassza a **hierarchiák** fület. Válassza a **+ Hozzáadás**lehetőséget.

    [![hierarchiák lap Hozzáadás gomb](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. A **hierarchia szerkesztése** panelen állítsa be a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | ---|
   | **Name (Név)** | Adja meg a **hely hierarchiáját**. |
   |**Szinten**| Adja meg az **országot** az első szint neveként <br> Válassza a **+ szint hozzáadása** lehetőséget <br> Adja meg a második szint **városát** , majd válassza a **+ szint hozzáadása** elemet. <br> A harmadik és utolsó szint neveként adja meg a **Building** nevet |

   Kattintson a **Mentés** gombra.

    [![hierarchia mezői a létrehozás gombbal](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   A létrehozott hierarchiát a következő helyen tekintheti meg:

    [a hierarchiával ![információk](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Navigáljon a **példányokhoz**. A jobb szélen lévő **műveletek** területen kattintson a ceruza ikonra az első példány szerkesztéséhez a következő értékekkel:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **lift**lehetőséget. |
    | **Name (Név)** | **1. felvonó** megadása|
    | **Leírás** | Adja meg **az 1. felvonó példányát** |

    [![egy példány Szerkesztés gombjára kattintva](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Navigáljon a **példány mezőibe** , és adja meg a következőket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **Város** | **Seattle** megadása |
    | **Létrehozása** | Adja meg a **térközt** |

    [![egy példány Szerkesztés gombjára kattintva](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Kattintson a **Mentés** gombra.

1. Ismételje meg a 8. lépést a másik két példánnyal a következő értékekkel:

    <span style="text-decoration: underline;">2. lift esetén</span>:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **lift**lehetőséget. |
    | **Name (Név)** | **2. felvonó** megadása|
    | **Leírás** | **A 2. felvonóhoz tartozó példány** megadása |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **Város** | **Seattle** megadása |
    | **Létrehozása** | A **Pacific Science Center** megadása |

    <span style="text-decoration: underline;">3. felvonó esetén</span>:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **lift**lehetőséget. |
    | **Name (Név)** | **3. felvonó** megadása|
    | **Leírás** | **A 3. felvonóhoz tartozó példány** megadása |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **Város** | Adja meg a **New York** -i |
    | **Létrehozása** | Adja meg a **Birodalom állapotának kiépítése** |

1. Váltson vissza az **elemzés** lapra a diagram ablaktábla megtekintéséhez. Az idősorozat-példányok megjelenítéséhez a **hely hierarchiája**alatt bontsa ki az összes hierarchia szintet:

   [Az elemzés lap ![](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. A **Pacific Science Center**alatt válassza ki az idősorozat-példány **2. liftjét**, majd válassza az **átlaghőmérséklet megjelenítése**lehetőséget.

    [![Time Series-példány átlagos hőmérsékletét jeleníti meg](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Ugyanannak a példánynak a **2. liftben**válassza a **padló megjelenítése**lehetőséget.

    [![idősorozat-példány a padló megjelenítése](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    A kategorikus változóval meghatározhatja, hogy a lift mennyi idő után kerüljön a felső, alsó és középső emeleten.

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
> [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója az adatmodellezés](./time-series-insights-update-tsm.md)
