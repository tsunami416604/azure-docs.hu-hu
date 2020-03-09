---
title: Jupyter-jegyzetfüzetek létrehozása és klónozása – Azure Notebooks előzetes verzió
description: Azure Notebooks az előzetes verziójú projektek jegyzetfüzetek és kapcsolódó fájlok gyűjteményét kezelik, amelyekkel új vagy klónozást hozhat létre más forrásokból.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360622"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Projektek létrehozása és klónozása Azure Notebooks előzetes verzióban

Azure Notebooks a Jupyter-jegyzetfüzeteket és a kapcsolódó fájlokat a *projektek*nevű logikai csoportba rendezi. Tárolójaként, először hozzon létre egy projektet, majd hozzon létre vagy klónozásához egy vagy több notebookok mellett egyéb projekt fájlokat egy mappában található. (Ezt a folyamatot az [oktatóanyag](tutorial-create-run-jupyter-notebook.md)mutatja be.)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Egy projektet is fenntartják, metaadatokat és egyéb konfigurációs beállítások, amelyek hatással lennének a kiszolgálóra notebookokban mely futtatja, beleértve az egyéni beállítási lépéseket és a csomag telepítése. További információ: [projektek kezelése és konfigurálása](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>A saját projektek irányítópult

A **saját projektek** irányítópultja `https://notebooks.azure.com/<userID>/projects`on, ahol megtekintheti, kezelheti és létrehozhatja a projekteket:

[![My projects irányítópultot a Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Mi mindent az irányítópulton attól függ, hogy jelentkezett be a fiók, amely a felhasználói azonosító tulajdonosa:

| Parancs | Elérhető: | Leírás |
| --- | --- | --- |
| **Futtassa** | Tulajdonos | A project server elindul, és a projektmappában nyílik Jupyter. (Leggyakrabban, akkor először lépjen a projektmappára, majd indítsa el egy jegyzetfüzetet innen.) |
| **Letöltés** | Bárki | ZIP-fájlként tölti le a kiválasztott projekt egy példányát. |
| **Megosztás** | Bárki | Megjeleníti a megosztás felugró ablakát, amelyen keresztül beszerezhet egy URL-címet egy kiválasztott projekthez, megoszthatja a közösségi médiát, e-mailt küldhet az URL-címmel, valamint HTML-vagy Markdown-kódot is beszerezhet a "notebook elindítása" jelvényre (lásd: [indítási jelvény beszerzése](#obtain-a-launch-badge)) az URL-címmel. |
| **Törlés** | Tulajdonos | Törli a kiválasztott projekthez. Ez a művelet nem vonható vissza. |
| **Terminál** | Tulajdonos | A project server kezdődik, majd megnyílik egy új böngészőablakot a bash, terminál, hogy a kiszolgáló. |
| **+ Új projekt** | Tulajdonos | Létrehoz egy új projektet. Lásd: [új projekt létrehozása](#create-a-new-project). |
| **GitHub-adattár feltöltése** | Tulajdonos | Importál egy projektet a Githubról. [Projekt importálása a githubról](#import-a-project-from-github). |
| **Klónozott** | Bárki | Másolja át a kiválasztott projekt a saját fiókba. Kéri, ha még nem már bejelentkezhet. Lásd: [projekt klónozása](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Szerezzen be egy indítási jelvény

Ha a **megosztás** parancsot használja, és kiválasztja a **beágyazás** lapot, akkor a "jegyzetfüzet indítása" jelvényt létrehozó HTML-kódot vagy Markdown másolhat:

![Indítsa el a jegyzetfüzet-jelvény](https://notebooks.azure.com/launch.png)

Ha nem rendelkezik egy Azure-jegyzetfüzetek projekt, egy hivatkozás, amely klónokat hozhat létre a Githubról közvetlenül használatával a következő sablonokat, és cserélje le a megfelelő felhasználónevet és a tárház nevét:

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
| Projektnév | Egy rövid nevet a projekthez, amely az Azure-jegyzetfüzetek megjelenítési célokra használja. Például: "My notebook Project". |
| Projektazonosító | Egy egyéni azonosító, amely a projekt megosztásához használt URL-cím részévé válik (az űrlap `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](#reserved-project-ids). Ha Ön nem tudja, hogy melyiket érdemes használni, a common konvenciónak, hogy a projekt neve kisbetűs verzióját, szóközöket, kötőjeleket, például a "my-jegyzetfüzet-projekt" (ha szükséges, hogy illeszkedjen a hosszra vonatkozó korlátot csonkolt) vannak kapcsolva. |
| Nyilvános | Ha a beállítása, lehetővé teszi, hogy bárki a hivatkozást a projekt eléréséhez. Privát projekt létrehozásakor törölje ezt a beállítást. |
| Ez a projekt és a egy információs fájl inicializálása | Ha be van állítva, a létrehoz egy alapértelmezett *readme.MD* -fájlt a projektben. A *readme.MD* -fájl a projekt dokumentációját adja meg, ha szükséges. |

### <a name="reserved-project-ids"></a>Fenntartott projektek azonosítói

A következő fenntartott szavak önmagukban nem használhatók projekt-azonosítóként. Ezek a fenntartott szavak azonban a hosszú projekt-azonosítók részeként is használhatók.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| körülbelül | account | adminisztráció | api | blog | osztályteremben |
| content | irányítópult | Ismerkedés | – gyakori kérdések | Súgó | HTML |
| Kezdőlap | importálása | erőforrástár | felügyelet | új | notebook |
| notebookok | PDF | előzetes verzió | árképzési | profile | Keresés |
| status | támogatja | test | | | |

Ha a következő szavak egyikét próbálja meg projekt-AZONOSÍTÓként használni, az **új projekt létrehozása** és a **projekt beállításainak** előugró ablakai jelzik, hogy a "könyvtár azonosítója fenntartott azonosító".

Mivel a projekt-azonosító egy projekt URL-címéhez is tartozik, az ad blocker szoftver blokkolhatja bizonyos kulcsszavak használatát, például a "hirdetést". Ilyen esetekben használjon egy másik szót a projekt-AZONOSÍTÓban.

## <a name="import-a-project-from-github"></a>A projekt importálása a Githubról

A teljes nyilvános GitHub-tárházat könnyedén importálhatja projektként, beleértve az összes adatés *readme.MD* fájlt is. Használja a **GitHub** -tárház feltöltése parancsot, adja meg a következő adatokat az előugró ablakban, majd válassza az **Importálás**lehetőséget:

| Mező | Leírás |
| --- | --- |
| GitHub-adattár | A github.com tárházban neve. Ha például az Azure-beli Jupyter-jegyzetfüzeteket szeretné klónozott Cognitive Services a [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), írja be a "Microsoft/kognitív-Services-notebookok" kifejezést.  |
| Klónozza a rekurzív módon | GitHub-adattárak több gyermek-tárházakat is tartalmazhat. Állítsa be ezt a beállítást, ha azt szeretné, a szülő-tárházat és az összes gyermekre a klónozásához. Szeretné, hogy számos gyermek-tárházhoz lehetőség, mert hagyja törölje ezt a beállítást, ha tudja, szüksége lesz rá. |
| Projektnév | Egy rövid nevet a projekthez, amely az Azure-jegyzetfüzetek megjelenítési célokra használja. |
| Projektazonosító | Egy egyéni azonosító, amely a projekt megosztásához használt URL-cím részévé válik (az űrlap `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](#reserved-project-ids). Ha Ön nem tudja, hogy melyiket érdemes használni, a common konvenciónak, hogy a projekt neve kisbetűs verzióját, szóközöket, kötőjeleket, például a "my-jegyzetfüzet-projekt" (ha szükséges, hogy illeszkedjen a hosszra vonatkozó korlátot csonkolt) vannak kapcsolva. |
| Nyilvános | Ha a beállítása, lehetővé teszi, hogy bárki a hivatkozást a projekt eléréséhez. Privát projekt létrehozásakor törölje ezt a beállítást. |

Az előzményeket is importálja egy tárház GitHub importálja. A terminálból a normál Git-parancsok használatával véglegesítse a módosításokat, kérje le a módosításokat a Githubról, és így tovább.

## <a name="clone-a-project"></a>Egy projekt klónozása

Egy meglévő projekt egy példányát a saját fiókját, ahol ezután futtassa, és minden olyan jegyzetfüzet vagy más fájlt a projektben módosítsa a Klónozás hoz létre. Használhatja a Klónozás példányát a saját projektek szükség lehet kísérletfuttatásért és más munkahelyi anélkül, hogy megzavarná az eredeti projektet.

A projekt klónozása:

1. A **saját projektek** irányítópulton kattintson a jobb gombbal a kívánt projektre, és válassza a **klónozás** lehetőséget (billentyűparancs: c).

    ![Klónozási parancs a projekt helyi menüjében](media/clone-command.png)

1. A **klónozott projekt** előugró ablakban adja meg a klón nevét és azonosítóját, és adja meg, hogy a klón nyilvános-e. Ezek a beállítások ugyanazok, mint egy [új projekt](#create-a-new-project)esetében.

    ![Klónozás projekt helyi menü](media/clone-project.png)

1. A **klónozás** gomb kiválasztását követően Azure Notebooks közvetlenül a másolatra navigál.

## <a name="next-steps"></a>Következő lépések

- [Minta-jegyzetfüzetek megismerése](azure-notebooks-samples.md)
- [Útmutató: projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Útmutató: bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
- [Útmutató: az adatfájlok használata](work-with-project-data-files.md)
- [Útmutató: az adaterőforrások elérése](access-data-resources-jupyter-notebooks.md)
- [Útmutató: a Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
