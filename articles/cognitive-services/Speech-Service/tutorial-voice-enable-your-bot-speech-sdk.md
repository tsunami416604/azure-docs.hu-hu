---
title: 'Oktatóanyag: A hangok lehetővé teszik a robot használatát a Beszéd SDK - Beszédszolgáltatás használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban hozzon létre egy Echo Bot-Framework használatával, telepítse az Azure-ba, és regisztrálja a Bot-Framework direct line beszédcsatornával. Ezután konfigurálegy windowsos mintaügyfél-alkalmazást, amely lehetővé teszi, hogy beszéljen a robottal, és hallja, amint válaszol Önnek.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 3c2d74eb7e46d9909d87a7ccadadd6129a3d48d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397899"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Oktatóanyag: A hangfunkció engedélyezése a beszédfelismerési SDK használatával

Most már használhatja a beszédszolgáltatás erejét, hogy egyszerűen engedélyezze a csevegőrobotot.

Ebben az oktatóanyagban hozzon létre egy Echo Bot-Framework használatával, telepítse az Azure-ba, és regisztrálja a Bot-Framework direct line beszédcsatornával. Ezután konfigurálegy windowsos mintaügyfél-alkalmazást, amely lehetővé teszi, hogy beszéljen a robottal, és hallja, amint válaszol Önnek.

Ez az oktatóanyag olyan fejlesztők számára készült, akik csak most kezdik meg az utazást az Azure- vagy bot-keretrobotokkal, a direct line beszéddel vagy a beszédbeszéd-SDK-val, és gyorsan szeretnének egy korlátozott kódolású működő rendszert építeni. Nincs szükség ezeknek a szolgáltatásoknak a tapasztalataira vagy ismeretére.

A gyakorlat végén olyan rendszert hoz létre, amely a következőképpen fog működni:

1. A mintaügyfél-alkalmazás úgy van konfigurálva, hogy közvetlen vonalbeli beszédcsatornához és az Echo Bothoz csatlakozzon
1. A hang rögzítésre kerül az alapértelmezett mikrofon gombnyomással (vagy folyamatosan rögzítik, ha egyéni kulcsszó aktiválva van)
1. Opcionálisan egyéni kulcsszófelismerés történik, a hangstreaming a felhőbe
1. A beszédfelismerési SDK használatával az alkalmazás közvetlen vonali beszédcsatornához csatlakozik, és hangátvitelt
1. Opcionálisan nagyobb pontosságú kulcsszóellenőrzés történik a szolgáltatáson
1. A hang átkerül a beszédfelismerő szolgáltatásba, és átírja a
1. A felismert szöveg botkerettevékenységként kerül átadásra az Echo-Bot nak 
1. A válaszszöveget a Text-to-Speech (TTS) szolgáltatás hanggá alakítja, és lejátszásra visszatovábbítja az ügyfélalkalmazásba

![diagramcímke](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "A beszédcsatorna áramlása")

> [!NOTE]
> Az oktatóanyag lépései nem igényelnek fizetős szolgáltatást. Új Azure-felhasználóként az ingyenes Azure próba-előfizetésből és a beszédfelismerési szolgáltatás ingyenes szintjéből származó krediteket használhatja az oktatóanyag befejezéséhez.

Itt van, amit ez a bemutató kiterjed:
> [!div class="checklist"]
> * Új Azure-erőforrások létrehozása
> * Az Echo Bot-minta létrehozása, tesztelése és üzembe helyezése egy Azure App Service-ben
> * A robot regisztrálása direct line beszédcsatornával
> * A Windows Voice Assistant-ügyfél felépítése és futtatása az Echo Botkal való interakcióhoz
> * Egyéni kulcsszó-aktiválás hozzáadása
> * Ismerje meg az elismert és beszélt beszéd nyelvének megváltoztatását

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következőkre lesz szüksége:

