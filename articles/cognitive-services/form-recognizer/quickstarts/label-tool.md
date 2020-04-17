---
title: 'Rövid útmutató: Űrlapok címkézése, modell betanítása és űrlap elemzése a mintacímkéző eszközzel – Űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Űrlapfelismerő mintacímkéző eszközzel manuálisan címkézheti az űrlapdokumentumokat. Ezután betanít egy egyéni modellt a címkézett dokumentumokkal, és a modell segítségével kulcs/érték párok kinyerése.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 790e2a148385f9da54df82f597c2ca52124dc2be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529849"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Űrlapfelismerő modell betanítása címkékkel a mintacímkéző eszközzel

Ebben a rövid útmutatóban a Form Recognizer REST API-t a mintacímkéző eszközzel fogja használni egy egyéni modell manuálisan címkézett adatokkal történő betanításához. Tekintse meg a [Vonat címkékkel](../overview.md#train-with-labels) szakaszaz áttekintés, hogy többet tudjon meg ezt a funkciót.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szüksége:

- Legalább hat azonos típusú formaból áll. Ezeket az adatokat a modell betanításához és az űrlap teszteléséhez fogja használni. Ehhez a rövid útmutatóhoz [mintaadatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) használhat. Töltse fel a betanítási fájlokat egy blob storage-tároló egy Azure Storage-fiók ban.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>A mintacímkéző eszköz beállítása

A Docker-motorsegítségével futtathatja a mintacímkéző eszközt. Kövesse az alábbi lépéseket a Docker-tároló beállításához. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> Az OCR-űrlapcímkéző eszköz nyílt forráskódú projektként is elérhető a GitHubon. Az eszköz egy webes alkalmazás segítségével készült React + Redux, és meg van írva TypeScript. További információért vagy közreműködésért olvassa el [az OCR-űrlapcímkéző eszköz .](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application)

1. Először telepítse a Dockert egy gazdaszámítógépen. Ez az útmutató bemutatja, hogyan használhatja a helyi számítógépet állomásként. Ha docker-üzemeltetési szolgáltatást szeretne használni az Azure-ban, tekintse [meg a mintacímkézési eszköz útmutatóüzembe helyezése](../deploy-label-tool.md) című témakört. 

   A gazdaszámítógépnek a következő hardverkövetelményeknek kell megfelelnie:

    | Tároló | Minimális | Ajánlott|
    |:--|:--|:--|
    |Mintacímkéző eszköz|2 mag, 4 GB memória|4 mag, 8 GB memória|

   Telepítse a Docker-t a gépére az operációs rendszerhez megfelelő utasításokat követve: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. A mintacímkéző eszköz `docker pull` tárolójának beszereznie a paranccsal.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Most már készen áll a `docker run`tároló futtatására.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Ez a parancs webböngészőn keresztül teszi elérhetővé a mintacímkéző eszközt. Nyissa [http://localhost:3000](http://localhost:3000)meg a.

> [!NOTE]
> A dokumentumokat és a modelleket a Form Recognizer REST API használatával is címkézheti. Betanításához és elemzéséhez a REST API-val című [témakörben a Betanítás címkékhasználatával a REST API és a Python használatával](./python-labeled-data.md)című témakörben található.

## <a name="set-up-input-data"></a>Bemeneti adatok beállítása

Először győződjön meg arról, hogy az összes képzési dokumentum azonos formátumú. Ha az űrlapok több formátumban vannak, a közös formátum alapján almappákba rendezheti őket. A betanításkor az API-t egy almappába kell irányítania.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Tartományok közötti erőforrás-megosztás (CORS) konfigurálása

Engedélyezze a CORS-t a tárfiókban. Válassza ki a tárfiókot az Azure Portalon, és kattintson a **CORS** fülre a bal oldali ablaktáblán. Az alsó sorban töltse ki a következő értékeket. Ezután kattintson a mentés **gombra** a tetején.

* Megengedett eredete = * 
* Engedélyezett módszerek \[= az összes kijelölése\]
* Engedélyezett fejlécek = *
* Kitett fejlécek = * 
* Maximális életkor = 200

> [!div class="mx-imgBorder"]
> ![CORS-beállítás az Azure Portalon](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Csatlakozás a mintacímkéző eszközhöz

A mintacímkéző eszköz egy forráshoz (ahol az eredeti űrlapok vannak) és egy célhoz (ahol a létrehozott címkéket és kimeneti adatokat exportálja).

A kapcsolatok beállíthatók és megoszthatók a projektek között. Bővíthető szolgáltatói modellt használnak, így egyszerűen hozzáadhat új forrás-/célszolgáltatókat.

Új kapcsolat létrehozásához kattintson az **Új kapcsolatok** (dugó) ikonra a bal oldali navigációs sávon.

Töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** - A kapcsolat megjelenítendő neve.
* **Leírás** - A projekt leírása.
* **SAS URL-cím** – Az Azure Blob Storage-tároló megosztott hozzáférésű url-címe. A SAS URL-címének beolvasásához nyissa meg a Microsoft Azure Storage Exploreralkalmazást, kattintson a jobb gombbal a tárolóra, és válassza **a Megosztott hozzáférésű aláírás beolvasása parancsot.** Állítsa be a lejárati időt egy időre a szolgáltatás használatba helyezését követően. Győződjön meg arról, hogy az **Olvasás**, **Írás**, **Törlés**és **Lista** engedélyek be vannak jelölve, majd kattintson a **Létrehozás gombra.** Ezután másolja az **URL-cím** szakasz értékét. Meg kell a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .

![A mintacímkéző eszköz kapcsolati beállításai](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Új projekt létrehozása

A mintacímkéző eszközben a projektek a konfigurációkat és a beállításokat tárolják. Hozzon létre egy új projektet, és töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** - a projekt megjelenítendő neve
* **Biztonsági jogkivonat** – Egyes projektbeállítások tartalmazhatnak bizalmas értékeket, például API-kulcsokat vagy más megosztott titkos kulcsokat. Minden projekt létrehoz egy biztonsági jogkivonatot, amely a bizalmas projektbeállítások titkosítására/visszafejtésére használható. A biztonsági jogkivonatokat az Alkalmazás beállítások ban találhatja meg, ha a bal oldali navigációs sáv alján található fogaskerék ikonra kattint.
* **Forráskapcsolat** – Az előző lépésben létrehozott Azure Blob Storage-kapcsolat, amelyet ehhez a projekthez szeretne használni.
* **Mappa elérési útja** – Nem kötelező – Ha a forrásűrlapok a blobtároló egyik mappájában találhatók, itt adja meg a mappa nevét
* **Form Recognizer Service Uri** – Az űrlapfelismerő végpont URL-címe.
* **API-kulcs** – Az űrlapfelismerő előfizetési kulcsa.
* **Leírás** - Nem kötelező - projekt leírása

![Új projektlap a mintacímkéző eszközön](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Az űrlapok címkézése

Projekt létrehozásakor vagy megnyitásakor megnyílik a fő címkeszerkesztő ablak. A címkeszerkesztő három részből áll:

* Átméretezhető előnézeti ablaktábla, amely a forráskapcsolatból származó űrlapok görgethető listáját tartalmazza.
* A fő szerkesztő ablaktábla, amely lehetővé teszi a címkék alkalmazását.
* A címkék szerkesztője ablaktábla, amely lehetővé teszi a felhasználók számára a címkék módosítását, zárolását, átrendezését és törlését. 

### <a name="identify-text-elements"></a>Szövegelemek azonosítása

Kattintson **az OCR futtatása gombra** a bal oldali ablaktáblában lévő összes fájlon az egyes dokumentumok szövegelrendezési adatainak lekérni. A címkéző eszköz határolókereteket rajzol az egyes szövegelemek köré.

### <a name="apply-labels-to-text"></a>Címkék alkalmazása szövegre

Ezután címkéket (címkéket) hoz létre, és alkalmazza őket a modell által felismert szövegelemekre.

1. Először a címkék szerkesztőablakában hozza létre az azonosítandó címkéket.
   1. Ide **+** kattintva új címkét hozhat létre.
   1. Írja be a címke nevét.
   1. A címke mentéséhez nyomja le az Enter billentyűt.
1. A főszerkesztőben kattintással és húzással jelöljön ki egy vagy több szót a kiemelt szövegelemekközül.
1. Kattintson az alkalmazni kívánt címkére, vagy nyomja meg a megfelelő billentyűzetbillentyűt. A számkulcsok az első 10 címke gyorsbillentyűjeként vannak hozzárendelve. A címkéket átrendezheti a címkeszerkesztő ablaktáblában található fel és le nyíl ikonnal.
    > [!Tip]
    > Az űrlapok címkézésekénél tartsa szem előtt az alábbi tippeket.
    > * Minden kijelölt szövegelemre csak egy címkét alkalmazhat.
    > * Minden címke oldalanként csak egyszer alkalmazható. Ha egy érték többször is megjelenik ugyanazon az űrlapon, hozzon létre különböző címkéket minden példányhoz. Például: "számla# 1", "számla# 2" és így tovább.
    > * A címkék nem terjedhetnek át az oldalakon.
    > * Az űrlapon megjelenő feliratok értékei; ne próbáljon meg két részre osztani egy értéket két különböző címkével. Egy címmezőt például egyetlen címkével kell ellátni, még akkor is, ha az több sorra terjed ki.
    > * Ne adjon meg csak az&mdash;értékeket a címkézett mezőkben.
    > * A táblaadatokat a rendszer automatikusan észleli, és a végső kimeneti JSON-fájlban is elérhetővé teszi. Ha azonban a modell nem észleli az összes táblaadatot, manuálisan is címkézheti ezeket a mezőket. A táblázat minden celláját más címkével címkézze fel. Ha az űrlapokon különböző számú sorú táblák vannak, győződjön meg arról, hogy legalább egy űrlapot a lehető legnagyobb táblával címkéz.

![A mintacímkéző eszköz főszerkesztőablaka](../media/label-tool/main-editor.png)

A fenti lépéseket követve legalább öt űrlapot címkézhet fel.

### <a name="specify-tag-value-types"></a>Címkeérték-típusok megadása

Szükség esetén beállíthatja az egyes címkék várt adattípusát. Nyissa meg a címkétől jobbra lévő helyi menüt, és válasszon ki egy típust a menüből. Ez a funkció lehetővé teszi, hogy az észlelési algoritmus bizonyos feltételezéseket, amelyek javítják a szöveg-észlelési pontosságot. Azt is biztosítja, hogy az észlelt értékek et a végső JSON-kimenetben szabványos formátumban adja vissza. 

> [!div class="mx-imgBorder"]
> ![Értéktípus-kijelölés mintacímkéző eszközzel](../media/whats-new/formre-value-type.png)

A következő értéktípusok és változatok jelenleg támogatottak:
* `string`
    * alapértelmezett, `no-whitespaces`,`alphanumeric`
* `number`
    * Alapértelmezett`currency`
* `date` 
    * alapértelmezett, `dmy` `mdy`, ,`ymd`
* `time`
* `integer`

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

A bal oldali ablaktábla Vonat ikonjára kattintva nyissa meg a Képzés lapot. Ezután kattintson a **Vonat** gombra a modell betanításának megkezdéséhez. A betanítási folyamat befejezése után a következő információk jelennek meg:

* **Modellazonosító** – a létrehozott és betanított modell azonosítója. Minden betanítási hívás létrehoz egy új modellt saját azonosítóval. Másolja ezt a karakterláncot biztonságos helyre; szüksége lesz rá, ha előrejelzési hívásokat szeretne tenni a REST API-n keresztül.
* **Átlagos pontosság** – A modell átlagos pontossága. A modell pontosságát további űrlapok címkézésével és új modell létrehozásához újra betanítással javíthatja. Javasoljuk, hogy öt űrlap címkézésével és szükség szerint további űrlapok hozzáadásával kezdje.
* A címkék listája és a címkénkénti becsült pontosság.

![képzési nézet](../media/label-tool/train-screen.png)

A betanítás befejezése után vizsgálja meg az **Átlagos pontosság értékét.** Ha alacsony, adjon hozzá további bemeneti dokumentumokat, és ismételje meg a fenti lépéseket. A már címkézett dokumentumok a projektindexben maradnak.

> [!TIP]
> A betanítási folyamat ot rest API-hívással is futtathatja. Ennek módjáról a [Vonat címkék python használatával](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Űrlap elemzése

Kattintson a Tippelés (villanykörte) ikonra a bal oldalon a modell teszteléséhez. Töltsön fel egy olyan űrlapdokumentumot, amelyet nem használt a betanítási folyamat során. Ezután kattintson a jobb oldali **Előrejelzés** gombra az űrlap kulcs-/érték-előrejelzésének leéséhez. Az eszköz címkéket alkalmaz a határolódobozokban, és minden címke megbízhatóságát jelenti.

> [!TIP]
> Az Elemzés API-t rest-hívással is futtathatja. Ennek módjáról a [Vonat címkék python használatával](./python-labeled-data.md).

## <a name="improve-results"></a>Az eredmények javítása

A jelentett pontosságtól függően érdemes lehet további képzést végezni a modell javítása érdekében. Miután elvégezte az előrejelzést, vizsgálja meg az alkalmazott címkék megbízhatósági értékeit. Ha az átlagos pontosságbetanítási érték magas volt, de a megbízhatósági pontszámok alacsonyak (vagy az eredmények pontatlanok), hozzá kell adnia az előrejelzéshez használt fájlt a betanítási készletbe, címkézze fel, és újra betanítsa.

A jelentett átlagos pontosság, megbízhatósági pontszámok és a tényleges pontosság lehet inkonzisztens, ha az elemzett dokumentumok eltérnek a képzés során használt. Ne feledje, hogy egyes dokumentumok hasonlóak, ha az emberek megtekintik, de az AI-modellhez képest különbözőek lehetnek. Például betanítása két változatból álló űrlaptípussal, ahol a betanítási készlet 20%- os A és 80%-os B változatból áll. Az előrejelzés során az A változat dokumentumainak megbízhatósági pontszámai valószínűleg alacsonyabbak lesznek.

## <a name="save-a-project-and-resume-later"></a>Projekt mentése és folytatása később

Ha a projektet egy másik időpontban vagy egy másik böngészőben szeretné folytatni, mentenie kell a projekt biztonsági jogkivonatát, és később újra be kell írnia. 

### <a name="get-project-credentials"></a>Projekthitelesítő adatok beszerezése
Lépjen a projektbeállítások lapra (csúszkaikon), és vegye figyelembe a biztonsági jogkivonat nevét. Ezután lépjen az alkalmazás beállításaihoz (fogaskerék ikon), amely az aktuális böngészőpéldány összes biztonsági jogkivonatát megjeleníti. Keresse meg a projekt biztonsági jogkivonatát, és másolja a nevét és a kulcsértékét egy biztonságos helyre.

### <a name="restore-project-credentials"></a>Projekthitelesítő adatok visszaállítása
Ha folytatni szeretné a projektet, először létre kell hoznia egy kapcsolatot ugyanahhoz a blobstorage-tárolóhoz. Ehhez ismételje meg a fenti lépéseket. Ezután lépjen az alkalmazás beállítások oldalra (fogaskerék ikon), és nézd meg, hogy a projekt biztonsági jogkivonata ott van-e. Ha nem, adjon hozzá egy új biztonsági jogkivonatot, és másolja át a jogkivonat nevét és kulcsát az előző lépésből. Ezután kattintson a Beállítások mentése gombra. 

### <a name="resume-a-project"></a>Projekt folytatása

Végül lépjen a főoldalra (ház ikon), és kattintson a Felhőprojekt megnyitása gombra. Ezután válassza ki a blob tárolási kapcsolatot, és válassza ki a projekt *.vott* fájlját. Az alkalmazás betölti a projekt összes beállítását, mert rendelkezik a biztonsági jogkivonatkal.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Űrlapfelismerő mintacímkéző eszközt a modell manuálisan címkézett adatokkal történő betanításához. Ha szeretné integrálni a címkézési eszközt a saját alkalmazásába, használja a REST API-kat, amelyek a címkézett adatoktatással foglalkoznak.

> [!div class="nextstepaction"]
> [Vonat címkékkel python használatával](./python-labeled-data.md)
