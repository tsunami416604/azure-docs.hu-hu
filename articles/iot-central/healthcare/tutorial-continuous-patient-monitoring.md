---
title: Folyamatos beteg monitorozási alkalmazás létrehozása az Azure IoT Centralkal | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre folyamatos beteg-figyelési alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: a4516674878c168d4ce7ea3f30e7b735409a779b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956543"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Oktatóanyag: folyamatos, beteg monitorozási alkalmazás sablonjának üzembe helyezése és áttekintése

Ez az oktatóanyag bemutatja, hogyan kezdheti el az első lépéseket egy IoT Central folyamatos beteg monitorozási alkalmazás sablonjának üzembe helyezésével. Megtudhatja, hogyan helyezheti üzembe a sablont, mit tartalmaz a mező, és mit szeretne tenni a következő lépésekkel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Alkalmazás sablonjának létrehozása
> * Az alkalmazás sablonjának átjárása

## <a name="create-an-application-template"></a>Alkalmazás sablonjának létrehozása

Navigáljon az [Azure IoT Central Application Manager webhelyére](https://apps.azureiotcentral.com/). Válassza a **Létrehozás** lehetőséget a bal oldali navigációs sávon, majd kattintson az **egészségügy** fülre. 

>[!div class="mx-imgBorder"] 
>![app Manager egészségügyi](media/app-manager-health.png)

A **create app (alkalmazás létrehozása** ) gombra kattintva megkezdheti az alkalmazás létrehozását, majd bejelentkezhet a Microsoft személyes, munkahelyi vagy iskolai fiókjával. Ekkor az **új alkalmazás** lapra kerül.

>[!div class="mx-imgBorder"] 
>![alkalmazás-egészségügyi](media/app-manager-health-create.png) létrehozása

Az alkalmazás létrehozása:

1. Az Azure IoT Central automatikusan javaslatot tesz az alkalmazás nevére a kiválasztott sablon alapján. Elfogadhatja ezt a nevet, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét, például a **folyamatos beteg monitorozását**. Az Azure IoT Central egy egyedi URL-előtagot is létrehoz az alkalmazás neve alapján. Ha szeretné, megváltoztathatja ezt az URL-előtagot valami emlékezetre.

2. Kiválaszthatja, hogy **próbaverziós** alkalmazást **vagy utólagos elszámolású alkalmazást szeretne** -e létrehozni. A **próbaverziós** alkalmazások a lejáratuk előtt hét napig ingyenesen használhatók, és akár öt ingyenes eszközt is lehetővé tesznek. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá. Ha létrehoz egy próbaverziós alkalmazást, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól. Az **utólagos elszámolású alkalmazások akár** két ingyenes eszközt is támogatnak, és az Azure-előfizetések adatait is be kell állítani.

3. Az alkalmazás üzembe helyezéséhez kattintson a lap alján található **Létrehozás** gombra.

## <a name="walk-through-the-application-template"></a>Az alkalmazás sablonjának átjárása

### <a name="dashboards"></a>Irányítópultok

Az alkalmazás-sablon üzembe helyezése után először a **Lamna a beteg monitorozási irányítópultján**kell megjelenni. A Lamna Healthcare egy fiktív kórházi rendszer, amely két kórházat tartalmaz: a Woodgrove kórházat és a Burkville kórházat. A Woodgrove Hospital ezen kezelői irányítópultján megtekintheti a sablonban található eszközök információit és telemetria, valamint parancsokat, feladatokat és műveleteket is végrehajthat. Az irányítópulton a következőket teheti:

* Lásd: az eszköz telemetria és tulajdonságai, például az eszköz **töltöttségi szintje** vagy a **kapcsolati** állapota.

* Megtekintheti a Smart Vitals javító eszköz **alapszintű csomagját** és helyét.

* Telepítse **újra** az intelligens vitális javítását az új páciensek számára.

* Tekintse meg a **szolgáltatói irányítópultot** , amelyet a kórházi ápolási csapat láthat a betegek nyomon követése érdekében.

* Módosítsa az eszköz **beteg állapotát** annak jelzésére, hogy az eszköz használatban van-e egy fekvőbeteg-vagy távoli forgatókönyvben.

>[!div class="mx-imgBorder"] 
>![Lamna fekvőbeteg-](media/lamna-in-patient.png)

Az **Ugrás a távoli beteg irányítópultra** lehetőségre kattintva megtekintheti a Burkville Kórházhoz használt második operátor irányítópultját. Ez az irányítópult a műveletek, a telemetria és az információk hasonló készletét tartalmazza. Emellett több, a használatban lévő eszközt is láthat, és a **belső vezérlőprogram frissítését** is lehetővé teszi.

>[!div class="mx-imgBorder"] 
>![Lamna távoli](media/lamna-remote.png)

Mindkét irányítópulton bármikor visszatérhet ehhez a dokumentációhoz.

### <a name="device-templates"></a>Eszközök sablonjai

Ha az **eszközbeállítások** lapra kattint, látni fogja, hogy a sablonhoz két különböző típusú eszköz tartozik:

* **Smart vitals-javítás**: ez az eszköz egy olyan javítást jelent, amely különböző, a kórházon belüli és kívüli betegek monitorozására szolgáló létfontosságú jeleket méri. Ha a sablonra kattint, látni fogja, hogy az eszközök, például az akkumulátorok és az eszközök hőmérsékletének elküldése mellett a javítás a beteg egészségügyi adatokat is elküldi, például a légzési ráta és a vérnyomás.

* **Intelligens térd zárójel**: ez az eszköz egy térd zárójelet jelöl, amelyet a betegek a térdtől való kiváltáskor használhatnak. Ha erre a sablonra kattint, az eszközön kívül olyan képességek jelennek meg, mint a mozgási és a gyorsítási tartomány.

>[!div class="mx-imgBorder"] 
>![Smart Vitals patch-eszköz sablonja](media/smart-vitals-device-template.png)

Ha az eszközcsoport (eszközcsoport **) lapra kattint** , azt is láthatja, hogy ezekhez az eszközökhöz automatikusan vannak létrehozva az erőforráscsoportok.

### <a name="rules"></a>Szabályok

A szabályok lapra ugráskor három, az alkalmazás sablonjában található szabály jelenik meg:

* **Kapcsos zárójel hőmérséklete**: Ez a szabály akkor aktiválódik, ha az intelligens térd zárójelének hőmérséklete nagyobb, mint 95&deg;F egy 5 perces ablakban. Ezt a szabályt használhatja arra, hogy figyelmeztesse a betegeket és a gondozási csapatot, és távolról lehűtsük az eszközt.

* **Fall észlelve**: Ez a szabály akkor aktiválódik, ha a beteg bukása észlelhető. Ezt a szabályt egy olyan művelet konfigurálására használhatja, amely egy operatív csapat üzembe helyezését segíti az elhullott beteg számára.

* **Alacsony javítási akkumulátor**: Ez a szabály akkor aktiválódik, ha az eszközön lévő akkumulátor töltöttségi szintje 10% alá esik. Ezt a szabályt használhatja arra, hogy elindítson egy értesítést a betegnek az eszköz feltöltéséhez.

>[!div class="mx-imgBorder"] 
>![zárójel hőmérsékletének felső szabálya](media/brace-temp-rule.png)

### <a name="devices"></a>Eszközök

Kattintson az **eszközök** fülre, majd válassza ki a **Smart térd zárójel**egy példányát. Láthatja, hogy három nézet áll rendelkezésre a kiválasztott eszköz információinak megismeréséhez. Ezek a nézetek az eszköz sablonjának létrehozásakor jönnek létre és jelennek meg. Ez azt jelenti, hogy az összes csatlakoztatott vagy szimulált eszközön konzisztensek lesznek.

Az **irányítópult** nézet áttekintést nyújt azokról a telemetria és tulajdonságokról, amelyek a kezelő által vezérelt eszközről érkeznek.

A **Tulajdonságok** lapon módosíthatja a felhő tulajdonságait, valamint az eszköz olvasási/írási tulajdonságait.

A **parancsok** lap lehetővé teszi, hogy az eszköz sablonjának részeként modellezett parancsokat futtasson.

>[!div class="mx-imgBorder"] 
>![a térd kapcsos zárójeles nézeteit](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az **adminisztráció > alkalmazás beállításai** között, és kattintson a **Törlés**gombra.

>[!div class="mx-imgBorder"] 
>![alkalmazás törlése](media/admin-delete.png)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan hozhat létre olyan szolgáltatói irányítópultot, amely kapcsolódik a IoT Central alkalmazáshoz.

> [!div class="nextstepaction"]
> [Szolgáltatói irányítópult létrehozása](howto-health-data-triage.md)