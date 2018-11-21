---
title: Az Azure AI-katalógusban kísérleteket |} A Microsoft Docs
description: Fedezze fel, és megoszthatja a kísérleteket az Azure AI-katalógusban.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.openlocfilehash: 43e5cc14ac98b85dd50391380033834cf642b1a9
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264471"
---
# <a name="discover-experiments-in-azure-ai-gallery"></a>Fedezze fel az Azure AI-katalógusban kísérletek

[Az Azure AI-katalógusban](http://gallery.cortanaintelligence.com) széles körének rendelkezik [kísérletek](https://gallery.cortanaintelligence.com/experiments) a fejlesztett [Azure Machine Learning Studio](https://studio.azureml.net). A gyors proof-of-concept kísérletek, amelyek bemutatják a machine learning-technika, az összetett gépi tanulási problémák teljes mértékben saját fejlesztésű megoldások adott tartomány kísérletek.

> [!NOTE]
> Egy ***kísérletezhet*** van a vásznon, a Machine Learning Studio prediktív elemzési modellek létrehozására használhatja. A modell különböző elemzési modulok csatlakoztatásával adatokat hoz létre. Próbálja ki a különböző ötleteket, próbafuttatások tegye, és végül helyezi üzembe a modellt webszolgáltatásként, amely az Azure-ban. Egy egyszerű kísérlet létrehozása példa: [Machine learning-oktatóanyag: az első kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md). Prediktív elemzési megoldások létrehozása egy átfogóbb leírását lásd: [forgatókönyv: a hitelkockázat értékelése az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Ismertetők
Kísérletek böngészéséhez [a katalógusban](http://gallery.cortanaintelligence.com), válassza ki a katalógus kezdőlap tetején **kísérletek**.

A **[kísérletek](https://gallery.cortanaintelligence.com/experiments)** lap nemrégiben hozzáadott és népszerű kísérletek listáját jeleníti meg. Az összes kísérletek megtekintéséhez válassza ki a **összes** gombra. Egy adott kísérletet keres, válassza ki a **összes**, és majd szűrőfeltételeket. A keresési kifejezéseket is megadhatja a **keresési** a katalógus lap tetején.

Kaphat kísérletet a kísérlet ismertető lapon tájékozódhat. Egy kísérlet Részletek lap megnyitásához, jelölje ki a kísérletet. Kísérlet a Részletek lap a **megjegyzések** szakaszban el megjegyzés, visszajelzés, vagy a kísérlet kapcsolatos kérdések feltevése. A kísérlet barátainak vagy Twitter- vagy LinkedIn munkatársaival is megoszthatja. A kísérlet részleteit megjelenítő oldalra, más felhasználók számára a lap megtekintéséhez meghívni egy hivatkozást is képes mail.

![Ez az elem megoszthatja ismerősök felvételének engedélyezése](./media/gallery-how-to-use-contribute-publish/share-links.png)

![A saját Megjegyzések hozzáadása](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Letöltés
Letöltheti a katalógusban található összes kísérlet egy példányát a Machine Learning Studio-munkaterülethez. Ezt követően módosíthatja a saját megoldásokat hozhat létre másolási.

Az Azure AI-katalógusban importálhat egy másolatot egy kísérlet két lehetőséget kínál:

* **A katalógusból**. Ha azt tapasztalja, egy kísérlet, amelynek a katalógusban, töltse le, és nyissa meg a Machine Learning Studio munkaterületét.
* **A Machine Learning Studióba**. A Machine Learning Studióban használhatja bármilyen kísérlet a katalógusban sablont egy új kísérlet létrehozásához.

### <a name="from-the-gallery"></a>A katalógusból

1. A katalógusban nyissa meg a kísérlet részletei lapon.
2. Válassza ki **Megnyitás a Studióban**.

    ![Nyissa meg kísérlet a katalógusból](./media/gallery-experiments/open-experiment-from-gallery.png)

Ha bejelöli **Megnyitás a Studióban**, a kísérletben a Machine Learning Studio munkaterületét nyílik meg. (Ha még nem jelentkezett Machine Learning Studióban, kéri az első bejelentkezéskor a Microsoft-fiókjával.)

### <a name="from-within-machine-learning-studio"></a>A Machine Learning Studióba

1. A Machine Learning Studióban, válassza ki a **új**.
2. Válassza ki **kísérlet**. A katalógus kísérletek listájából válassza ki, vagy keresse meg egy egyedi kísérletet használatával a **keresési** mezőbe.
3. Mutasson az egérmutatóval a kísérletet, és válassza ki **Megnyitás a Studióban**. (A kísérlet kapcsolatos információk megtekintéséhez válasszon **megjelenítése katalógusban**. Ekkor megjelenik a katalógusban a kísérlet részleteit megjelenítő oldalra.)

    ![Nyissa meg a katalógus kísérletezhet a Machine Learning Studio belül](./media/gallery-experiments/open-experiment-from-studio.png)

Testre szabhatja, ismételt futtatásával, és üzembe helyezése egy letöltött kísérletet, mint bármilyen más kísérlet a Machine Learning Studióban létrehozott.

![Kísérlet a Studio megnyitása](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Közreműködés
Amikor bejelentkezik a katalógusban, a katalógus Közösség egy tagja lesz. A Közösség tagjaként működhet közre a saját kísérletek, így más felhasználók is kihasználhatják a korábban felderített megoldások.

### <a name="publish-your-experiment-to-the-gallery"></a>A kísérlet katalógusbeli közzététele

1. Jelentkezzen be a Microsoft-fiók használatával Machine Learning Studióban.
2. A kísérlet létrehozása, és futtassa azt.
3. Ha készen áll a kísérlethez közzététele a katalógusban a műveleteknek a listája, a kísérletvászon alatt válassza ki a **közzététel a katalógus**.

    ![Válassza a "Gyűjtemény közzététele"](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. Az a **kísérlet leírás** lap, adja meg egy címet és címkék. Ellenőrizze a címet és a címkék leíró. Jelölje ki a használt technikák, vagy a való életből vett probléma megoldására. Egy leíró kísérlet címe például "bináris osztályozás: Twitter-Hangulatelemzés."

    ![Adja meg a címet és a címkék a közzétételre](./media/gallery-experiments/experiment-description.png)
5. Az a **összefoglaló** mezőbe írja be a kísérlethez összegzését. Röviden ismertesse a kísérlet megoldja a problémát, és hogyan válaszadásra.
6. Az a **részletes LEÍRÁST** mezőben írja le a lépéseket, az egyes részek a kísérlet. Néhány hasznos témakörök tartalmazzák a következők:
   * Kísérlet diagram képernyőképe
   * Az adatforrások és magyarázat
   * Adatfeldolgozás
   * Jellemzőkiemelés
   * Modell leírása
   * Eredmények és modellek teljesítményének kiértékelése

   A leírás formázása a markdown használatával is. Hogyan fog kinézni a bejegyzéseket a kísérlet leírás oldalon, a kísérlet közzétételekor megtekintéséhez válasszon **előzetes**.

   ![Válassza az "Előnézet" megtekintheti az szöveg fog kinézni](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > A megadott markdown-szerkesztő és az előzetes verzió kis szövegmezők. Azt javasoljuk, hogy írja be a kísérlet dokumentációjában egy markdown-szerkesztő, másolja, és illessze be a szövegmezőbe a katalógusban a befejezett dokumentációját. Után is futtathatja a kísérletet tesz közzé, amelyet Ön is olyanokkal standard web-alapú eszközök segítségével szerkesztése és előnézete számára, hogy használja markdown.

7. Az a **a kijelölési** lapon a asztalnak a miniatűrjére, a kísérlethez. Az asztalnak a miniatűrjére a kísérlet részletei lap, és a kísérlet csempén legfelül jelenik meg. Más felhasználók látni fogják az asztalnak a miniatűrjére, a gyűjtemény böngészése közben. Töltsön fel egy képet a számítógépről, vagy megvásárolható kép kiválasztása a katalógusból.
    </br>
    ![Töltse fel, vagy válasszon ki egy képet a katalógus](./media/gallery-experiments/select-gallery-image.png)
8. A a **beállítások** lap **láthatósági**, döntse el, hogy a tartalom nyilvánosan közzé (**nyilvános**) vagy az, hogy csak a személyeket, akik rendelkeznek a lap (mutató hivatkozás számára elérhető **Fel nem sorolt**).

    ![Döntse el, hogy nyilvánosan, illetve fel nem sorolt közzététele](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Ha azt szeretné, hogy a dokumentáció helyesnek tűnik nyilvánosan kiadása előtti, közzéteheti a kísérletet, először **Unlisted**. Később, módosíthatja a láthatósági való **nyilvános** kísérlet részleteit megjelenítő oldalon.
   >
   >
9. A kísérlet közzététele a katalógusban, jelölje be a **OK** pipa jelre.

    ![Válassza ki az OK pipára a kísérlet közzététele](./media/gallery-experiments/ok-checkmark.png)

Hogyan tehet közzé egy kiváló minőségű katalógus kísérletet a tippek: [dokumentálásáért és közzététele a kísérlethez tippek](#tips-for-documenting-and-publishing-your-experiment).

Ennyi – az összes elkészült.

Most már megtekintheti a kísérlet során a katalógusban, és hivatkozás megosztása másokkal. Ha közzé használatával is futtathatja a kísérletet a **nyilvános** láthatóságának beállítását, a katalógusban tallózással keresse meg és a keresési eredmények megjelennek a kísérletet. A kísérlet dokumentációs kísérlet részleteit megjelenítő oldalon jelentkezett be a katalógus bármikor szerkesztheti.

Hozzájárulások listájának megtekintéséhez válassza ki a lemezképet bármely katalógus oldal jobb felső sarkában. Válassza ki a nevét, a fiók lap megnyitásához.

![Válassza ki a fiók nevét](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>A kísérlet frissítése
Ha szeretné, módosíthatja a munkafolyamatnak a kísérletet, amelyek a katalógusban közzétett (modulok, paraméterek és így tovább). A Machine Learning Studióban ne módosítsa, győződjön meg arról, a kísérletvászonra, és tegye közzé újra szeretné. A közzétett kísérletek frissülni fog a módosításokat.

Közvetlenül a katalógusban a kísérlethez módosíthatja a következő információkat:

* Kísérlet neve
* Összefoglalás vagy leírása
* Címkék
* Kép
* Láthatósági (**nyilvános** vagy **Unlisted**)

A kísérlet a katalógusból is törli.

Ezeket a módosításokat, vagy törölni a kísérletet a kísérlet részleteit megjelenítő oldalon vagy a profiloldalán a katalógusban.


#### <a name="from-the-experiment-details-page"></a>A kísérlet részleteit megjelenítő oldalon
A kísérlet ismertető lapon módosításához a részletek a kísérlethez válassza **szerkesztése**.

![Válassza ki a szerkesztési kísérletét szerkesztése](./media/gallery-experiments/edit-button.png)

A Részletek lap szerkesztési módba kerül. A módosításokat, válassza ki a **szerkesztése** mellett a kísérlet neve, Összegzés és címkék. Amikor végzett módosításokat, válassza ki a **kész**.

![Válassza a "Szerkesztés" adatok szerkesztéséhez, és válassza a "Kész" befejezése](./media/gallery-experiments/edit-details-page.png)

Módosíthatja a profilláthatósági beállításokról a kísérlet (**nyilvános** vagy **Unlisted**), vagy a katalógusból a kísérlet törléséhez válassza ki a **beállítások** ikonra.

![Válassza ki a "Beállítások" látható-e módosítani vagy törölni a kísérlet](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>A profiloldalán
A profil lapon jelölje ki a lefelé mutató nyílra a kísérlethez, és válassza **szerkesztése**. Ekkor megjelenik a kísérletbe és szerkesztési módban részleteit megjelenítő oldalon. Amikor végzett módosításokat, válassza ki a **kész**.

A kísérlet törölni a katalógusból, válassza ki a **törlése**.

![Válassza ki a "Szerkesztés" vagy "Delete"](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Dokumentálja a és a közzététele a kísérlethez tippek
* Akkor feltételezheti, hogy az olvasó rendelkezik a korábbi élmény adatelemzéshez, de a egyszerű nyelv hasznos lehet. Dolog, amikor csak lehetséges részletesen ismertetik.
* Cortana Intelligence Suite viszonylag új. Nem minden olvasók hogyan használható a tapasztalatokkal. Adjon meg elegendő információk és részletes magyarázatokat, keresse meg a kísérlethez olvasók könnyebben.
* Vizualizációk az olvasók számára értelmezi és megfelelően használja a kísérlet dokumentációjában hasznos lehet. Vizualizációk a kísérleti diagramok, képernyőfelvételekkel mutatja be, az adatok közé tartozik. A kísérlet dokumentációjában képeket is kapcsolatos további információkért tekintse meg a [a közzététellel kapcsolatos irányelvek és a példákat gyűjtemény](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Ha egy adatkészlet adja meg a kísérlet során (azaz nem importál az adatkészlet keresztül az adatok importálása modullal), a készlet része is futtathatja a kísérletet, és a katalógusban közzétett. Győződjön meg arról, hogy az adatkészlet közzététele licencelési feltételeit, amelyek lehetővé teszik a megosztási és bárki letöltése. Az Azure galériához vonatkozik [használati](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Mik a követelmények elküldése, és a egy kép kísérletemben szerkesztési?**

Elküldött is futtathatja a kísérletet a képek az hozzájárulásra egy kísérlet csempe létrehozásához használt. Azt javasoljuk, hogy képeket lehet kisebb, mint 500 KB-aspect ratio 3:2 és 960 felbontása &#215; 640.

**Mi történik, az adatkészlet, a kísérletben használt? Az adatkészlet is közzéteszi a katalógusban?**

Ha az adatkészlethez része is futtathatja a kísérletet, és keresztül az adatok importálása modullal nem importálása folyamatban van, az adatkészlet a katalógusban az kísérlet részeként van közzétéve. Győződjön meg arról, hogy az adatkészlet, amely közzéteszi a kísérlet során a megfelelő licencelési feltételeit. A licencelési feltételeket kell, hogy bárki megosztani, és töltse le az adatokat.

**Van egy kísérletet, amely az adatok Azure HDInsight vagy az SQL Server-adatok importálása modullal használatával. A hitelesítő adataimat használ az adatok lekéréséhez. Közzéteheti a kísérlet az ilyen típusú? Hogyan tudok biztos lehet abban, hogy nem osztható meg a hitelesítő adataimat?**

Jelenleg nem lehet közzétenni kísérletet, amelyek a hitelesítő adatokat használ a katalógusban.

**Hogyan nevezhetek több címke?**

Miután megadta a címkét, adjon meg egy másik címke, nyomja le a Tab billentyűt.

**[A katalógus megnyitása](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
