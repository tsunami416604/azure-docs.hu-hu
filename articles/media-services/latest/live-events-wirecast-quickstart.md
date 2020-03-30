---
title: Hozzon létre egy Azure Media Services élő közvetítést a portállal és a Wirecasttal
description: További információ az Azure Media Service Live-közvetítés létrehozásáról
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: e5bdd75ca61d53a64f003633d74e3d8f7992a98b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336336"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Hozzon létre egy Azure Media Services élő közvetítést a portállal és a Wirecasttal

Ez az első lépések útmutató feltételezi, hogy rendelkezik egy Azure-előfizetéssel, és létrehozott egy Azure Media Services-fiókot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Nyissa meg a webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

Ebben a rövid útmutatóban a következőket fedjük le:

- Helyszíni kódoló létrehozása a Telestream Wirecast ingyenes próbaverziójával
- Élő közvetítés beállítása
- Élő közvetítés kimenetének beállítása
- Alapértelmezett streamelési végpont futtatása
- Az Azure Media Player használata az élő közvetítés és az igény szerinti kimenet megtekintéséhez

Az egyszerű ség érdekében az Azure Media Services kódolási készletét használjuk a Wirecastban, az átadó felhőkódolásban és az RTMP-ben.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Helyszíni kódoló beállítása wirecasttal

1. Töltse le és telepítse a Wirecast-ot az operációs rendszerhez ahttps://www.telestream.net
1. Indítsa el az alkalmazást, és használja kedvenc e-mail címét a termék regisztrálásához.  Tartsa nyitva az alkalmazást.
1. Kapni fog egy e-mailt, amelyben megkéri, hogy ellenőrizze e-mail címét, majd az alkalmazás elindítja az ingyenes próbaverziót.
1. AJÁNLOTT: Nézze meg a videót bemutató a nyitó alkalmazás képernyőjén.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Az Azure Media Services élő közvetítése

