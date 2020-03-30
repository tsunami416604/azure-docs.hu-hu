---
title: Eszközadatok elemzése az Azure IoT Central alkalmazásban | Microsoft dokumentumok
description: Eszközadatok elemzése az Azure IoT Central alkalmazásban.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158282"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Az eszközadatok elemzésének elemzése az elemzéssel

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*



Az Azure IoT Central gazdag elemzési lehetőségeket biztosít a korábbi trendek elemzéséhez és az eszközök különböző telemetriáinak korrelációjához. Első lépésekhez keresse fel az **Analytics** elemet a bal oldali ablaktáblán.

## <a name="understanding-the-analytics-ui"></a>Az Analytics felhasználói felületének ismertetése
Az Analytics felhasználói felülete három fő összetevőből áll:
- **Adatkonfigurációpanel:** A konfigurációs panelen először jelölje ki azt az eszközcsoportot, amelynek az adatait elemezni szeretné. Ezután válassza ki az elemezni kívánt telemetriai adatokat, és válassza ki az egyes telemetriai adatok összesítési módszerét. **A Felosztás vezérlő** vel az eszköz tulajdonságainak dimenzióként történő használatával csoportosíthatja az adatokat.

- **Időszabályozás:** Az idővezérlővel választhatja ki azt az időtartamot, amelynek az adatait elemezni szeretné. Az időcsúszka két végénhúzva kijelölheti az időtartamot. Az idővezérlőhöz egy **Intervallumméret** csúszka is található, amely a gyűjtőt vagy az adatok összesítéséhez használt intervallumméretet szabályozza. 

