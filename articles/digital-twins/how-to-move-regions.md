---
title: Példány áthelyezése másik Azure-régióba
titleSuffix: Azure Digital Twins
description: 'Lásd: Azure Digital Twins-példány áthelyezése egyik Azure-régióból a másikba.'
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 1725c3ff162e4f6b7ac3a5ea1ede6976c827b510
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328495"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure digitális Twins-példány áthelyezése egy másik Azure-régióba

Ha át kell helyeznie az Azure Digital Twins-példányt az egyik régióból a másikba, a jelenlegi folyamat az **új régióban lévő erőforrások**újbóli létrehozása, majd az eredeti erőforrások törlése. A folyamat végén egy új, az elsővel azonos Azure Digital Twins-példánnyal fog dolgozni, kivéve a frissített helyet.

Ez a cikk útmutatást nyújt arról, hogyan végezhető el a teljes áthelyezés, a másolás mindenre, amire szüksége lesz az új példánynak az eredetivel egyezőnek kell lennie.

Ez a folyamat a következő lépéseket tartalmazza:
1. Felkészülés: töltse le az eredeti modelleket, az ikreket és a Graphot.
2. Áthelyezés: hozzon létre egy új Azure Digital Twins-példányt egy új régióban.
3. Move: feltölti az új Azure Digital Twins-példányt.
    - Feltöltheti az eredeti modelleket, az ikreket és a Graphot.
    - Hozza létre újra a végpontokat és az útvonalakat.
    - Csatolja újra a csatlakoztatott erőforrásokat.
4. Forrás erőforrásainak tisztítása: az eredeti példány törlése.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Digital Twins-példány újbóli létrehozásának megkísérlése előtt érdemes áttekinteni az eredeti példány összetevőit, és világos képet kapni az összes olyan darabról, amelyet újra létre kell hozni.

Íme néhány kérdés, amelyet érdemes figyelembe vennie:
* Milyen **modellek** vannak feltöltve a saját példányba? Hány van?
* Milyen **ikrek** szerepelnek a saját példányban? Hány van?
* Mi a példában szereplő **gráf** általános alakja? Hány kapcsolat létezik?
* Milyen **végpontok** vannak a saját példányban?
* Milyen **útvonalakat** használhatok a saját példányban? Vannak szűrők?
* Hol kapcsolódnak a példányok **más Azure-szolgáltatásokhoz**? Néhány gyakori integrációs pont tartalmaz...
    - Event Grid, Event hub vagy Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Device kiépítési szolgáltatás (DPS)
* Milyen más **személyes vagy vállalati alkalmazások** csatlakoznak a saját példányhoz?

