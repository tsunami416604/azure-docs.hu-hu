---
title: 'Rövid útmutató: űrlapok címkézése, modell betanítása és az űrlap elemzése a minta feliratozási eszköz – űrlap felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő minta címkézési eszköz használatával manuálisan címkézheti az űrlapos dokumentumokat. Ezután betanít egy egyéni modellt a címkével ellátott dokumentumokkal, és a modell használatával kinyerheti a kulcs/érték párokat.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 8ab673c1a268f5ab663e8f423dd9b60cdfde14ab
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118373"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Űrlap-felismerő modell betanítása címkékkel a minta feliratozási eszköz használatával

Ebben a rövid útmutatóban az űrlap-felismerő REST API és a minta feliratozási eszköz használatával végezheti el a manuálisan címkézett adattípusú egyéni modell betanítását. A szolgáltatással kapcsolatos további információkért tekintse meg az Áttekintés a [címkékkel](../overview.md#train-with-labels) foglalkozó szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:

- Legalább hat egyforma típusú formátumból álló készlet. Ezeket az adattípusokat fogja használni a modell betanításához és egy űrlap teszteléséhez. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat. Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy Azure Storage-fiókban.

## <a name="set-up-the-sample-labeling-tool"></a>A minta feliratozási eszköz beállítása

A minta címkéző eszköz futtatásához a Docker-motort fogja használni. A Docker-tároló beállításához kövesse az alábbi lépéseket. A Docker és a Container alapjairól a [Docker áttekintésében](https://docs.docker.com/engine/docker-overview/)talál további információt.
1. Először telepítse a Docker-t egy gazdagépre. A gazdaszámítógép lehet a helyi számítógép ([Windows](https://docs.docker.com/docker-for-windows/), [MacOS](https://docs.docker.com/docker-for-mac/)vagy [Linux](https://docs.docker.com/install/)). Vagy használhat egy Docker-üzemeltetési szolgáltatást az Azure-ban, mint például az [Azure Kubernetes szolgáltatás](https://docs.microsoft.com/azure/aks/index), [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/index)vagy egy [Azure stack üzembe helyezett](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910)Kubernetes-fürt. A gazdagépnek meg kell felelnie a következő hardverkövetelmények követelményeinek:

    | Tároló | Minimális | Ajánlott|
    |:--|:--|:--|
    |Minta címkéző eszköz|2 mag, 4 GB memória|4 mag, 8 GB memória|
    
1. Szerezze be a minta címkéző eszköz tárolóját a `docker pull` paranccsal.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Most már készen áll a tároló futtatására `docker run`használatával.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Ezzel a paranccsal a minta feliratozási eszköz elérhetővé válik egy webböngészőn keresztül. Nyissa meg a következőt: [http://localhost:3000](http://localhost:3000).

> [!NOTE]
> A dokumentumokat és a betanítási modelleket a Form felismerő REST API használatával is címkézheti. A REST API betanításához és elemzéséhez lásd: [a betanítás a REST API és a Python használatával](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Bemeneti adatok beállítása

Először győződjön meg arról, hogy az összes betanítási dokumentum formátuma azonos. Ha több formátumban is rendelkezik űrlapokkal, a közös formátum alapján rendezheti őket almappákba. A betanítás során az API-t egy almappába kell irányítani.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Tartományok közötti erőforrás-megosztás (CORS) konfigurálása

Engedélyezze a CORS a Storage-fiókban. Válassza ki a Storage-fiókját a Azure Portalban, és kattintson a bal oldali ablaktábla **CORS** fülére. Az alsó sorban adja meg a következő értékeket. Ezután kattintson a felső **Mentés** gombra.

* Engedélyezett Origins = * 
* Engedélyezett metódusok = \[az összes kijelölése\]
* Engedélyezett fejlécek = *
* Elérhető fejlécek = * 
* Max Age = 200

> [!div class="mx-imgBorder"]
> ![CORS beállítása a Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Kapcsolódás a minta feliratozási eszközhöz

A minta feliratozási eszköz egy forráshoz (az eredeti űrlapokhoz) és egy célhoz (a létrehozott feliratok és a kimeneti adatokat tartalmazó helyhez) csatlakozik.

A kapcsolatok beállítható és megoszthatók a projektek között. Egy bővíthető szolgáltatói modellt használnak, így egyszerűen hozzáadhat új forrás-és célkiszolgáló-szolgáltatókat.

Új kapcsolat létrehozásához kattintson az **új kapcsolatok** (beépülő) ikonra a bal oldali navigációs sávon.

Töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** – a kapcsolatok megjelenítendő neve.
* **Leírás** – a projekt leírása.
* **Sas URL-cím** – az Azure Blob Storage tároló megosztott hozzáférés-aláírási (SAS) URL-címe. Az SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. A szolgáltatás használatának elkezdése után állítsa be a lejárati időt. Győződjön meg arról, hogy az **olvasási**, **írási**, **törlési**és **listázási** engedélyek be vannak jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![A minta-címkéző eszköz csatlakoztatási beállításai](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Új projekt létrehozása

A minta feliratozási eszközben a projektek a konfigurációkat és a beállításokat tárolják. Hozzon létre egy új projektet, és töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** – a projekt megjelenítendő neve
* **Biztonsági jogkivonat** – egyes Project-beállítások bizalmas értékeket is tartalmazhatnak, például API-kulcsokat vagy más közös titkokat. Minden projekt egy biztonsági jogkivonatot állít elő, amely a bizalmas projektek beállításainak titkosítására és visszafejtésére használható. A biztonsági jogkivonatok a bal oldali navigációs sáv alsó sarkában található fogaskerék ikonra kattintva találhatók meg az alkalmazás beállításainál.
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

Ezután létre kell hoznia a címkéket, és alkalmaznia kell azokat a szöveges elemekre, amelyeket fel szeretne ismerni a modellből.

1. Először a címkék szerkesztő paneljén hozza létre azokat a címkéket (címkéket), amelyeket azonosítani szeretne.
1. A fő szerkesztőben kattintson és húzással jelöljön ki egy vagy több szót a Kiemelt szöveges elemek közül.

    > [!NOTE]
    > Jelenleg nem választhat több oldalra kiterjedő szöveget.
1. Kattintson az alkalmazni kívánt címkére, vagy nyomja le a megfelelő billentyűt. Csak egy címkét alkalmazhat az egyes kijelölt szöveges elemekre, és az egyes címkék csak egyszer alkalmazhatók oldalanként.

    > [!TIP]
    > A kulcsok az első tíz címkéhez gyorsbillentyűként vannak hozzárendelve. A címkéket átrendezheti a címke-szerkesztő ablaktábla fel és le nyíl ikonjának használatával.

Kövesse a fenti lépéseket az űrlapok öt megjelöléséhez, majd folytassa a következő lépéssel.

![A minta-címkéző eszköz főszerkesztő ablaka](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Egyéni modell betanítása

A betanítási oldal megnyitásához kattintson a bal oldali panel vonat ikonjára (a vonat autója). Ezután kattintson a **vonat** gombra a modell tanításának megkezdéséhez. A betanítási folyamat befejezése után a következő információk láthatók:

* **Modell azonosítója** – a létrehozott és betanított modell azonosítója. Minden betanítási hívás létrehoz egy új modellt a saját azonosítójával. A karakterlánc másolása biztonságos helyre; szüksége lesz rá, ha előrejelzési hívásokat kíván végrehajtani a REST APIon keresztül.
* **Átlagos pontosság** – a modell átlagos pontossága. A modell pontosságát úgy javíthatja, ha további űrlapokat és képzést is felcímkéz, és új modellt hoz létre. Javasoljuk, hogy öt űrlap feliratozásával kezdjen hozzá, és szükség esetén további űrlapokat adjon hozzá.
* A címkék és a becsült pontosság a címkén.

![képzés nézet](../media/label-tool/train-screen.png)

A betanítás befejezése után vizsgálja meg az **átlagos pontossági** értéket. Ha alacsony, adjon hozzá további bemeneti dokumentumokat, és ismételje meg a fenti lépéseket. A már címkézett dokumentumok a projekt indexében maradnak.

> [!TIP]
> A betanítási folyamatot REST API hívással is futtathatja. Ennek megismeréséhez tekintse meg a [címkék a Python használatával történő betanítását](./python-labeled-data.md)ismertető témakört.

## <a name="analyze-a-form"></a>Űrlap elemzése

Kattintson a bal oldali előrejelzés (téglalapok) ikonra a modell teszteléséhez. Töltse fel a betanítási folyamat során nem használt űrlap-dokumentumot. Ezután kattintson a jobb oldali **Előrejelzés** gombra az űrlaphoz tartozó kulcs/érték előrejelzések beszerzéséhez. Az eszköz címkét fog alkalmazni a határolókeret mezőiben, és az egyes címkék megbízhatóságát fogja jelenteni.

> [!TIP]
> Az elemzés API-t REST-hívással is futtathatja. Ennek megismeréséhez tekintse meg a [címkék a Python használatával történő betanítását](./python-labeled-data.md)ismertető témakört.

## <a name="improve-results"></a>Az eredmények javítása

A jelentett pontosságtól függően érdemes lehet további képzést végezni a modell fejlesztéséhez. Miután elvégezte az előrejelzést, vizsgálja meg az egyes alkalmazott címkék megbízhatósági értékeit. Ha az átlagos pontossági érték magas volt, de a megbízhatósági pontszámok alacsonyak (vagy az eredmények pontatlanok), adja hozzá az előrejelzéshez használt fájlt a betanítási készlethez, címkézze fel, és ismételje meg a betanítást.

A jelentett átlagos pontosság, a megbízhatósági pontszám és a tényleges pontosság inkonzisztens lehet, ha az elemzett dokumentumok eltérnek a betanításban használt dokumentumtól. Ne feledje, hogy egyes dokumentumok ugyanúgy néznek ki, mint a felhasználók, de az AI-modellre is kitűnnek. Előfordulhat például, hogy a betanítás két változattal rendelkezik, ahol a betanítási készlet 20%-os és 80%-os változatot tartalmaz. Az előrejelzés során az A variációs dokumentumok megbízhatósági pontszámai valószínűleg alacsonyabbak lesznek.

## <a name="save-a-project-and-resume-later"></a>Projekt mentése és későbbi folytatás

Ha a projektet egy másik időpontban vagy egy másik böngészőben szeretné folytatni, mentse a projekt biztonsági jogkivonatát, és később adja meg újra. 

### <a name="get-project-credentials"></a>Projekt hitelesítő adatainak beolvasása
Lépjen a Project Settings (csúszka ikon) lapra, és jegyezze fel a biztonsági jogkivonat nevét. Ezután nyissa meg az alkalmazás beállításait (fogaskerék ikon), amely megjeleníti az aktuális böngésző-példány összes biztonsági jogkivonatát. Keresse meg a projekt biztonsági jogkivonatát, és másolja a nevét és a kulcs értékét egy biztonságos helyre.

### <a name="restore-project-credentials"></a>A projekt hitelesítő adatainak visszaállítása
Ha folytatni szeretné a projekt folytatását, először létre kell hoznia egy kapcsolódást ugyanahhoz a blob Storage-tárolóhoz. Ehhez hajtsa végre a fenti lépéseket. Ezután nyissa meg az Alkalmazásbeállítások lapot (fogaskerék ikon), és ellenőrizze, hogy van-e a projekt biztonsági jogkivonata. Ha nem, adjon hozzá egy új biztonsági jogkivonatot, és másolja át a token nevét és kulcsát az előző lépésből. Ezután kattintson a beállítások mentése gombra. 

### <a name="resume-a-project"></a>Projekt folytatása
Végül nyissa meg a Főoldalt (ház ikon), és kattintson a Cloud Project megnyitása lehetőségre. Ezután válassza ki a blob Storage-kapcsolatokat, és válassza ki a projekt *. vott* fájlját. Az alkalmazás betölti a projekt összes beállítását, mert a biztonsági jogkivonattal rendelkezik.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja az űrlap-felismerő minta címkézési eszközt egy olyan modell betanításához, amely manuálisan címkézett adattal rendelkezik. Ha szeretné integrálni a címkéző eszközt a saját alkalmazásba, használja a megcímkézett adatok betanításával foglalkozó REST API-kat.

> [!div class="nextstepaction"]
> [Betanítás címkékkel a Python használatával](./python-labeled-data.md)
