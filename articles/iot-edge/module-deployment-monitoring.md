---
title: "Modulok telepítése Azure IoT peremhálózati |} Microsoft Docs"
description: "További tudnivalók: hogyan modulok telepítve, a peremhálózati eszköz"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d8688ab2daefd400e9c0948853459dd238fa0d43
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>IoT peremhálózati telepítések egyetlen eszközökhöz vagy léptékű megismerése – előzetes

Hajtsa végre az Azure IoT peremeszközök egy [eszközéletciklus] [ lnk-lifecycle] az IoT-eszközök más típusú hasonló:

1. Az IoT-peremeszközök törlődnek, amely magában foglalja a lemezképkészítő eszköz egy operációs rendszer, és telepíti a [IoT peremhálózati futásidejű][lnk-runtime].
1. Az eszközök történő futtatásra vannak konfigurálva [IoT peremhálózati modulok][lnk-modules], és majd figyelni a állapotát. 
1. Eszközök végül vonni, cseréje vagy elavulttá válnak.  

Az Azure IoT peremhálózati IoT peremhálózati eszközökön való futtatásához a modulok konfigurálása két lehetőséget biztosít: egyet a fejlesztési és gyors ismétlési egy eszközön (az Azure IoT peremhálózati oktatóanyagok a használt), és egy IoT peremeszközök nagy flottái kezeléséhez. Ezen módszerek esetén érhető el az Azure portálon és szoftveresen.

Ez a cikk a konfigurációs összpontosít, és figyelési eszközök flották szakaszból együttesen a IoT peremhálózati központi telepítések. A teljes üzembe helyezés lépései a következők:   

1. Operátor határozza meg a központi telepítések, modulok, valamint a Céleszközök együttesét írja le. Minden központi telepítés rendelkezik egy üzembe helyezési jegyzék, amely tükrözi ezt az információt. 
1. Az IoT-központ szolgáltatás az összes megcélzott eszköz konfigurálhatja azokat a kívánt modulok kommunikál. 
1. Az IoT-központ szolgáltatás állapota lekéri az IoT-peremeszközök, és elérhetővé teszi azokat a figyelheti az operátor.  Operátor láthatja például, ha peremhálózati eszköz beállítása nem sikerült, vagy ha egy modul futásidejű sikertelen lesz. 
1. Bármikor új IoT peremeszközök, amelyek megfelelnek a célcsoport-kezelési feltételek a központi telepítés vannak konfigurálva. Például központi telepítések, minden IoT peremhálózati eszköz, Washington államban célozza automatikusan konfigurálja egy új IoT peremhálózati eszköz, amennyiben van üzembe helyezve, és Washington államban eszközcsoporthoz adja hozzá. 
 
Ez a cikk végigvezeti a minden érintett konfigurálása, és a központi telepítés figyelésének összetevőt. Létrehozása és frissítése a központi telepítés részletes útmutatás, lásd: [központi telepítése és figyelése IoT peremhálózati modulok léptékű][lnk-howto].

## <a name="deployment"></a>Környezet

A központi telepítés IoT peremhálózati modul lemezképek IoT peremeszközök a célzott megfelelő példányokat futtató rendeli hozzá. Működését tekintve a konfigurálása egy IoT peremhálózati üzembe helyezési jegyzék tartalmazza a megfelelő paraméterekkel inicializálási modulokat. A központi telepítés rendelhetők hozzá egyetlen eszközt (általában eszközazonosító alapján), vagy egy eszközcsoportra (címkék alapján). IoT peremhálózati eszköz egy üzembe helyezési jegyzék kap, ha azt letölti a modul tároló képek telepíti a megfelelő tárolót tárházak találhatók, és ennek megfelelően konfigurálja azokat. A központi telepítés létrehozása után operátor figyelheti a központi telepítési állapotának megtekintéséhez, hogy a megcélzott eszköz helyesen van-e konfigurálva.   

Eszközök kell üzembe helyezhető IoT peremeszközök állítani azokat a központi telepítés. A következő előfeltételek, és nem szerepelnek a központi telepítés:
* Az alap operációs rendszer
* Docker 
* Az IoT-Edge futásidejű kiépítése 

### <a name="deployment-manifest"></a>Az üzembe helyezési jegyzék

Egy üzembe helyezési jegyzék, amely leírja a célzott IoT peremeszközök konfigurálni kell a modulok JSON-dokumentumból. A konfigurációs metaadatok összes moduljánál, beleértve a rendszer szükséges modulokat (pontosabban a IoT peremhálózati ügynök és peremhálózati IoT hub) tartalmaz.  

A konfigurációs modulokhoz tartozó metaadatokban: 
* Verzió 
* Típus 
* Állapot (pl. futó vagy leállt) 
* Indítsa újra a házirendet 
* Kép és a tároló tárház 
* A bemeneti és kimeneti adatok útvonalak 

### <a name="target-condition"></a>Cél feltétel

Célcsoport-kezelési feltételek adja meg, hogy IoT peremhálózati eszköz egy központi telepítés hatálya alá kell lennie. Célcsoport-kezelési feltételek eszköz iker címkék alapulnak. 

### <a name="priority"></a>Prioritás

