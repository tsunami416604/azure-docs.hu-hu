---
title: Az Azure Media Services élő közvetítése
description: Megtudhatja, hogyan hozhat létre élő közvetítést az Azure Media Services élő közvetítése a portál és a Wirecast használatával
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984997"
---
# <a name="create-an-azure-media-services-live-stream"></a>Az Azure Media Services élő közvetítése

Ez a rövid útmutató segít létrehozni egy Azure Media Services élő közvetítés az Azure Portalon és a Telestream Wirecast használatával. Feltételezi, hogy Rendelkezik egy Azure-előfizetéssel, és létrehozott egy Media Services-fiókot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőt, és nyissa meg a [Microsoft Azure portált.](https://portal.azure.com/) Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

Ebben a rövid útmutatóban a következőket tjük ki:

- Helyszíni kódoló beállítása a Telestream Wirecast ingyenes próbaverziójával.
- Élő közvetítés beállítása.
- Élő közvetítés kimenetének beállítása.
- Alapértelmezett streamelési végpont futtatása.
- Az Azure Media Player használatával megtekintheti az élő közvetítést és az igény szerinti kimenetet.

Az egyszerű ség érdekében az Azure Media Services kódolási készletét használjuk a Wirecastban, az átadó felhőkódolásban és az RTMP-ben.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Helyszíni kódoló beállítása a Wirecast használatával

1. Töltse le és telepítse a Wirecast-ot az operációs rendszeréhez a [Telestream weboldalán.](https://www.telestream.net)
1. Indítsa el az alkalmazást, és használja kedvenc e-mail címét a termék regisztrálásához. Tartsa nyitva az alkalmazást.
1. A kapott e-mailben ellenőrizze az e-mail címét. Ezután az alkalmazás elindítja az ingyenes próbaverziót.
1. Ajánlott: Nézze meg a videót bemutató a nyitó alkalmazás képernyőjén.

## <a name="set-up-an-azure-media-services-live-stream"></a>Az Azure Media Services élő közvetítése

1. Nyissa meg az Azure Media Services-fiókot a portálon belül, majd válassza az **Élő közvetítés lehetőséget** a Media Services-listáról. **Media Services**

   ![Élő közvetítési hivatkozás](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Új élő közvetítési esemény létrehozásához válassza **az Élő esemény hozzáadása** lehetőséget.

   ![Élő esemény hozzáadása ikon](media/live-events-wirecast-quickstart/add-live-event.png)
1. Adja meg az új esemény nevét, például *a TestLiveEvent*eseményt az **Élő esemény neve** mezőben.

   ![Élő esemény neve mező](media/live-events-wirecast-quickstart/live-event-name.png)
1. Adja meg az esemény nem kötelező leírását a **Leírás** mezőben.
1. Válassza az **Áthaladás – nincs felhőkódolási** lehetőség.

   ![Felhőalapú kódolási beállítás](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Válassza az **RTMP** lehetőséget.
1. Győződjön meg arról, hogy a **Nincs** lehetőség van kiválasztva az **Élő esemény indítása**beállításhoz, hogy elkerülje az élő esemény számlázását, mielőtt készen állna. (A számlázás az élő esemény indításakor kezdődik.)

   ![Élő esemény indítása lehetőség](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. A beállítások áttekintéséhez válassza a **Véleményezés + létrehozás** gombot.
1. Az élő esemény létrehozásához kattintson a **Létrehozás** gombra. Ezután visszatérsz az élő esemény hirdetésére.
1. Válaszd ki az imént létrehozott élő eseményre mutató hivatkozást. Figyelje meg, hogy az esemény leállt.
1. Tartsa nyitva ezt a lapot a böngészőben. Majd később visszatérünk rá.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Élő közvetítés beállítása a Wirecast Studio használatával

1. A Wirecast alkalmazásban válassza a főmenü **Üres dokumentum létrehozása parancsát,** majd válassza a **Folytatás parancsot.**

   ![Wirecast kezdőképernyő](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Vigye az egérmutatót a **Wirecast rétegek** területének első rétege fölé.  Válassza a megjelenő **Hozzáadás** ikont, és jelölje ki az adatfolyamként bevitt videobemenetet.

   ![Wirecast hozzáadás ikon](media/live-events-wirecast-quickstart/add-icon.png)

   Megnyílik **a Mesterréteg 1** párbeszédpanel.
1. Válassza a menü **Videorögzítés parancsát,** majd jelölje ki a használni kívánt fényképezőgépet.

   ![Előnézeti terület a videorögzítéshez](media/live-events-wirecast-quickstart/video-shot-selection.png)

   A kamera nézete megjelenik az előnézeti területen.
1. Vigye az egérmutatót a **Drótlevetítés rétegek** területén a második réteg fölé. Jelölje ki a megjelenő **Hozzáadás** ikont, és jelölje ki az adatfolyamként streamelni kívánt hangbemenetet. Megnyílik **a Mesterréteg 2** párbeszédpanel.
1. Válassza a menü **Hangrögzítés parancsát,** majd válassza ki a használni kívánt hangbemenetet.

   ![Bemenetek a hangrögzítéshez](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. A főmenüben válassza a **Kimeneti beállítások lehetőséget.** Megjelenik **a Kimeneti cél kiválasztása** párbeszédpanel.
1. Válassza ki az **Azure Media Services** a **Cél** legördülő listából. Az Azure Media Services kimeneti beállítása automatikusan feltölti a *kimeneti* beállítások nagy részét.

   ![Wirecast kimeneti beállítások képernyő](media/live-events-wirecast-quickstart/azure-media-services.png)


A következő eljárásban a böngészőben visszatér az Azure Media Services szolgáltatáshoz, és a bemeneti URL-cím másolásához adja meg a kimeneti beállításokat:

1. A portál Azure Media Services-oldalán válassza az **Indítás** lehetőséget az élő közvetítési esemény elindításához. (A számlázás most kezdődik.)

   ![Start ikon](media/live-events-wirecast-quickstart/start.png)
2. Állítsa a **Secure/Not Secure** kapcsolót **Nem biztonságosra.** Ez a lépés az RTMPS helyett RTMP protokollra állítja be a protokollt.
3. A **Beviteli URL-cím mezőben** másolja az URL-címet a vágólapra.

   ![Bemeneti URL](media/live-events-wirecast-quickstart/input-url.png)
4. Váltson át a Wirecast alkalmazásra, és illessze be a **bemeneti URL-t** a kimeneti beállítások **Cím** mezőjébe.

   ![Wirecast bemeneti URL-címe](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Válassza **az OK gombot.**

## <a name="set-up-outputs"></a>Kimenetek beállítása

Ez a rész beállítja a kimeneteket, és lehetővé teszi, hogy mentse a felvételt az élő közvetítés.  

> [!NOTE]
> Ahhoz, hogy ezt a kimenetet streamelje, a streamelési végpontnak futnia kell. Tekintse meg a későbbi [Az alapértelmezett streamelési végpont szakasz futtatása.](#run-the-default-streaming-endpoint)

1. Válassza a **Kimenetek létrehozása hivatkozást** a **Kimenetek** videómegjelenítő alatt.
1. Ha szeretné, szerkesztheti a kimenet nevét a **Név** mezőben valami felhasználóbarátabbra, hogy később könnyen megtalálhassa.
   
   ![Kimenet neve mező](media/live-events-wirecast-quickstart/output-name.png)
1. Egyelőre hagyd békén a többi dobozt.
1. Válassza a **Tovább lehetőséget** a streamelési lokátor hozzáadásához.
1. Ha szeretné, módosítsa a lokátor nevét valami felhasználóbarátabbra.
   
   ![Lokátor neve mező](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Hagyja minden mást ezen a képernyőn egyedül most.
1. Kattintson a **Létrehozás** gombra.

## <a name="start-the-broadcast"></a>Az adás indítása

1. A Wirecast, válassza **kimeneti** > **start / leállítási broadcasting** > Start Azure Media**Services: Azure Media Services** a főmenüből.

   ![Szórás menüelemeinek indítása](media/live-events-wirecast-quickstart/start-broadcast.png)

   Miután az adatfolyamot elküldték az élő eseményre, a Wirecast **Élő** ablaka megjelenik a videólejátszóban az Azure Media Services élő eseményoldalán.

1. Az előnézeti ablak ban kattintson az **Ugrás** gombra a Wirecast-rétegekhez kiválasztott videó és hang sugárzásának megkezdéséhez.

   ![Wirecast Go gomb](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Ha hiba történt, próbálja meg újratölteni a lejátszót a **lejátszó újratöltése** linkre kattintva.

## <a name="run-the-default-streaming-endpoint"></a>Az alapértelmezett streamelési végpont futtatása

1. Válassza a **Streamelési végpontok lehetőséget** a Media Services listában.

   ![Végpontok streamelése menüelem](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Ha az alapértelmezett streamelési végpont állapota le van állítva, jelölje ki. Ez a lépés az adott végpont lapjára viszi.
1. Válassza az **Indítás** elemet.
   
   ![Start gomb a streamelési végponthoz](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>A kimeneti közvetítés lejátszása az Azure Media Player használatával

1. Másolja a streamelési URL-címet a **Kimeneti** videolejátszó alá.
1. Webböngészőben nyissa meg az [Azure Media Player bemutatóját.](https://ampdemo.azureedge.net/azuremediaplayer.html)
1. Illessze be a streamelési URL-címet az Azure Media Player **URL-címmezőjébe.**
1. Válassza a **Lejátszó frissítése** gombot.
1. Az élő közvetítés megtekintéséhez válassza a Videó lejátszásikonját. **Play**

## <a name="stop-the-broadcast"></a>Az adás leállítása

Ha úgy gondolja, hogy elegendő tartalmat streamelt, állítsa le az adást.

1. A Wirecast alkalmazásban válassza a **Közvetítés** gombot. Ez a lépés leállítja az adást a Wirecast-ból.
1. A portálon válassza a **Stop (Leállítás)** lehetőséget. Ezután kap egy figyelmeztető üzenetet, hogy az élő közvetítés leáll, de a kimenet most lesz egy on-demand eszköz.
1. A figyelmeztető üzenetben válassza a **Leállítás** lehetőséget. Az Azure Media Player most egy hibát jelenít meg, mert az élő közvetítés már nem érhető el.

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
