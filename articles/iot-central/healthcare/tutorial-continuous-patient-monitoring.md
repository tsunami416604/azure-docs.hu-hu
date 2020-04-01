---
title: Folyamatos betegfigyelő alkalmazás létrehozása az Azure IoT Central szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egy folyamatos betegfigyelő alkalmazást az Azure IoT Central alkalmazássablonokkal.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021287"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Oktatóanyag: Folyamatos betegfigyelő alkalmazássablon telepítése és forgatókönyve



Ez az oktatóanyag bemutatja, mint a megoldás készítője, hogyan lehet az iot central folyamatos betegfigyelési alkalmazássablon telepítésével. Megtudhatja, hogyan telepítheti a sablont, mi szerepel a dobozon kívül, és mi a következő lépés.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Alkalmazássablon létrehozása
> * Az alkalmazássablon végigjárása

## <a name="create-an-application-template"></a>Alkalmazássablon létrehozása

Nyissa meg az [Azure IoT Central alkalmazáskezelő webhelyét.](https://apps.azureiotcentral.com/) Válassza a bal oldali navigációs sáv **építés gombját,** majd kattintson az **Egészségügy** fülre. 

>[!div class="mx-imgBorder"] 
>![App manager Egészségügyi](media/app-manager-health.png)

Kattintson az **Alkalmazás létrehozása** gombra az alkalmazás létrehozásának megkezdéséhez, majd jelentkezzen be egy Microsoft-személyes, munkahelyi vagy iskolai fiókkal. Ez elviszi az **új alkalmazás** oldalon.

![Alkalmazás létrehozása Egészségügyi](media/app-manager-health-create.png)

![Alkalmazás egészségügyi számlázási adatainak létrehozása](media/app-manager-health-create-billinginfo.png)

Az alkalmazás létrehozása:

1. Az Azure IoT Central automatikusan javaslatot tesz egy alkalmazás név re a kiválasztott sablon alapján. Elfogadhatja ezt a nevet, vagy megadhatja saját rövid alkalmazásnevét, például **a Folyamatos betegfigyelés**t. Az Azure IoT Central is létrehoz egy egyedi URL-előtagot az alkalmazás neve alapján. Ön szabadon módosíthatja ezt az URL-előtagot, hogy valami emlékezetesebb, ha szeretné.

2. Kiválaszthatja, hogy az alkalmazást az *ingyenes* díjszabási csomag vagy a *standard* díjcsomagok egyikével szeretné-e létrehozni. Az ingyenes csomag használatával létrehozott alkalmazások hét napig ingyenesek, mielőtt lejárnak, és legfeljebb öt ingyenes eszközt engedélyeznek. Az alkalmazásokat az ingyenes csomagból bármikor áthelyezheti egy szabványos díjcsomagba, mielőtt lejár. Ha az ingyenes csomagot választja, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól. A szabványos csomag használatával létrehozott alkalmazások legfeljebb két ingyenes eszközt támogatnak, és meg kell adnia az Azure-előfizetés adatait a számlázáshoz.

3. Válassza a Lap alján a **Létrehozás** lehetőséget az alkalmazás központi telepítéséhez.

## <a name="walk-through-the-application-template"></a>Az alkalmazássablon végigjárása

### <a name="dashboards"></a>Irányítópultok

Az alkalmazássablon telepítése után először a **Lamna fekvőbeteg-figyelő irányítópulton fog landolni.** Lamna Healthcare egy fiktív kórházi rendszer, amely két kórház: Woodgrove Kórház és Burkville Kórház. A Woodgrove Kórház operátori irányítópultján a sablonban található eszközökre vonatkozó információk és telemetriai adatok, valamint a parancsok, feladatok és műveletek készlete látható. Az irányítópultról a következőket teheti:

* Tekintse meg az eszköz telemetriai adatait és tulajdonságait, például az eszköz **akkumulátorának töltöttségi szintjét** vagy **a kapcsolat** állapotát.

* Tekintse meg a Smart Vitals Patch eszköz **alaprajzát** és helyét.

* A Smart Vitals **Patch-et** egy új betegnek.

* Tekintsemeg a **szolgáltató irányítópultjának** példáját, amelyet egy kórházi betegcsoport láthat a betegei nyomon követéséhez.

* Módosítsa az eszköz **betegállapotát** annak jelzésére, hogy az eszközt fekvőbeteg-beteg vagy távoli forgatókönyvhöz használják-e.

>[!div class="mx-imgBorder"] 
>![Lamna fekvőbeteg](media/lamna-in-patient.png)

A **Go to remote patient dashboard (Ugrás a távoli betegirányítópultra)** lehetőségre kattintva megtekintheti a Burkville Kórház második kezelői irányítópultját. Ez az irányítópult hasonló műveleteket, telemetriai adatokat és információkat tartalmaz. Ezenkívül több eszközt is használhat, és mindegyiken **frissítheti a belső vezérlőprogramot.**

>[!div class="mx-imgBorder"] 
>![Lamna távoli](media/lamna-remote.png)

Mindkét irányítópulton bármikor visszakapcsolhat ehhez a dokumentációhoz.

### <a name="device-templates"></a>Eszközsablonok

Ha az **Eszközsablonok** fülre kattint, látni fogja, hogy két különböző eszköztípus van a sablon része:

* **Smart Vitals Patch**: Ez a készülék egy olyan tapaszt képvisel, amely különböző típusú életjeleket mér. Ezt fel lehet használni a betegek megfigyelésére a kórházban és azon kívül. Ha rákattint a sablonra, látni fogja, hogy az eszköz adatok, például az akkumulátor töltöttségi szintje és az eszköz hőmérséklete mellett a tapasz olyan betegegészségügyi adatokat is küld, mint a légzésszám és a vérnyomás.

* **Intelligens térdmerevítő:** Ez a készülék olyan térdmerevítőt képvisel, amelyet a betegek használhatnak, amikor térdprotézis műtétből lábadoznak. Ha erre a sablonra kattint, az eszközadatok mellett olyan képességeket is látni fog, mint a mozgástartomány és a gyorsítás.

>[!div class="mx-imgBorder"] 
>![Smart Vitals patch eszköz sablon](media/smart-vitals-device-template.png)

Ha az **Eszközcsoportok** fülre kattint, azt is látni fogja, hogy ezek az eszközsablonok automatikusan eszközcsoportokat hoznak létre számukra.

### <a name="rules"></a>Szabályok

A szabályok lapra ugráskor három szabály jelenik meg az alkalmazássablonban:

* **Magas merevítőhőmérséklet**: Ez a szabály akkor aktiválódik, ha a&deg;Smart Knee Brace készülék hőmérséklete 5 perces ablakban 95 F-nél nagyobb. Ezzel a szabállyal figyelmeztetheti a beteget és az ápolási csapatot, és távolról lehűtheti az eszközt.

* **Esés észlelve**: Ez a szabály akkor aktiválódik, ha a beteg elesését észleli. Ezzel a szabállyal konfigurálhat egy műveletet egy operatív csapat üzembe helyezéséhez, hogy segítse az elesett beteget.

* **A javítás akkumulátora alacsony:** Ez a szabály akkor aktiválódik, ha az eszköz akkumulátorának töltöttségi szintje 10% alá csökken. Ezzel a szabállyal értesítést indíthat el a betegnek az eszköz töltéséhez.

>[!div class="mx-imgBorder"] 
>![Merevítő hőmérséklet magas szabály](media/brace-temp-rule.png)

### <a name="devices"></a>Eszközök

Kattintson az **Eszközök** fülre, majd válassza ki az **Intelligens térdmerevítő egy példányát.** Látni fogja, hogy három nézetből tudja megvizsgálni a kiválasztott eszközadatait. Ezek a nézetek jönnek létre, és közzé, amikor az eszköz sablon az eszköz, ami azt jelenti, hogy lesz konzisztens az összes eszköz, amely csatlakoztatja vagy szimulálja.

Az **Irányítópult** nézet áttekintést nyújt a telemetriai adatokról és az eszközről érkező, kezelőorientált tulajdonságokról.

A **Tulajdonságok** lapon szerkesztheti a felhő tulajdonságait, és olvashatja/írhatja az eszköz tulajdonságait.

A **Parancsok** lapon futtathatja az eszközsablon részeként modellezett parancsokat.

>[!div class="mx-imgBorder"] 
>![Térdmerevítő nézetek](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az alkalmazást a **Felügyeleti > alkalmazásbeállításai** hoz, és kattintson a **Törlés gombra.**

>[!div class="mx-imgBorder"] 
>![Alkalmazás törlése](media/admin-delete.png)

## <a name="next-steps"></a>További lépések

Lépjen tovább a következő cikkhez, amelyből megtudhatja, hogyan hozhat létre olyan szolgáltatói irányítópultot, amely csatlakozik az IoT Central-alkalmazáshoz.

> [!div class="nextstepaction"]
> [Szolgáltatói irányítópult létrehozása](howto-health-data-triage.md)