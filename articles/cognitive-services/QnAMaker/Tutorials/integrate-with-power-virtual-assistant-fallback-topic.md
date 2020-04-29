---
title: 'Oktatóanyag: integrálás a Power Virtual agentekkel – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban az aktív tanulással fejlesztheti tudásbázisának minőségét. Tekintse át, fogadja el vagy utasítsa el, vagy vegye fel a meglévő kérdések eltávolítása vagy módosítása nélkül.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81402819"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Oktatóanyag: a Tudásbázis hozzáadása a virtuális ügynökökhöz
Hozzon létre és terjesszen ki egy [Power Virtual Agents](https://powervirtualagents.microsoft.com/) -robotot, hogy válaszokat nyújtson a Tudásbázisból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

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

## <a name="power-automate-connects-to-generateanswer-action"></a>A Power gyorsbüfé csatlakozik `GenerateAnswer` a művelethez

Ha az ügynököt a tudásbázishoz szeretné kapcsolni, a művelet létrehozásához használja a Power Gyorsbüfét. A Power automatizál egy folyamatot biztosít, amely a QnA Maker `GenerateAnswer` API-hoz csatlakozik.

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
    * Hozzon létre egy folyamatot egy összekötővel [QnA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker közzétett Tudásbázis-információk:
            * Tudásbázis-azonosító
            * Erőforrás-végponti gazdagép QnA Maker
            * QnA Maker erőforrás-végpont kulcsa
        * Bemeneti – felhasználói lekérdezés
        * Kimenet – Tudásbázis-válasz
    * Hozzon létre egy megoldást, és adja hozzá a folyamatot.
* Visszatérés a Power Virtual Agent Ügynökökbe:
    * Válassza ki a megoldás kimenetét üzenetként egy témakörhöz.

## <a name="create-and-publish-a-knowledge-base"></a>Tudásbázis létrehozása és közzététele

1. Egy Tudásbázis [létrehozásához kövesse a rövid](../Quickstarts/create-publish-knowledge-base.md) útmutató lépéseit. Ne fejezze be az utolsó szakaszt a robot létrehozásával kapcsolatban. Ehelyett ezt az oktatóanyagot használva hozzon létre egy robotot a Power Virtual Agents használatával.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a közzétett Tudásbázis-beállításokról](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Adja meg a közzétett Tudásbázis-beállításokat a [QnA Maker](https://www.qnamaker.ai/) portál **Beállítások** lapján. Ezekre az információkra szüksége lesz a QnA Maker `GenerateAnswer` -kapcsolatok konfigurálásához szükséges [energiagazdálkodási lépésekhez](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) .

1. A QnA Maker portál **Beállítások** lapján keresse meg a végpont kulcsát, a végpont gazdagépét és a Tudásbázis azonosítóját.

## <a name="create-an-agent-in-power-virtual-agents"></a>Ügynök létrehozása a Power Virtual Agents szolgáltatásban

1. [Jelentkezzen be a Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)szolgáltatásba. Használja az iskolai vagy munkahelyi e-mail-fiókját.
1. Ha ez az első robot, az ügynök **kezdőlapján** fog megjelenni. Ha ez nem az első robotja, válassza ki a robotot a lap jobb felső területén, és válassza az **+ új robot**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A Power Virtual Agents kezdőlapjának képernyőképe](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Adja meg a közzétett Tudásbázis-beállításokat a [QnA Maker](https://www.qnamaker.ai/) portál **Beállítások** lapján.

## <a name="topics-provided-in-the-bot"></a>A robotban megadott témakörök

Az ügynök a témakör gyűjtemény használatával válaszol a tárgy területén található kérdésekre. Ebben az oktatóanyagban az ügynök számos témakört tartalmaz, amelyeket felhasználói témakörökbe és rendszertémakörökbe oszthat meg.

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

    > [!div class="mx-imgBorder"]
    > ![A Power Virtual Agents tartalék témakör képernyőképe](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Válassza ki **+** az összekötőt az **üzenet** mezőben, majd válassza a **művelet meghívása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a művelet meghívásáról](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Kattintson a **Folyamat létrehozása** elemre. A folyamat végigvezeti a Power automatizáló portálon.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a folyamat létrehozásáról](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>A tudásbázishoz való kapcsolódáshoz hozzon létre egy automatizáló energiagazdálkodási folyamatot

Az alábbi eljárás egy olyan energiagazdálkodási folyamatot hoz létre, amely:
* Beolvassa a bejövő felhasználó szövegét, és elküldi a QnA Makernak.
* Hozzárendeli a QnA Maker legfelső szintű választ egy változóhoz, és elküldi a változót (a legjobb választ) az ügynöknek küldött válaszként.

1. A **Power gyorsbüféban**a **flow-sablon** el lesz indítva. A **Power Virtual Agents** flow elemnél válassza a **Szerkesztés** lehetőséget az ügynöktől a tudásbázisba érkező bemeneti változó konfigurálásához. A Text-alapú bemeneti változó az ügynöktől származó, felhasználó által küldött szöveges kérdés.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel a Power automatizálási lehetőségről szöveges karakterláncként a bemeneti változó konfigurálásához](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adjon hozzá egy szövegbevitelt, és nevezze el `InputText`a változót a leírásával `IncomingUserQuestion`. Ez a Névadás segít megkülönböztetni a bemeneti szöveget a később létrehozott kimeneti szövegtől.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Power automatizálási lehetőségről a bemeneti változó nevének és leírásának konfigurálásához](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Válassza ki **+** a **Power Virtual Agents (energiaellátási virtuális ügynökök** ) mezőben, hogy új lépést helyezzen be a folyamatba (mielőtt a visszaadott **érték (eke) t bekapcsolja a virtuális ügynöknek**). Ezután válassza **a művelet hozzáadása**lehetőséget.

1. `Qna` Keresse meg a **QnA Maker** műveleteket, majd válassza a **Válasz előállítása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A válasz létrehozásának képernyőképe](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    A QnA Maker szükséges kapcsolati beállításai megjelennek a műveletben és a kérdéses beállításokban az ügynöktől.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel a szükséges kapcsolatbeállításokat](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Konfigurálja a műveletet a Tudásbázis-AZONOSÍTÓval, a végpont-gazdagéptel és a végponti kulccsal. Ezek a Tudásbázis **Beállítások** lapján találhatók a QnA Maker portálon.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a közzétett Tudásbázis-beállításokról](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. A **kérdés**konfigurálásához jelölje ki a szövegmezőt, majd válassza ki a `InputText` elemet a listából.

1. Ha új lépést szeretne beszúrni a folyamatba, válassza **+** ki az összekötőt a **Válasz létrehozása** műveletből. Ezután válassza **a művelet hozzáadása**lehetőséget.

1. Ha hozzá szeretne adni egy változót a kapott válasz szövegének rögzítéséhez `GenerateAnswer`, keresse meg `Initialize variable` és válassza ki a műveletet.

    Állítsa be a változó nevét a értékre `OutgoingQnAAnswer`, és válassza ki a típust **karakterláncként**. Ne állítsa be az **értéket**.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a kimeneti változó inicializálásáról](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Új lépés a folyamatba való beszúrásához válassza ki az **+** összekötőt a **változó inicializálása** műveletből. Ezután válassza **a művelet hozzáadása**lehetőséget.

1. Ha a teljes Tudásbázis JSON-válaszát szeretné beállítani a változóra, keresse meg és`Apply to each` válassza ki a műveletet. Válassza a `GenerateAnswer` `answers`elemet.

1. Ha csak a legfelső szintű választ szeretné visszaadni, ugyanez **vonatkozik az egyes** mezőkre, majd válassza a **művelet hozzáadása**lehetőséget. Keresse meg és válassza ki a **változó beállítása**elemet.

    A **set változó** mezőben jelölje ki a **név**szövegmezőt, majd a listából válassza a **OutgoingQnAAnswer** lehetőséget.

    Jelölje ki a szövegmezőt az **érték**mezőben, majd válassza a **Válasz** választ a listából.

    > [!div class="mx-imgBorder"]
    > ![A változó nevének és értékének beállítására szolgáló képernyőkép](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. A változó (és a hozzá tartozó érték) visszaadásához válassza ki a visszaadott **érték (eke) t a virtuális ügynök** flow eleméhez. Ezután válassza a **Szerkesztés** > **kimenet hozzáadása**elemet. Válasszon ki egy **szöveges** kimeneti típust, majd adja meg a `FinalAnswer` **címét** . Jelölje ki az **érték**szövegmezőjét, majd válassza ki a `OutgoingQnAAnswer` változót.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a visszatérési érték beállításáról](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. A folyamat mentéséhez válassza a **Mentés** lehetőséget.

## <a name="create-a-solution-and-add-the-flow"></a>Megoldás létrehozása és a folyamat hozzáadása

Ahhoz, hogy az ügynök megtalálja és csatlakozhasson a folyamathoz, a folyamatnak szerepelnie kell egy automatizálási megoldásban.

1. Miközben továbbra is a Power automatizáló portálon, válassza a **megoldások** lehetőséget a bal oldali navigációs sávon.

1. Válassza az **+ Új megoldás** elemet.

1. Adjon meg egy megjelenítendő nevet. A megoldások listája a szervezet vagy az iskola minden megoldását tartalmazza. Válasszon egy elnevezési konvenciót, amely segít a megoldásokban való szűrésben. Például megadhatja az e-mail-címet a megoldás neveként `jondoe-power-virtual-agent-qnamaker-fallback`:.

1. Válassza ki a közzétevőt a lehetőségek listájából.

1. Fogadja el a név és a verzió alapértelmezett értékeit.

1. A folyamat befejezéséhez válassza a **Létrehozás** lehetőséget.

## <a name="add-your-flow-to-the-solution"></a>Folyamat hozzáadása a megoldáshoz

1. A megoldások listájában válassza ki az imént létrehozott megoldást. A lista tetején kell lennie. Ha nem, keresse meg az e-mail-nevet, amely a megoldás nevének részét képezi.

1. A megoldásban válassza a **+ meglévő hozzáadása**lehetőséget, majd válassza a **folyamat** lehetőséget a listából.

1. Keresse meg a folyamatot, majd válassza a **Hozzáadás** lehetőséget a folyamat befejezéséhez. Ha sok folyamat van, tekintse meg a **módosított** oszlopot a legutóbbi folyamat megkereséséhez.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>A megoldás folyamatának hozzáadása a virtuális ügynökökhöz

1. Térjen vissza a böngésző lapra az ügynökével a Power Virtual Agents szolgáltatásban. A szerzői vászonnak továbbra is nyitva kell lennie.

1. Ha új lépést szeretne beszúrni a folyamatba, az **üzenet** művelete mezőben válassza ki **+** az összekötőt. Ezután válassza **a művelet meghívása**lehetőséget.

1. Az új művelet területen válassza ki a **UnrecognizedTriggerPhrase**bemeneti értékét. Ezzel átadja a szöveget az ügynöktől a folyamatnak.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Power Virtual Agents lehetőségről a nem felismerhető trigger kifejezés kiválasztásához](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Ha új lépést szeretne beszúrni a folyamatba, a **művelet** mezőben válassza ki az **+** összekötőt. Ezután válassza **az üzenet megjelenítése**lehetőséget.

1. Adja meg az üzenet szövegét `Your answer is:`. A `FinalAnswer` helyi eszköztár funkciójának használatával válassza ki a környezeti változót.

    > [!div class="mx-imgBorder"]
    > ![Az üzenet szövegének megadásához a Power Virtual Agents lehetőség képernyőképe](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. A környezeti eszköztáron válassza a **Mentés**lehetőséget, hogy mentse a szerzői vászon részleteit a témakörben.

Így néz ki a végső ügynök vászonja.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a végleges ügynök vászonról](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Az ügynök tesztelése

1. A teszt ablaktáblán állítsa be a **témakörök közötti**váltást. Ez lehetővé teszi, hogy megtekintse a témakörök közötti előrehaladást, valamint egy adott témakört.

1. Tesztelje az ügynököt úgy, hogy a következő sorrendben írja be a felhasználói szöveget. A szerzői vászon a sikeres lépéseket zöld pipa jelzi.

    |Kérdés sorrendje|Kérdések tesztelése|Cél|
    |--|--|--|
    |1|helló|Beszélgetés indítása|
    |2|Tárolási idő|Példa témakörre. Ez úgy van konfigurálva, hogy az Ön részéről további munka nélkül működjön.|
    |3|Igen|Válasz címzettje`Did that answer your question?`|
    |4|Kiváló|Válasz címzettje`Please rate your experience.`|
    |5|Igen|Válasz címzettje`Can I help with anything else?`|
    |6|Mi az a Tudásbázis?|Ez a kérdés elindítja a visszalépési műveletet, amely választ küld a szövegnek a tudásbázisba. Ezután megjelenik a válasz. |

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a végleges ügynök vászonról](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>A robot közzététele

Ahhoz, hogy az ügynök elérhető legyen az iskola vagy szervezet összes tagja számára, közzé kell tennie.

1. A bal oldali navigációs sávon válassza a **Közzététel**lehetőséget. Ezután válassza a **Közzététel** elemet az oldalon.

1. Próbálja ki a robotot a bemutató webhelyén (keresse meg a **Közzététel**alatt található hivatkozást).

    Megnyílik egy új weblap a robottal. Kérdezze meg a robotot ugyanazzal a tesztelési kérdéssel:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel a végleges ügynök vászonról](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>A robot megosztása

A bemutató webhely megosztásához konfigurálja a csatornát csatornaként.

1. A bal oldali navigációs sávon válassza a csatornák **kezelése** > **Channels**lehetőséget.

1. Válassza ki a **bemutató webhelyet** a csatornák listából.

1. Másolja a hivatkozást, és válassza a **Mentés**lehetőséget. Illessze be a bemutató webhelyére mutató hivatkozást egy e-mailbe az iskolájában vagy a szervezet tagjainak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a tudásbázissal, távolítsa el a Azure Portal QnA Maker erőforrásait.

## <a name="next-step"></a>Következő lépés

[Tudásbázis elemzésének lekérése](../How-To/get-analytics-knowledge-base.md)

További információk:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker-összekötő](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) és az [összekötő beállításai](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)