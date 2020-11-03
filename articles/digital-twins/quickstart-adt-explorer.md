---
title: Rövid útmutató – példa a forgatókönyvre
titleSuffix: Azure Digital Twins
description: Rövid útmutató – a ADT Explorer minta használatával megjelenítheti és feltárhatja az előre elkészített forgatókönyvet.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242279"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Gyors útmutató – a minta Azure digitális Twins-forgatókönyvek megismerése a ADT Explorer használatával

Az Azure Digital Twins segítségével valós környezetei élő modelljeit hozhat létre és kezelhet. Ezt úgy teheti meg, hogy az egyes elemeket **digitális ikrekként** modellezi, majd összekapcsolja őket egy **olyan Tudásbázisban, amely** képes reagálni az élő eseményekre, és információkat kér le.

Ebben a rövid útmutatóban egy előre elkészített Azure digitális Twins-gráfot fog felfedezni, amely az [**Azure Digital Twins (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)nevű minta alkalmazás segítségével készült. A ADT Explorer lehetővé teszi egy környezet digitális megjelenítését, megtekintheti az ikrek és a gráf vizuális képeit, amelyek az Azure Digital Twins környezetének ábrázolására készültek, és más felügyeleti tevékenységeket végezhetnek egy böngészőalapú, vizuális felülettel.

A rövid útmutató a következő főbb lépéseket tartalmazza:

1. Azure digitális Twins-példány és ADT Explorer beállítása
1. Előre elkészített modellek és gráf-adatok feltöltése a minta létrehozásához
1. A létrehozott forgatókönyv-gráf megismerése
1. Módosítsa a gráfot

A példában szereplő gráf két emeleten és két szobával működő épületet jelöl. A gráf így fog kinézni:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva.":::

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, **[hozzon létre egyet most ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

A gépen **Node.js** is szüksége lesz. A legújabb verziót a következő hivatkozásra kattintva érheti el: [Node.js](https://nodejs.org/).

Végül le kell töltenie azt a mintát, amelyet a rövid útmutatóban is használni fog: a **ADT Explorer** -minta alkalmazás. Ez a minta tartalmazza a rövid útmutatóban használt alkalmazást, amely egy Azure digitális Twins-forgatókönyv betöltésére és vizsgálatára, valamint a minta forgatókönyvek fájljaira vonatkozik. A minta beszerzéséhez navigáljon ide: [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). A letöltéshez kattintson a *zip letöltése* gombra *.* A MINTAKÓD zip-fájlja a gépre. Ezzel letölti a-t. A ZIP-mappát _**Azure_Digital_Twins__ADT__explorer.zipként**_ . Bontsa ki a mappát, és bontsa ki a fájlokat.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Az Azure digitális Twins és a ADT Explorer beállítása

Az Azure Digital Twins használatának első lépése **egy Azure digitális Twins-példány beállítása** . Miután létrehozta a szolgáltatás egy példányát, és **beállította a hitelesítő adatait** a ADT Explorerrel való hitelesítéshez, a **ADT Explorerben csatlakozhat a példányhoz** , és feltöltheti azt a rövid útmutatóban szereplő adatokkal.

A szakasz további részében végigvezeti a lépéseket.

### <a name="set-up-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Helyi Azure-beli hitelesítő adatok beállítása

A ADT Explorer alkalmazás a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal, amikor futtatja azt a helyi gépen. További információk az ügyfélalkalmazások az Azure Digital Twins szolgáltatással való hitelesítésének különböző módjairól [*: How-to: Write app Authentication Code*](how-to-authenticate-client.md).

Az ilyen típusú hitelesítés esetében a ADT Explorer a helyi környezetben, például egy helyi [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -ben vagy a Visual Studio/Visual Studio Code-ban található hitelesítő adatokat keres. Ez azt jelenti, hogy **helyileg kell bejelentkeznie az Azure-ba** ezen mechanizmusok egyikével a ADT Explorer alkalmazás hitelesítő adatainak beállításához.

Ha már bejelentkezett az Azure-ba az egyik ilyen módon, ugorjon a [következő szakaszra](#run-and-configure-adt-explorer).

Ellenkező esetben a helyi **Azure CLI** -t a következő lépésekkel telepítheti:
1. Kövesse a telepítési [**hivatkozás**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) folyamatát az operációs rendszerének megfelelő telepítés befejezéséhez.
2. Nyisson meg egy konzolablak ablakot a gépen.
3. Futtassa `az login` a parancsot, és kövesse a hitelesítési kéréseket, és jelentkezzen be az Azure-fiókjába.

Ennek elvégzése után a ADT Explorer automatikusan felveszi az Azure-beli hitelesítő adatait, amikor a következő szakaszban futtatja.

Ha szeretné, zárja be a hitelesítési konzol ablakát, vagy tartsa megnyitva a következő lépésben való használatra.

### <a name="run-and-configure-adt-explorer"></a>A ADT Explorer futtatása és konfigurálása

Ezután futtassa a ADT Explorer alkalmazást, és konfigurálja az Azure Digital Twins-példányhoz.

Navigáljon a letöltött és kibontott _**Azure_Digital_Twins__ADT__explorer**_ mappához. Nyisson meg egy konzol ablakot a mappa helyére *Azure_Digital_Twins__ADT__explorer/Client/src* .

A futtatásával `npm install` töltse le az összes szükséges függőséget.

Ezután indítsa el az alkalmazást a futtatásával `npm run start` .

Néhány másodperc elteltével megnyílik egy böngészőablak, és az alkalmazás megjelenik a böngészőben.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Az ablak tetején található *Bejelentkezés* gombra kattintva (az alábbi képen látható) megadhatja, hogy a ADT Explorer működjön a beállított példánnyal. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Adja meg az [Előfeltételek](#prerequisites) szakaszban korábban összegyűjtött *Azure digitális Twins-példány URL-címét* a *https://{instance Host Name}* formátumban.

>[!NOTE]
> Bármikor újra felkeresheti vagy szerkesztheti ezeket az adatokat. Ehhez jelölje ki ugyanazt az ikont, ha újra fel szeretné húzni a bejelentkezési mezőt. A rendszer megtartja a beadott értékeket.

> [!TIP]
> Ha a `SignalRService.subscribe` csatlakozáskor hibaüzenet jelenik meg, győződjön meg arról, hogy az Azure digitális Twins URL-címe a *https://* -vel kezdődik.

Ha megjelenik a Microsoft előugró ablakát *kérő engedély* , adja meg az alkalmazás jóváhagyását, és fogadja el a folytatást.

## <a name="add-the-sample-data"></a>A mintaadatok hozzáadása

Ezután importálni fogja a minta forgatókönyvet és a Graphot a ADT Explorerben. A minta forgatókönyv a korábban letöltött **Azure_Digital_Twins__ADT__explorer** mappában is található.

### <a name="models"></a>Modellek

Az Azure digitális ikrek megoldásának első lépéseként definiálja a környezet szókincsét. Ez egyéni [**modellek**](concepts-models.md)létrehozásával történik, amelyek leírják a környezetben létező entitások típusait. 

Minden modell egy **Digital Twin Definition Language (DTDL)** nevű JSON-ld-szerű nyelven íródott, és a *Tulajdonságok* , *telemetria* , *kapcsolatok* és *összetevők* tekintetében egyetlen típusú entitást ír le. Később ezeket a modelleket fogja használni a digitális ikrek alapjaként, amelyek az ilyen típusú példányokat jelölik.

A modell létrehozásakor általában három lépést kell végrehajtania:
1. Írja meg a modell definícióját (a rövid útmutatóban, amely már a mintaoldat részeként lett elvégezve)
2. Ellenőrizze, hogy a szintaxis helyes-e (a rövid útmutatóban, amely már a mintaoldat részeként lett elvégezve)
3. Töltse fel az Azure Digital Twins-példányba
 
Ebben a rövid útmutatóban a modell fájljait már megírtuk és érvényesítjük az Ön számára, és beletartoznak a letöltött megoldásba. Ebben a szakaszban két előre megírt modellt fog feltölteni a példányra az építési környezet ezen összetevőinek definiálásához:
* Floor
* Szoba

#### <a name="upload-models"></a>Modellek feltöltése

A *modell nézet* mezőben nyomja meg a *modell feltöltése* ikont.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. A megjelenő fájl kiválasztása mezőben navigáljon a letöltött adattár *Azure_Digital_Twins__ADT__explorer/Client/examples* mappájába.
2. Válassza a *Room.js* be és *Floor.js* be lehetőséget, majd kattintson az OK gombra. (Ha szeretné, további modelleket is feltölthet, de ez a rövid útmutatóban nem használható.)
3. Az Azure-fiókba való bejelentkezéshez kattintson a felugró ablakra.

>[!NOTE]
>Ha a következő hibaüzenet jelenik meg: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." border="false"::: 
> Próbálja meg letiltani a felugró ablakok blokkolása vagy egy másik böngésző használatával.

A ADT Explorer ekkor feltölti ezeket a modell-fájlokat az Azure Digital Twins-példányba. Meg kell jelennie a *modell nézet* mezőben, amely megjeleníti a felhasználóbarát neveket és a teljes modell azonosítóit. A *modellre* vonatkozó információk megtekintése buborékok lehetőségre kattintva megtekintheti a mögöttes DTDL-kódot.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Ikrek és a Twin gráf

Most, hogy egyes modellek fel lettek töltve az Azure Digital Twins-példányba, hozzáadhat [**digitális ikreket**](concepts-twins-graph.md) , amelyek követik a modell definícióit. 

A digitális Twins az üzleti környezet tényleges entitásait képviseli: olyan dolgok, mint például az érzékelők a farmon, a fények egy autóban, vagy – ebben a rövid útmutatóban – az épületen belüli szobák. Az adott típusú típusok (például több, a *terem* modelljét használó szobák) közül sok ikreket hozhat létre, és összekapcsolhatók a kapcsolattal a teljes környezetet jelképező **kettős gráfban** .

Ebben a szakaszban fel kell töltenie az előre létrehozott ikreket, amelyek egy előre létrehozott gráfhoz kapcsolódnak. A gráf két emeletet és két szobát tartalmaz, amelyek a következő elrendezésben vannak csatlakoztatva:
* *Floor0*
    - *Room0* tartalmaz
* *Floor1*
    - *Room1* tartalmaz

#### <a name="import-the-graph"></a>A gráf importálása

A *gráf nézet* mezőben kattintson a *diagram importálása* ikonra.

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/import-graph.png":::

A fájl kiválasztása mezőben navigáljon a *Azure_Digital_Twins__ADT__explorer/Client/examples* mappára, és válassza ki a _**buildingScenario.xlsx**_ számolótábla-fájlt. Ez a fájl tartalmazza a minta gráf leírását. Nyomja meg az OK gombot.

Néhány másodperc elteltével a ADT Explorer megnyílik egy *importálási* nézet, amely megjeleníti a betöltendő gráf előnézetét.

A gráf feltöltésének megerősítéséhez nyomja meg a *diagram nézet* jobb felső sarkában található *Save (Mentés* ) ikont:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A ADT Explorer most a feltöltött fájllal hozza létre a kért ikreket és kapcsolatokat. Ekkor megjelenik egy párbeszédpanel, amely megjeleníti, hogy a művelet befejeződött. *Bezárás* megnyomva.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A gráf már fel lett töltve a ADT Explorerben. A gráf megjelenítéséhez nyomja le a *lekérdezés futtatása* gombot a *Graph EXPLORERBEN* , a ADT Explorer ablakának tetején. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/run-query.png":::

Ez az alapértelmezett lekérdezést fogja futtatni az összes digitális ikrek kiválasztásához és megjelenítéséhez. A ADT Explorer lekéri az összes ikreket és kapcsolatot a szolgáltatásból, és a *gráf nézet* mezőben rajzolja meg az általuk meghatározott gráfot.

## <a name="explore-the-graph"></a>A gráf megismerése

Most láthatja a minta forgatókönyv feltöltött gráfját:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva.":::

A körök (gráf "csomópontok") a digitális ikreket jelölik, és a vonalak a kapcsolatokat jelképezik. Látni fogja, hogy a *Floor0* Twin *Room0* tartalmaz, és a *Floor1* Twin tartalmazza a *Room1* .

Ha egeret használ, a gráf elemeire kattintva és húzásával helyezheti át őket.

### <a name="view-twin-properties"></a>Dupla tulajdonságok megtekintése 

A Twin elem kiválasztásával megtekintheti a tulajdonságok és azok értékeinek listáját a *Property Explorer* mezőben. 

A *Room0* tulajdonságai:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vegye figyelembe, hogy a *Room0* **70** -as hőmérséklettel rendelkezik.

A *Room1* tulajdonságai:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vegye figyelembe, hogy a *Room1* **80** -as hőmérséklettel rendelkezik.

### <a name="query-the-graph"></a>A gráf lekérdezése

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához. 

Az ikrek a gráfban való lekérdezésének egyik módja a *tulajdonságai* . A tulajdonságok alapján történő lekérdezés a különböző kérdések megválaszolásához nyújt segítséget, például olyan kiugró értékeket találni a környezetben, amelyeknek szükségük lehet a beavatkozásra.

Ebben a szakaszban egy lekérdezést fog futtatni, hogy válaszoljon a következő kérdésre: _**Mi a környezetem minden olyan része, amely a 75-nál nagyobb hőmérséklettel rendelkezik?**_

A válasz megtekintéséhez futtassa a következő lekérdezést a *query Explorerben* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

A korábbi tulajdonságok megtekintésének visszahívása, hogy a *Room0* a **70** -as hőmérséklettel rendelkezik, és a *Room1* hőmérséklete **80** . Ennek eredményeképpen csak a _**Room1**_ jelennek meg az eredmények között.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Más összehasonlító operátorok (,,, *<* *>* *=* vagy *! =* ) is támogatottak a fenti lekérdezésen belül. Kipróbálhatja ezeket, különböző értékeket vagy különböző Twin tulajdonságokat a lekérdezésbe, hogy kipróbálja a saját kérdéseinek megválaszolását.

## <a name="edit-data-in-the-graph"></a>A gráfban lévő adatszerkesztés

A ADT Explorer segítségével szerkesztheti a gráfban ábrázolt ikrek tulajdonságait. Ebben a szakaszban **_a Room0 hőmérsékletét_ 76** -ra fogjuk növelni.

Ehhez válassza a *Room0* lehetőséget, és hozza létre a tulajdonságok listáját a *Property Explorer* mezőben.

A lista tulajdonságai szerkeszthető. Az új érték beírásának engedélyezéséhez válassza a **70** hőmérséklet-értéket. Adja meg a **76** értéket, és nyomja meg a *Save (Mentés* ) ikont a hőmérséklet **76** értékre való frissítéséhez.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A sikeres mentést követően megjelenik egy *javítási információ* ablak, amely megjeleníti a háttérben az Azure digitális Twins [API](how-to-use-apis-sdks.md) -kkal használt javítási kódot a frissítéshez. *Bezárás* megnyomva.

### <a name="query-to-see-the-result"></a>Az eredmény megjelenítésének lekérdezése

Annak ellenőrzéséhez, hogy a gráf sikeresen regisztrálta-e a frissítést a *Room0* hőmérsékletére, futtassa újra a lekérdezést a korábbi verzióról, hogy az **összes ikreket a környezetben, a 75-nál nagyobb hőmérséklettel** adja meg:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Most, hogy a *Room0* hőmérséklete **70** – **76** értékre változott, mindkét ikreknek meg kell jelennie az eredményben.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A &quot;Floor1&quot; címkével ellátott kör egy &quot;Room1&quot; címkével ellátott nyíllal van összekötve. a &quot;Floor0&quot; címkével ellátott kör egy &quot;Room0&quot; címkével ellátott nyíllal van összekötve. A &quot;Floor1&quot; és a &quot;Floor0&quot; nincs csatlakoztatva." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>A contextualize áttekintése és megismerése

Ebben a rövid útmutatóban létrehozott egy Azure Digital Twins-példányt, csatlakoztatta azt a ADT Explorerrel, és feltölti azt egy minta forgatókönyvvel. 

Ezután megvizsgálta a gráfot...
1. Lekérdezés használata a forgatókönyvre vonatkozó kérdés megválaszolásához.
2. Egy tulajdonság szerkesztése egy digitális Twin-ben.
3. A lekérdezés ismételt futtatásával megtekintheti, hogy a válasz Hogyan módosult a frissítés eredményeként.

Ennek a gyakorlatnak a célja, hogy bemutassa, hogyan használhatja az Azure digitális Twins gráfot a környezettel kapcsolatos kérdések megválaszolására, még akkor is, ha a környezet folyamatosan változik. 

Bár ebben a rövid útmutatóban manuálisan hajtotta végre a hőmérséklet-frissítést, gyakori az Azure digitális Twins-ben, hogy a digitális ikreket a valódi IoT-eszközökhöz csatlakoztassuk, hogy a telemetria-információk alapján automatikusan megkapják a frissítéseket. Így olyan élő gráfokat építhet ki, amelyek mindig tükrözik a környezet valós állapotát, és a lekérdezésekkel információt kaphatnak arról, hogy mi történik a környezetében valós időben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutatóhoz tartozó munka becsomagolásához először fejezze be a futó konzol alkalmazást. Ezzel kikapcsolja a ADT Explorer alkalmazáshoz való kapcsolódást a böngészőben, és többé nem fogja tudni megtekinteni az élő adatmegjelenítést a böngészőben. A böngésző fület lezárhatja.

Ha azt tervezi, hogy folytatja az Azure digitális Twins-oktatóanyagokat, az ebben a rövid útmutatóban használt példány újra felhasználható a cikkekhez, és nem kell eltávolítania.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Végezetül törölje a helyi gépre ( _**Azure_Digital_Twins__ADT__explorer**_ ) letöltött Project Sample mappát. Előfordulhat, hogy törölnie kell a tömörített és a kibontott verziókat is.

## <a name="next-steps"></a>Következő lépések 

Ezután folytassa az Azure digitális Twins-oktatóanyagokkal, és készítse elő saját Azure digitális Twins-forgatókönyvét és interakciós eszközeit.

> [!div class="nextstepaction"]
> [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)