- **Diagram vezérlő:** A diagramvezérlő vonaldiagramként ábrázolja az adatokat. Az egyes vonalak láthatóságát a diagram jelmagyarázatával való interakcióval módosíthatja. 


  ![Elemzési felhasználói felület – áttekintés](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Az adatok lekérdezése

Először válasszon egy **eszközcsoportot**és az elemezni kívánt telemetriai adatokat. Miután elkészült, válassza **az Elemzés** lehetőséget az adatok megjelenítésének megkezdéséhez.

- **Eszközcsoport:** Az [eszközcsoport](tutorial-use-device-groups.md) az eszközök felhasználó által definiált csoportja. Például az oaklandi összes hűtőszekrény vagy az Összes 2.0-s verziós szélturbina.

- **Telemetria:** Válassza ki az elemezni és feltárni kívánt telemetriai adatokat. Több telemetriát is kiválaszthat együtt. Az alapértelmezett összesítési módszer értéke Átlag a numerikus és a Darabszám adattípushoz. A numerikus adattípusok támogatott összesítési módszerei az Átlag, a Maximum, a Minimum, a Darabszám és az Összeg.  A karakterlánc-adattípus támogatott összesítési metódusai számítanak.

- **Felosztás:** A "Split by" vezérlő segít az adatok csoportosításához az eszköz tulajdonságainak dimenzióként való használatával. Az eszköz és a felhő tulajdonságai nak értékei a telemetriai adatokkal együtt csatlakoznak, ahogy és amikor az eszköz elküldi. Ha a felhő vagy az eszköz tulajdonság frissítve lett, majd a diagramon különböző értékek szerint csoportosított telemetriai adatok at fog látni.

    > [!TIP]
    > Az egyes eszközök adatainak külön-külön való megtekintéséhez válassza az Eszközazonosító lehetőséget a "Felosztás" vezérlőben.

## <a name="interacting-with-your-data"></a>Az adatokkal való interakció

Miután lekérdezte az adatokat, elkezdheti azokat a vonaldiagramon való megjelenítésben. Megjelenítheti/elrejtheti a telemetriai adatokat, módosíthatja az időtartamot, megtekintheti a telemetriai adatokat egy adatrácsban.

- **Időszerkesztő panel:** Alapértelmezés szerint az elmúlt egy nap adatait kérünk le. Az időidőtartam módosításához húzza az időcsúszka két végét. A naptár vezérlővel kiválaszthat egy előre definiált időgyűjtőt, vagy egyéni időtartományt is választhat. Az idővezérlőhöz egy **Intervallumméret** csúszka is található, amely a gyűjtőt vagy az adatok összesítéséhez használt intervallumméretet szabályozza.

    ![Időszerkesztő](media/howto-create-analytics/timeeditorpanel.png)

    - **Belső dátumtartomány csúszka eszköz:** A két végpontvezérlőt úgy használhatja, hogy a kívánt időtartamon keresztül húzza őket. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlője korlátozza.
    
   
    - **Külső dátumtartomány csúszka vezérlője**: A végpontvezérlőelemek segítségével válassza ki a külső dátumtartományt, amely elérhető lesz a belső dátumtartomány-vezérlőhöz.

    - **Dátumtartomány gombjainak növelése és csökkentése:** Az időtartam növelése vagy csökkentése a kívánt intervallum hoz való bármelyik gomb kiválasztásával.

    - **Intervallumméretű csúszka:** Ezzel nagyíthatja és kicsinyítheti az intervallumokat ugyanebben az időszakban. Ez a művelet pontosabban szabályozza a nagy szeletek közötti mozgást. Segítségével részletes, nagy felbontású nézeteket láthat az adatokról, akár ezredmásodpercek ig is. A csúszka alapértelmezett kezdőpontja a kijelölésből származó adatok legoptimálisabb nézeteként van beállítva, amely egyensúlyt teremt a felbontás, a lekérdezés sebessége és a részletesség között.
    
    - **Dátumtartomány-választó:** Ezzel a webes vezérlővel könnyedén kiválaszthatja a kívánt dátum- és időtartományokat. A vezérlővel a különböző időzónák közötti váltáshoz is válthat. Miután elvégzett egy módosítást, hogy az aktuális munkaterületre vonatkozzon, válassza a Mentés gombot.

    > [!TIP]
    > Az intervallum mérete dinamikusan határozható meg a kiválasztott időtartam alapján. A kisebb időtartamok lehetővé teszik az adatok nagyon részletes, legfeljebb néhány másodperces intervallumokba történő összesítését.


- **Diagram jelmagyarázata:** A diagram jelmagyarázata a kijelölt telemetriai adatokat jeleníti meg a diagramon. Az egérmutatót a jelmagyarázat egyes elemei fölé mutatógombokkal fókuszálhatja a diagramon. A "Felosztás alapja" használatakor a telemetriai adatok a kiválasztott dimenzió megfelelő értékei szerint vannak csoportosítva. A csoport nevére kattintva válthat az egyes telemetriai adatok vagy az egész csoport láthatóságát.  


- **Y tengely formátumvezérlés:** az y-tengely mód végighalad a rendelkezésre álló y-tengely nézetbeállításokon. Ez a vezérlő csak akkor érhető el, ha különböző telemetries láthatóvá. Az y tengelyt a következő három mód közül választhat:

    - **Halmozott:** Minden telemetriai elemtarát, és minden a grafikonok saját y-tengelye van. Ez a mód van beállítva alapértelmezettként.
    - **Megosztva:** A diagram minden telemetriai adatok alapján ugyanazzal az y-tengelysel.
    - **Átfedés:** Több sor egymásra verése ugyanazon az y tengelyen, az y-tengely adatai nak a kijelölt vonal alapján történő megváltoztatásával.

  ![Adatok elrendezése az y tengelyen keresztül különböző vizualizációs módokkal](media/howto-create-analytics/yaxiscontrol.png)

- **Nagyítás vezérlése:** A nagyítás lehetővé teszi, hogy tovább fúrjon az adatokba. Ha olyan időszakot talál, amelyet az eredményhalmazon belül szeretne összpontosítani, az egérmutatóval fogja meg a területet, majd húzza a kívánt végpontra. Ezután kattintson a jobb gombbal a kiválasztott területre, és kattintson a Nagyítás gombra.

  ![Az adatok nagyítása](media/howto-create-analytics/zoom.png)

A három pont alatt több diagramvezérlő is található az adatokkal való interakcióhoz.

- **Rács megjelenítése:** Az eredmények táblázatformátumban érhetők el, így megtekintheti az egyes adatpontokkal megadott értéket.

- **Jelölő eldobása:** Az "Drop Marker" vezérlő lehetővé teszi bizonyos adatpontok rögzítését a diagramon. Ez akkor hasznos, ha több sor adatait próbálja összehasonlítani különböző időszakokban.

  ![Az elemzés rácsnézetének megjelenítése](media/howto-create-analytics/additionalchartcontrols.png)