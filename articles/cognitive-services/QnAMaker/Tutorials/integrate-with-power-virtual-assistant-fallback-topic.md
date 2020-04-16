---
title: 'Oktatóanyag: Integrálható a virtuális power ügynökökkel - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban javíthatja a tudásbázis minőségét az aktív tanulással. A meglévő kérdések eltávolítása vagy módosítása nélkül tekintse át, fogadja el vagy utasítsa el, illetve adja hozzá.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402819"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Oktatóanyag: Adja hozzá tudásbázisát a virtuális virtuális ügynökök höz
Hozzon létre és bővítsen egy [virtuális virtuális ügynökök robotot,](https://powervirtualagents.microsoft.com/) hogy válaszokat adjon a tudásbázisból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

<!-- green checkmark -->
> [!div class="checklist"]
> * Virtuális energiaügynökök robot létrehozása
> * Rendszertartalék-témakör létrehozása
> * QnA Maker hozzáadása műveletként egy témakörhöz Power Automate folyamatként
> * Power Automate megoldás létrehozása
> * Power Automate-folyamat hozzáadása a megoldáshoz
> * Virtuális nagy teljesítményű ügynökök közzététele
> * Tesztelje a virtuális teljesítményügynököket, és kapjon választ a QnA Maker tudásbázisától

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Ügynök integrálása tudásbázissal

[Power virtuális ügynökök](https://powervirtualagents.microsoft.com/) lehetővé teszi a csapatok, hogy hozzon létre erős botok segítségével irányított, no-code grafikus felület. Nincs szükség adatszakértőkre vagy fejlesztőkre.

A Virtuális power ügynökök, hozzon létre egy ügynök egy sor témakörök (tárgyi területek), annak érdekében, hogy válaszoljon a felhasználói kérdésekre műveletek végrehajtásával. Ha a válasz nem található, a rendszer tartalék visszaadhat választ.

Konfigurálja úgy az ügynököt, hogy a kérdést a témakör műveletének részeként vagy a *Rendszertartalék* témakör elérési útjának részeként küldje el a tudásbázisnak. Mindketten egy műveletsegítségével csatlakoznak a tudásbázishoz, és választ adnak vissza.

## <a name="power-automate-connects-to-generateanswer-action"></a>A Power Automate `GenerateAnswer` a művelethez csatlakozik

Ha az ügynököt a tudásbázisához szeretné csatlakoztatni, a Power Automate segítségével hozza létre a műveletet. A Power Automate folyamatfolyamatot biztosít, amely a `GenerateAnswer` QnA Maker API-jához csatlakozik.

Miután megtervezte és mentette a folyamatot, power automate megoldásból érhető el. Használja ezt a megoldást műveletként az ügynökében.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Ügynök csatlakoztatása a tudásbázishoz

Az alábbiakban áttekintést olvashat a power virtuális ügynökök ben lévő ügynökök nek a QnA Maker egyik tudásbázisához való csatlakoztatásához.

* A [QnA Maker](https://www.qnamaker.ai/) portálon:
    * Hozza létre és tegye közzé tudásbázisát.
    * Másolja a tudásbázis adatait, beleértve az azonosítót, a futásidejű végpontkulcsot és a futásidejű végpontgazdat.
* A [virtuális energiaügynökök portálon:](https://powerva.microsoft.com/)
    * Ügynöktémakör létrehozása.
    * Művelet hívása (a Power Automate flow-hoz).
* A [Power Automate](https://us.flow.microsoft.com/) portálon:
    * Hozzon létre egy folyamatot a [QnA Maker GenerateAnswer című üzenetéhez](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)csatlakozóval.
        * A QnA Maker közzétette a tudásbázis adatait:
            * Tudásbázis azonosítója
            * QnA Maker erőforrás-végpontgazda
            * QnA Maker erőforrás-végpontkulcs
        * Bemenet - felhasználói lekérdezés
        * Kimenet - tudásbázis válasz
    * Hozzon létre egy megoldást, és adja hozzá a folyamatot.
* Visszatérés a virtuális ügynökök áramellátásához:
    * Válassza ki a megoldás kimenetét egy témakör üzenetében.

## <a name="create-and-publish-a-knowledge-base"></a>Tudásbázis létrehozása és közzététele

1. Kövesse a [rövid útmutatót](../Quickstarts/create-publish-knowledge-base.md) a tudásbázis létrehozásához. Ne töltse ki az utolsó szakaszt a robot létrehozásáról. Ehelyett használja ezt az oktatóanyagot, hogy hozzon létre egy robot power virtuális ügynökök.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a tudásbázis közzétett beállításairól](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Adja meg a [QnA Maker](https://www.qnamaker.ai/) portál **Beállítások** lapján található közzétett tudásbázis-beállításokat. Erre az információra szüksége lesz a [Power Automate lépéshez](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) a QnA Maker-kapcsolat `GenerateAnswer` konfigurálásához.

1. A QnA Maker portál **Beállítások** lapján keresse meg a végpontkulcsot, a végpontállomást és a tudásbázis-azonosítót.

## <a name="create-an-agent-in-power-virtual-agents"></a>Ügynök létrehozása a virtuális energiaügynökök ben

1. [Jelentkezzen be a virtuális energiaügynökök bekapcsolása.](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) Használja iskolai vagy munkahelyi e-mail fiókját.
1. Ha ez az első bot, akkor a **honlapon** az ügynök. Ha nem ez az első robot, válassza ki a bot ot az oldal jobb felső részén, és válassza a **+ Új robot**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Virtuális teljesítményügynökök kezdőlapról](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Adja meg a közzétett tudásbázis-beállításokat, amelyek a [QnA Maker](https://www.qnamaker.ai/) portál **Beállítások** lapján találhatók.

## <a name="topics-provided-in-the-bot"></a>Témák a bot

Az ügynök a témakörgyűjtemény segítségével válaszol a tárgyterületen lévő kérdésekre. Ebben az oktatóanyagban az ügynök számos témakört biztosít az Ön számára, felhasználói témákra és rendszertémakörökre osztva.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az ügynökben található témakörökről](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>A rendszertartalék témakör létrehozása

Bár az ügynök bármely témakörből csatlakozhat a tudásbázishoz, ez az oktatóanyag a *rendszertartalék témakört* használja. A tartalék témakör akkor használatos, ha az ügynök nem talál választ. Az ügynök továbbítja a felhasználó szövegét a `GenerateAnswer` QnA Maker API-jának, megkapja a választ a tudásbázisból, és üzenetként jeleníti meg a felhasználónak.

1. A [Power Virtual Agents](https://powerva.microsoft.com/#/) portál jobb felső sarkában válassza a **Beállítások** (a fogaskerék ikon) lehetőséget. Ezután válassza **a Rendszertartalék lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Virtuális energiaminőségkezelők virtuális eszközök menüeleméről a Rendszertartalékhoz](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. A **+ Add lehetőséget** választva hozzáadhat egy tartalék témakört.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép egy tartalék témakör hozzáadásáról.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. A témakör hozzáadása után válassza a **Go to Fallback témakört** a tartalék témakör szerzői a szerzői vásznon.

    > [!TIP]
    > Ha vissza kell térnie a tartalék témakörhöz, az a **Rendszer** témaköreinek részeként a **Témakörök témakörei** szakaszban érhető el.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Művelet hozzáadása a szerzői vászon használatával

A Power Virtual Agents authoring canvas használatával csatlakoztathatja a tartalék témakört a tudásbázishoz. A témakör az ismeretlen felhasználói szöveggel kezdődik. Adjon hozzá egy műveletet, amely átadja a szöveget a QnA Makernek, majd a választ üzenetként jeleníti meg. A válasz megjelenítésének utolsó lépése külön [lépésként](#add-your-solutions-flow-to-power-virtual-agents)történik , az oktatóanyag későbbi részében.

Ez a szakasz létrehozza a tartalék témakör beszélgetési folyamat.

1. Előfordulhat, hogy az új tartalék művelet már rendelkezik beszélgetési folyamatelemekkel. Törölje az **Eszkalálás** elemet a **Beállítások** menü kiválasztásával.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Virtuális power ügynökök tartalék témaköréről](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Jelölje **+** ki az **Üzenet** mezőből áramló összekötőt, majd válassza **a Művelet hívása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: Művelet hívása](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Kattintson a **Folyamat létrehozása** elemre. A folyamat a Power Automate portálra vezet.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: Folyamat létrehozása](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Hozzon létre egy Power Automate folyamatot a tudásbázishoz való csatlakozáshoz

Az alábbi eljárás létrehoz egy Power Automate folyamatot, amely:
* Fogja a bejövő felhasználói szöveget, és elküldi a QnA Makernek.
* Hozzárendeli a QnA Maker felső válasz egy változó, és elküldi a változó (felső válasz) a válasz vissza az ügynök.

1. A **Power Automate**alkalmazásban a **Folyamatsablon** elindul. A **virtuális energiaügynökök** folyamatelemében válassza a **Szerkesztés** lehetőséget az ügynöktől a tudásbázisba érkező bemeneti változó konfigurálásához. A szövegalapú beviteli változó az ügynök felhasználó által benyújtott szöveges kérdése.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Power Automate beállításról, amely en a bemeneti változót szöveges karakterláncként konfigurálhatja](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adjon hozzá egy szövegbevitelt, és nevezze el a változót `InputText`a `IncomingUserQuestion`leírásával. Ez az elnevezés segít megkülönböztetni a bemeneti szöveget a később létrehozott kimeneti szövegtől.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Power Automate beállításról a bemeneti változó nevének és leírásának konfigurálásához](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. A **+** **Virtuális energiaügynökök** mezőből áramló összekötő kiválasztásával új lépést szúrhat be a folyamatba (a **Visszatérési érték(ek) a virtuális energiaügynökhöz.** Ezután válassza **a Művelet hozzáadása**lehetőséget.

1. Keresse `Qna` meg a **QnA** Maker-műveleteket, és válassza **a Válasz generálása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: Válasz generálása](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    A QnA Maker szükséges csatlakozási beállításai megjelennek a műveletben és az ügynök kérdésbeállításaiban.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a szükséges csatlakozási beállításokról](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Konfigurálja a műveletet a tudásbázis-azonosítóval, a végpontállomással és a végpontkulkkal. Ezek a tudásbázis **Beállítások** lapján, a QnA Maker portálon találhatók.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a tudásbázis közzétett beállításairól](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. A **Kérdés**beállításához jelölje ki a szövegdobozt, majd a `InputText` listából.

1. Ha új lépést szeretne beszúrni **+** a folyamatba, jelölje ki a **válasz generálása** műveletmezőből áramló összekötőt. Ezután válassza **a Művelet hozzáadása**lehetőséget.

1. Ha változót szeretne hozzáadni a `GenerateAnswer`visszaadott válaszszöveg `Initialize variable` rögzítéséhez, keresse meg és jelölje ki a műveletet.

    Állítsa a változó nevét `OutgoingQnAAnswer`a beállításra, és válassza ki a **karakterláncot String**néven. Ne állítsa be az **Értéket**.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a kimeneti változó inicializálásáról](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Ha új lépést szeretne beszúrni **+** a folyamatba, jelölje ki az **Inicializálás változó** műveletmezőjéből áramló összekötőt. Ezután válassza **a Művelet hozzáadása**lehetőséget.

1. A teljes tudásbázis JSON-válaszának a változóra`Apply to each` való beállításához keresse meg és jelölje ki a műveletet. Válassza `GenerateAnswer` `answers`ki a lehetőséget.

1. Ha csak a legfelső választ szeretné visszaadni, ugyanabban az **Alkalmazás az egyes** mezőben válassza a Művelet **hozzáadása**lehetőséget. Keresse meg a Változó **beállítása parancsot,** és válassza a Változó beállítása lehetőséget.

    A **Változó beállítása** mezőben jelölje ki a **Név**mezőt, majd válassza a List ából a **KimenőqnAAnswer** elemet.

    Jelölje ki az **Érték**szövegmezőt, majd a listából válassza a **Válaszok válaszok** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: a változó nevének és értékének beállítása](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. A változó (és értéke) visszaadására válassza a **Visszatérési érték(ek)et** a Virtuális ügynök folyamatelembe. Ezután válassza **a** > **Kimenet hozzáadása**lehetőséget. Jelöljön ki egy **szövegkimeneti** típust, majd írja be a `FinalAnswer` **címet.** Jelölje ki az **Érték**szövegmezőjét, `OutgoingQnAAnswer` majd a változót.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a visszatérési érték beállításáról](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. A folyamat mentéséhez válassza a **Mentés** lehetőséget.

## <a name="create-a-solution-and-add-the-flow"></a>Megoldás létrehozása és a folyamat hozzáadása

Ahhoz, hogy az ügynök megtalálja és csatlakozzon az áramláshoz, az áramlást tartalmaznia kell egy Power Automate megoldásban.

1. Miközben még mindig a Power Automate portálon tartózkodik, válassza a **megoldások lehetőséget** a bal oldali navigációs sávon.

1. Válassza az **+ Új megoldás** elemet.

1. Adjon meg egy megjelenítendő nevet. A megoldások listája a szervezet vagy az iskola minden megoldását tartalmazza. Válasszon egy elnevezési konvenciót, amely segít a szűrést, hogy csak a megoldásokat. Előfordulhat például, hogy az e-mailt `jondoe-power-virtual-agent-qnamaker-fallback`előtaggal rögzíti a megoldás nevére: .

1. Válassza ki a közzétevőt a választási lehetőségek listájából.

1. Fogadja el a név és a verzió alapértelmezett értékeit.

1. A folyamat befejezéséhez válassza a **Létrehozás** gombot.

## <a name="add-your-flow-to-the-solution"></a>Adja hozzá az áramlást a megoldáshoz

1. A megoldások listájában válassza ki az imént létrehozott megoldást. A lista élén kell lennie. Ha nem, keressen az e-mail neve alapján, amely a megoldás nevének része.

1. A megoldásban válassza a **+ Létező hozzáadása**lehetőséget, majd válassza a **folyamat** elemet a listából.

1. Keresse meg a folyamatot, majd a **Hozzáadás gombra** a folyamat befejezéséhez. Ha sok folyamat van, tekintse meg a **Módosított** oszlopot a legutóbbi folyamat megkereséséhez.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>A megoldás folyamatának hozzáadása a virtuális energiaügynökökhöz

1. Térjen vissza a böngészőlapra az ügynökével a Virtuális power ügynökökben. A szerzői vászonnak továbbra is nyitva kell lennie.

1. Ha új lépést szeretne beszúrni **Message** a folyamatba, **+** az Üzenet műveletmező alatt jelölje ki az összekötőt. Ezután válassza **a Művelet hívása**lehetőséget.

1. Az új műveletben válassza ki az **UnrecognizedTriggerPhrase**bemeneti értékét. Ezzel átadja a szöveget az ügynöktől a folyamathoz.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: A virtuális energiaügynökök lehetőség az ismeretlen eseményindító kifejezés kiválasztásához](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Ha új lépést szeretne beszúrni **Action** a folyamatba, **+** a Művelet mező alatt jelölje ki az összekötőt. Ezután válassza **az Üzenet megjelenítése**lehetőséget.

1. Írja be az `Your answer is:`üzenet szövegét, . Környezeti `FinalAnswer` változóként jelöljön ki a helyben lévő eszköztár funkciójával.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép: Virtuális energiaügynökök lépnek be az üzenet szövegére](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. A környezet eszköztárán válassza a **Mentés**lehetőséget a témakör szerzői vászonrészleteinek mentéséhez.

Így néz ki az utolsó ügynökvászon.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a végső ügynök vászonról](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Az ügynök tesztelése

1. A tesztablakban kapcsolja be a **Sávot a témakörök között**. Ez lehetővé teszi, hogy nézni a progresszió a témák között, valamint egy adott témakörben.

1. Tesztelje az ügynököt a felhasználói szöveg megadásával a következő sorrendben. A szerzői vászon zöld pipával jelenti a sikeres lépéseket.

    |Kérdés sorrendje|Kérdések tesztelése|Cél|
    |--|--|--|
    |1|helló|Beszélgetés megkezdése|
    |2|Nyitvatartási idő|Minta téma. Ez az Ön számára van konfigurálva, anélkül, hogy további munkát végezne az Ön részéről.|
    |3|Igen|Válaszul a`Did that answer your question?`|
    |4|Kiváló|Válaszul a`Please rate your experience.`|
    |5|Igen|Válaszul a`Can I help with anything else?`|
    |6|Mi az a tudásbázis?|Ez a kérdés elindítja a tartalék műveletet, amely elküldi a szöveget a tudásbázisnak, hogy válaszoljon. Ezután megjelenik a válasz. |

> [!div class="mx-imgBorder"]
> ![Képernyőkép a végső ügynök vászonról](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>A robot közzététele

Ahhoz, hogy az ügynök elérhető legyen az iskola vagy szervezet minden tagja számára, közzé kell tennie azt.

1. A bal oldali navigációs sávon válassza a **Közzététel**lehetőséget. Ezután válassza a **Közzététel** lehetőséget a lapon.

1. Próbálja ki a bot a demo honlapján (keresse meg a linket **a Közzététel**).

    Egy új weboldal nyílik meg a robottal. Kérdezze meg a bot ugyanazt a vizsgálati kérdést:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a végső ügynök vászonról](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Ossza meg a bot

A bemutató webhely megosztásához konfiguráld csatornaként.

1. A bal oldali navigációs sávon válassza a**Csatornák** **kezelése** > lehetőséget.

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