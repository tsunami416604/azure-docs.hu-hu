---
title: 'Oktatóanyag: Integrálható a Power Virtual Agent - QnA Maker'
description: Ebben az oktatóanyagban javíthatja a tudásbázis minőségét az aktív tanulással. Tekintse át, fogadja el vagy utasítsa el, adja hozzá a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398876"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Oktatóanyag: Tudásbázis hozzáadása a Power Virtual Agent hez
Hozzon létre és bővítsen egy [virtuális ügynök](https://powervirtualagents.microsoft.com/) robotot, hogy válaszokat adjon a tudásbázisból.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Virtuális energiaügynök létrehozása
> * Rendszertartalék témakör létrehozása
> * Add QnA Maker műveletként a témakörhöz, mint Power Automate flow
> * Power Automate megoldás létrehozása
> * Power Automate folyamat hozzáadása a megoldáshoz
> * Virtuális energiaügynök közzététele
> * Teszt Power Virtuális Ügynök, választ kap a QnA Maker tudásbázisától

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Virtuális virtuális ügynök integrálása tudásbázissal

[A Power Virtual Agents](https://powervirtualagents.microsoft.com/) lehetővé teszi a csapatok számára, hogy könnyedén hozzanak létre hatékony robotokat egy irányított, kódnélküli grafikus felület segítségével anélkül, hogy adatszakértőkre vagy fejlesztőkre lenne szükség.

A Power Virtual Agent jön létre egy sor témakörök (tárgyi területek), annak érdekében, hogy válaszoljon a felhasználói kérdésekre műveletek végrehajtásával. Ha a válasz nem található, a rendszer tartalék visszaadhat választ.

Konfigurálja úgy az ügynököt, hogy a kérdést a témakör műveletének részeként vagy a **Rendszertartalék** témakör elérési útjának részeként küldje el a tudásbázisnak. Mindketten ugyanazt a műveletmechanizmust használják, hogy csatlakozzanak a tudásbázishoz, és választ adjanak vissza.

## <a name="power-automate-connects-to-generateanswer-action"></a>A Power Automate a GenerateAnswer művelethez csatlakozik

Ha az ügynököt a tudásbázisához szeretné csatlakoztatni, a Power Automate segítségével hozza létre a műveletet. A Power Automate **folyamatfolyamatot**biztosít, amely a QnA Maker GenerateAnswer API-jához csatlakozik.

Miután az **áramlást** megtervezték és mentette, power automate **megoldásból**érhető el.  Miután a GenerateAnswer folyamat ot hozzáadta a megoldáshoz, használja ezt a megoldást műveletként az ügynökben.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Az ügynök nek a tudásbázishoz való csatlakoztatásának lépései

A következő lépések áttekintésként jelennek meg, amelyek segítségével megismerheti, hogy a lépések hogyan kapcsolódnak a Virtuális virtuális ügynök qna maker tudásbázishoz való csatlakoztatásának céljához.

Virtuális energiaügynök és a QnA Maker használatának lépései:
* A [QnA Maker](https://www.qnamaker.ai/) portálon
    * Tudásbázis létrehozása és közzététele
    * Tudásbázis részleteinek másolása, beleértve a tudásbázis-azonosítót, a futásidejű végpontkulcsot és a futásidejű végpontgazdat.
* A [Power Virtual Agent](https://powerva.microsoft.com/) portálon
    * Ügynök témakörének létrehozása
    * Művelet hívása (a Power Automate Flow-hoz)
* A [Power Automate](https://us.flow.microsoft.com/) portálon
    * A [QnA Maker GenerateAnswer-hez](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/) csatlakozóval rendelkező folyamat létrehozása
        * A QnA Maker közzétette a tudásbázis adatait
            * Tudásbázis azonosítója
            * QnA Maker erőforrás-végpontgazda
            * QnA Maker erőforrás-végpontkulcs
        * Bemenet - felhasználói lekérdezés
        * Kimenet - tudásbázis válasz
    * Megoldás létrehozása és folyamat hozzáadása
* Visszatérés a virtuális ügynök höz
    * Válassza ki a megoldás kimenetét üzenetként a témakörhöz

## <a name="create-and-publish-a-knowledge-base"></a>Tudásbázis létrehozása és közzététele

1. Kövesse a [rövid útmutatót](../Quickstarts/create-publish-knowledge-base.md) a tudásbázis létrehozásához. Ne fejezze be az utolsó szakaszt, hogy hozzon létre egy bot. Ez az oktatóanyag helyettesíti a rövid útmutató utolsó szakaszát, mivel ez az oktatóanyag a Power Virtual Agent segítségével hoz létre egy robotot a Robotkeret robot helyett a rövid útmutatóban.

    > [!div class="mx-imgBorder"]
    > ![Adja meg a közzétett tudásbázis-beállításokat a [QnA Maker]https://www.qnamaker.ai/) (portál) **Beállítások** lapján.](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Erre az információra szüksége lesz a [Power Automate lépéshez](#create-power-automate-flow-to-connect-to-your-knowledge-base) a QnA Maker GenerateAnswer kapcsolat konfigurálásához.

1. Keresse meg a végpontkulcsot, a végpontállomást és a tudásbázis-azonosítót a QnA Maker portál **Beállítások** lapján.

## <a name="create-power-virtual-agent"></a>Virtuális energiaügynök létrehozása

1. Jelentkezzen be a Power Virtual Agent [alkalmazásba](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) iskolai vagy munkahelyi e-mail fiókjával.
1. Ha ez az első bot, akkor kell a **honlapon** az ügynök. Ha nem ez az első Power Virtual Agent, válassza ki a bot a jobb felső navigációs és válassza **+ Új bot**.

    > [!div class="mx-imgBorder"]
    > ![Adja meg a közzétett tudásbázis-beállításokat a [QnA Maker]https://www.qnamaker.ai/) (portál) **Beállítások** lapján.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Számos téma áll rendelkezésre a bot

Az ügynök a témakörgyűjtemény segítségével válaszol a tárgyterületen lévő kérdésekre. Ebben a bemutatóban az ügynök számos témakört biztosít az Ön számára, osztva **felhasználói témák** és a **rendszer témák**.

> [!div class="mx-imgBorder"]
> ![Az ügynök a témakörgyűjtemény segítségével válaszol a tárgyterületen lévő kérdésekre. Ebben az oktatóanyagban az ügynök számos témakört biztosít a **Felhasználói témákra** és **Rendszertémákra**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>A Power Virtual Agent rendszertartalék-témakörének létrehozása

Bár az ügynök bármely témakörből csatlakozhat a tudásbázishoz, ez az oktatóanyag a **Rendszertartalék témakört** használja. A tartalék témakör akkor használatos, ha az ügynök nem talál választ. Az ügynök továbbítja a felhasználó szövegét a QnA Maker GenerateAnswer API-jának, megkapja a választ a tudásbázisból, és üzenetként jeleníti meg a felhasználónak.

1. A [Virtuális energiagazdálkodási ügynökök](https://powerva.microsoft.com/#/) portálon a navigáció jobb felső sarkában válassza a **Beállítások** lapot. Az oldal ikonja a fogaskerék. Válassza **a Rendszertartalék lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Power Virtual ügynök menüpont a rendszer tartalék](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Az előugró **ablakok beállításai** ablakban válassza a **+ Hozzáadás** lehetőséget a Rendszertartalék témakör hozzáadásához.

    > [!div class="mx-imgBorder"]
    > ![A Beállítások ablakban adja hozzá a tartalék témakört.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. A témakör hozzáadása után válassza a **Go to Fallback témakört** a tartalék témakör szerzői a szerzői vásznon.

    > [!TIP]
    > Ha vissza kell térnie a Tartalék témakörhöz, az a **Rendszer** témaköreinek részeként a **Témakörök** szakaszban érhető el.

## <a name="use-authoring-canvas-to-add-an-action"></a>Művelet hozzáadása szerzői vászon használata

A Virtuális energiagazdálkodási ügynökök készítő vászon használatával csatlakoztathatja a tartalék témakört a tudásbázishoz. A témakör az **ismeretlen felhasználói szöveggel**kezdődik. Adjon hozzá egy műveletet, amely átadja a szöveget a QnA Makernek, majd a választ üzenetként jeleníti meg. A válasz megjelenítésének utolsó lépése az oktatóanyag későbbi részében [külön lépésként](#add-solutions-flow-to-power-virtual-agent) történik.

Ez a szakasz létrehozza a tartalék témakör beszélgetési folyamat.

1. Előfordulhat, hogy az új tartalék művelet már rendelkezik beszélgetési folyamatelemekkel. Törölje az **Eszkalálás** elemet a Beállítások menü kiválasztásával.

    > [!div class="mx-imgBorder"]
    > ![Tartalék művelet indítása eseményindító-kifejezésekkel](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Jelölje **+** ki az **Üzenet** mezőből áramló összekötőt, majd válassza **a Művelet hívása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Művelet felhívása](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Kattintson a **Folyamat létrehozása** elemre. A folyamat a **Power Automate**, egy másik böngésző-alapú portál.

    > [!div class="mx-imgBorder"]
    > ![Művelet felhívása](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Hozzon létre Power Automate Flow-t, hogy csatlakozzon a tudásbázishoz

Az alábbi eljárás létrehoz egy **Power Automate** folyamatot, amely:
* veszi a bejövő felhasználói szöveget
* elküldi a QnA Makernek
* hozzárendeli a QnA Maker felső válaszát egy változóhoz
* elküldi a változót (felső válasz) válaszként az ügynökének

1. A **Power Automate**alkalmazásban a **Folyamatsablon** elindul. A **virtuális energiaügynökök** folyamatelemében válassza a **Szerkesztés** lehetőséget az ügynöktől a tudásbázisba érkező bemeneti változó konfigurálásához. A szövegalapú beviteli változó az ügynök felhasználó által benyújtott szöveges kérdése.

    > [!div class="mx-imgBorder"]
    > ![A bemeneti változó konfigurálása szöveges karakterláncként](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adjon hozzá egy szövegbevitelt, és nevezze el a változót `InputText` a leírásával. `IncomingUserQuestion` Ez az elnevezés segít megkülönböztetni a bemeneti szöveget a később létrehozott kimeneti szövegtől.

    > [!div class="mx-imgBorder"]
    > ![Adjon hozzá egy szövegbevitelt, és nevezze el a "InputText" változót a "UserQuestion" leírásával](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Jelölje **+** ki a **Virtuális energiaügynökök** mezőből áramló összekötőt egy új lépés beszúrásához (mielőtt a **Visszatérési érték(ek) a virtuális energiaügynöknek**lenne), majd válassza **a Művelet hozzáadása**lehetőséget.

1. Keresse `Qna` meg a **QnA Maker-műveletek** megkereséséhez, majd válassza a Válasz **generálása lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Keressen rá a "Qna" kifejezésre a **QnA Maker** műveletek megkereséséhez, majd válassza a **Generate answer**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    A QnA Maker három (3) szükséges csatlakozási beállítása megjelenik a műveletben és a virtuális energiaügynök kérdésbeállításaiban.

    > [!div class="mx-imgBorder"]
    > ![A QnA Maker kapcsolatbeállításai megjelennek a műveletben.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Konfigurálja a műveletet a tudásbázis-azonosítóval, a végpontgazdagép-állomással és a végpontkulkkal. Ezek a tudásbázis **Beállítások** lapján, a QnA Maker portálon találhatók.

    > [!div class="mx-imgBorder"]
    > ![Adja meg a közzétett tudásbázis-beállításokat a [QnA Maker]https://www.qnamaker.ai/) (portál) **Beállítások** lapján.](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. A **Kérdés**beállításához jelölje ki a `InputText` szövegdobozt, majd a listából.

1. Ha új lépést szeretne beszúrni **+** a folyamatba, jelölje ki a **válasz létrehozása** műveletmezőből áramló összekötőt, majd kattintson **a Művelet hozzáadása gombra.**

1. A GenerateAnswer szolgáltatásból visszaadott válaszszöveg rögzítéséhez változóhozzáadásához keresse meg és jelölje ki a `Initialize variable` műveletet.

    Állítsa a változó nevét `OutgoingQnAAnswer`a beállításra, és válassza ki a **karakterláncot String**néven. Ne állítsa be az **Értéket**.

    > [!div class="mx-imgBorder"]
    > ![Állítsa a változó nevét "QnAAnswer" (QnAAnswer) karakterre, és válassza ki a **String** típust.](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Ha új lépést szeretne beszúrni **+** a folyamatba, jelölje ki a **változó inicializálása** műveletmezőből áramló összekötőt, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A teljes tudásbázis JSON-válaszának a változóra`Apply to each` való beállításához keresse meg és jelölje ki a műveletet. Válassza a `answers`GenerateAnswer lehetőséget .

1. Ha csak a legfelső választ szeretné visszaadni, ugyanabban az **Alkalmazás az egyes** mezőben válassza a Művelet **hozzáadása**lehetőséget. Keresse meg a Változó **beállítása parancsot,** és válassza a Változó beállítása lehetőséget.

    A **Változó beállítása** mezőben jelölje ki a **Név**mezőt, majd válassza a List ából a **KimenőqnAAnswer** elemet.

    Jelölje ki az **Érték**szövegmezőt, majd a listából válassza a **Válaszok válaszok lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![A változó nevének és értékének beállítása](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. A változó (és értéke) visszaadására jelölje ki a **Visszatérési értéket a virtuális ügynök folyamatelembe,** majd kattintson a **Szerkesztés gombra,** majd **a Kimenet hozzáadása**parancsra. Jelöljön ki egy **szövegkimeneti** típust, majd írja be a `FinalAnswer` **címét.** Jelölje ki az **Érték**szövegmezőjét, majd a `OutgoingQnAAnswer` változót.

    > [!div class="mx-imgBorder"]
    > ![A visszatérési érték beállítása](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. A Folyamat mentéséhez válassza a **Mentés** lehetőséget.

## <a name="create-solution-and-add-flow"></a>Megoldás létrehozása és folyamat hozzáadása

Annak érdekében, hogy a virtuális energiaügynök megtalálja és csatlakozzon a folyamathoz, a folyamatnak szerepelnie kell egy Power Automate megoldásban.

1. Miközben még mindig a Power Automate portálon tartózkodik, válassza a **megoldások lehetőséget** a bal oldali navigációs sávon.

1. Válassza az **+ Új megoldás** elemet.

1. Adjon meg egy megjelenítendő nevet. A megoldások listája a szervezet vagy az iskola minden megoldását tartalmazza. Válasszon olyan elnevezési konvenciót, amely segít a szűrésben, hogy `jondoe-power-virtual-agent-qnamaker-fallback`csak a megoldások, mint például az e-mail előrögzítése a megoldás neve: .

1. Válassza ki a közzétevőt a választási lehetőségek listájából.

1. Fogadja el a név és a verzió alapértelmezett értékeit.

1. A folyamat befejezéséhez válassza a **Létrehozás** gombot.

## <a name="add-flow-to-solution"></a>Folyamat hozzáadása a megoldáshoz

1. A megoldások listájában válassza ki az imént létrehozott megoldást. A lista élén kell lennie. Ha nem, keressen az e-mail neve alapján, amely a megoldás nevének része.

1. A megoldásban válassza a **+ Add existing**lehetőséget, majd válassza a **folyamat** elemet a listából.

1. Keresse meg a folyamatot, majd a **Hozzáadás gombra** a folyamat befejezéséhez. Ha sok folyamat van, tekintse meg a **Módosított** oszlopot a legutóbbi folyamat megkereséséhez.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Megoldás folyamatának hozzáadása a power virtual ügynökhöz

1. Térjen vissza a böngészőlapra a Power Virtual Agent segítségével. A szerzői vászonnak továbbra is nyitva kell lennie.

1. Jelölje **+** ki az összekötőt az **Üzenet** műveletmező alatt, ha új lépést szeretne beszúrni a folyamatba, majd válassza **a Művelet hívása**lehetőséget.

1. Az új műveletben válassza ki az **UnrecognizedTriggerPhrase**bemeneti értékét. Ezzel átadja a szöveget az ügynöktől a folyamathoz.

    > [!div class="mx-imgBorder"]
    > ![Az új műveletben válassza ki a **UnrecognizedTriggerPhrase**bemeneti értékét.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Jelölje **+** ki az **összekötőt** a Művelet mező ben, ha új lépést szeretne beszúrni a folyamatba, majd kattintson az **Üzenet megjelenítése gombra.**

1. Írja be az `Your answer is:`üzenet `FinalAnswer` szövegét , és jelölje ki környezeti változóként a helyi eszköztár funkciójával.

    > [!div class="mx-imgBorder"]
    > ![Írja be az üzenet szövegét és a "FinalAnswer" a Power Automate Flow.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Válassza a **Környezet eszköztármentés parancsát** a témakör szerzői vászonrészleteinek mentéséhez.

A végső vászon alább látható.

> [!div class="mx-imgBorder"]
> ![Végső ügynök vászon](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Virtuális ügynök tesztelése

1. A tesztablakban kapcsolja be a **Sávot a témakörök között**. Ez lehetővé teszi, hogy nézni a progresszió a témák között, valamint egy témát.

1. Tesztelje az ügynököt a felhasználói szöveg beírásával az alábbi sorrendben. A szerzői vászon zöld pipával jelenti a sikeres lépéseket.

|Kérdés sorrendje|Kérdések tesztelése|Cél|
|--|--|--|
|1|helló|Beszélgetés megkezdése|
|2|Nyitvatartási idő|Minta téma - konfigurálva az Ön számára anélkül, hogy további munkát az Ön részéről.|
|3|Igen|Válaszul a`Did that answer your question?`|
|4|Kiváló|Válaszul a`Please rate your experience.`|
|5|Igen|Válaszul a`Can I help with anything else?`|
|6|Mi az a tudásbázis?|Ez a kérdés elindítja a tartalék műveletet, amely elküldi a szöveget a tudásbázisnak, hogy válaszoljon, majd megjelenik a válasz. |

> [!div class="mx-imgBorder"]
> ![Végső ügynök vászon](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>A robot közzététele

Annak érdekében, hogy az ügynök elérhető legyen az iskola vagy szervezet minden tagja számára, közzé kell tennie azt.

1. Válassza a **közzététel** lehetőséget a bal oldali navigációs sávon, majd a Lapon válassza a **Közzététel** lehetőséget.

1. Próbálja ki a bot a demo honlapján, feltéve, mint egy link alatt a **Közzététel** gombra .

    Egy új weboldal nyílik meg a robottal. Kérdezze meg a bot ugyanazt a vizsgálati kérdést:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Végső ügynök vászon](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Ossza meg a bot

A bemutató webhely megosztásához konfiguráld csatornaként.

1. Válassza a Bal oldali navigációs sáv **Csatornák ezután** **történő** kezelése lehetőséget.

1. Válassza a **Demó webhelylehetőséget** a csatornák listájából.

1. Másolja a vágólapra a hivatkozást, és válassza a **Mentés lehetőséget.** Illessze be a bemutató webhelyére mutató hivatkozást az iskolája vagy a szervezet tagjainak küldött e-mailbe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a tudásbázissal, távolítsa el a QnA Maker-erőforrásokat az Azure Portalon.

## <a name="next-step"></a>Következő lépés

[Tudásbázis elemzésének lekérése](../How-To/get-analytics-knowledge-base.md)

További információk:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker csatlakozó](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) és [az összekötő beállításai](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)