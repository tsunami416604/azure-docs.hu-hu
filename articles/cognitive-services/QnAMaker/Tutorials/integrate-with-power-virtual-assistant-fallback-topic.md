---
title: 'Oktatóanyag: integrálás a Power Virtual agentekkel – QnA Maker'
description: Ebben az oktatóanyagban az aktív tanulással fejlesztheti tudásbázisának minőségét. Tekintse át, fogadja el vagy utasítsa el, vagy vegye fel a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: f1d51f6ad8892252161238eb71fbb02f463463fd
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84635375"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Oktatóanyag: a Tudásbázis hozzáadása a virtuális ügynökökhöz
Hozzon létre és terjesszen ki egy [Power Virtual Agents](https://powervirtualagents.microsoft.com/) -robotot, hogy válaszokat nyújtson a Tudásbázisból.

Az oktatóanyag a következőket ismerteti:

<!-- green checkmark -->
> [!div class="checklist"]
> * Power Virtual Agent-ügynökök létrehozása
> * Rendszer-tartalék témakör létrehozása
> * QnA Maker hozzáadása műveletként a témakörben a Power automatizáló folyamatként
> * Automatizálási megoldás létrehozása
> * Automatizálási folyamat hozzáadása a megoldáshoz
> * Power Virtual agentek közzététele
> * Teljesítményű virtuális ügynökök tesztelése és válasz fogadása az QnA Maker Tudásbázisból

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Ügynök integrálása tudásbázissal

A [Power Virtual Agents](https://powervirtualagents.microsoft.com/) lehetővé teszi, hogy a csapatok egy interaktív, kód nélküli grafikus felülettel hozzanak létre hatékony botokat. Nincs szüksége adatszakértőkre vagy fejlesztőknek.

A Power Virtual Agent-Ügynökökben létrehozhat egy olyan ügynököt, amely témaköröket tartalmaz (a tárgy területe), hogy a műveletek végrehajtásával válaszolni lehessen a felhasználói kérdésekre. Ha a válasz nem található, a rendszer-tartalék visszaküldheti a választ.

Konfigurálja úgy az ügynököt, hogy a kérdést a Tudásbázisban a témakör műveletének részeként küldje el, vagy a *rendszerszintű* témakör elérési útjának részeként. Mindkettő egy műveletet használ a tudásbázishoz való kapcsolódáshoz és válasz visszaküldéséhez.

## <a name="power-automate-connects-to-generateanswer-action"></a>A Power gyorsbüfé csatlakozik a `GenerateAnswer` művelethez

Ha az ügynököt a tudásbázishoz szeretné kapcsolni, a művelet létrehozásához használja a Power Gyorsbüfét. A Power automatizál egy folyamatot biztosít, amely a QnA Maker API-hoz csatlakozik `GenerateAnswer` .

Miután megtervezte és mentette a folyamatot, elérhető a Power automatizáló megoldásból. Ezt a megoldást használja műveletként az ügynökben.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Ügynök összekötése a tudásbázissal

Az alábbiakban áttekintheti, hogyan csatlakoztathatók az ügynökök a Power Virtual Agent szolgáltatásokban a QnA Maker tudásbázisában.

* A [QnA Maker](https://www.qnamaker.ai/) portálon:
    * Hozza létre és tegye közzé a tudásbázist.
    * Másolja ki a Tudásbázis adatait, beleértve az azonosítót, a futásidejű végpont kulcsát és a futásidejű végpont gazdagépét.
* A [Power Virtual Agents](https://powerva.microsoft.com/) portálon:
    * Hozzon létre egy ügynök témakört.
    * Művelet meghívása (a folyamat automatizálása).
* A [Power automatizáló](https://us.flow.microsoft.com/) portálon:
    * _Válasz készítése QnA Maker sablon használatával_
    * A sablon segítségével konfigurálja a folyamatot [QnA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)használatára.
        * QnA Maker közzétett Tudásbázis-információk:
            * Tudásbázis-azonosító
            * Erőforrás-végponti gazdagép QnA Maker
            * QnA Maker erőforrás-végpont kulcsa
        * Bemeneti – felhasználói lekérdezés
        * Kimenet – Tudásbázis-válasz
    * Hozzon létre egy megoldást, adja hozzá a folyamatot, vagy vegyen fel folyamatot meglévő megoldásba.
* Visszatérés a Power Virtual Agent Ügynökökbe:
    * Válassza ki a megoldás kimenetét üzenetként egy témakörhöz.

## <a name="create-and-publish-a-knowledge-base"></a>Tudásbázis létrehozása és közzététele

1. Egy Tudásbázis [létrehozásához kövesse a rövid](../Quickstarts/create-publish-knowledge-base.md) útmutató lépéseit. Ne fejezze be az utolsó szakaszt a robot létrehozásával kapcsolatban. Ehelyett ezt az oktatóanyagot használva hozzon létre egy robotot a Power Virtual Agents használatával.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a közzétett Tudásbázis-beállításokról](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Adja meg a közzétett Tudásbázis-beállításokat a [QnA Maker](https://www.qnamaker.ai/) portál **Beállítások** lapján. Ezekre az információkra szüksége lesz a QnA Maker-kapcsolatok konfigurálásához szükséges [energiagazdálkodási lépésekhez](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) `GenerateAnswer` .

1. A QnA Maker portál **Beállítások** lapján keresse meg a végpont kulcsát, a végpont gazdagépét és a Tudásbázis azonosítóját.

## <a name="create-an-agent-in-power-virtual-agents"></a>Ügynök létrehozása a Power Virtual Agents szolgáltatásban

1. [Jelentkezzen be a Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)szolgáltatásba. Használja az iskolai vagy munkahelyi e-mail-fiókját.

1. Ha ez az első robot, az ügynök **kezdőlapján** fog megjelenni. Ha ez nem az első robotja, válassza ki a robotot a lap jobb felső területén, és válassza az **+ új robot**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A Power Virtual Agents kezdőlapjának képernyőképe](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>A robotban megadott témakörök

Az ügynök a témakör gyűjtemény használatával válaszol a tárgy területén található kérdésekre. Ebben az oktatóanyagban az ügynök számos témakört tartalmaz, amelyeket felhasználói témakörökbe és rendszertémakörökbe oszthat meg.

A bal oldali navigációs sávon válassza a **témakörök** lehetőséget a robot által biztosított témakörök megtekintéséhez.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az ügynökben szereplő témakörökről](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>A rendszerszintű tartalék témakör létrehozása

Bár az ügynök bármely témakörből csatlakozhat a tudásbázishoz, ez az oktatóanyag a rendszer- *tartalék* témakört használja. A tartalék témakör akkor használatos, ha az ügynök nem talál választ. Az ügynök átadja a felhasználó szövegét QnA Maker `GenerateAnswer` API-nak, megkapja a választ a tudásbázistól, és üzenetként jeleníti meg a felhasználónak.

1. A [Power Virtual Agents](https://powerva.microsoft.com/#/) Portal jobb felső sarkában válassza a **Beállítások** (a fogaskerék ikon) lehetőséget. Ezután válassza a **rendszer-tartalék**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel a Power Virtual Agents menüpontról a rendszerszintű tartalékhoz](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Válassza a **+ Hozzáadás** elemet a rendszer tartalék témakör hozzáadásához.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a tartalék témakör hozzáadásáról.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. A témakör hozzáadása után kattintson az **Ugrás a tartalék témakörre** elemre a szerzői műveletek vásznon található tartalék témakör létrehozásához.

    > [!TIP]
    > Ha vissza kell térnie a tartalék témakörhöz, az a **témakörök** szakaszban **, a rendszertémakörök** részeként érhető el.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Művelet hozzáadása az authoring Canvas használatával

A Power Virtual Agent szerzői vászon használatával kapcsolja össze a tartalék témakört a Tudásbázisban. A témakör az ismeretlen felhasználói szöveggel kezdődik. Adjon hozzá egy olyan műveletet, amely átadja a szöveget a QnA Makernak, majd üzenetként jeleníti meg a választ. A válasz megjelenítésének utolsó lépéseként az oktatóanyag későbbi részében [külön lépésként](#add-your-solutions-flow-to-power-virtual-agents)kezeli a rendszer.

Ez a szakasz a tartalék témakör beszélgetési folyamatát hozza létre.

1. Lehetséges, hogy az új tartalék művelet már rendelkezik a beszélgetési folyamat elemeivel. A **Beállítások** menü kiválasztásával törölje a **kiterjesztés** elemet.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="A beszélgetési folyamat részleges képernyőképe, a törlés lehetőség kiemelve.":::

1. Válassza ki az **+** összekötőt az **üzenet** mezőbe, majd válassza a **művelet meghívása**lehetőséget.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="A művelet meghívásának részleges képernyőképe.":::

1. Kattintson a **Folyamat létrehozása** elemre. A folyamat végigvezeti a Power automatizáló portálon.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a folyamat létrehozásáról](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    A Power automatizálás új sablonra nyílik meg. Ezt az új sablont nem fogja használni.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Az új flow-sablonnal történő automatizálás részleges képernyőképe.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>A tudásbázishoz való kapcsolódáshoz hozzon létre egy automatizáló energiagazdálkodási folyamatot

Az alábbi eljárás egy olyan energiagazdálkodási folyamatot hoz létre, amely:
* Beolvassa a bejövő felhasználó szövegét, és elküldi a QnA Makernak.
* Visszaadja a legmagasabb választ az ügynöknek.

1. A **Power automatizáló**lapon válassza a **sablonok** lehetőséget a bal oldali navigációs sávon. Ha a rendszer megkérdezi, hogy el kívánja-e hagyni a böngésző oldalát, fogadja el a szabadságot.

1. A sablonok lapon keresse meg a sablon **előállítása választ a QnA Maker használatával** , majd válassza ki a sablont. Ez a sablon a QnA Maker meghívásához szükséges összes lépést megadta a Tudásbázis-beállításokkal, és a legnagyobb választ adja vissza.

1. A QnA Maker folyamat új képernyőjén válassza a **Folytatás**lehetőséget.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="A QnA Maker-sablon folyamatának részleges képernyőképe, a Folytatás gomb kiemelve.":::

1. Jelölje be a **Válasz létrehozása** művelet jelölőnégyzetet, majd adja meg a QnA Maker beállításait egy korábbi [, Tudásbázis létrehozása és közzététele](#create-and-publish-a-knowledge-base)című szakaszban. Az alábbi képen a **Service Host** a Tudásbázis gazdagép- **gazdagépére** hivatkozik, és a formátuma a következő: `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` .


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="A QnA Maker-sablon folyamatának részleges képernyőképe, a Folytatás gomb kiemelve.":::

1. A folyamat mentéséhez válassza a **Mentés** lehetőséget.

## <a name="create-a-solution-and-add-the-flow"></a>Megoldás létrehozása és a folyamat hozzáadása

Ahhoz, hogy az ügynök megtalálja és csatlakozhasson a folyamathoz, a folyamatnak szerepelnie kell egy automatizálási megoldásban.

1. Miközben továbbra is a Power automatizáló portálon, válassza a **megoldások** lehetőséget a bal oldali navigációs sávon.

1. Válassza az **+ Új megoldás** elemet.

1. Adjon meg egy megjelenítendő nevet. A megoldások listája a szervezet vagy az iskola minden megoldását tartalmazza. Válasszon egy elnevezési konvenciót, amely segít a megoldásokban való szűrésben. Például megadhatja az e-mail-címet a megoldás neveként: `jondoe-power-virtual-agent-qnamaker-fallback` .

1. Válassza ki a közzétevőt a lehetőségek listájából.

1. Fogadja el a név és a verzió alapértelmezett értékeit.

1. A folyamat befejezéséhez válassza a **Létrehozás** lehetőséget.

## <a name="add-your-flow-to-the-solution"></a>Folyamat hozzáadása a megoldáshoz

1. A megoldások listájában válassza ki az imént létrehozott megoldást. A lista tetején kell lennie. Ha nem, keresse meg az e-mail-nevet, amely a megoldás nevének részét képezi.

1. A megoldásban válassza a **+ meglévő hozzáadása**lehetőséget, majd válassza a **folyamat** lehetőséget a listából.

1. Keresse meg a folyamatot a **külső megoldások** listájáról, majd válassza a **Hozzáadás** lehetőséget a folyamat befejezéséhez. Ha sok folyamat van, tekintse meg a **módosított** oszlopot a legutóbbi folyamat megkereséséhez.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>A megoldás folyamatának hozzáadása a virtuális ügynökökhöz

1. Térjen vissza a böngésző lapra az ügynökével a Power Virtual Agents szolgáltatásban. A szerzői vászonnak továbbra is nyitva kell lennie.

1. Ha új lépést szeretne beszúrni a folyamatba, az **üzenet** művelete mező felett válassza ki az **+** összekötőt. Ezután válassza **a művelet meghívása**lehetőséget.

1. A **folyamat** előugró ablakában válassza ki a válaszok létrehozása nevű új folyamatot **QnA Maker Tudásbázis használatával..**. Az új művelet megjelenik a folyamaton.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Részleges képernyőkép az áramellátási virtuális ügynökről témakör beszélgetési vászon QnA Maker folyamat hozzáadása után.":::

1. A bemeneti változónak a QnA Maker művelethez való helyes beállításához válassza **a válasszon változót**, majd kattintson a bot elemre **. UnrecognizedTriggerPhrase**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Részleges képernyőkép az energiagazdálkodási virtuális ügynökről témakör beszélgetési vászon a bemeneti változó kiválasztása.":::


1. Ha a kimeneti változót helyesen szeretné beállítani a QnA Maker művelethez, az **üzenet** műveletben válassza a **UnrecognizedTriggerPhrase**lehetőséget, majd válassza az ikont a változó beszúrásához, `{x}` majd válassza a **FinalAnswer**lehetőséget.

1. A környezeti eszköztáron válassza a **Mentés**lehetőséget, hogy mentse a szerzői vászon részleteit a témakörben.

A végleges ügynök vásznon látható.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a végleges ügynök vászonról](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Az ügynök tesztelése

1. A teszt ablaktáblán állítsa be a **témakörök közötti**váltást. Ez lehetővé teszi, hogy megtekintse a témakörök közötti előrehaladást, valamint egy adott témakört.

1. Tesztelje az ügynököt úgy, hogy a következő sorrendben írja be a felhasználói szöveget. A szerzői vászon a sikeres lépéseket zöld pipa jelzi.

    |Kérdés sorrendje|Kérdések tesztelése|Szerep|
    |--|--|--|
    |1|helló|Beszélgetés indítása|
    |2|Tárolási idő|Példa témakörre. Ez úgy van konfigurálva, hogy az Ön részéről további munka nélkül működjön.|
    |3|Yes|Válasz címzettje`Did that answer your question?`|
    |4|Kiváló|Válasz címzettje`Please rate your experience.`|
    |5|Yes|Válasz címzettje`Can I help with anything else?`|
    |6|Hogyan javíthatom a lekérdezési előrejelzések teljesítményének teljesítményét?|Ez a kérdés elindítja a visszalépési műveletet, amely választ küld a szövegnek a tudásbázisba. Ezután megjelenik a válasz. az egyes műveletekhez tartozó zöld pipa jelzi az egyes műveletek sikerességét.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Képernyőkép a csevegési robotról, amely zöld pipa jelzi a sikeres műveletekhez.":::

## <a name="publish-your-bot"></a>A robot közzététele

Ahhoz, hogy az ügynök elérhető legyen az iskola vagy szervezet összes tagja számára, közzé kell tennie.

1. A bal oldali navigációs sávon válassza a **Közzététel**lehetőséget. Ezután válassza a **Közzététel** elemet az oldalon.

1. Próbálja ki a robotot a bemutató webhelyén (keresse meg a **Közzététel**alatt található hivatkozást).

    Megnyílik egy új weblap a robottal. Kérdezze meg a robotot ugyanazzal a tesztelési kérdéssel:`How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel a végleges ügynök vászonról](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>A robot megosztása

A bemutató webhely megosztásához konfigurálja a csatornát csatornaként.

1. A bal oldali navigációs sávon válassza a csatornák **kezelése**lehetőséget  >  **Channels**.

1. Válassza ki a **bemutató webhelyet** a csatornák listából.

1. Másolja a hivatkozást, és válassza a **Mentés**lehetőséget. Illessze be a bemutató webhelyére mutató hivatkozást egy e-mailbe az iskolájában vagy a szervezet tagjainak.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha elkészült a tudásbázissal, távolítsa el a Azure Portal QnA Maker erőforrásait.

## <a name="next-step"></a>Következő lépés

[Tudásbázis elemzésének lekérése](../How-To/get-analytics-knowledge-base.md)

További információk:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker-összekötő](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) és az [összekötő beállításai](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)