Ezeket az információkat a [Azure Portal](https://portal.azure.com), az [Azure digitális Twins API-](how-to-use-apis-sdks.md)k, az SDK-k, az [Azure digitális Twins CLI-parancsok](how-to-use-cli.md)vagy az [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -minta használatával gyűjthetjük össze.

## <a name="prepare"></a>Előkészítés

Ebben a szakaszban elő fogja készíteni a példány újbóli létrehozását, ha az eredeti példányból **letölti az eredeti modelleket, az ikreket és a gráfot** . Ez a cikk az [Azure Digital Twins (ADT) Explorer-](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) mintát használja.

>[!NOTE]
>Előfordulhat, hogy már van olyan fájl, amely tartalmazza a modelleket és/vagy a diagramot a példányban. Ha így van, nem kell újra letöltenie az összes adatot – csak a hiányzó darabokat vagy olyan dolgokat, amelyek megváltoztak, mivel eredetileg feltöltötte ezeket a fájlokat (például az olyan ikreket, amelyek esetleg frissültek az új adatokkal).

### <a name="limitations-of-adt-explorer"></a>A ADT Explorer korlátai

Az [Azure Digital Twins (ADT) Explorer minta](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) egy olyan ügyfélalkalmazás-minta, amely támogatja a gráf vizuális ábrázolását, és vizuális interakciót biztosít a példánnyal. Ebből a cikkből megtudhatja, hogyan használhatja le, majd később feltöltheti a modelleket, az ikreket és a diagramokat.

Vegye figyelembe azonban, hogy ez egy **minta** , nem pedig teljes eszköz. Nem tesztelték a hangsúlyt, és nem a nagy méretű gráfok kezelésére készült. Ezért kérjük, vegye figyelembe a következő, a beépített mintákra vonatkozó korlátozásokat:
* A minta jelenleg csak a 1000-es és a 2000-es kapcsolattal rendelkező gráf méretekben lett tesztelve
* A minta nem támogatja az újrapróbálkozást bármely időszakos hiba esetén
* A minta nem feltétlenül értesíti a felhasználót, ha a feltöltött adatfeldolgozás nem fejeződött be
* A minta nem kezeli a nagyon nagy gráfokból származó, a rendelkezésre álló erőforrásokat (például a memóriát) meghaladó hibákat.

Ha a minta nem tudja kezelni a gráf méretét, exportálhatja és importálhatja a gráfot más Azure Digital Twins fejlesztői eszközök használatával:
* [Azure digitális Twins CLI-parancsok](how-to-use-cli.md)
* [Azure digitális Twins API-k és SDK-k](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>A ADT Explorer alkalmazás beállítása

A ADT Explorer folytatásához először töltse le a minta alkalmazás kódját, és állítsa be úgy, hogy az a gépen fusson. 

Navigáljon ide a mintához: [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). A letöltéshez kattintson a *zip letöltése* gombra *. * A MINTAKÓD zip-fájlja _**ADT_Explorer.zipként **_ a gépre. Bontsa ki a fájlt.

Ezután állítson be engedélyeket a ADT Explorer számára a gépen való futtatáshoz. Ehhez kövesse az Azure digitális Twins-útmutató [*ADT-kezelő engedélyeinek beállítása*](quickstart-adt-explorer.md#set-adt-explorer-permissions) szakaszának lépéseit.

Végül futtassa és konfigurálja a ADT Explorert az eredeti Azure Digital Twins-példányhoz való kapcsolódáshoz. Kövesse a gyors útmutató [*ADT-kezelő futtatása és konfigurálása*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) című szakaszának lépéseit.

Most meg kell jelennie a ADT Explorer-alkalmazásnak, amely egy böngészőben fut a gépen. A mintát az eredeti Azure Digital Twins-példányhoz kell csatlakoztatni.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/explorer-blank.png":::

A kapcsolat ellenőrzéséhez kattintson a *lekérdezés futtatása* gombra, és futtassa az alapértelmezett lekérdezést, amely az összes ikreket és kapcsolatot megjeleníti a Graph *Explorer* mezőben.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/run-query.png":::

A ADT Explorer futását elhagyhatja, mivel a cikk későbbi részében később újra fel fogja tölteni ezeket az elemeket az új példányba a célként megadott régióban.

### <a name="download-models-twins-and-graph"></a>Modellek, ikrek és Graph letöltése

Ezután töltse le a modelleket, az ikreket és a gráfot a megoldásában a gépre.

Ha egyszerre szeretné letölteni ezeket, először győződjön meg arról, hogy a *gráf nézet* mezőben a teljes gráf látható (ezt a `SELECT * FROM digitaltwins` *lekérdezés Explorer* ablakának alapértelmezett lekérdezésének újbóli futtatásával teheti meg).
 
Ezután nyomja meg a Graph *nézet* ábrán látható diagram *exportálása* ikont.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/export-graph.png":::

Ez lehetővé teszi a *letöltési* hivatkozást a *Graph nézetben*. Válassza ki a lekérdezési eredmény JSON-alapú megjelenítésének letöltéséhez, beleértve a modelleket, az ikreket és a kapcsolatokat. Ehhez le kell töltenie egy *. JSON* fájlt a gépre.

>[!NOTE]
>Ha a letöltött fájl más kiterjesztésű, akkor próbálja meg közvetlenül szerkeszteni a bővítményt, és módosítsa a *. JSON*fájlba.

## <a name="move"></a>Áthelyezés

Ezután végrehajtja a példány áthelyezését úgy, hogy létrehoz egy új példányt a céltartományban, és feltölti azt az eredeti példányból származó adatokkal és összetevőkkel.

### <a name="create-a-new-instance"></a>Új példány létrehozása

Először **hozzon létre egy új Azure Digital Twins-példányt a megcélzott régióban**. Ennek elvégzéséhez kövesse az [*útmutató: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md), az alábbi mutatók szem előtt tartásával:
* Megtarthatja az új példány nevét, **Ha** az egy másik erőforráscsoporthoz esik. Ha ugyanazt az erőforráscsoportot kell használnia, amely az eredeti példányt tartalmazza, akkor az új példánynak saját nevet kell adnia.
* Ha a rendszer kéri, adja meg a cél új régiót.
* **Nem kell** újból létrehoznia az alkalmazás regisztrációját. Az új példány felhasználhatja ugyanazt az alkalmazás-regisztrációt, amelyhez már rendelkezik.
    - Ha a [parancsfájl](how-to-set-up-instance-scripted.md) -telepítési cikket használja, a meglévő alkalmazás-regisztráció részleteit újra megadhatja az új név megadásakor, amikor a rendszer kéri.
    - Ha a manuális [portál](how-to-set-up-instance-portal.md) vagy a [parancssori](how-to-set-up-instance-cli.md) felület telepítési cikkeit használja, leállíthatja az *Azure Digital Twins-példány létrehozása* és a *felhasználói hozzáférési engedélyek beállítása* lépéseit. Nem kell folytatnia az *ügyfélalkalmazások hozzáférési engedélyeinek beállítását*.

Ha ez befejeződik, szüksége lesz az új példány **állomásneve** , hogy továbbra is beállítsa az adatait. Ha a telepítés során nem jegyezze fel ezt a megjegyzést, kövesse az [alábbi utasításokat](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) , hogy most már a Azure Portal.

### <a name="repopulate-old-instance"></a>Régi példány újrafeltöltése

Ezután be kell állítania az új példányt úgy, hogy az az eredeti példánya legyen.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Eredeti modellek, ikrek és gráf feltöltése a ADT Explorerrel

Ebben a szakaszban újra feltöltheti a modelleket, az ikreket és a Graphot az új példányra. Ha nem rendelkezik modellekkel, ikrekkel vagy gráfokkal az eredeti példányban, vagy nem szeretné áthelyezni őket az új példányra, ugorjon a [következő szakaszra](#recreate-endpoints-and-routes).

Ellenkező esetben a folytatáshoz térjen vissza a **ADT Explorer** alkalmazást futtató böngészőablakba, és kövesse az alábbi lépéseket.

##### <a name="connect-to-the-new-instance"></a>Kapcsolódás az új példányhoz

A ADT Explorer jelenleg az eredeti Azure Digital Twins-példányhoz csatlakozik. Állítsa át a kapcsolódást úgy, hogy az az új példányra mutasson, és a *Bejelentkezés* gombot az ablak tetején. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/sign-in.png":::

Mivel újra használja az alkalmazás regisztrációját, csak le kell cserélnie a *ADT URL-címét*. Módosítsa ezt az értéket úgy, hogy az beolvassa a *https://{New instance hostname}* szöveget.

Kattintson a *kapcsolatra*. Előfordulhat, hogy az Azure-beli hitelesítő adataival újra be kell jelentkeznie, és/vagy meg kell adnia az alkalmazásnak a példányhoz való jóváhagyását.

##### <a name="upload-models-twins-and-graph"></a>Modellek, ikrek és gráfok feltöltése

Ezután töltse fel az új példányra korábban letöltött megoldás-összetevőket.

Ha fel szeretné tölteni a **modelleket, az ikreket és a Graphot**, nyomja meg az *importálási gráf* ikont a *gráf nézet* mezőben. Ez a lehetőség egyszerre mindhárom összetevőt feltölti (még a gráfban jelenleg nem használt modelleket is).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/import-graph.png":::

A fájl kiválasztása mezőben navigáljon a letöltött gráfhoz. Válassza ki a Graph *. JSON* fájlt, és kattintson a *Megnyitás gombra*.

Néhány másodperc elteltével a ADT Explorer megnyílik egy *importálási* nézet, amely megjeleníti a betöltendő gráf előnézetét.

A gráf feltöltésének megerősítéséhez nyomja meg a *diagram nézet*jobb felső sarkában található *Save (Mentés* ) ikont:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A ADT Explorer most feltölti modelljeit és gráfját (beleértve az ikreket és a kapcsolatokat) az új Azure Digital Twins-példányra. A következő üzenetnek kell megjelennie arról, hogy hány modell, ikrek és kapcsolat lett feltöltve:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Annak ellenőrzéséhez, hogy minden sikeresen feltöltve lett, a *Graph Explorerben* a *lekérdezés futtatása* gombra kattintva futtathatja az alapértelmezett lekérdezést, amely megjeleníti az összes ikreket és kapcsolatot a gráfban. Ez a *modell nézetben*is frissíti a modellek listáját.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/run-query.png":::

Meg kell jelennie a gráfnak a *Graph Explorerben* megjelenő összes ikrek és kapcsolat között. Emellett a *modell nézet* mezőben is megjelennek a modellek.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok." lightbox="media/how-to-move-regions/post-upload.png":::

Ezzel megerősíti, hogy a modelleket, az ikreket és a gráfot újra feltöltötte a célként megadott régióban lévő új példányra.

#### <a name="recreate-endpoints-and-routes"></a>Végpontok és útvonalak újbóli létrehozása

Ha az eredeti példányban vannak **végpontok és/vagy útvonalak** , újra létre kell hoznia őket az új példányban. Ha nem rendelkezik végpontokkal vagy útvonalakkal az eredeti példányban, vagy nem szeretné őket áthelyezni az új példányra, ugorjon a [következő szakaszra](#re-link-connected-resources).

Ellenkező esetben folytassa a lépéseket a [*útmutató: végpontok és útvonalak kezelése*](how-to-manage-routes-portal.md) az új példánnyal című témakör lépéseit követve, és tartsa szem előtt a következőket: 
* **Nem kell** újból létrehoznia az Event Grid, az Event hub vagy a Service Bus erőforrást, amelyet a végponthoz használ (*előfeltételi* szakaszban a végpont utasításában). Most újra létre kell hoznia a végpontot az Azure Digital Twins-példányon.
* A végpontok és az útvonalak **nevei**újra felhasználhatók, mivel azok egy másik példányra vannak szűkítve.
* Ne felejtse el hozzáadni a szükséges **szűrőket** a létrehozott útvonalakhoz.

#### <a name="re-link-connected-resources"></a>Csatlakoztatott erőforrások újbóli csatolása

Ha más alkalmazásokkal vagy Azure-erőforrásokkal rendelkezik, amelyek az eredeti Azure Digital Twins-példányhoz csatlakoznak, akkor a kapcsolatot úgy kell szerkesztenie, hogy az új példányhoz jussanak. Ebbe beletartozhatnak más Azure-szolgáltatások, illetve személyes vagy céges alkalmazások is, amelyek az Azure digitális Twins használatával működnek.

Ha nincs más erőforrás csatlakoztatva az eredeti példányhoz, vagy nem szeretné őket áthelyezni az új példányra, ugorjon a [következő szakaszra](#verify).

Ellenkező esetben a folytatáshoz vegye figyelembe a csatlakoztatott erőforrásokat a forgatókönyvben. Nincs szükség a csatlakoztatott erőforrások törlésére és újbóli létrehozására; Ehelyett csak szerkesztenie kell azokat a pontokat, amelyekben egy Azure digitális Twins-példányhoz csatlakoznak az **állomásnévn**keresztül, és ezt a frissítést úgy kell frissíteni, hogy az eredeti helyett az új példány állomásnevét használja.

A szerkesztéshez szükséges pontos erőforrások a forgatókönyvtől függenek, de íme néhány gyakori integrációs pont:
* Azure Functions. Ha van olyan Azure-függvénye, amelynek kódjában szerepel az eredeti példány állomásneve, frissítse ezt az értéket az új példány állomásneve, és tegye közzé újra a függvényt.
* Event Grid, Event Hubs vagy Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Device kiépítési szolgáltatás (DPS)
* Az Azure-on kívüli személyes vagy vállalati alkalmazások, például az [*oktatóanyagban*](tutorial-code.md)létrehozott **ügyfélalkalmazás** : az a-ügyfélalkalmazás, amely csatlakozik a példányhoz, és meghívja az Azure Digital Twins API-kat

A lépés elvégzése után a célként megadott régióban lévő új példánynak az eredeti példány másolatának kell lennie.

## <a name="verify"></a>Ellenőrzés

Az új példány helyes beállításának ellenőrzéséhez a következő eszközöket használhatja:
* A [**Azure Portal**](https://portal.azure.com) (jó annak ellenőrzéséhez, hogy az új példány létezik-e, és hogy a megfelelő cél régióban van-e; a végpontok és útvonalak ellenőrzéséhez, valamint az egyéb Azure-szolgáltatásokhoz való kapcsolódáshoz is jó.)
* Az [Azure digitális Twins **CLI-parancsai** ](how-to-use-cli.md) (jó annak ellenőrzéséhez, hogy az új példány létezik-e, és hogy a megfelelő célcsoportban van-e; a példányok adatai ellenőrzéséhez is használható)
* [**ADT Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (jó a példányok adattípusának, például modellek, ikrek és gráfok ellenőrzéséhez)
* Az [Azure digitális Twins API-jai és SDK-](how-to-use-apis-sdks.md) k (jó a példányok, például modellek, ikrek és gráfok ellenőrzéséhez, a végpontok és útvonalak ellenőrzéséhez is jó.)

Az eredeti példánnyal futtatott egyéni alkalmazásokat vagy végpontok közötti folyamatokat is futtathatja, így ellenőrizheti, hogy megfelelően működik-e az új példánnyal.

## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése

Most, hogy az új példánya be van állítva a megcélzott régióban az eredeti példány adatai és kapcsolatainak másolatával, **törölheti az eredeti példányt**.

Ezt megteheti a [Azure Portalban](https://portal.azure.com), a [CLI](how-to-use-cli.md)-vel vagy a [Control Plan sík API](how-to-use-apis-sdks.md#overview-control-plane-apis)-kkal.

Ha a Azure Portal használatával szeretné törölni a példányt, [nyissa meg a portált](https://portal.azure.com) egy böngészőablakban, és navigáljon az eredeti Azure Digital Twins-példányhoz, és keresse meg a saját nevét a portálon.

Nyomja meg a *delete (Törlés* ) gombot, és kövesse az utasításokat a törlés befejezéséhez.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="A böngészőablakban futó alkalmazást bemutató böngészőablak: 3000. Az alkalmazás neve ADT Explorer, és tartalmazza a lekérdezési Explorer, a modell nézet, a Graph View és a Property Explorer mezőit. Még nincsenek megjeleníthető adathalmazok.":::