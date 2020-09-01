---
title: Mintaforgatókönyv felfedezése
titleSuffix: Azure Digital Twins
description: A ADT Explorer minta használatával megjelenítheti és feltárhatja az előre elkészített forgatókönyvet.
author: baanders
ms.author: baanders
ms.date: 8/12/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: bbb1378419c68db07be5ca14ca6834810f2fc9f5
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055464"
---
# <a name="explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Egy minta Azure digitális Twins-forgatókönyv megismerése a ADT Explorer használatával

Az Azure Digital Twins segítségével valós környezetei élő modelljeit hozhat létre és kezelhet. Ezt úgy teheti meg, hogy az egyes elemeket **digitális ikrekként**modellezi, majd összekapcsolja őket egy **olyan Tudásbázisban, amely** képes reagálni az élő eseményekre, és információkat kér le.

Ebben a rövid útmutatóban egy előre elkészített Azure digitális Twins-gráfot fog felfedezni, amely az [**Azure Digital Twins (ADT) Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)nevű minta alkalmazás segítségével készült. A ADT Explorer lehetővé teszi a forgatókönyvek feltöltését, az ikrek és gráf vizualizációs ábrázolásának megtekintését, valamint más felügyeleti tevékenységek végrehajtását egy böngészőalapú, vizuális felülettel.

A rövid útmutató a következő főbb lépéseket tartalmazza:

1. Azure digitális Twins-példány és ADT Explorer beállítása
1. Előre elkészített modellek és gráf-adatok feltöltése a minta létrehozásához
1. A létrehozott forgatókönyv-gráf megismerése
1. Módosítsa a gráfot