A prioritás határozza meg, hogy a központi telepítés alkalmazni kell a megcélzott eszköz viszonyítva más központi. A központi telepítés érték egy pozitív egész szám, az azt jelöli, magasabb prioritású nagyobb számmal rendelkező. Ha egynél több központi telepítési IoT peremhálózati eszköz, a központi telepítés a legmagasabb prioritású vonatkozik.  Alacsonyabb prioritások telepítések esetén nem érvényesek, sem azok egyesítve lesznek.  Ha egy eszköz két vagy több központi telepítéssel azonos prioritással rendelkező, a legutóbb létrehozott központi telepítés (határozza meg a létrehozás időbélyegzője) vonatkozik.

### <a name="labels"></a>Címkék 

Címkék karakterlánc kulcs/érték párok, amelyek segítségével és a központi telepítések csoportja. A telepítés több címke lehet. Címkék megadása nem kötelező, és tegye nincs hatással az IoT-peremeszközök tényleges konfigurációját. 

### <a name="deployment-status"></a>Üzembe helyezés állapota

A központi telepítés annak meghatározásához, hogy azt a rendszer sikeresen alkalmazta a célzott IoT peremhálózati eszköz figyelhető.  A megcélzott peremhálózati eszköz egy vagy több következő állapot kategória jelenik meg: 
* **Cél** jeleníti meg az IoT él eszközöket, amelyek megfelelnek a feltétel célzó központi telepítést.
* **Tényleges** jeleníti meg a célként megadott IoT él, nem a nagyobb prioritású egy másik telepítés által célzott.
* **Kifogástalan** jeleníti meg az IoT él, jelentettek vissza a szolgáltatás a modulok telepítése sikeresen megtörtént. 
* **A nem megfelelő** jeleníti meg az IoT él eszközöket a szolgáltatással, amelyre egy vissza jelentett vagy modulok nem lett sikeresen telepített. A további vizsgálathoz a hiba, távolról csatlakozni, amellyel az eszközöket, és a naplófájlban.
* **Ismeretlen** jeleníti meg az IoT él, nem jelentettek bármely vonatkozó a központi telepítés állapotát. A további vizsgálathoz, adatait és a naplófájlok szolgáltatásfájlokat megtekintése.

## <a name="phased-rollout"></a>Fázisokra bontva történő bevezetéséhez 

Fázisokra bontva történő bevezetéséhez az egy teljes folyamat, amellyel operátor telepíti a módosítások IoT peremeszközök kiterjesszük készlete. A célja, hogy a módosítások fokozatosan csökkenthető a kockázata, hogy széles méretezési megszakítása a módosítások elvégzéséhez.  

Egy fázisokra bontva történő bevezetéséhez a következő fázisok és lépések végrehajtása: 
1. IoT peremhálózati eszköz egy tesztkörnyezetben létesíteni létesítési őket, és egy eszköz a két címke például `tag.environment='test'`. A tesztkörnyezetben az éles környezetben, amely a központi telepítési idővel által megcélzott tükrözve. 
1. Beleértve a kívánt modulok és konfigurációk központi telepítés létrehozása. A célcsoport-kezelési feltételt kell célként a vizsgálat IoT peremhálózati eszköz környezetben.   
1. Ellenőrizze a tesztkörnyezetben új modul konfigurációját.
1. Frissítse a központi telepítés, hogy új címke hozzáadása a célcsoport-kezelési feltétel éles IoT peremhálózati eszköz egy részhalmazát tartalmazzák. Emellett győződjön meg arról, hogy a központi telepítés a prioritás értéke magasabb, mint más azokra jelenleg célzott központi telepítések 
1. Győződjön meg arról, hogy a telepítés sikeres volt a célzott az IoT-eszközök által a központi telepítés állapotának megtekintése.
1. Frissítse az összes többi éles IoT peremeszközök telepítés.

## <a name="rollback"></a>Visszaállítás

Központi telepítések is vissza lesz vonva hibák vagy konfigurációs hibák esetén.  Mivel a központi telepítés IoT peremhálózati eszköz abszolút modul konfigurációja határozza meg, egy további telepítési kell is telepíthető ugyanarra az eszközre alacsonyabb prioritással akkor is, ha a cél az, hogy távolítsa el az összes modul.  

Az alábbi sorrendben hajtsa végre a visszagörgetése: 
1. Győződjön meg arról, hogy a második központi telepítés is irányuló eszköz ugyanazokat. Ha a visszaállítás célja, hogy távolítsa el az összes modul, a második telepítést nem tartalmazhat modul. 
1. Módosítsa vagy távolítsa el a cél a Feltételkifejezés a központi telepítés kívánja visszaállítani, hogy az eszközök már nem felel meg a célcsoport-kezelési feltétel.
1. Győződjön meg arról, hogy a visszaállítás sikeresen befejeződött, a központi telepítés állapotának megtekintésével.
   * A állítva visszaírt telepítési állapotúnak kell lennie már nem az eszközöket, vissza lett állítva.
   * A második telepítést most tartalmaznia kell az eszközöket, amelyek vissza lett állítva, a telepítés állapota.


## <a name="next-steps"></a>Következő lépések

* A létrehozása, frissítése vagy törlése a központi telepítés lépéseit ismerteti [központi telepítése és figyelése IoT peremhálózati modulok léptékű][lnk-howto].
* További információ más IoT peremhálózati fogalmakat, például a [IoT peremhálózati futásidejű] [ lnk-runtime] és [IoT peremhálózati modulok][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md