- Windows 10-es pc működő mikrofonnal és hangszórókkal (vagy fejhallgatóval)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) vagy újabb
- [.NET Core SDK](https://dotnet.microsoft.com/download) 2.1-es vagy újabb verzió
- Egy Azure-fiók. [Iratkozzon fel ingyen](https://azure.microsoft.com/free/ai/).
- [GitHub-fiók](https://github.com/)
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az ebben az oktatóanyagban létrehozott ügyfélalkalmazás néhány Azure-szolgáltatást használ. A robot tól érkező válaszok oda-vissza idejének csökkentése érdekében győződjön meg arról, hogy ezek a szolgáltatások ugyanabban az Azure-régióban találhatók. Ebben a szakaszban hozzon létre egy erőforráscsoportot az **USA nyugati régiójában.** Ez az erőforráscsoport a Bot-Framework, a Direct Line Speech channel és a Speech service egyedi erőforrásainak létrehozásakor lesz használva.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Erőforráscsoport létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. A rendszer kérni fogja, hogy adjon meg néhány információt:
   * Állítsa **az előfizetést** **ingyenes próbaverzióra** (meglévő előfizetést is használhat).
   * Adja meg az **Erőforrás csoport**nevét. Javasoljuk **SpeechEchoBotTutorial-ResourceGroup**.
   * A Régió legördülő **menüben** válassza az **USA nyugati régiója**lehetőséget.
1. Kattintson az **Áttekintés és létrehozás** elemre. Meg kell jelennie egy banner, hogy olvassa **érvényesítés telt el**.
1. Kattintson **a Létrehozás gombra.** Az erőforráscsoport létrehozása eltarthat néhány percig.
1. Az oktatóanyag későbbi részében létrehozott erőforrásokhoz ugyanúgy célszerű ezt az erőforráscsoportot az irányítópultra rögzíteni a könnyű hozzáférés érdekében. Ha rögzíteni szeretné ezt az erőforráscsoportot, kattintson az irányítópult jobb felső részén található pin ikonra.

### <a name="choosing-an-azure-region"></a>Azure-régió kiválasztása

Ha egy másik régiót szeretne használni az oktatóanyaghoz, ezek a tényezők korlátozhatják a lehetőségeket:

* Győződjön meg arról, hogy [egy támogatott Azure-régiót](regions.md#voice-assistants)használ.
* A Direct Line Speech csatorna a szövegfelolvasó szolgáltatást használja, amely szabványos és neurális hangokkal rendelkezik. A neurális hangok [meghatározott Azure-régiókra korlátozódnak.](regions.md#standard-and-neural-voices)
* Az ingyenes próbakulcsok egy adott régióra korlátozódhatnak.

A régiókról további információt az [Azure-helyek című témakörben talál.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="create-resources"></a>Erőforrások létrehozása

Most, hogy egy támogatott régióban rendelkezik egy erőforráscsoporttal, a következő lépés az, hogy hozzon létre minden egyes szolgáltatás, amely az oktatóanyagban használni fog.

### <a name="create-a-speech-service-resource"></a>Beszédfelismerési szolgáltatás-erőforrás létrehozása

A Beszédfelismerési erőforrás létrehozásához kövesse az alábbi utasításokat:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Beszédfelismerési szolgáltatás-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. A rendszer kérni fogja, hogy adjon meg néhány információt:
   * Adjon nevet az **erőforrásnak.** Javasoljuk **SpeechEchoBotTutorial-Speech**
   * Előfizetés **esetén**győződjön meg arról, hogy az **ingyenes próbaverzió** ki van jelölve.
   * A **Hely lehetőséget**az USA nyugati helye területen válassza a **(USA) hely lehetőséget.**
   * A **Tarifacsomag esetében**válassza az **F0**lehetőséget. Ez az ingyenes szint.
   * **Az Erőforrás csoport**csoportban válassza a **SpeechEchoBotTutorial-ResourceGroup lehetőséget.**
5. Miután megadta az összes szükséges információt, kattintson a **Létrehozás gombra.** Az erőforrás létrehozása eltarthat néhány percig.
6. Az oktatóanyag későbbi részében előfizetési kulcsokra lesz szüksége ehhez a szolgáltatáshoz. Ezeket a kulcsokat bármikor elérheti az erőforrás **áttekintése** (Kulcsok kezelése) vagy **a kulcsok .**

Ezen a ponton ellenőrizze, hogy az erőforráscsoport **(SpeechEchoBotTutorial-ResourceGroup)** rendelkezik-e beszédfelismerési erőforrással:

| Név | Típus  | Hely |
|------|-------|----------|
| SpeechEchoBotBemutató-Beszéd | Cognitive Services | USA nyugati régiója |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

A következő lépés egy App Service-csomag létrehozása. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure App Service-csomag létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. A rendszer kérni fogja, hogy adjon meg néhány információt:
   * Állítsa **az előfizetést** **ingyenes próbaverzióra** (meglévő előfizetést is használhat).
   * **Az Erőforrás csoport**csoportban válassza a **SpeechEchoBotTutorial-ResourceGroup lehetőséget.**
   * Adjon nevet az **erőforrásnak.** Javasoljuk **SpeechEchoBotTutorial-AppServicePlan**
   * **Operációs rendszer esetén**válassza a **Windows**lehetőséget.
   * A **Régió**területen válassza **az USA nyugati régiója**lehetőséget.
   * A **tarifacsomag**esetében győződjön meg arról, hogy a **Standard S1** ki van jelölve. Ennek kell lennie az alapértelmezett értéknek. Ha nem, győződjön meg arról, hogy az **operációs rendszert** a fent leírt módon **windowsra** állította.
5. Kattintson az **Áttekintés és létrehozás** elemre. Meg kell jelennie egy banner, hogy olvassa **érvényesítés telt el**.
6. Kattintson **a Létrehozás gombra.** Az erőforráscsoport létrehozása eltarthat néhány percig.

Ezen a ponton ellenőrizze, hogy az erőforráscsoport **(SpeechEchoBotTutorial-ResourceGroup)** két erőforrással rendelkezik-e:

| Név | Típus  | Hely |
|------|-------|----------|
| SpeechEchoBottutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotBemutató-Beszéd | Cognitive Services | USA nyugati régiója |

## <a name="build-an-echo-bot"></a>Echo bot építése

Most, hogy létrehozott néhány erőforrást, építsünk egy robotot. Kezdjük az Echo Bot mintával, ami, ahogy a neve is mutatja, megismétli a szöveget, amit beírt válaszként. Ne aggódjon, a mintakód készen áll a módosítások nélküli használatra. Úgy van beállítva, hogy működjön együtt a Direct Line speech channel, amely akkor csatlakozik, miután üzembe helyezte a robot az Azure-ban.

> [!NOTE]
> Az alábbi utasítások, valamint az Echo Bottal kapcsolatos további információk a [minta GitHubon található README című](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)tájékoztatójában találhatók.

### <a name="run-the-bot-sample-on-your-machine"></a>Futtassa a robot mintát a gépen

1. Klónozza a mintatárolót:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Indítsa el a Visual Studiót.
3. Az eszköztáron válassza a**Project/Solution** > **fájlmegnyitása** > lehetőséget, és nyissa meg az Echo Bot projektmegoldást: **File**

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. A projekt betöltése után nyomja le az <kbd>F5 billentyűt</kbd> a projekt létrehozásához és futtatásához.
5. A böngészőnek el kell indulnia, és egy ehhez hasonló képernyő jelenik meg.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot fut localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Tesztelje a bot mintát a Bot Framework emulátor

A [Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) egy asztali alkalmazás, amely lehetővé teszi a robotfejlesztők számára, hogy teszteljék és debug a botok helyileg vagy távolról egy alagúton keresztül. Az emulátor támogatja a beírt szöveget bemenetként (nem hangként). A robot szöveggel válaszol. Kövesse ezeket a lépéseket a Bot Framework emulátor használatával tesztelje az Echo Bot helyileg futó, szövegbeviteli és szöveges kimenet. Miután üzembe helyeztük a robotot az Azure-ba, hangbemenettel és hangkimenettel teszteljük.

1. A [Bot Framework Emulátor](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 4.3.0-s vagy újabb verziójának telepítése
2. Indítsa el a Bot Framework emulátort, és nyissa meg a robotot:
   * **Fájl** -> **megnyitása Bot**.
3. Adja meg a robot URL-címét. Példa:

   ```
   http://localhost:3978/api/messages
   ```
   és nyomja meg a "Connect" gombot.
4. A bot azonnal üdvözölje Önt a "Hello and welcome!" üzenetet küldi vissza. Írjon be bármilyen szöveges üzenetet, és erősítse meg, hogy választ kap a robottól.
5. Így nézhet ki egy echo botpéldányokkal folytatott kommunikáció cseréje: [ ![bot-framework-emulátor](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot keretemor")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>A robot üzembe helyezése egy Azure App Service-ben

A következő lépés az Echo Bot üzembe helyezése az Azure-ba. A robot üzembe helyezésének több módja is van, de ebben az oktatóanyagban közvetlenül a Visual Studio-ból való közzétételre összpontosítunk.

> [!NOTE]
> Másik lehetőségként üzembe helyezhet egy robotot az [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) és a [telepítési sablonok](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)használatával.

1. A Visual Studio-ból nyissa meg a Direct Line Speech channel-hez konfigurált Echo Botot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. A **Megoldáskezelőben**kattintson a jobb gombbal az **EchoBot** projektre, és válassza **a Közzététel...**
1. Megnyílik egy új ablak, melynek **címe: Közzétételi cél kiválasztása.**
1. Válassza az **App Service** elemet az **Azure-szolgáltatások** navigációjában, válassza az **Új létrehozása**lehetőséget, majd kattintson a Profil **létrehozása gombra.**
1. Amikor megjelenik az **App Service létrehozása** ablak:
   * Kattintson **a Fiók hozzáadása**gombra, és jelentkezzen be az Azure-fiók hitelesítő adataival. Ha már be van jelentkezve, válassza ki a kívánt fiókot a legördülő listából.
   * Az **alkalmazás neve**, meg kell adnia egy globálisan egyedi nevet a bot. Ez a név egy egyedi robot URL-cím ének létrehozására szolgál. A program a dátumot és az időt is tartalmazza az alapértelmezett értéket (például: "EchoBot20190805125647"). Az oktatóanyag alapértelmezett nevét használhatja.
   * **Előfizetés esetén**állítsa **ingyenes próbaverzióra**
   * **Erőforráscsoport**esetén válassza a **SpeechEchoBotTutorial-ResourceGroup** lehetőséget.
   * **A Tárhelycsomag**esetén válassza a **SpeechEchoBotTutorial-AppServicePlan lehetőséget**
   * Az **Application Insights**esetében hagyja meg a Nincs **(Nincs)**
1. Kattintson a **Létrehozás gombra.**
1. A Visual Studio sikerességi üzenetét a következőképpen kell látnia:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Az alapértelmezett böngészőnek meg kell nyitnia és meg kell jelenítenie egy oldalt, amely így szól: "A robot készen áll!".
1. Ezen a ponton ellenőrizze az erőforráscsoport **SpeechEchoBotTutorial-ResourceGroup** az Azure Portalon, és ellenőrizze, hogy három erőforrás:

| Név | Típus  | Hely |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA nyugati régiója |
| SpeechEchoBottutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotBemutató-Beszéd | Cognitive Services | USA nyugati régiója |

## <a name="enable-web-sockets"></a>Webszoftver-szoftvercsatornák engedélyezése

Egy kis konfigurációs módosítást kell eszközölnie, hogy a robot webszoftvercsatornák használatával kommunikáljon a közvetlen vonalbeszédcsatornával. A webes szoftvercsatornák engedélyezéséhez kövesse az alábbi lépéseket:

1. Keresse meg az [Azure Portalon,](https://portal.azure.com)és keresse meg az App Service. Az erőforrást az **EchoBot20190805125647-hez** (az egyedi alkalmazásnévhez) hasonló nak kell nevezni.
2. Az **Azure-szolgáltatások** navigációjában a **Beállítások**csoportban kattintson a **Konfiguráció gombra.**
3. Válassza az **Általános beállítások** lapot.
4. Keresse meg a **webszoftvercsatornák kapcsolóját,** és állítsa **be a Be beállításra.**
5. Kattintson a **Mentés** gombra.

> [!TIP]
> Az Azure App Service-lap tetején található vezérlők használatával leállíthatja vagy újraindíthatja a szolgáltatást. Ez a hibaelhárítás során hasznos lehet.

## <a name="create-a-channel-registration"></a>Csatornaregisztráció létrehozása

Most, hogy létrehozott egy Azure App Service-t a robot üzemeltetéséhez, a következő lépés a **Bot Channels Registration létrehozása.** A csatornaregisztráció létrehozása előfeltétele a robot bot-keretcsatornákkal való regisztrálásának, beleértve a Direct Line Speech csatornát is.

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy a robotok hogyan használják ki a csatornákat, olvassa el [a Robot csatlakoztatása csatornákhoz ..](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure botcsatornák regisztrációjának létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. A rendszer kérni fogja, hogy adjon meg néhány információt:
   * **A Bot kezelni,** írja **SpeechEchoBotTutorial-BotRegistration**.
   * **Előfizetés esetén**válassza az **Ingyenes próbaverzió**lehetőséget.
   * **Az Erőforrás csoport**csoportban válassza a **SpeechEchoBotTutorial-ResourceGroup lehetőséget.**
   * A **Hely lehetőséget**az USA nyugati helye területen válassza a **(USA) hely lehetőséget.**
     * A **Tarifacsomag esetében**válassza az **F0**lehetőséget.
     * Az **Üzenetek végpont**esetében adja meg a webalkalmazás URL-címét a `/api/messages` végén hozzáfűzve. Ha például globálisan egyedi alkalmazásneve **EchoBot20190805125647**volt, az üzenetvégpont ja: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Az **Application insights**beállításához állítsa ezt a Ki **beállítást.** További információ: [Bot analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Figyelmen kívül hagyja **az automatikus létrehozási alkalmazásazonosítót és a jelszót**.
5. A **Bot Channels Registration** panel alján kattintson a **Létrehozás gombra.**

Ezen a ponton ellenőrizze a Resource Group **SpeechEchoBotTutorial-ResourceGroup** az Azure Portalon. Most négy forrást kell mutatnia:

| Név | Típus  | Hely |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA nyugati régiója |
| SpeechEchoBottutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotTutorial-BotRegistration | Bot csatornák regisztráció | Globális |
| SpeechEchoBotBemutató-Beszéd | Cognitive Services | USA nyugati régiója |

> [!IMPORTANT]
> A Bot Channels Registration erőforrás a globális régiót jeleníti meg, annak ellenére, hogy az USA nyugati régióját választotta. Ez a várható eredmény.

## <a name="register-the-direct-line-speech-channel"></a>A Direct Line beszédcsatorna regisztrálása

Most itt az ideje, hogy regisztrálja a robot a Direct Line Speech channel. Ez a csatorna az, amit a visszhangrobot és a beszédbeszéd SDK-val összeállított ügyfélalkalmazás közötti kapcsolat létrehozásához használnak.

1. Keresse meg és nyissa meg a **SpeechEchoBotTutorial-BotRegistration** erőforrást az [Azure Portalon.](https://portal.azure.com)
1. Az **Azure-szolgáltatások** navigációjában válassza a **Csatornák**lehetőséget.
   * Keressen **további csatornákat,** keresse meg és kattintson a **Közvetlen vonal beszédfelismerése parancsra.**
   * Tekintse át a **Közvetlen vonalbeszéd konfigurálása**című lapon található szöveget, majd bontsa ki a "Cognitive service account" feliratú legördülő menüt.
   * Válassza ki a korábban létrehozott beszéderőforrást (pl. **SpeechEchoBotTutorial-Speech)** a menüből, és társítsa a robotot a beszéd-előfizetési kulcshoz.
   * Kattintson a **Mentés** gombra.

1. A **Robotfelügyeleti** navigációs sávon kattintson a **Beállítások gombra.**
   * Jelölje be a **Streamelési végpont engedélyezése jelölőnégyzetet.** Erre a robot és a közvetlen vonalbeszédcsatorná közötti webszoftvercsatornákra épülő kommunikációs protokoll engedélyezéséhez van szükség.
   * Kattintson a **Mentés** gombra.

> [!TIP]
> Ha többet szeretne megtudni, olvassa el [a Robot csatlakoztatása közvetlen vonalbeszédhez című témakört.](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) Ez az oldal további információkat és ismert problémákat tartalmaz.

## <a name="build-the-windows-voice-assistant-client"></a>A Windows hangsegéd-ügyfél felépítése

Ebben a lépésben létre fogja építeni a Windows hangsegéd-ügyfelet. Az ügyfél egy Windows Megjelenítési alaprendszer (WPF) alkalmazás C# nyelven, amely a [beszédfelismerési SDK-t](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) használja a robottal való kommunikáció kezelésére a Direct Line Speech channel használatával. Használja, hogy kölcsönhatásba lépnek, és tesztelje a bot, mielőtt egy egyéni ügyfél alkalmazás írása előtt.

A Windows Voice Assistant-ügyfél egy egyszerű felhasználói felülettel rendelkezik, amely lehetővé teszi a robottal való kapcsolat konfigurálását, a szöveges beszélgetés megtekintését, a Bot-Framework tevékenységek JSON formátumban való megtekintését és az adaptív kártyák megjelenítését. Támogatja az egyéni kulcsszavak használatát is. Ezt az ügyfelet fogja használni, hogy beszéljen a robotjával, és hangválaszt kapjon.

Mielőtt továbblépnénk, győződjön meg arról, hogy a mikrofon és a hangszórók engedélyezve vannak és működnek.

1. Nyissa meg a Windows Voice [Assistant-ügyfél](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)GitHub-tárházát.
2. Kövesse a megadott utasításokat a tárház klónozásához, a projekt létrehozásához, az ügyfél konfigurálásához és az ügyfél elindításához.
3. Kattintson az **Újracsatlakozás gombra,** és győződjön meg arról, hogy megjelenik az üzenet **Nyomja meg a mikrofon gombot, vagy írja be a beszélgetést a robottal való beszélgetéshez.**
4. Próbáljuk ki. Kattintson a mikrofon gombra, és beszéljen néhány szót angolul. A felismert szöveg beszédkor jelenik meg. Ha befejezte a beszédet, a robot a saját hangján válaszol, mondván, hogy "echo", majd az elismert szavak.
5. A robottal való kommunikációhoz szöveget is használhat. Csak írja be a szöveget az alsó sávba. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>A Windows Hangsegéd-ügyfél hibáinak elhárítása

Ha hibaüzenet jelenik meg a fő alkalmazásablakban, a táblázat segítségével azonosíthatja és elháríthatja a hibát:

| Hiba | Mi a teendő? |
|-------|----------------------|
|Hiba: A WebSocket-frissítés hitelesítési hibával (401) nem sikerült. Helyes előfizetési kulcs (vagy engedélyezési jogkivonat) és régiónév ellenőrzése| Az alkalmazás Beállítások lapján győződjön meg arról, hogy helyesen adta meg a Beszédalapú előfizetés kulcsot és annak régióját.<br>Ellenőrizze, hogy helyesen adta-e meg a beszédkulcsot és a kulcsterületet. |
|ConnectionFailure hiba: A kapcsolatot a távoli állomás lezárta. Hibakód: 1011. Hiba részletei: Nem tudtunk csatlakozni a robothoz az üzenet elküldése előtt | Győződjön meg arról, hogy [bejelölte a "Streamelési végpont engedélyezése"](#register-the-direct-line-speech-channel) jelölőnégyzetet és/vagy [bevan kapcsolva **a webszoftvercsatornák** ](#enable-web-sockets) at.<br>Győződjön meg arról, hogy az Azure App Service fut. Ha úgy tűnik, próbálja meg újraindítani az App Service-t.|
|ConnectionFailure hiba: A kapcsolatot a távoli állomás lezárta. Hibakód: 1011. Hiba részletei: A válasz állapotkódja nem jelzi a sikert: 500 (InternalServerError)| A robot megadott egy neurális hangot a kimeneti tevékenység [felolvasása](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) mezőben, de a beszédbeli előfizetési kulcshoz társított Azure-régió nem támogatja a neurális hangokat. Lásd: [Standard és neurális hangok](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|ConnectionFailure hiba: A kapcsolatot a távoli állomás lezárta. Hibakód: 1000. Hiba részletei: Túllépte a webcsatorna-kapcsolat tétlen maximális időtartamát (> 300000 ms)| Ez egy várt hiba, ha a csatorna kapcsolata nyitva marad, és öt percnél tovább inaktív marad. |

Ha a probléma nem oldható meg a táblázatban, olvassa el a [Hangasszisztensek: Gyakori kérdések című témakört.](faq-voice-assistants.md)

### <a name="view-bot-activities"></a>Bot-tevékenységek megtekintése

Minden robot **tevékenységüzeneteket** küld és fogad. A Windows Voice Assistant-ügyfél **Tevékenységnapló** ablakában időbélyeggel ellátott naplók jelennek meg minden olyan tevékenységgel, amelyet az ügyfél a robottól kapott. Megtekintheti azokat a tevékenységeket is, amelyeket [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) az ügyfél a módszer rel küldött a robotnak. Amikor kiválaszt egy naplóelemet, a társított tevékenység részleteit JSON-ként jeleníti meg.

Itt egy minta json egy tevékenység az ügyfél kapott:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Ha többet szeretne megtudni arról, hogy mi jelenik meg a JSON-kimenetben, olvassa el [a Tevékenység mezőit.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md) Az oktatóanyag alkalmazásában a Szöveg és a [Felolvasás](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) mezőkre összpontosíthat. [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>A beszédbeszéd SDK-hívásainak ügyfélforráskódjának megtekintése

A Windows Hangsegéd-ügyfél a [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)NuGet csomagot használja, amely a beszédfelismerési SDK-t tartalmazza. A mintakód áttekintésének jó kiindulópontja az InitSpeechConnector() metódus a fájlban, [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)amely létrehozza ezt a két beszédsdkk-objektumot:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- A konfigurációs beállítások (pl. beszéd előfizetés kulcs, kulcs régió)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- A csatornakapcsolat és az ügyfél-előfizetési események kezelése az elismert beszéd- és robotválaszok kezeléséhez.

## <a name="add-custom-keyword-activation"></a>Egyéni kulcsszó-aktiválás hozzáadása

A beszédstabk támogatja az egyéni kulcsszóaktiválást. A Microsoft Assistant "Hey Cortana" kifejezéséhez hasonlóan olyan alkalmazást is írhat, amely folyamatosan figyeli az Ön által választott kulcsszót. Ne feledje, hogy egy kulcsszó lehet egy szó vagy többszavas kifejezés.

> [!NOTE]
> A *kulcsszó* kifejezést gyakran szinonimaként használják az *ébresztési szó*kifejezéssel, és mindkettőt a Microsoft dokumentációjában is láthatja.

A kulcsszóészlelés az ügyfélalkalmazásban történik. Kulcsszó használata esetén a rendszer csak akkor továbbítja a hangot a Direct Line Speech csatornára, ha a kulcsszót észleli. A Direct Line Speech csatorna tartalmaz egy *kulcsszó-ellenőrzést (KWV)* nevű összetevőt, amely összetettebb feldolgozást végez a felhőben annak ellenőrzésére, hogy a kiválasztott kulcsszó a hangadatfolyam elején van-e. Ha a kulcsszó-ellenőrzés sikeres, akkor a csatorna kommunikál a robottal.

Kövesse az alábbi lépéseket kulcsszómodell létrehozásához, konfigurálja a Windows Voice Assistant-ügyfelet a modell használatára, és végül tesztelje azt a robottal.

1. Egyéni kulcsszó létrehozásához kövesse az alábbi utasításokat [a Beszédszolgáltatás használatával.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
2. Csomagolja ki az előző lépésben letöltött modellfájlt. Meg kell nevezni a kulcsszó. Egy fájl nevét `kws.table`keresi.
3. A Windows hangsegéd-ügyfélben keresse meg a **Beállítások** menüt (keresse meg a fogaskerék ikont a jobb felső sarokban). Keresse meg **a Modellfájl elérési útját,** és írja be a fájl teljes elérési útjának nevét a `kws.table` 2.
4. Győződjön meg arról, hogy be van jelölve az **Engedélyezve**feliratú jelölőnégyzet. A következő jelölőnégyzet mellett ezt az üzenetet kell látnia: "Figyelni fogja a kulcsszót a következő kapcsolatután". Ha rossz fájlt vagy érvénytelen elérési utat adott meg, hibaüzenetet kell látnia.
5. Adja meg a **beszédalapú előfizetési kulcsot**, **az előfizetési kulcs régióját**, majd kattintson az **OK** gombra a **Beállítások** menü bezárásához.
6. Kattintson **az Újracsatlakozás gombra.** Meg kell jelennie egy üzenet, amely így szól: "Új beszélgetés kezdődött - írja be, nyomja meg a mikrofon gombot, vagy mondja ki a kulcsszót". Az alkalmazás most folyamatosan figyel.
7. Beszéljen bármilyen kifejezést, amely a kulcsszóval kezdődik. Például: "**{a kulcsszó}**, mennyi az idő?". A kulcsszó kimondása után nem kell szüneteltetnie. Amikor elkészült, két dolog történik:
   * Látni fogja a beszédének átiratát.
   * Nem sokkal később, hallani fogja a bot válasza
8. Folytassa a kísérletezést a robot által támogatott három beviteli típussal:
   * Szöveg beírása az alsó sávon
   * A mikrofon ikon és a beszéd megnyomása
   * A kulcsszóral kezdődő kifejezés kimondása

### <a name="view-the-source-code-that-enables-keyword"></a>A kulcsszót lehetővé tévő forráskód megtekintése

A Windows Voice Assistant ügyfélforráskódjában tekintse át ezeket a fájlokat, és tekintse át a kulcsszóészlelés engedélyezéséhez használt kódot:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)a Speech SDK metódus [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)hívását is tartalmazza, amely a modell lemezen lévő helyi fájlból történő példányosítására szolgál.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)tartalmaz egy hívás speech SDK módszer [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync), amely aktiválja a folyamatos kulcsszófelismerés.

## <a name="optional-change-the-language-and-bot-voice"></a>(Nem kötelező) A nyelv és a bot hangjának módosítása

A létrehozott robot angol ultrásan fog figyelni és válaszolni, az alapértelmezett amerikai angol szövegfelolvasó hanggal. Azonban nem korlátozódik az angol vagy az alapértelmezett hang használatára. Ebben a szakaszban megtudhatja, hogyan módosíthatja a nyelvet, amelyet a robot figyelni fog, és amelyben válaszol. Azt is megtudhatja, hogyan válasszon másik hangot az adott nyelvhez.

### <a name="change-the-language"></a>A nyelv módosítása

A [beszédfelismerési](language-support.md#speech-to-text) táblázatban említett nyelvek közül bármelyik közül választhat. Az alábbi példában a nyelvet németnyelvre változtatjuk.

1. Nyissa meg a Windows Voice Assistant Ügyfél alkalmazást, kattintson a `de-de` beállítások gombra (jobb felső fogaskerék ikon), és írja be a Nyelv mezőbe (ez a [beszéd-szöveg](language-support.md#speech-to-text) táblázatban említett területi beállítási érték). Ezzel beállítja a felismert et, `en-us`felülírva az alapértelmezett nyelvet. Ez arra is utasítja a Direct Line Speech channel-t, hogy a Bot válaszához használjon alapértelmezett német hangot.
2. Zárja be a beállítások oldalt, és kattintson az Újracsatlakozás gombra, hogy új kapcsolatot hozzon létre a visszhangrobottal.
3. Kattintson a mikrofon gombra, és mondjon egy mondatot németül. Az elismert szöveg és az echo bot az alapértelmezett német hanggal válaszol.

### <a name="change-the-default-bot-voice"></a>Az alapértelmezett robothang módosítása

A szövegfelolvasó hang kiválasztása és a kiejtés szabályozása akkor végezhető el, ha a bot egyszerű szöveg helyett [beszédszintetizáló nyelvi](speech-synthesis-markup.md) (SSML) formában adja meg a választ. Az echo bot nem használja az SSML-t, de ehhez könnyen módosíthatjuk a kódot. Az alábbi példában ssml-t adunk az echo bot válaszához, hogy a német hang, Stefan Apollo (férfi hang) legyen használva az alapértelmezett női hang helyett. Tekintse meg az Ön nyelvén támogatott [standard hangok](language-support.md#standard-voices) és [neurális hangok](language-support.md#neural-voices) listáját.

1. Kezdjük a megnyitásával `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Keresse meg ezt a két sort:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Cserélje ki őket a következőkre:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Készítse el a megoldást a Visual Studióban, és javítsa ki az esetleges buildhibákat.

A második érv a metódus "MessageFactory.Text" beállítja a [tevékenység beszélése mező](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) a bot választ. A fenti módosítással egyszerű szövegről SSML-re cserélték, hogy nem alapértelmezett német hangot adjon meg.

### <a name="redeploy-your-bot"></a>A robot újratelepítése

Most, hogy végrehajtotta a szükséges módosítást a roboton, a következő lépés az, hogy újra közzétegye az Azure App Service-ben, és kipróbálja:

1. A Megoldáskezelő ablakban kattintson a jobb gombbal az **EchoBot** projektre, és válassza a **Közzététel parancsot.**
2. Az előző központi telepítési konfiguráció már be van töltve alapértelmezettként. Egyszerűen kattintson a **Közzététel** gombra az **EchoBot20190805125647 – Web Deploy mellett.**
3. A **Sikeres közzététel** üzenet megjelenik a Visual Studio kimeneti ablakában, és egy weblap indul a következő üzenettel: "A robot készen áll!".
4. Nyissa meg a Windows Voice Assistant Ügyfél alkalmazást, kattintson a beállítások gombra `de-de` (jobb felső fogaskerék ikon), és győződjön meg arról, hogy még mindig a Nyelv mezőben található.
5. Kövesse a [Windows voice assistant-ügyfél felkészítése](#build-the-windows-voice-assistant-client) című részben található utasításokat, hogy újra kapcsolatba léphessen az újonnan üzembe helyezett robottal, beszéljen az új nyelven, és hallja, ahogy a robot ezen a nyelven válaszol az új hanggal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni az oktatóanyagban üzembe helyezett echo-botot, eltávolíthatja azt és az összes kapcsolódó Azure-erőforrást egyszerűen törli az Azure Resource group **SpeechEchoBotTutorial-ResourceGroup .If**you're not continue to using the echo-bot deployed in this tutorial, you can remove it and all its associated Azure resources by simply dets the Azure Resource group SpeechEchoBotTutorial-ResourceGroup .

1. Az [Azure Portalon](https://portal.azure.com)kattintson az **Erőforráscsoportok** az **Azure-szolgáltatások** navigációs.
2. Keresse meg a következő nevű erőforráscsoportot: **SpeechEchoBotTutorial-ResourceGroup**. Kattintson a három pontra (...).
3. Válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Saját ügyfélalkalmazás létrehozása a beszédfelismerési SDK-val](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Lásd még

* Üzembe helyezés egy [Közeli Azure-régióban](https://azure.microsoft.com/global-infrastructure/locations/) a robot válaszidejének javításához
* Üzembe helyezés olyan [Azure-régióban, amely támogatja a kiváló minőségű neurális TTS-hangokat](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* A Direct Line Speech csatornához társított árak:
  * [Bot Service árképzés](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Beszédszolgáltatás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Saját hangalapú robot létrehozása és üzembe helyezése:
  * Építs egy [Bot-Framework bot](https://dev.botframework.com/). Regisztrálja a [Direct Line Speech channel](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) és [testre szabhatja a bot hang](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Fedezze fel a meglévő [Bot-Framework megoldásokat:](https://microsoft.github.io/botframework-solutions/index)Építsen virtuális [asszisztenst,](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) és [terjessze ki a direct line beszédre](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
