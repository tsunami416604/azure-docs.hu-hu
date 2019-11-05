---
title: Jupyter-jegyzetfüzetek létrehozása és klónozása az Azure-ban
description: Azure Notebooks-projektek jegyzetfüzetek és kapcsolódó fájlok gyűjteményét kezelik, amelyekkel új vagy klónozást hozhat létre más forrásokból.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 3e0c49d1141d976558ece325baaa99fc9981275e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496702"
---
# <a name="create-and-clone-projects"></a>Projektek létrehozása és klónozása

Azure Notebooks a Jupyter-jegyzetfüzeteket és a kapcsolódó fájlokat a *projektek*nevű logikai csoportba rendezi. Először hozzon létre egy projektet tárolóként, majd hozzon létre vagy klónozott több jegyzetfüzetet egy másik projektfájl mellett egy mappán belül. (Ezt a folyamatot az [oktatóanyag](tutorial-create-run-jupyter-notebook.md)mutatja be.)

A projektek metaadatokat és egyéb konfigurációs beállításokat is fenntartanak, amelyek hatással vannak arra a kiszolgálóra, amelyen a jegyzetfüzetek futnak, beleértve az egyéni telepítési lépéseket és a csomagok telepítését. További információ: [projektek kezelése és konfigurálása](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>A saját projektek irányítópult használata

A **saját projektek** irányítópultja `https://notebooks.azure.com/<userID>/projects`on, ahol megtekintheti, kezelheti és létrehozhatja a projekteket:

[![My projects irányítópultot a Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Az irányítópulton elvégezhető műveletek attól függnek, hogy a felhasználói azonosítót birtokló fiókkal jelentkezett-e be:

| Parancs | Elérhető: | Leírás |
| --- | --- | --- |
| **Futtassa** | Tulajdonos | Elindítja a Project kiszolgálót, és megnyitja a projekt mappáját a Jupyter. (Gyakrabban navigáljon először a projekt mappájába, majd indítson el egy jegyzetfüzetet innen.) |
| **Letöltés** | Bárki | A kiválasztott projekt másolatának letöltése ZIP-fájlként. |
| **Megosztás** | Bárki | Megjeleníti a megosztás felugró ablakát, amelyen keresztül beszerezhet egy URL-címet egy kiválasztott projekthez, megoszthatja a közösségi médiát, e-mailt küldhet az URL-címmel, valamint HTML-vagy Markdown-kódot is beszerezhet a "notebook elindítása" jelvényre (lásd: [indítási jelvény beszerzése](#obtain-a-launch-badge)) az URL-címmel. |
| **Törlés** | Tulajdonos | Törli a kiválasztott projektet. Ez a művelet nem vonható vissza. |
| **Terminál** | Tulajdonos | Elindítja a Project Server kiszolgálót, majd egy új böngészőablakot nyit meg az adott kiszolgálóhoz tartozó bash-terminálon. |
| **+ Új projekt** | Tulajdonos | Új projekt létrehozása. Lásd: [új projekt létrehozása](#create-a-new-project). |
| **GitHub-adattár feltöltése** | Tulajdonos | Projekt importálása a GitHubról. [Projekt importálása a githubról](#import-a-project-from-github). |
| **Klónozott** | Bárki | Egy kiválasztott projektet másol a saját fiókjába. Ha még nem tette meg, a rendszer felszólítja a bejelentkezésre. Lásd: [projekt klónozása](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Indítási jelvény beszerzése

Ha a **megosztás** parancsot használja, és kiválasztja a **beágyazás** lapot, akkor a "jegyzetfüzet indítása" jelvényt létrehozó HTML-kódot vagy Markdown másolhat:

![Jegyzetfüzet-jelvény indítása](https://notebooks.azure.com/launch.png)

Ha nem rendelkezik Azure Notebooks-projekttel, létrehozhat egy hivatkozást, amely a GitHubról közvetlenül a következő sablonok használatával klónozott, a megfelelő Felhasználónév és adattár nevét helyettesítve:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Új projekt létrehozása

Ha az **+ új projekt** parancsot használja, Azure Notebooks megjeleníti az **új projekt létrehozása** előugró ablakban. Ebben az előugró ablakban adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

| Mező | Leírás |
| --- | --- |
| Projektnév | A projekt felhasználóbarát neve, amelyet a Azure Notebooks a megjelenítési célokra használ. Például: "My notebook Project". |
| Projektazonosító | Egy egyéni azonosító, amely a projekt megosztásához használt URL-cím részévé válik (az űrlap `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](#reserved-project-ids). Ha nem tudja biztosan, hogy mit kell használni, a közös konvenció a projekt nevének olyan kisbetűs verzióját használja, amelyben a szóközök kötőjelbe vannak bekapcsolva, például "My-notebook-Project" (ha szükséges a hosszhoz). |
| Nyilvános | Ha be van állítva, lehetővé teszi, hogy bárki hozzáférjen a projekthez. Privát projekt létrehozásakor törölje ezt a beállítást. |
| A projekt inicializálása README-val | Ha be van állítva, a létrehoz egy alapértelmezett *readme.MD* -fájlt a projektben. A *readme.MD* -fájl a projekt dokumentációját adja meg, ha szükséges. |

### <a name="reserved-project-ids"></a>Fenntartott projektek azonosítói

A következő fenntartott szavak önmagukban nem használhatók projekt-azonosítóként. Ezek a fenntartott szavak azonban a hosszú projekt-azonosítók részeként is használhatók.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| körülbelül | account | felügyeleti | api-t | blog | osztályteremben |
| content | irányítópult | Ismerkedés | – gyakori kérdések | segítség | html |
| Kezdőlap | importálása | Erőforrástár | felügyelet | új | notebook |
| notebookok | PDF | előzetes verzió | Árképzési | Profil | Keresés |
| status | Támogatja | test | | | |

Ha a következő szavak egyikét próbálja meg projekt-AZONOSÍTÓként használni, az **új projekt létrehozása** és a **projekt beállításainak** előugró ablakai jelzik, hogy a "könyvtár azonosítója fenntartott azonosító".

Mivel a projekt-azonosító egy projekt URL-címéhez is tartozik, az ad blocker szoftver blokkolhatja bizonyos kulcsszavak használatát, például a "hirdetést". Ilyen esetekben használjon egy másik szót a projekt-AZONOSÍTÓban.

## <a name="import-a-project-from-github"></a>Projekt importálása a GitHubról

A teljes nyilvános GitHub-tárházat könnyedén importálhatja projektként, beleértve az összes adatés *readme.MD* fájlt is. Használja a **GitHub** -tárház feltöltése parancsot, adja meg a következő adatokat az előugró ablakban, majd válassza az **Importálás**lehetőséget:

| Mező | Leírás |
| --- | --- |
| GitHub-adattár | A github.com lévő forrás adattár neve. Ha például az Azure-beli Jupyter-jegyzetfüzeteket szeretné klónozott Cognitive Services a [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), írja be a "Microsoft/kognitív-Services-notebookok" kifejezést.  |
| Rekurzív klónozás | A GitHub-adattárak több gyermek tárházat is tartalmazhatnak. Akkor adja meg ezt a beállítást, ha a szülő tárházat és annak összes gyermekét szeretné klónozott módon megtekinteni. Mivel lehetséges, hogy egy tárház sok gyermeket tartalmaz, hagyja üresen ezt a lehetőséget, ha nem tudja, hogy szüksége van rá. |
| Projektnév | A projekt felhasználóbarát neve, amelyet a Azure Notebooks a megjelenítési célokra használ. |
| Projektazonosító | Egy egyéni azonosító, amely a projekt megosztásához használt URL-cím részévé válik (az űrlap `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](#reserved-project-ids). Ha nem tudja biztosan, hogy mit kell használni, a közös konvenció a projekt nevének olyan kisbetűs verzióját használja, amelyben a szóközök kötőjelbe vannak bekapcsolva, például "My-notebook-Project" (ha szükséges a hosszhoz). |
| Nyilvános | Ha be van állítva, lehetővé teszi, hogy bárki hozzáférjen a projekthez. Privát projekt létrehozásakor törölje ezt a beállítást. |

A tárháznak a GitHubról való importálása is importálja az előzményeket. A terminál szabványos git-parancsaival új módosításokat alkalmazhat, lekérheti a módosításokat a GitHubról, és így tovább.

## <a name="clone-a-project"></a>Projekt klónozása

A klónozás egy meglévő projekt másolatát hozza létre a saját fiókjában, ahol ezután futtathatja és módosíthatja a projektben lévő jegyzetfüzeteket vagy más fájlokat. A klónozást is használhatja arra, hogy másolatot készítsen saját projektjeiről, amelyekben kísérleteket vagy más munkát végez az eredeti projekt megzavarása nélkül.

Projekt klónozása:

1. A **saját projektek** irányítópulton kattintson a jobb gombbal a kívánt projektre, és válassza a **klónozás** lehetőséget (billentyűparancs: c).

    ![Klónozási parancs a projekt helyi menüjében](media/clone-command.png)

1. A **klónozott projekt** előugró ablakban adja meg a klón nevét és azonosítóját, és adja meg, hogy a klón nyilvános-e. Ezek a beállítások ugyanazok, mint egy [új projekt](#create-a-new-project)esetében.

    ![Projekt előugró ablakának klónozása](media/clone-project.png)

1. A **klónozás** gomb kiválasztását követően Azure Notebooks közvetlenül a másolatra navigál.

## <a name="next-steps"></a>További lépések

- [Minta-jegyzetfüzetek megismerése](azure-notebooks-samples.md)
- [Útmutató: projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Útmutató: bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
- [Útmutató: az adatfájlok használata](work-with-project-data-files.md)
- [Útmutató: az adaterőforrások elérése](access-data-resources-jupyter-notebooks.md)
- [Útmutató: a Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
