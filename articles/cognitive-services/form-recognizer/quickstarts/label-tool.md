---
title: 'Rövid útmutató: űrlapok címkézése, modell betanítása és az űrlap elemzése a minta feliratozási eszköz – űrlap felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő minta címkézési eszköz használatával manuálisan címkézheti az űrlapos dokumentumokat. Ezután betanít egy egyéni modellt a címkével ellátott dokumentumokkal, és a modell használatával kinyerheti a kulcs/érték párokat.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 234d6af25f37bf04d3240177142d5ef919e8dd2d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903476"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Űrlap-felismerő modell betanítása címkékkel a minta feliratozási eszköz használatával

Ebben a rövid útmutatóban az űrlap-felismerő REST API és a minta feliratozási eszköz használatával végezheti el a manuálisan címkézett adattípusú egyéni modell betanítását. A szolgáltatással kapcsolatos további információkért tekintse meg az Áttekintés a [címkékkel](../overview.md#train-with-labels) foglalkozó szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:

- Legalább hat egyforma típusú formátumból álló készlet. Ezeket az adattípusokat fogja használni a modell betanításához és egy űrlap teszteléséhez. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat. Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy standard teljesítményű Azure Storage-fiókban.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>A minta feliratozási eszköz beállítása

A minta címkéző eszköz futtatásához a Docker-motort fogja használni. A Docker-tároló beállításához kövesse az alábbi lépéseket. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> Az OCR űrlap címkéző eszköze nyílt forráskódú projektként is elérhető a GitHubon. Az eszköz a reakciós + Redux használatával létrehozott, írógéppel használható webalkalmazás. További információért vagy a közreműködés megismeréséhez tekintse meg az [OCR űrlap feliratozási eszközének](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) tárházát. Az eszköz online kipróbálásához lépjen a [FOTT webhelyére](https://fott.azurewebsites.net/).   

1. Először telepítse a Docker-t egy gazdagépre. Ez az útmutató bemutatja, hogyan használható a helyi számítógép gazdagépként. Ha Docker-üzemeltetési szolgáltatást szeretne használni az Azure-ban, tekintse meg a [minta címkézési eszköz üzembe helyezése](../deploy-label-tool.md) útmutató című témakört. 

   A gazdagépnek meg kell felelnie a következő hardverkövetelmények követelményeinek:

    | Tároló | Minimális | Ajánlott|
    |:--|:--|:--|
    |Minta címkéző eszköz|2 mag, 4 GB memória|4 mag, 8 GB memória|

   Telepítse a Docker-t a gépre az operációs rendszerének megfelelő utasítások követésével: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Szerezze be a minta címkéző eszköz tárolóját a `docker pull` paranccsal.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Most már készen áll a tároló futtatására a használatával `docker run` .
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Ezzel a paranccsal a minta feliratozási eszköz elérhetővé válik egy webböngészőn keresztül. Nyissa meg a következőt: `http://localhost:3000`.

> [!NOTE]
> A dokumentumokat és a betanítási modelleket a Form felismerő REST API használatával is címkézheti. A REST API betanításához és elemzéséhez lásd: [a betanítás a REST API és a Python használatával](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Bemeneti adatok beállítása

Először győződjön meg arról, hogy az összes betanítási dokumentum formátuma azonos. Ha több formátumban is rendelkezik űrlapokkal, a közös formátum alapján rendezheti őket almappákba. A betanítás során az API-t egy almappába kell irányítani.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Tartományok közötti erőforrás-megosztás (CORS) konfigurálása

Engedélyezze a CORS a Storage-fiókban. Válassza ki a Storage-fiókját a Azure Portalban, és kattintson a bal oldali ablaktábla **CORS** fülére. Az alsó sorban adja meg a következő értékeket. Ezután kattintson a felső **Mentés** gombra.

* Engedélyezett Origins = * 
* Engedélyezett metódusok = \[ összes kijelölése\]
* Engedélyezett fejlécek = *
* Elérhető fejlécek = * 
* Max Age = 200

> [!div class="mx-imgBorder"]
> ![CORS-telepítő a Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Kapcsolódás a minta feliratozási eszközhöz

A minta feliratozási eszköz egy forráshoz (az eredeti űrlapokhoz) és egy célhoz (ahol a létrehozott címkék és a kimeneti adatokat exportálja) csatlakozik.

A kapcsolatok beállítható és megoszthatók a projektek között. Egy bővíthető szolgáltatói modellt használnak, így egyszerűen hozzáadhat új forrás-és célkiszolgáló-szolgáltatókat.

Új kapcsolat létrehozásához kattintson az **új kapcsolatok** (beépülő) ikonra a bal oldali navigációs sávon.

Töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** – a kapcsolatok megjelenítendő neve.
* **Leírás** – a projekt leírása.
* **Sas URL-cím** – az Azure Blob Storage tároló megosztott hozzáférés-aláírási (SAS) URL-címe. Az SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. A szolgáltatás használatának elkezdése után állítsa be a lejárati időt. Győződjön meg arról, hogy az **olvasási**, **írási**, **törlési**és **listázási** engedélyek be vannak jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

![A minta-címkéző eszköz csatlakoztatási beállításai](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Új projekt létrehozása

A minta feliratozási eszközben a projektek a konfigurációkat és a beállításokat tárolják. Hozzon létre egy új projektet, és töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** – a projekt megjelenítendő neve
* **Biztonsági jogkivonat** – egyes Project-beállítások bizalmas értékeket is tartalmazhatnak, például API-kulcsokat vagy más közös titkokat. Minden projekt egy biztonsági jogkivonatot állít elő, amely a bizalmas projektek beállításainak titkosítására és visszafejtésére használható. A biztonsági jogkivonatokat a bal oldali navigációs sáv alján található fogaskerék ikonra kattintva tekintheti meg az Alkalmazásbeállítások között.
* **Forrásoldali kapcsolódás** – a projekthez használni kívánt előző lépésben létrehozott Azure Blob Storage-kapcsolódás.
* **Mappa elérési útja** – nem kötelező – ha a forrás űrlapjai a blob tároló egyik mappájában találhatók, itt adja meg a mappa nevét.
* **Űrlap-felismerő szolgáltatás URI-ja** – az űrlap-felismerő végpontjának URL-címe.
* **API-kulcs** – az űrlap-felismerő előfizetési kulcsa.
* **Leírás** – nem kötelező – a projekt leírása

![Új projekt lap a minta címkézési eszközön](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Űrlapok címkézése

Amikor létrehoz vagy megnyit egy projektet, megnyílik a fő címke-szerkesztő ablak. A címke szerkesztője három részből áll:

* Egy átméretezhető betekintő ablaktábla, amely a forrás-és az űrlapok görgethető listáját tartalmazza.
* A főszerkesztő ablaktábla, amely lehetővé teszi a címkék alkalmazását.
* A címkék szerkesztő panelje lehetővé teszi a felhasználók számára címkék módosítását, zárolását, átrendezését és törlését. 

### <a name="identify-text-elements"></a>Szöveges elemek azonosítása

Kattintson az OCR futtatása elemre a bal oldali ablaktábla **összes fájlján** az egyes dokumentumok szöveg-elrendezési adatainak lekéréséhez. A címkézési eszköz az egyes szöveges elemek köré rajzolja meg a határoló mezőket.

### <a name="apply-labels-to-text"></a>Feliratok alkalmazása szövegre

Ezután létre kell hoznia címkéket (címkéket), és alkalmaznia kell azokat a szöveges elemekre, amelyeket fel szeretne ismerni a modellből.

1. Először a címkék szerkesztő paneljén hozza létre az azonosítani kívánt címkéket.
   1. Kattintson ide **+** új címke létrehozásához.
   1. Adja meg a címke nevét.
   1. Nyomja le az ENTER billentyűt a címke mentéséhez.
1. A főszerkesztőben kattintson ide a Kiemelt szöveges elemek szavainak kiválasztásához.
1. Kattintson az alkalmazni kívánt címkére, vagy nyomja le a megfelelő billentyűt. A kulcsok az első 10 címkéhez gyorsbillentyűként vannak hozzárendelve. A címkéket átrendezheti a címke-szerkesztő ablaktábla fel és le nyíl ikonjának használatával.
    > [!Tip]
    > Az űrlapok címkézése során tartsa szem előtt az alábbi tippeket.
    > * Csak egy címkét alkalmazhat az egyes kijelölt szöveges elemekre.
    > * Az egyes címkék csak egyszer alkalmazhatók oldalanként. Ha egy érték többször is megjelenik ugyanazon az űrlapon, hozzon létre különböző címkéket az egyes példányokhoz. Például: "számla # 1", "számla # 2" és így tovább.
    > * A címkék nem terjedhetnek át a lapokra.
    > * Az űrlapon megjelenő címkézett értékek ne próbáljon két részre osztani egy értéket két különböző címkével. Például egy cím mezőt egyetlen címkével kell megcímkézni, még akkor is, ha több sort is felölel.
    > * A címkézett mezőkben ne szerepeljenek kulcsok, &mdash; csak az értékek.
    > * A tábla adatokat automatikusan kell észlelni, és a végső kimeneti JSON-fájlban lesznek elérhetők. Ha azonban a modell nem ismeri fel az összes tábla adatait, manuálisan is címkézheti ezeket a mezőket. Címkézze fel a tábla minden celláját egy másik címkével. Ha az űrlapok különböző számú sort tartalmazó táblázatokkal rendelkeznek, ügyeljen arra, hogy legalább egy űrlapot címkével lássa el a lehető legnagyobb táblázattal.
    > * A **+** címkék kereséséhez, átnevezéséhez, átrendezéséhez és törléséhez kattintson a jobb oldalon található gombokra.
    > * Ha el szeretné távolítani egy alkalmazott címkét a címke törlése nélkül, válassza ki a címkézett téglalapot a dokumentum nézetben, és nyomja le a DELETE billentyűt.

![A minta-címkéző eszköz főszerkesztő ablaka](../media/label-tool/main-editor.png)

Kövesse a fenti lépéseket az űrlapok legalább öt megjelöléséhez.

### <a name="specify-tag-value-types"></a>Címke típusú értékek megadása

Opcionálisan megadhatja az egyes címkék várt adattípusát. Nyissa meg a címke jobb oldalán a helyi menüt, és válasszon egy típust a menüből. Ez a funkció lehetővé teszi, hogy az észlelési algoritmus bizonyos feltételezéseket tegyen elérhetővé, amelyek javítják a szöveg-észlelési pontosságot. Azt is biztosítja, hogy a rendszer az észlelt értékeket a végső JSON-kimenet szabványosított formátumában adja vissza. 

> [!div class="mx-imgBorder"]
> ![Értéktípus kiválasztása a minta címkézési eszközzel](../media/whats-new/formre-value-type.png)

A következő típusú értékek és változatok jelenleg támogatottak:
* `string`
    * alapértelmezett, `no-whitespaces` ,`alphanumeric`
* `number`
    * alapértelmezett`currency`
* `date` 
    * alapértelmezett, `dmy` , `mdy` ,`ymd`
* `time`
* `integer`

> [!NOTE]
> A dátum formázásához tekintse meg a következő szabályokat:
> 
> A következő karakterek használhatók DMY dátum határolójelként: `, - / . \` . Szóközök nem használhatók határolójelként. Például:
> * 01, 01, 2020
> * 01-01-2020
> * 01/01/2020
>
> A nap és a hónap egy vagy két számjegyből állhat, az év pedig két vagy négy számjegyből állhat:
> * 1-1-2020
> * 1-01-20
>
> Ha egy DMY nyolc számjegyet tartalmaz, a határolójel nem kötelező:
> * 01012020
> * 01 01 2020
>
> A hónap teljes vagy rövid neveként is megadható. Ha a név használatos, a határoló karakterek nem kötelezőek:
> * 01/Jan/2020
> * 01Jan2020
> * 01 Jan 2020

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Kattintson a vonat ikonra a bal oldali ablaktáblán a képzés lap megnyitásához. Ezután kattintson a **vonat** gombra a modell tanításának megkezdéséhez. A betanítási folyamat befejezése után a következő információk láthatók:

* **Modell azonosítója** – a létrehozott és betanított modell azonosítója. Minden betanítási hívás létrehoz egy új modellt a saját azonosítójával. A karakterlánc másolása biztonságos helyre; szüksége lesz rá, ha az [REST API](./curl-train-extract.md) vagy az [ügyféloldali függvénytáron](./client-library.md)keresztül szeretne előrejelzési hívásokat végezni.
* **Átlagos pontosság** – a modell átlagos pontossága. A modell pontosságát úgy javíthatja, ha további űrlapokat és képzést is felcímkéz, és új modellt hoz létre. Javasoljuk, hogy öt űrlap feliratozásával kezdjen hozzá, és szükség esetén további űrlapokat adjon hozzá.
* A címkék és a becsült pontosság a címkén.

![képzés nézet](../media/label-tool/train-screen.png)

A betanítás befejezése után vizsgálja meg az **átlagos pontossági** értéket. Ha alacsony, adjon hozzá további bemeneti dokumentumokat, és ismételje meg a fenti lépéseket. A már címkézett dokumentumok a projekt indexében maradnak.

> [!TIP]
> A betanítási folyamatot REST API hívással is futtathatja. Ennek megismeréséhez tekintse meg a [címkék a Python használatával történő betanítását](./python-labeled-data.md)ismertető témakört.

## <a name="analyze-a-form"></a>Űrlap elemzése

Kattintson a bal oldali előrejelzés (villanykörte) ikonra a modell teszteléséhez. Töltse fel a betanítási folyamatban még nem használt űrlap-dokumentumot. Ezután kattintson a jobb oldali **Előrejelzés** gombra az űrlaphoz tartozó kulcs/érték előrejelzések beszerzéséhez. Az eszköz címkét fog alkalmazni a határolókeret mezőiben, és az egyes címkék megbízhatóságát fogja jelenteni.

> [!TIP]
> Az elemzés API-t REST-hívással is futtathatja. Ennek megismeréséhez tekintse meg a [címkék a Python használatával történő betanítását](./python-labeled-data.md)ismertető témakört.

## <a name="improve-results"></a>Az eredmények javítása

A jelentett pontosságtól függően érdemes lehet további képzést végezni a modell fejlesztéséhez. Miután elvégezte az előrejelzést, vizsgálja meg az egyes alkalmazott címkék megbízhatósági értékeit. Ha az átlagos pontossági érték magas volt, de a megbízhatósági pontszámok alacsonyak (vagy az eredmények pontatlanok), adja hozzá az előrejelzéshez használt fájlt a betanítási készlethez, címkézze fel, és ismételje meg a betanítást.

A jelentett átlagos pontosság, a megbízhatósági pontszám és a tényleges pontosság inkonzisztens lehet, ha az elemzett dokumentumok eltérnek a betanításban használt adatoktól. Ne feledje, hogy egyes dokumentumok ugyanúgy néznek ki, mint a felhasználók, de az AI-modellre is kitűnnek. Előfordulhat például, hogy a betanítás két változattal rendelkezik, ahol a betanítási készlet 20%-os és 80%-os változatot tartalmaz. Az előrejelzés során az A variációs dokumentumok megbízhatósági pontszámai valószínűleg alacsonyabbak lesznek.

## <a name="save-a-project-and-resume-later"></a>Projekt mentése és későbbi folytatás

Ha a projektet egy másik időpontban vagy egy másik böngészőben szeretné folytatni, mentse a projekt biztonsági jogkivonatát, és később adja meg újra. 

### <a name="get-project-credentials"></a>Projekt hitelesítő adatainak beolvasása
Lépjen a Project Settings (csúszka ikon) lapra, és jegyezze fel a biztonsági jogkivonat nevét. Ezután nyissa meg az alkalmazás beállításait (fogaskerék ikon), amely megjeleníti az aktuális böngésző-példány összes biztonsági jogkivonatát. Keresse meg a projekt biztonsági jogkivonatát, és másolja a nevét és a kulcs értékét egy biztonságos helyre.

### <a name="restore-project-credentials"></a>A projekt hitelesítő adatainak visszaállítása
Ha folytatni szeretné a projekt folytatását, először létre kell hoznia egy kapcsolódást ugyanahhoz a blob Storage-tárolóhoz. Ehhez ismételje meg a fenti lépéseket. Ezután nyissa meg az Alkalmazásbeállítások lapot (fogaskerék ikon), és ellenőrizze, hogy van-e a projekt biztonsági jogkivonata. Ha nem, adjon hozzá egy új biztonsági jogkivonatot, és másolja át a token nevét és kulcsát az előző lépésből. Ezután kattintson a beállítások mentése gombra. 

### <a name="resume-a-project"></a>Projekt folytatása

Végül nyissa meg a Főoldalt (ház ikon), és kattintson a Cloud Project megnyitása lehetőségre. Ezután válassza ki a blob Storage-kapcsolatokat, és válassza ki a projekt *. vott* fájlját. Az alkalmazás betölti a projekt összes beállítását, mert a biztonsági jogkivonattal rendelkezik.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az űrlap-felismerő minta címkézési eszközt egy olyan modell betanításához, amely manuálisan címkézett adattal rendelkezik. Ha szeretné integrálni a címkéző eszközt a saját alkalmazásba, használja a megcímkézett adatok betanításával foglalkozó REST API-kat.

> [!div class="nextstepaction"]
> [Betanítás címkékkel a Python használatával](./python-labeled-data.md)
