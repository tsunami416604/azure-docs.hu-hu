---
title: A Botot jelentéskészítő SDK használatával a C# az Azure-ban bot LUIS integrálása |} Microsoft Docs
description: A Botot keretrendszer LUIS alkalmazással integrált bot felépítéséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 3db2588edefa2f07d22609d7c454367d5572d5e0
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349954"
---
# <a name="web-app-bot-using-the-luis-template-for-c"></a>Webes alkalmazás Botot a C# LUIS sablonnal

Hozzon létre egy chatbot az integrált nyelvi ismertetése.

## <a name="prerequisite"></a>Előfeltétel

* [HomeAutomation LUIS app](luis-get-started-create-app.md). A leképezések az LUIS app leképezés a botot párbeszédpanel kezelők. 

## <a name="luis-homeautomation-intents"></a>LUIS HomeAutomation leképezések

| Kísérlet történt | Példa utterance | Botot funkció |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Kapcsolja be a fény. | Ha a LUIS célt `HomeAutomation.TurnOn` észlel, a botot meghívja a `OnIntent` párbeszédpanel kezelő. Ezen a párbeszédpanelen, ahol meghívta egy IoT-szolgáltatás kapcsolja be az eszközön, és kérje meg a felhasználót, hogy az eszköz be van kapcsolva. |
| HomeAutomation.TurnOff | Kapcsolja ki a szobája fény. | Ha a LUIS célt `HomeAutomation.TurnOff` észlel, a botot meghívja a `OffIntent` párbeszédpanel kezelő. Ezen a párbeszédpanelen, ahol meghívta egy IoT-szolgáltatás kapcsolja ki egy eszközt, a felhasználó értesítése, hogy az eszköz ki lett kapcsolva. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Hozzon létre egy nyelvi ismertetése botot Botot szolgáltatással

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **hozzon létre új erőforrást** a felső bal oldali menüben.

    ![Új erőforrás létrehozása](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. A keresőmezőbe, keresse meg a **Web App Botot**. 

    ![Új erőforrás létrehozása](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. Kattintson a webes alkalmazás Botot ablakban **létrehozása**.

4. A **Botot szolgáltatás**, adja meg a szükséges információkat, és kattintson a **létrehozása**. Ez létrehozza, és a botot szolgáltatás és az alkalmazás LUIS telepíti az Azure-bA. Ha a használni kívánt [beszéd elsődleges](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), tekintse át [régió követelmények](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) a botot létrehozása előtt. 
    * Állítsa be **alkalmazásnév** a botot névre. A név lesz a altartomány a botot a felhőbe (például mynotesbot.azurewebsites.net) telepítésekor. <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Válassza ki az előfizetést, [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App service-csomag, és [hely](https://azure.microsoft.com/regions/).
    * Válassza ki a **nyelvi ismertetése (C#)** sablon a **Botot sablon** mező.
    * Válassza ki a **LUIS App hely**. Ez az a szerzői műveletekhez [régió] [ LUIS] az alkalmazás létrehozása.
    * Jelölje be a megerősítő a jogi nyilatkozatot. A jogi nyilatkozat feltételeit. a rendszer alatt a jelölőnégyzet jelölését.

    ![Robotszolgáltatás](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Győződjön meg arról, hogy a botot szolgáltatás már alkalmazva van.
    * Kattintson az értesítések (a harang ikonra, amely mentén a felső szegélyéhez, hogy az Azure portálon található). Az értesítés-ről változik **telepítése megkezdődött** való **KözpontiTelepítés sikerült**.
    * Után vált, az értesítés **KözpontiTelepítés sikerült**, kattintson a **forrást** adott értesítésre kattinthat.

> [!Note]
> A webes alkalmazás botot létrehozási folyamata is létrehoz egy új LUIS alkalmazást. Rendelkezik lett betanítása és közzétett meg. 

## <a name="try-the-default-bot"></a>Az alapértelmezett botot próbálja

Győződjön meg arról, hogy a botot kiválasztásával lett-e telepítve. a **értesítések** jelölőnégyzetet. Az értesítések a változik **telepítés folyamatban...**  való **KözpontiTelepítés sikerült**. Kattintson a **forrást** gombra kattintva nyissa meg a botot erőforrásokat.

Amikor a botot van telepítve, kattintson a **vizsgálati a webes Csevegés** a webes Csevegés ablaktábla megnyitása. Írja be a "Hello" szövegrészt webes Csevegés.

  ![A botot webes Csevegés tesztelése](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

A botot válaszol kimondásával "üdvözlés elérése. Ön említett: hello ".  Ez a válasz megerősíti, hogy a botot a üzenetet kapott, és egy alapértelmezett LUIS-alkalmazást, amely az jön létre, az átadott. Ez az alapértelmezett LUIS app egy üdvözlés leképezés észlelhető. A következő lépésben a botot fog csatlakozni a korábban létrehozott helyett az alapértelmezett LUIS app LUIS alkalmazást.

## <a name="connect-your-luis-app-to-the-bot"></a>Az LUIS alkalmazás csatlakoztatása a botot

Nyissa meg **Alkalmazásbeállítások** és szerkesztése a **LuisAppId** magában foglalja az LUIS alkalmazás Alkalmazásazonosító mezőben. USA nyugati régiója nem abban a régióban létrehozott az HomeAutomation LUIS alkalmazást, ha módosítani szeretné a **LuisAPIHostName** is. A **LuisAPIKey** jelenleg a szerzői műveletekhez kulcs van beállítva. Ezt módosítani az előfizetési kulccsal, ha a forgalom meghaladja az ingyenes szint kvótát. 

  ![Frissítse a LUIS Alkalmazásazonosító az Azure-ban](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Ha nincs LUIS alkalmazás Azonosítóját a [Home Automation app](luis-get-started-create-app.md), jelentkezzen be a [LUIS](luis-reference-regions.md) webhely ugyanazzal a fiókkal jelentkezzen be az Azure használatával. 
> 1. Kattintson a **alkalmazásaimat**. 
> 2. A korábban létrehozott LUIS alkalmazás, amely tartalmazza a leképezések és entitásokat a HomeAutomation tartományból megkereséséhez.
> 3. Az a **beállítások** LUIS alkalmazás lapon található, és másolja az alkalmazás azonosítóját. Ellenőrizze, hogy az [képzett](Train-Test.md) és [közzétett](PublishApp.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>A botot kód módosítása

1. Kattintson a **Build** majd **nyitott online kód szerkesztése**.

   ![Nyissa meg az online kód szerkesztése](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Kattintson a jobb gombbal `build.cmd` válassza **futtatása konzolról** az alkalmazás elkészítésére. A szolgáltatás automatikusan meg befejezése több összeállítási lépésből áll. A build kész, amikor befejezte volna a "Sikeresen befejeződött."

3. Nyissa meg a kód szerkesztése `/Dialogs/BasicLuisDialog.cs`. A következő kódot tartalmazza:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>HomeAutomation leképezések kód módosítása


1. Távolítsa el a három leképezési attribútumok és módszerek **köszöntés**, **Mégse**, és **súgó**. A leképezések nem használják a HomeAutomation előre elkészített tartományban. Győződjön meg arról, hogy a **nincs** leképezési attribútum és metódust. 

2. Adja a függőségeket, a más függőségekkel rendelkező a fájl elejéhez:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Állandók kezeléséhez karakterláncok tetején adja hozzá a `BasicLuisDialog ` osztály:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Hozzáadja a kódot az új leképezések az `HomeAutomation.TurnOn` és `HomeAutomation.TurnOff` belül a `BasicLuisDialog ` osztály:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Adja hozzá a kódot a LUIS belül található entitások lekérni a `BasicLuisDialog ` osztály:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Változás **ShowLuisResult** metódust a `BasicLuisDialog ` osztály a pontszám kerekíteni, gyűjtése az entitásokat, és a válaszüzenetet megjelennek a chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>A botot összeállítása
A kód-szerkesztőben kattintson a jobb gombbal a `build.cmd` válassza **futtatása konzolról**.

![Webes botot összeállítása ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

A kód nézetre egy terminálablakot, állapotának és eredményeinek a build megjelenítő helyére.

![Webes botot létrehozása sikeres](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Válassza ki a felső kék színű sáv botot nevét, és válassza ki az alternatív módszert hozhat létre a botot **nyissa meg a Kudu konzolt**. Megnyitja a konzol **D:\home**. 
> 
> Lépjen be **D:\home\site\wwwroot** beírásával: `cd site\wwwroot`
>
> A build parancsfájl futtatásához írja be: `build.cmd`

## <a name="test-the-bot"></a>A botot tesztelése

Az Azure-portálon kattintson a **webes Csevegés tesztelése** a botot teszteléséhez. Írja be a üzenet like "kapcsolja be a fény" és "kapcsolja ki a melegítőkráter" hozzáadott, akkor a leképezések meghívni.

   ![Webes Csevegés HomeAutomation botot tesztelése](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> A LUIS app a botot kód módosítás nélkül is működik. Lásd: [adja hozzá például utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) és [betanítása és a alkalmazás tesztelése a LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/train-test). 

## <a name="download-the-bot-to-debug"></a>Töltse le a botot hibakeresése
Ha a botot nem működik, a projekt letöltése a helyi számítógépen, és továbbra is [hibakeresés](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-an-azure-app-service-web-app-c-bot). 

## <a name="learn-more-about-bot-framework"></a>További tudnivalók Botot keretrendszer
További információ [Botot keretrendszer](https://dev.botframework.com/) és a [3.x](https://github.com/Microsoft/BotBuilder) és [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK-k.

## <a name="next-steps"></a>További lépések

Adja hozzá a LUIS leképezések és Botot szolgáltatás párbeszédpanelek célból **súgó**, **Mégse**, és **köszöntés** leképezések. Ne felejtse el a vonat, közzététele, és [build](#build-the-bot) a webes alkalmazás botot. LUIS, mind a botot azonos leképezések kell rendelkeznie.

> [!div class="nextstepaction"]
> [Adja hozzá a leképezések](./luis-how-to-add-intents.md)
> [beszéd betanítási művelet](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)
<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[AssignedEndpointDoc]:https://docs.microsoft.com/azure/cognitive-services/LUIS/manage-keys
[VisualStudio]: https://www.visualstudio.com/
[LUIS]:luis-reference-regions.md
<!-- tested on Win10 -->
