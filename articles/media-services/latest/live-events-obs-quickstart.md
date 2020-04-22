---
title: Hozzon létre egy Azure Media Services élő közvetítést az OBS Studio segítségével
description: Ismerje meg, hogyan hozhat létre élő közvetítést az Azure Media Services élő közvetítése a portál és az OBS Studio használatával
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726612"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Hozzon létre egy Azure Media Services élő közvetítést az OBS-sel

Ez a rövid útmutató segít létrehozni egy Azure Media Services élő közvetítés az Azure Portal és az Open Broadcasting Studio (OBS) használatával. Feltételezi, hogy Rendelkezik egy Azure-előfizetéssel, és létrehozott egy Media Services-fiókot.

Ebben a rövid útmutatóban a következőket tjük ki:

- Helyszíni kódoló beállítása a fedélzeti vizsgálattal.
- Élő közvetítés beállítása.
- Élő közvetítés kimenetének beállítása.
- Alapértelmezett streamelési végpont futtatása.
- Az Azure Media Player használatával megtekintheti az élő közvetítést és az igény szerinti kimenetet.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőt, és nyissa meg a [Microsoft Azure portált.](https://portal.azure.com/) Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Helyszíni kódoló beállítása az OBS használatával

1. Töltse le és telepítse az OBS-t az operációs rendszeréhez az [Open Broadcaster Software weboldalon.](https://obsproject.com/)
1. Indítsa el az alkalmazást, és tartsa nyitva.

## <a name="run-the-default-streaming-endpoint"></a>Az alapértelmezett streamelési végpont futtatása

1. Válassza a **Streamelési végpontok lehetőséget** a Media Services listában.

   ![Végpontok streamelése menüelem](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Ha az alapértelmezett streamelési végpont állapota le van állítva, jelölje ki. Ez a lépés az adott végpont lapjára viszi.
1. Válassza az **Indítás** elemet.

   ![Start gomb a streamelési végponthoz](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Az Azure Media Services élő közvetítése

1. Nyissa meg az Azure Media Services-fiókot a portálon belül, majd válassza az **Élő közvetítés lehetőséget** a Media Services-listáról. **Media Services**

   ![Élő közvetítési hivatkozás](media/live-events-obs-quickstart/select-live-streaming.png)
1. Új élő közvetítési esemény létrehozásához válassza **az Élő esemény hozzáadása** lehetőséget.

   ![Élő esemény hozzáadása ikon](media/live-events-obs-quickstart/add-live-event.png)
1. Adja meg az új esemény nevét, például *a TestLiveEvent*eseményt az **Élő esemény neve** mezőben.

   ![Élő esemény neve mező](media/live-events-obs-quickstart/live-event-name.png)
1. Adja meg az esemény nem kötelező leírását a **Leírás** mezőben.
1. Válassza az **Áthaladás – nincs felhőkódolási** lehetőség.

   ![Felhőalapú kódolási beállítás](media/live-events-obs-quickstart/cloud-encoding.png)
1. Válassza az **RTMP** lehetőséget.
1. Győződjön meg arról, hogy a **Nincs** lehetőség van kiválasztva az **Élő esemény indítása**beállításhoz, hogy elkerülje az élő esemény számlázását, mielőtt készen állna. (A számlázás az élő esemény indításakor kezdődik.)

   ![Élő esemény indítása lehetőség](media/live-events-obs-quickstart/start-live-event-no.png)
1. A beállítások áttekintéséhez válassza a **Véleményezés + létrehozás** gombot.
1. Az élő esemény létrehozásához kattintson a **Létrehozás** gombra. Ezután visszatérsz az élő esemény hirdetésére.
1. Válaszd ki az imént létrehozott élő eseményre mutató hivatkozást. Figyelje meg, hogy az esemény leállt.
1. Tartsa nyitva ezt a lapot a böngészőben. Majd később visszatérünk rá.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Élő közvetítés beállítása az OBS Studio használatával

Az OBS egy alapértelmezett jelenettel kezdődik, de nincs kijelölt bemenet.

   ![AZ OBS alapértelmezett képernyője](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Videoforrás hozzáadása

1. A **Források** panelen kattintson a **hozzáadás** ikonra egy új forráseszköz kiválasztásához. Megnyílik **a Források** menü.

1. Válassza a forráseszköz menüjének **Videorögzítő eszköz parancsát.** Megnyílik **a Forrás létrehozása/kiválasztása** menü.

   ![OBS források menü videoeszköz kiválasztva](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Jelölje be a **Meglévő** választóhozzáadása gombot, majd kattintson **az OK gombra.** Megnyílik **a Videoeszköz tulajdonságai** menü.

   ![OBS új videoforrás menü meglévő kijelölt hozzáadásával](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Az **Eszköz** legördülő listában válassza ki az adáshoz használni kívánt videobemenetet. A többi beállítást hagyja békén, és kattintson az **OK**gombra. A bemeneti forrás hozzáadódik a **Források** panelhez, és a videobemeneti nézet megjelenik az **Előnézet** területen.

   ![AZ OBS kamera beállításai](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Hangforrás hozzáadása

1. A **Források** panelen kattintson a **hozzáadás** ikonra egy új forráseszköz kiválasztásához. Megnyílik a Forráseszköz menü.

1. Válassza a **forráseszköz menüjének Hangbemenet-rögzítés parancsát.** Megnyílik **a Forrás létrehozása/kiválasztása** menü.

   ![OBS források menü hangeszközzel kiválasztva](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Jelölje be a **Meglévő** választóhozzáadása gombot, majd kattintson **az OK gombra.** Megnyílik **a Tulajdonságok a hangbevitel rögzítéséhez** menüben.

   ![AZ OBS hangforrása a meglévők hozzáadása beállítással ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Az **Eszköz** legördülő listából válassza ki az adáshoz használni kívánt hangrögzítő eszközt. Hagyja a többi beállítást egyedül most, és kattintson az OK gombra. A hangrögzítő eszköz hozzáadódik az audiokeverő panelhez.

   ![AZ OBS hangeszköz kiválasztása legördülő lista](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Streamelés beállítása az OBS-ben

A következő eljárásban a böngészőben visszatér az Azure Media Services szolgáltatáshoz, és a bemeneti URL-cím másolásához adja meg a kimeneti beállításokat:

1. A portál Azure Media Services-oldalán válassza az **Indítás** lehetőséget az élő közvetítési esemény elindításához. (A számlázás most kezdődik.)

   ![Start ikon](media/live-events-obs-quickstart/start.png)
1. Állítsa az **RTMP** kapcsolót **RTMPS-re.**
1. A **Beviteli URL-cím mezőben** másolja az URL-címet a vágólapra.

   ![Bemeneti URL](media/live-events-obs-quickstart/input-url.png)

1. Váltson át az OBS alkalmazásra.

1. Kattintson a **Beállítások** gombra a **Vezérlők** panelen. Megnyílik a Beállítások beállítások.

   ![Az OBS vezérlők panel kijelölt beállításokkal](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Válassza a Beállítások menü **Adatfolyam** **parancsát.**

1. A **Szolgáltatás** legördülő listában válassza az Összes megjelenítése, majd **az Egyéni...** lehetőséget.

1. A **Kiszolgáló** mezőbe illessze be a vágólapra másolt RTMPS-URL-címet.

1. Írjon be valamit az **Adatfolyam kulcsmezőbe.**  Nem igazán számít, hogy mi az, de kell, hogy legyen értéke.

    ![AZ OBS-adatfolyam beállításai](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Válassza a Beállítások menü **Kimenet** **parancsát.**

1. Írja be a *2* értéket a **Kulcsképkocka-intervallum** mezőbe. Ezzel a töredék hossza 2 másodpercre van állítva. Alacsonyabb késés élő szállítás esetén használja az 1 másodperces értéket.

1. NEM KÖTELEZŐ: Állítsa a **CPU-használati készletet** *nagyongyorsra,* ha alacsony feldolgozási teljesítményt használó számítógépet használ. Szükség esetén a kbps-t is beállíthatja valami alacsonyabbra, ha nemkívánatos hálózati feltételek vannak.

   ![AZ OBS kimeneti beállításai](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Hagyja a többi beállítást véletlenül, és kattintson az **OK gombra.**

### <a name="start-streaming"></a>A streamelés megkezdése

1. A **Vezérlők** panelen kattintson a **Streamelés megkezdése gombra.**

    ![Az OBS elindítja a streamelésgombot](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Váltson át az Azure Media Services Live eseményképernyőjére a böngészőben, és kattintson a **Lejátszó újratöltése** hivatkozásra. Most már látnia kell a streamet az Előnézet lejátszóban.

## <a name="set-up-outputs"></a>Kimenetek beállítása

Ez a rész beállítja a kimeneteket, és lehetővé teszi, hogy mentse a felvételt az élő közvetítés.  

> [!NOTE]
> Ahhoz, hogy ezt a kimenetet streamelje, a streamelési végpontnak futnia kell. Tekintse meg a későbbi [Az alapértelmezett streamelési végpont szakasz futtatása.](#run-the-default-streaming-endpoint)

1. Válassza a **Kimenetek létrehozása hivatkozást** a **Kimenetek** videómegjelenítő alatt.
1. Ha szeretné, a **Név** mezőben a kimenet nevét valami felhasználóbarátabbra szerkesztheti, hogy később könnyen megtalálhassa.

   ![Kimenet neve mező](media/live-events-wirecast-quickstart/output-name.png)
1. Egyelőre hagyd békén a többi dobozt.
1. Válassza a **Tovább lehetőséget** a streamelési lokátor hozzáadásához.
1. Ha szeretné, módosítsa a lokátor nevét valami felhasználóbarátabbra.

   ![Lokátor neve mező](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Hagyja minden mást ezen a képernyőn egyedül most.
1. Kattintson a **Létrehozás** gombra.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>A kimeneti közvetítés lejátszása az Azure Media Player használatával

1. Másolja a streamelési URL-címet a **Kimeneti** videolejátszó alá.
1. Webböngészőben nyissa meg az [Azure Media Player bemutatóját.](https://ampdemo.azureedge.net/azuremediaplayer.html)
1. Illessze be a streamelési URL-címet az Azure Media Player **URL-címmezőjébe.**
1. Válassza a **Lejátszó frissítése** gombot.
1. Az élő közvetítés megtekintéséhez válassza a Videó lejátszásikonját. **Play**

## <a name="stop-the-broadcast"></a>Az adás leállítása

Ha úgy gondolja, hogy elegendő tartalmat streamelt, állítsa le az adást.

1. A portálon válassza a **Stop (Leállítás)** lehetőséget.

1. Az OBS-ben jelölje ki az **Adatfolyam leállítása** gombot a **Vezérlők** panelen. Ez a lépés leállítja az OBS-ből származó adást.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Az igény szerinti kimenet lejátszása az Azure Media Player használatával

A létrehozott kimenet már elérhető az igény szerinti streameléshez, amíg a streamelési végpont fut.

1. Nyissa meg a Media Services listáját, és válassza **az Eszközök**lehetőséget.
1. Keresse meg a korábban létrehozott eseménykimenetet, és válassza ki az eszközre mutató hivatkozást. Megnyílik az eszközkimeneti lap.
1. Másolja a streamelési URL-címet az eszköz videolejátszója alá.
1. Térjen vissza az Azure Media Player hez a böngészőben, és illessze be a streamelési URL-címet az URL-címmezőbe.
1. Válassza **a Player frissítése**lehetőséget.
1. Az igény szerinti eszköz megtekintéséhez válassza a videó **Lejátszás** ikonját.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!IMPORTANT]
> Állítsa le a szertartást! Miután elvégezte a rövid útmutató lépéseit, győződjön meg róla, hogy leállítja az élő eseményt és a streamelési végpontot, vagy a továbbiakban számlázzuk ki a további futási időt. Az élő esemény leállításához olvassa el [a Broadcast](#stop-the-broadcast) eljárás leállítása 2.

A streamelési végpont leállítása:

1. A Media Services listából válassza a **Streamelési végpontok**lehetőséget.
2. Válassza ki a korábban elindított alapértelmezett streamelési végpontot. Ez a lépés megnyitja a végpont lapját.
3. Válassza **a Leállítás**lehetőséget.

> [!TIP]
> Ha nem szeretné megtartani az eseményből származó eszközöket, törölje őket, hogy ne kelljen fizetnie a tárolásért.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Élő események és élő kimenetek a Media Services szolgáltatásban](./live-events-outputs-concept.md)
