---
title: Azure Media Services élő stream létrehozása a portál és a Wirecast segítségével
description: Útmutató Azure Media Service Live Stream létrehozásához
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927625"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Azure Media Services élő stream létrehozása a portál és a Wirecast segítségével

Ez az Első lépések útmutató feltételezi, hogy rendelkezik Azure-előfizetéssel, és létrehozott egy Azure Media Services fiókot.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Nyissa meg a webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

Ebben a rövid útmutatóban a következőket fogjuk lefedni:

- Helyszíni kódoló beállítása a Wirecast ingyenes próbaidőszakával
- Élő stream beállítása
- Élő stream-kimenetek beállítása
- Alapértelmezett folyamatos átviteli végpont futtatása
- Az élő stream és az igény szerinti kimenet megtekintése a Azure Media Player használatával

A dolgok egyszerű megtartása érdekében a Wirecast, a felhőalapú kódolás és az RTMP használatával Azure Media Services kódolási beállításkészletet fogunk használni.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Helyszíni kódoló beállítása a Wirecast-mel

1. Az operációs rendszer Wirecast letöltése és telepítése https://www.telestream.net
1. Indítsa el az alkalmazást, és használja kedvenc e-mail-címét a termék regisztrálásához.  Az alkalmazás nyitva tartása.
1. Egy e-mailt fog kapni, amely arra kéri, hogy ellenőrizze az e-mail-címét, majd az alkalmazás elindítja az ingyenes próbaverziót.
1. AJÁNLOTT: Tekintse meg a videós oktatóanyagot az alkalmazás megnyitása képernyőn.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Azure Media Services élő stream beállítása

1. Ha a portálon belül navigált a Azure Media Services-fiókhoz, válassza az **élő közvetítés** lehetőséget a Media Services listában.
1. Az új élő közvetítési esemény létrehozásához kattintson az **élő esemény hozzáadása** lehetőségre.
1. Adja meg az új esemény nevét, például *TestLiveEvent* az élő esemény **neve** mezőben.
1. A **Leírás** mezőben adja meg az esemény opcionális leírását.
1. Válassza ki az **áteresztő – nincs felhőalapú kódolás** választógombot.
1. Válassza az **RTMP** -választógombot. 
1. Győződjön meg arról, hogy a **nem** választógomb van kiválasztva az élő esemény indításához, hogy elkerülje az élő esemény számlázását, mielőtt készen lenne.  (A számlázás az élő esemény elindításakor kezdődik.)
1. A beállítások áttekintéséhez kattintson a **felülvizsgálat + létrehozás** gombra.
1. Az élő esemény létrehozásához kattintson a **Létrehozás** gombra. Ekkor a rendszer visszaadja az élő esemény listázási nézetét.
1. Kattintson az imént létrehozott **élő eseményre mutató hivatkozásra** . Figyelje meg, hogy az esemény leáll.
1. Tartsa meg a lapot a böngészőben.  Később ismét vissza fogunk térni.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Élő stream beállítása a Wirecast Studióval

1. Feltételezve, hogy a Wirecast alkalmazás továbbra is nyitva van, válassza a főmenü **Üres dokumentum létrehozása** elemét, majd kattintson a **Folytatás**gombra.
1. Vigye az egérmutatót az Wirecast rétegek területén lévő első réteg fölé.  Kattintson a megjelenő **Hozzáadás** ikonra, és válassza ki a továbbítani kívánt videó bemenetét.  Ekkor megnyílik a Master Layer 1 párbeszédablak.
1. Válassza a **videó rögzítése** lehetőséget a menüből, majd válassza ki a használni kívánt kamerát. A kamera nézete megjelenik az előnézet területén.
1. Vigye a kurzort a Wirecast rétegek területén található második réteg fölé. Kattintson a megjelenő **Hozzáadás** ikonra, és válassza ki az adatfolyamként használni kívánt hangbemenetet.  Ekkor megnyílik a Master Layer 2 párbeszédablak.
1. Válassza a menüben a **hangrögzítés** lehetőséget, majd válassza ki a használni kívánt hangbemenetet. 
1. A főmenüben válassza a **kimeneti beállítások**lehetőséget.  Ekkor megjelenik a kimenet párbeszédpanel.
1. Válassza ki **Azure Media Services** a kimenet legördülő menüből.  A Azure Media Services kimeneti beállítása automatikusan kitölti a kimeneti beállítások *többségét* .
1. A következő szakaszban visszatérhet Azure Media Services a böngészőben, hogy a *bemeneti URL-címet* másolja a kimeneti beállításokba való belépéshez.

### <a name="copy-and-paste-the-input-url"></a>A bemeneti URL-cím másolása és beillesztése

1. A portál Azure Media Services oldalán kattintson a **Start** gombra az élő stream esemény elindításához. (A számlázás most kezdődik.)
2. Kattintson a **biztonságos/nem biztonságos** váltógomb beállításra, hogy **ne legyen biztonságos**.  Ez a protokoll a RTMP helyett RTMP-re lesz állítva.
3. Másolja a **bemeneti URL-címet** a vágólapra.
4. Váltson a Wirecast alkalmazásra, és illessze be a **bemeneti URL** -címet a kimeneti beállítások **cím** mezőjébe.
5. Kattintson az **OK**gombra.

## <a name="setting-up-outputs"></a>Kimenetek beállítása

Ez a rész beállítja a kimeneteket, és lehetővé teszi az élő stream rögzítésének mentését.  