A példában szereplő gráf két emeleten és két szobával működő épületet jelöl. A gráf így fog kinézni:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Egy 4 kör alakú csomópontból álló gráf nézete, amely a nyilakhoz csatlakozik. A Floor1 címkével ellátott kör egy Room1 címkével ellátott nyíllal van összekötve. a Floor0 címkével ellátott kör egy Room0 címkével ellátott nyíllal van összekötve. A Floor1 és a Floor0 nincs csatlakoztatva.":::

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, **[hozzon létre egyet most ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

A rövid útmutató elindítása előtt két mintát is le kell töltenie:
* A **ADT Explorer** minta alkalmazás. Ez a minta a rövid útmutatóban használt fő alkalmazást tartalmazza egy Azure digitális Twins-forgatókönyv betöltéséhez és megismeréséhez. Az alkalmazás beszerzéséhez navigáljon ide: [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). A letöltéshez kattintson a *zip letöltése* gombra *. * A MINTAKÓD zip-fájlja _**ADT_Explorer.zipként **_ a gépre.
* **Példa Azure digitális Twins-forgatókönyvre**. Ez egy előre elkészített Azure digitális Twins-gráfot tartalmaz, amelyet a ADT Explorerbe fog betölteni, hogy működjön a-mel. A forgatókönyv eléréséhez navigáljon ide: [Azure Digital Twins-minták](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). A letöltéshez kattintson a *zip letöltése* gombra *. * A MINTAKÓD zip-fájlja _**Azure_Digital_Twins_samples.zipként **_ a gépre.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Az Azure digitális Twins és a ADT Explorer beállítása

Az Azure Digital Twins használatának első lépése egy **Azure digitális Twins-példány**beállítása. Miután létrehozta a szolgáltatás egy példányát, a rövid útmutatóban később feltöltheti a példában szereplő adatokkal.

Emellett be kell állítania a ADT Explorer futtatásához szükséges engedélyeket a számítógépen, és elérheti az Azure digitális Twins-példányát. Ez lehetővé teszi, hogy a minta alkalmazás használatával vizsgálja meg a példányát és adatait.

### <a name="set-up-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

A példányok és a szükséges hitelesítések beállításának legegyszerűbb módja egy automatizált üzembehelyezési parancsfájl-minta futtatása. Kövesse az útmutató [*: példány és hitelesítés beállítása (megírt)*](how-to-set-up-instance-scripted.md)című témakör utasításait. Az utasítások azt is tartalmazzák, hogy az egyes lépéseket sikeresen elvégezte-e, és készen állnak az új példány használatára való áttérésre.

Ebben a rövid útmutatóban a példány beállításakor a következő értékekre lesz szüksége. Ha újra össze kell gyűjtenie ezeket az értékeket, az alábbi hivatkozásokra kattintva megkeresheti azokat a [Azure Portalban](https://portal.azure.com).
* Azure digitális Twins-példány **_állomásneve_** ([Keresés a portálon](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD App Registration **_Application (ügyfél-) azonosító_** ([Keresés a portálon](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD-alkalmazás regisztrációs **_könyvtárának (bérlői) azonosítója_** ([Keresés a portálon](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="set-adt-explorer-permissions"></a>ADT Explorer engedélyeinek beállítása

Ezt követően készítse elő a létrehozott Azure digitális Twins-példányt, hogy együttműködjön a ADT Explorerrel, amely egy helyileg üzemeltetett webalkalmazás. Látogasson el a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) lapra a Azure Portal, és válassza ki az alkalmazás regisztrációjának nevét a listából.

Válassza a *hitelesítés* lehetőséget a regisztráció menüjében, és nyomja meg *a + platform hozzáadása*elemet.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Az alkalmazás-regisztráció hitelesítési adatainak Azure Portal lapja. A platform hozzáadása gomb körül van egy kiemelés" lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

A következő *platformok konfigurálása* oldalon válassza a *web*lehetőséget.
Adja meg a konfigurációs adatokat a következőképpen:
* **Átirányítási**URI-k: adjon hozzá egy ÁTirányítási URI-t *http://localhost:3000* .
* **Implicit támogatás**: jelölje be a *hozzáférési jogkivonatok*jelölőnégyzetét.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="A platformok konfigurálása oldal, a fent ismertetett információk kiemelése":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A *Konfigurálás* befejezéséhez.

Most már rendelkezik egy olyan webes konfigurációval, amelyet a ADT Explorer használni fog. Ennek a Azure Portal hitelesítés lapjának kell szerepelnie.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Az alkalmazás-regisztráció hitelesítési adatainak Azure Portal lapja. A webplatformok szakasza egy átirányítási URI-t tartalmaz http://localhost:3000 , és a hozzáférési tokenek számára engedélyezve van az implicit engedélyezés.":::

### <a name="run-and-configure-adt-explorer"></a>A ADT Explorer futtatása és konfigurálása

Ezután futtassa a ADT Explorer alkalmazást, és konfigurálja az Azure Digital Twins-példányhoz.

Navigáljon a letöltött _**ADT_Explorer.zip**_ mappára, és csomagolja ki. Nyisson meg egy parancssort a mappa helye *ADT_explorer/Client/src*.

A futtatásával `npm install` töltse le az összes szükséges függőséget.

Ezután indítsa el az alkalmazást a futtatásával `npm run start` .

Néhány másodperc elteltével megnyílik egy böngészőablak, és az alkalmazás megjelenik a böngészőben.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Az ablak tetején található *Bejelentkezés* gombra kattintva állítsa be, hogy a ADT Explorer működjön a beállított példánnyal. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer – a bejelentkezési ikon kiemelése az ablak tetején. Az ikon a kulcs sziluettjét ábrázoló személy egyszerű sziluettjét jeleníti meg." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Adja meg az [Előfeltételek](#prerequisites) szakaszban korábban összegyűjtött fontos adatokat:
* Alkalmazás (ügyfél) azonosítója
* Címtár (bérlő) azonosítója
* ADT URL-cím a *https://{instance Host Name}* formátumban

>[!NOTE]
> Bármikor újra felkeresheti vagy szerkesztheti ezeket az adatokat. Ehhez jelölje ki ugyanazt az ikont, ha újra fel szeretné húzni a bejelentkezési mezőt. A rendszer megtartja a beadott értékeket.

Ha megjelenik a Microsoft előugró ablakát *kérő engedély* , adja meg az alkalmazás jóváhagyását, és fogadja el a folytatást.

## <a name="add-the-sample-data"></a>A mintaadatok hozzáadása

Ezután importálni fogja a minta forgatókönyvet és a Graphot a ADT Explorerben.

A minta forgatókönyv a letöltött  _**Azure_Digital_Twins_samples.zip**_ mappában található, ezért most navigáljon a mappához, és bontsa ki a mappát.

### <a name="models"></a>Modellek

Az Azure digitális ikrek megoldásának első lépéseként definiálja a környezet szókincsét. Ez egyéni [**modellek**](concepts-models.md)létrehozásával történik, amelyek leírják a környezetben létező entitások típusait. 

Minden modell egy **Digital Twin Definition Language (DTDL)** nevű JSON-ld-szerű nyelven íródott, és a *Tulajdonságok*, *telemetria*, *kapcsolatok*és *összetevők*tekintetében egyetlen típusú entitást ír le. Később ezeket a modelleket fogja használni a digitális ikrek alapjaként, amelyek az ilyen típusú példányokat jelölik.

A modell létrehozásakor általában három lépést kell végrehajtania:
1. Írja meg a modell definícióját (a rövid útmutatóban, amely már a mintaoldat részeként lett elvégezve)
2. Ellenőrizze, hogy a szintaxis helyes-e (a rövid útmutatóban, amely már a mintaoldat részeként lett elvégezve)
3. Töltse fel az Azure Digital Twins-példányba
 
Ebben a rövid útmutatóban a modell fájljait már megírtuk és érvényesítjük az Ön számára, és beletartoznak a letöltött megoldásba. Ebben a szakaszban két előre megírt modellt fog feltölteni a példányra az építési környezet ezen összetevőinek definiálásához:
* Floor
* Szoba

#### <a name="upload-models"></a>Modellek feltöltése

A *modell nézet* mezőben nyomja meg a *modell feltöltése* ikont.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="A modell nézet mezőben a középső ikon ki van emelve. Egy felhőbe mutató nyilat mutat be." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. A megjelenő fájl kiválasztása mezőben navigáljon a letöltött adattár *Azure_Digital_Twins_samples/adtsampleapp/sampleclientapp/models* mappájába.
2. Válassza a *Room.js* be és *Floor.js*be lehetőséget, majd kattintson az OK gombra. (Ha szeretné, feltöltheti a többi modellt is, de ebben a rövid útmutatóban nem használható.)
3. Az Azure-fiókba való bejelentkezéshez kattintson a felugró ablakra.

>[!NOTE]
>Ha a következő hibaüzenet jelenik meg: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="egy felugró ablak olvasása hiba: hiba történt a modellek beolvasásakor: ClientAuthError: hiba A felugró ablak megnyitásakor. Ez akkor fordulhat elő, ha az IE-t használja, vagy ha az előugró ablakok le vannak tiltva a böngészőben. egy Bezárás gomb alul" border="false":::
> Próbálja meg letiltani a felugró ablakok blokkolása vagy egy másik böngésző használatával.

A ADT Explorer ekkor feltölti ezeket a modell-fájlokat az Azure Digital Twins-példányba. Meg kell jelennie a *modell nézet* mezőben, amely megjeleníti a felhasználóbarát neveket és a teljes modell azonosítóit. A *modellre* vonatkozó információk megtekintése buborékok lehetőségre kattintva megtekintheti a mögöttes DTDL-kódot.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="A Model View (modell nézet) mező nézete két, a padlón (dtmi: Floor; 1) és a Room (dtmi: példa: Room; 1) felsorolt modell-definícióval. A modell megtekintése ikon egy kör i betűjét jeleníti meg az egyes modellekhez." lightbox="media/quickstart-adt-explorer/model-info.png":::        
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

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="A gráf nézet mezőben egy ikon van kiemelve. Egy felhőbe mutató nyilat mutat be." lightbox="media/quickstart-adt-explorer/import-graph.png":::

A fájl kiválasztása mezőben navigáljon a *Azure_Digital_Twins_samples/adtsampleapp/sampleclientapp* mappára, és válassza ki a _**buildingScenario.xlsx**_ számolótábla-fájlt. Ez a fájl tartalmazza a minta gráf leírását. Nyomja meg az OK gombot.

Néhány másodperc elteltével a ADT Explorer megnyílik egy *importálási* nézet, amely megjeleníti a betöltendő gráf előnézetét.

A gráf feltöltésének megerősítéséhez nyomja meg a *diagram nézet*jobb felső sarkában található *Save (Mentés* ) ikont:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="A Save (Mentés) ikon kiemelése a gráf betekintő ablaktábláján" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A ADT Explorer most a feltöltött fájllal hozza létre a kért ikreket és kapcsolatokat. Ekkor megjelenik egy párbeszédpanel, amely megjeleníti, hogy a művelet befejeződött. *Bezárás*megnyomva.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="A Graph importálás sikerességét jelző párbeszédpanel. A sikeres importálást olvassa be. 49 ikrek importálva. 50 kapcsolat importálva. " lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A gráf már fel lett töltve a ADT Explorerben. Ha látni szeretné a gráfot, a ADT Explorer ablakának tetején kattintson a *lekérdezés futtatása* gombra a *Graph Explorerben* . 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Az ablak felső részén található lekérdezés futtatása gomb beolvasása megnyomva jelenik meg" lightbox="media/quickstart-adt-explorer/run-query.png":::

Ez az alapértelmezett lekérdezést fogja futtatni az összes digitális ikrek kiválasztásához és megjelenítéséhez. A ADT Explorer lekéri az összes ikreket és kapcsolatot a szolgáltatásból, és a *gráf nézet* mezőben rajzolja meg az általuk meghatározott gráfot.

## <a name="explore-the-graph"></a>A gráf megismerése

Most láthatja a minta forgatókönyv feltöltött gráfját:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="A Graph View (gráf nézet) mező megjelenítése, amelyen belül egy dupla gráf található. A floor1 címkével ellátott kör egy room1 címkével ellátott nyíllal van összekötve. a floor0 címkével ellátott kör egy room0 címkével ellátott nyíllal van összekötve.":::

A körök (gráf "csomópontok") a digitális ikreket jelölik, és a vonalak a kapcsolatokat jelképezik. Látni fogja, hogy a *Floor0* Twin *Room0*tartalmaz, és a *Floor1* Twin tartalmazza a *Room1*.

Ha egeret használ, a gráf elemeire kattintva és húzásával helyezheti át őket.

### <a name="view-twin-properties"></a>Dupla tulajdonságok megtekintése 

A Twin elem kiválasztásával megtekintheti a tulajdonságok és azok értékeinek listáját a *Property Explorer* mezőben. 

A *Room0*tulajdonságai:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Jelölje ki a Property Explorer (Tulajdonságok) panelt, amely a Room0 tulajdonságait mutatja, beleértve a (többek között) $dtId mezőt a Room0, a 70-es hőmérsékleti mező és a 30-as páratartalom mező mellett." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vegye figyelembe, hogy a *Room0* **70**-as hőmérséklettel rendelkezik.

A *Room1*tulajdonságai:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Jelölje ki a Property Explorer (Tulajdonságok) panelt, amely a Room1 tulajdonságait mutatja, beleértve a (többek között) $dtId mezőt a Room1, a 80-es hőmérsékleti mező és a 60-es páratartalom mező mellett." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vegye figyelembe, hogy a *Room1* **80**-as hőmérséklettel rendelkezik.

### <a name="query-the-graph"></a>A gráf lekérdezése

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához. 

Az ikrek a gráfban való lekérdezésének egyik módja a *tulajdonságai*. A tulajdonságok alapján történő lekérdezés a különböző kérdések megválaszolásához nyújt segítséget, például olyan kiugró értékeket találni a környezetben, amelyeknek szükségük lehet a beavatkozásra.

Ebben a szakaszban egy lekérdezést fog futtatni, hogy válaszoljon a következő kérdésre: _ **Mi a környezetem minden olyan része, amely a 75-nál nagyobb hőmérséklettel rendelkezik?**_

A válasz megtekintéséhez futtassa a következő lekérdezést a *query Explorerben* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

A korábbi tulajdonságok megtekintésének visszahívása, hogy a *Room0* a **70** -as hőmérséklettel rendelkezik, és a *Room1* hőmérséklete **80**. Ennek eredményeképpen csak a _**Room1**_ jelennek meg az eredmények között.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="A tulajdonság-lekérdezés eredménye, amely csak a Room1 jeleníti meg" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Más összehasonlító operátorok (,,, *<* *>* *=* vagy *! =*) is támogatottak a fenti lekérdezésen belül. Kipróbálhatja ezeket, különböző értékeket vagy különböző Twin tulajdonságokat a lekérdezésbe, hogy kipróbálja a saját kérdéseinek megválaszolását.

## <a name="edit-data-in-the-graph"></a>A gráfban lévő adatszerkesztés

A ADT Explorer segítségével szerkesztheti a gráfban ábrázolt ikrek tulajdonságait. Ebben a szakaszban ** _a Room0 hőmérsékletét_ 76**-ra fogjuk növelni.

Ehhez válassza a *Room0*lehetőséget, és hozza létre a tulajdonságok listáját a *Property Explorer* mezőben.

A lista tulajdonságai szerkeszthető. Az új érték beírásának engedélyezéséhez válassza a **70** hőmérséklet-értéket. Adja meg a **76**értéket, és nyomja meg a *Save (Mentés* ) ikont a hőmérséklet **76**értékre való frissítéséhez.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="A Property Explorer mező, amely a Room0 tulajdonságait mutatja. A hőmérséklet értéke egy 76-es szerkeszthető mező, amely a mentés ikon körül látható." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A sikeres mentést követően megjelenik egy *javítási információ* ablak, amely megjeleníti a háttérben az Azure digitális Twins [API](how-to-use-apis-sdks.md) -kkal használt javítási kódot a frissítéshez. *Bezárás*megnyomva.

### <a name="query-to-see-the-result"></a>Az eredmény megjelenítésének lekérdezése

Annak ellenőrzéséhez, hogy a gráf sikeresen regisztrálta-e a frissítést a *Room0*hőmérsékletére, futtassa újra a lekérdezést a korábbi verzióról, hogy az **összes ikreket a környezetben, a 75-nál nagyobb hőmérséklettel**adja meg:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Most, hogy a *Room0* hőmérséklete **70** – **76**értékre változott, mindkét ikreknek meg kell jelennie az eredményben.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="A Room0 és a Room1 mutató tulajdonság-lekérdezés eredményei" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>A contextualize áttekintése és megismerése

Ebben a rövid útmutatóban létrehozott egy Azure Digital Twins-példányt, csatlakoztatta azt a ADT Explorerrel, és feltölti azt egy minta forgatókönyvvel. 

Ezután megvizsgálta a gráfot...
1. Lekérdezés használata a forgatókönyvre vonatkozó kérdés megválaszolásához.
2. Egy tulajdonság szerkesztése egy digitális Twin-ben.
    * A lekérdezés ismételt futtatásával megtekintheti, hogy a válasz Hogyan módosult a frissítés eredményeként.

Ennek a gyakorlatnak a célja, hogy bemutassa, hogyan használhatja az Azure digitális Twins gráfot a környezettel kapcsolatos kérdések megválaszolására, még akkor is, ha a környezet folyamatosan változik. 

Bár ebben a rövid útmutatóban manuálisan hajtotta végre a hőmérséklet-frissítést, gyakori az Azure digitális Twins-ben, hogy a digitális ikreket a valódi IoT-eszközökhöz csatlakoztassuk, hogy a telemetria-információk alapján automatikusan megkapják a frissítéseket. Így olyan élő gráfokat építhet ki, amelyek mindig tükrözik a környezet valós állapotát, és a lekérdezésekkel információt kaphatnak arról, hogy mi történik a környezetében valós időben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutatóhoz tartozó munka becsomagolásához először fejezze be a futó konzol alkalmazást. Ezzel kikapcsolja a ADT Explorer alkalmazáshoz való kapcsolódást a böngészőben, és többé nem fogja tudni megtekinteni az élő adatmegjelenítést a böngészőben. A böngésző fület lezárhatja.

Ha azt tervezi, hogy folytatja az Azure digitális Twins-oktatóanyagokat, az ebben a rövid útmutatóban használt példány újra felhasználható a cikkekhez, és nem kell eltávolítania.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Végezetül törölje a helyi gépre letöltött Project Sample-mappákat (_**ADT_Explorer.zip**_ és _**Azure_Digital_Twins_samples.zip**_).

## <a name="next-steps"></a>Következő lépések 

Ezután folytassa az Azure digitális Twins-oktatóanyagokkal, és készítse elő saját Azure digitális Twins-forgatókönyvét és interakciós eszközeit.

> [!div class="nextstepaction"]
> [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)