1. Miután a portálon az Azure Media Services-fiókra navigált, válassza az **Élő közvetítés lehetőséget** a Media Services-listáról.<br/>
![Válassza az Élő közvetítés hivatkozását](media/live-events-wirecast-quickstart/select-live-streaming.png)<br/>
1. Új **élő** közvetítési esemény létrehozásához kattints az Élő esemény hozzáadása elemre.<br/>
![Élő esemény hozzáadása ikon](media/live-events-wirecast-quickstart/add-live-event.png)<br/>
1. Adja meg az új esemény nevét, például a *TestLiveEvent* eseményt az Élő esemény **neve** mezőben.<br/>
![Élő esemény neve szövegmező](media/live-events-wirecast-quickstart/live-event-name.png)<br/>
1. Adja meg az esemény nem kötelező leírását a **Leírás** mezőben.
1. Válassza az **Áthaladás – nincs felhőalapú kódolás választógomb.**<br/>
![Felhőalapú kódolásválasztó gomb](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Válassza az **RTMP** választógombot.
1. Győződjön meg arról, hogy a **Nincs** választógomb van kiválasztva az Élő esemény indítása korhoz, hogy megakadályozza az élő esemény számlázását, mielőtt készen állna.  (A számlázás az élő esemény elindítását követően kezdődik.) ![Élő esemény rádiógombindítása](media/live-events-wirecast-quickstart/start-live-event-no.png)<br/>
1. A beállítások áttekintéséhez kattintson a **Véleményezés + létrehozás** gombra.
1. Az élő esemény létrehozásához kattintson a **Létrehozás** gombra. Ezután visszatér az élő esemény lista nézetébe.
1. Kattintson az **imént létrehozott élő eseményre mutató linkre.** Figyelje meg, hogy az esemény leállt.
1. Tartsa nyitva ezt a lapot a böngészőben.  Majd később visszatérünk rá.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Élő közvetítés beállítása a Wirecast Studio segítségével

1. Feltéve, hogy a Wirecast alkalmazás még mindig meg van nyitva, válassza a főmenü **Üres dokumentum létrehozása parancsát,** majd kattintson a **Folytatás parancsra.**
![Wirecast kezdőképernyő](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Vigye az egérmutatót a Wirecast rétegek területének első rétege fölé.  Kattintson a megjelenő **Hozzáadás** ikonra, és jelölje ki az adatfolyamként bevitt videobemenetet.  Megnyílik a Mesterréteg 1 párbeszédablak.<br/>
![Wirecast hozzáadás ikon](media/live-events-wirecast-quickstart/add-icon.png)
1. Válassza a menü **Videorögzítés parancsát,** majd válassza ki a használni kívánt fényképezőgépet. Ha fényképezőgépet választ, a kamera nézete megjelenik az Előnézet területen.
![Wirecast videó felvétel kiválasztása képernyő](media/live-events-wirecast-quickstart/video-shot-selection.png)
1. Vigye az egérmutatót a Drótlevetítés rétegek területén a második réteg fölé. Kattintson a megjelenő **Hozzáadás** ikonra, és jelölje ki az adatfolyamként hangot.  Megnyílik a Master Layer 2 párbeszédablak.
1. Válassza a menü **Hangrögzítés parancsát,** majd válassza ki a használni kívánt hangbemenetet.
![Wirecast audio felvétel kiválasztása képernyő](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. A főmenüben válassza a **Kimeneti beállítások lehetőséget.**  Megjelenik a Kimenet párbeszédpanel.
1. Válassza ki az **Azure Media Services** a kimeneti legördülő menüből.  Az Azure Media Services kimeneti beállítása automatikusan feltölti a *kimeneti* beállítások nagy részét.<br/>
![Wirecast kimeneti beállítások képernyő](media/live-events-wirecast-quickstart/azure-media-services.png)
1. A következő szakaszban visszatér az Azure Media Services böngészőjében a *bemeneti URL-cím* másolásához a kimeneti beállítások megadásához.

### <a name="copy-and-paste-the-input-url"></a>A bemeneti URL másolása és beillesztése

1. A portál Azure Media Services-oldalán kattintson az **Indítás** gombra az élő közvetítési esemény elindításához. (A számlázás most kezdődik.)<br/>
![Start ikon](media/live-events-wirecast-quickstart/start.png)
2. Kattintson a **Biztonságos/Nem biztonságos** kapcsolóra, ha nem **biztonságosra**szeretné állítani.  Ez a protokollt RTMPS helyett RTMP-re állítja.
3. Másolja a **szövegbeviteli URL-címet** a vágólapra.
![Bemeneti URL](media/live-events-wirecast-quickstart/input-url.png)
4. Váltson át a Wirecast alkalmazásra, és illessze be a **bemeneti URL-címet** a Kimeneti beállítások **Cím** mezőjébe.<br/>
![Wirecast bemeneti URL-címe](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Kattintson **az Rendben gombra.**

## <a name="setting-up-outputs"></a>Kimenetek beállítása

Ez a rész beállítja a kimeneteket, és lehetővé teszi, hogy mentse a felvételt az élő közvetítés.  

> [!NOTE]
> A kimenet streameléséhez a streamelési végpontnak futnia kell.  Lásd: Az alapértelmezett streamelési végpont futtatása szakasz alább.

1. Kattintson a **Kimenetek létrehozása** linkre a Kimenetek videómegjelenítő alatt.
1. Ha szeretné, szerkesztheti a kimenet nevét a **Név** mezőben valami felhasználóbarátabbra, hogy később könnyen megtalálhassa.
![Kimeneti név mező](media/live-events-wirecast-quickstart/output-name.png)
1. Egyelőre hagyd békén a többi mezőt.
1. Kattintson a **Következő** adatfolyam-lokátor hozzáadása elemre.
1. Módosítsa a lokátor nevét valami felhasználóbarátabbra, ha szeretné.
![Lokátor névmezője](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Hagyja minden mást ezen a képernyőn egyedül most.
1. Kattintson **a Létrehozás gombra.**

## <a name="starting-the-broadcast"></a>Az adás indítása

1. A Wirecast ban válassza **a Kimeneti > Start / Stop broadcasting > Start Azure Media Services : Azure Media Services** a főmenüből.  Miután az adatfolyamot elküldték az élő eseményre, a Wirecast élő ablaka megjelenik az élő esemény videólejátszójában az Azure Media Services élő eseményoldalán.

   ![Szórás menüelemeinek indítása](media/live-events-wirecast-quickstart/start-broadcast.png)

1. Az előnézeti ablak ban található **Ugrás** gombra kattintva megkezdheti a Wirecast-rétegekhez kiválasztott video- és hanganyagok sugárzását.

   ![Wirecast Go gomb](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Ha hiba történt, próbálja meg újratölteni a lejátszót a lejátszó újratöltése linkre kattintva.

## <a name="running-the-default-streaming-endpoint"></a>Az alapértelmezett streamelési végpont futtatása

1. Győződjön meg arról, hogy a streamelési végpont fut, ha a Media Services-terméklistában a **Streamelési végpontokat** választja. A lejátszási végpontok oldalára kerül.<br/>
![Végpont menüelem streamelése](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Ha az alapértelmezett streamelési végpont állapota le van állítva, kattintson az **alapértelmezett** streamelési végpontra. Ez az adott végpontlapjára vezet.
1. Kattintson a **Start gombra.**  Ez elindítja a streamelési végpontot.<br/>
![Végpont menüelem streamelése](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-with-azure-media-player"></a>A kimeneti közvetítés lejátszása az Azure Media Player rel

1. Másolja a **streamelési URL-címet** a Kimeneti videólejátszó alá.
1. Webböngészőben nyissa meg a bemutatót az Azure Media Playerhttps://ampdemo.azureedge.net/azuremediaplayer.html
1. Illessze be a **streamelési URL-címet** az Azure Media Player URL-mezőjébe.
1. Kattintson a **Lejátszó frissítése** gombra.
1. Az **play** élő közvetítés megtekintéséhez kattints a videó lejátszásikonjára.

## <a name="stopping-the-broadcast"></a>Az adás leállítása

Ha úgy gondolja, hogy elegendő tartalmat streamelt, állítsa le az adást.

1. A Wirecast, kattintson a **broadcast** gombra.  Ez leállítja az adást a Wirecast-ból.
1. A portálon kattintson a **Stop**gombra. Kap egy figyelmeztető üzenetet, hogy az élő közvetítés leáll, de a kimenet most antól igény szerinti eszközlesz.
1. Kattintson a **Stop** gombra a figyelmeztető üzenetben. Az Azure Media Player is megjelenik egy hiba, mivel az élő közvetítés már nem érhető el.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Az igény szerinti kimenet lejátszása az Azure Media Player rel

A létrehozott kimenet most már elérhető az igény szerinti streameléshez, amíg a streamelési végpont fut.

1. Nyissa meg a Media Services listát, és válassza **az Eszközök lehetőséget.**
1. Keresse meg a korábban létrehozott eseménykimenetet, és kattintson **az eszközre mutató hivatkozásra.** Megnyílik az eszközkimeneti lap.
1. Másolja a **streamelési URL-címet** az eszköz videolejátszója alá.
1. Térjen vissza az Azure Media Player a böngészőben, és illessze be a **streamelési URL-címet** az Azure Media Player URL-mezőjébe.
1. Kattintson **a Lejátszó frissítése gombra.**
1. Kattintson **play** a videó lejátszásikonjára az igény szerinti eszköz megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!IMPORTANT]
> Állítsa le a szertartást! Miután elvégezte a lépéseket ebben a rövid útmutatóban, győződjön meg róla, hogy leállítja az élő eseményt és a streamelési végpontot, vagy továbbra is fizetni fog a további futásidejére. Az élő esemény leállításához olvassa el az Adás leállítása című témakört a fenti 2.

### <a name="stopping-the-streaming-endpoint"></a>A streamelési végpont leállítása

1. A Media Services listából válassza a **Streamelési végpontok**lehetőséget.
2. Kattintson a korábban elindított **alapértelmezett** streamelési végpontra. Ezzel megnyitja a végpont oldalát.
3. Kattintson a **Stop gombra.**  Ez leállítja a streamelési végpontot.

> [!TIP]
> Ha nem szeretné megtartani az eseményekből származó eszközöket, törölje őket, hogy megakadályozza a tárolás számlázását.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Mi a következő cikk sorrendben](./live-events-outputs-concept.md)