> [!NOTE]
> A kimenet továbbításához a streaming végpontjának futnia kell.  Lásd alább az alapértelmezett adatfolyam-végpont futtatása szakaszt.

1. Kattintson a kimenetek **létrehozása** hivatkozásra a kimenetek videó megjelenítője alatt.
1. Ha szeretné, a **név** mezőben adja meg a kimenet nevét, hogy még több felhasználóbarát legyen, így később könnyen megtalálhatja.
1. Az összes többi mezőt már csak most hagyja.
1. Kattintson a **tovább** a folyamatos átviteli lokátor hozzáadása lehetőségre.
1. Ha szeretné, módosítsa a lokátor nevét egy másik felhasználó barátra.
1. A képernyő minden más elemének kihagyása egyelőre.
1. Kattintson a  **Create** (Létrehozás) gombra.

## <a name="starting-the-broadcast"></a>A szórás elindítása

1. A Wirecast területen válassza a **kimenet > a szórás indítása/leállítása > start Azure Media Services: Azure Media Services** lehetőséget a főmenüben.  Ha az adatfolyamot elküldték az élő eseményre, a Wirecast élő ablaka megjelenik az élő esemény videójában a Azure Media Services élő esemény lapján.

1. Kattintson az előnézet ablak alatti **Ugrás** gombra a Wirecast-rétegekhez kiválasztott videó és hang szórásának megkezdéséhez.

> [!TIP]
> Ha hiba merül fel, próbálja meg újra betölteni a lejátszót a lejátszón a lejátszó újratöltésére szolgáló hivatkozásra kattintva.

## <a name="running-the-default-streaming-endpoint"></a>Az alapértelmezett folyamatos átviteli végpont futtatása

1. Ellenőrizze, hogy fut-e a folyamatos átviteli végpont a Media Services listában a **folyamatos átviteli végpontok** lehetőség kiválasztásával. Ekkor megnyílik a folyamatos átviteli végpontok oldal.
1. Ha az alapértelmezett folyamatos átviteli végpont állapota leáll, kattintson az **alapértelmezett** adatfolyam-végpontra. Ekkor megnyílik a végpont lapja.
1. Kattintson a **Start** gombra.  Ekkor elindul a folyamatos átviteli végpont.

## <a name="play-the-output-broadcast-with-azure-media-player"></a>A kimeneti szórás lejátszása Azure Media Player

1. Másolja a **streaming URL-címet** a kimeneti videolejátszó alá. 
1. A böngészőben nyissa meg a bemutató Azure Media Player https://ampdemo.azureedge.net/azuremediaplayer.html
1. Illessze be a **streaming URL-címet** a Azure Media Player URL-mezőjébe.
1. Kattintson a **lejátszó frissítése** gombra.
1. Kattintson a videó **Lejátszás** ikonjára az élő stream megtekintéséhez.

## <a name="stopping-the-broadcast"></a>A szórás leállítása

Ha úgy gondolja, hogy elég tartalmat továbbított, állítsa le a szórást.

1. A Wirecast-ben kattintson a **szórás** gombra.  Ezzel leállítja a szórást a Wirecast.
1. A portálon kattintson a **Leállítás**gombra. Megjelenik egy figyelmeztető üzenet arról, hogy az élő stream leáll, de a kimenet már igény szerinti eszköz lesz.
1. Kattintson a **Leállítás** gombra a figyelmeztető üzenetben. A Azure Media Player mostantól hibaüzenetet jelenít meg, mivel az élő stream már nem érhető el.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Az igény szerinti kimenet lejátszása a Azure Media Player

A létrehozott kimenet elérhető az igény szerinti folyamatos átvitelhez, ha a streaming végpontja fut.

1. Navigáljon a Media Services listára, és válassza az **eszközök**elemet.
1. Keresse meg a korábban létrehozott esemény kimenetét, és kattintson az **eszközre mutató hivatkozásra**. Ekkor megnyílik az eszköz kimenete oldal.
1. Másolja a **streaming URL-címet** az adott eszközhöz tartozó videolejátszó alá.
1. Térjen vissza a Azure Media Player a böngészőben, és illessze be a **streaming URL-címet** a Azure Media Player URL-mezőjébe.
1. Kattintson a **lejátszó frissítése**gombra.
1. Kattintson a videó **Lejátszás** ikonjára az igény szerinti eszköz megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

  > [!IMPORTANT]
  > Állítsa le a szolgáltatásokat! Ha elvégezte az ebben a rövid útmutatóban szereplő lépéseket, érdemes lehet leállítani az élő eseményt és a folyamatos átviteli végpontot, vagy ha továbbra is a futása alatt marad. Az élő esemény leállításához tekintse meg a szórás leállítása, 2. és 3. lépéseit.

### <a name="stopping-the-streaming-endpoint"></a>A folyamatos átviteli végpont leállítása

1. A Media Services listában válassza a **folyamatos átviteli végpontok**lehetőséget.
2. Kattintson a korábban elindított **alapértelmezett** adatfolyam-végpontra. Ekkor megnyílik a végpont lapja.
3. Kattintson a **Leállítás**gombra.  Ezzel leállítja a folyamatos átviteli végpontot.

>[!TIP]
>Ha nem szeretné megtartani az adott eseményből származó eszközöket, ne felejtse el törölni őket, hogy elkerülje a tárolás díját.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Mi a cikk a következő sorban](./live-events-outputs-concept.md)
