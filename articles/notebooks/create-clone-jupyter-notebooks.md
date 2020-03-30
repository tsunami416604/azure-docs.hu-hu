---
title: Jupyter-jegyzetfüzetek létrehozása és klónozása – Azure Notebookelőzetes verzió
description: Az Azure Notebookelőzetes-projektek notebookok és kapcsolódó fájlok gyűjteményét kezelik, amelyeket új vagy klónozhat egy másik forrásból.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280572"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Projektek létrehozása és klónozása az Azure Notebookelőzetes verzióban

Az Azure Notebooks a Jupyter-jegyzetfüzeteket és a kapcsolódó fájlokat *projekteknek*nevezett logikai csoportokba rendezi. Először tárolóként hoz létre projektet, majd hozzon létre vagy klónozzon egy mappán belül egy vagy több jegyzetfüzetet más projektfájlok mellett. (Ez a folyamat mutatja be a [bemutató](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A projekt olyan metaadatokat és egyéb konfigurációs beállításokat is fenntart, amelyek hatással vannak arra a kiszolgálóra, amelyen a jegyzetfüzetek futnak, beleértve az egyéni telepítési lépéseket és a csomag telepítését. További információt a [Projektek kezelése és konfigurálása](configure-manage-azure-notebooks-projects.md)című témakörben talál.

## <a name="use-the-my-projects-dashboard"></a>A Saját projektek irányítópult használata

A **Saját** projektek `https://notebooks.azure.com/<userID>/projects` irányítópulton megtekintheti, kezelheti és hozhat létre projekteket:

[![Saját projektek irányítópult az Azure-jegyzetfüzetekben](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Az irányítópulton ellátható teendők attól függnek, hogy be van-e jelentkezve a felhasználói azonosítót birtokló fiókkal:

| Parancs | Elérhető a következőszámára: | Leírás |
| --- | --- | --- |
| **Futtassa a következőt:** | Tulajdonos | Elindítja a projektkiszolgálót, és megnyitja a projektmappát a Jupyter programban. (Gyakrabban először egy projektmappába navigál, majd onnan indít el egy jegyzetfüzetet.) |
| **Letöltés** | Bárki | Letölti a kijelölt projekt egy példányát ZIP-fájlként. |
| **Megosztás** | Bárki | Megjeleníti azt a megosztási előugró ablakot, amelyen keresztül megkaphatja egy kiválasztott projekt URL-címét, megoszthatja a közösségi médiában, e-mailt küldhet az URL-címmel, és HTML- vagy Markdown-kódot is kaphat az URL-lel ellátott "launch notebook" kitűzővel (lásd launch [badge beszerzése).](#obtain-a-launch-badge) |
| **Szabályzat** | Tulajdonos | A kijelölt projekt törlése. Ez a művelet nem vonható vissza. |
| **Terminál** | Tulajdonos | Elindítja a projektkiszolgálót, majd megnyit egy új böngészőablakot a kiszolgáló bash termináljával. |
| **+ Új projekt** | Tulajdonos | Új projektet hoz létre. Lásd: [Új projekt létrehozása](#create-a-new-project). |
| **GitHub-tártartalom feltöltése** | Tulajdonos | Projekt importálása a GitHubról. [Projekt importálása a GitHubról](#import-a-project-from-github). |
| **Klónozás** | Bárki | A kijelölt projektet a saját fiókjába másolja. A rendszer kéri, hogy jelentkezzen be, ha még nem. Lásd: [Projekt klónozása](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Indítójelvény beszerzése

Ha a **Megosztás** parancsot használja, és a **Beágyazás lapot választja,** a HTML-kódot vagy a "launch notebook" jelvényt létrehozó Markdown-kódot másolhatja:

![Jegyzetfüzet-jelvény indítása](https://notebooks.azure.com/launch.png)

Ha nem rendelkezik Azure Notebooks-projekttel, létrehozhat egy hivatkozást, amely közvetlenül a GitHubról klónozza a következő sablonokat, helyettesítve a megfelelő felhasználónevet és tárházneveket:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Új projekt létrehozása

A + **Új projekt** parancs használatakor az Azure Notebooks **egy Új projekt létrehozása** előugró ablakot jelenít meg. Ebben az előugró ablakban adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

| Mező | Leírás |
| --- | --- |
| Projektnév | A projekt rövid neve, amelyet az Azure Notebooks megjelenítési célokra használ. Például a "Saját jegyzetfüzetprojekt". |
| Projektazonosító | Egyéni azonosító, amely a projekt megosztásához használt URL részévé válik (az űrlap é. űrlap). `https://notebooks.azure.com/<user_id>/projects/<project_id>` Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakter ből állhat, és nem lehet [fenntartott projektazonosító.](#reserved-project-ids) Ha nem biztos benne, hogy mit szeretne használni, általános konvenció a projektnév kisméretű változatának használata, ahol a szóközök kötőjelekké alakulnak, például "jegyzetfüzet-projekt" (szükség esetén csonkolva, hogy illeszkedjen a hosszkorláthoz). |
| Nyilvános | Ha be van állítva, lehetővé teszi, hogy bárki, aki a linkkel rendelkezik, hozzáférjen a projekthez. Privát projekt létrehozásakor törölje a program ezt a beállítást. |
| A projekt inicializálása README-vel | Ha be van állítva, létrehoz egy alapértelmezett *README.md* fájlt a projektben. A *README.md* fájl, ahol a dokumentációt a projekt, ha szükséges. |

### <a name="reserved-project-ids"></a>Fenntartott projektazonosítók

A következő fenntartott szavakat önmagukban nem használhatják projektazonosítóként. Ezek a fenntartott szavak azonban hosszabb projektazonosítók részeként használhatók.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| névjegy | account | adminisztráció | api-t | blog | osztályterem |
| content | irányítópult | Fedezze fel | Gyik | segítség | html |
| Kezdőlap | Importálása | Könyvtár | felügyelet | Új | Notebook |
| notebookok | Pdf | Előnézet | Árképzés | profil | keresés |
| status | támogatás | test | | | |

Ha ezeket a szavakat projektazonosítóként próbálja használni, az **Új projekt-** és **projektbeállítások** létrehozása előugró ablakok azt jelzik, hogy "A könyvtárazonosító fenntartott azonosító".

Mivel a projektazonosító a projekt URL-címének is része, a hirdetésblokkoló szoftver blokkolhatja bizonyos kulcsszavak, például a "hirdetés" használatát. Ilyen esetekben használjon másik szót a projektazonosítóban.

## <a name="import-a-project-from-github"></a>Projekt importálása a GitHubról

Könnyedén importálhat egy teljes nyilvános GitHub-tárta projektként, beleértve az adatokat és *a README.md* fájlokat. Használja a **GitHub-tárhét** tartalmazó rendszerbe, adja meg a következő részleteket az előugró ablakban, majd válassza az **Importálás**lehetőséget:

| Mező | Leírás |
| --- | --- |
| GitHub-adattár | A forrástár neve a github.com. Például a Jupyter-jegyzetfüzetek az Azure [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)Cognitive Services a – adja meg a "Microsoft/cognitive-services-notebookok".  |
| Klónrekurzívan | A GitHub-adattárak több gyermektárházat is tartalmazhatnak. Állítsa be ezt a beállítást, ha klónozni szeretné a szülőtárházat és annak összes gyermekét. Mivel lehetséges, hogy egy tárháznak sok gyermeke legyen, hagyja ezt a lehetőséget, hacsak nem tudja, hogy szüksége van rá. |
| Projektnév | A projekt rövid neve, amelyet az Azure Notebooks megjelenítési célokra használ. |
| Projektazonosító | Egyéni azonosító, amely a projekt megosztásához használt URL részévé válik (az űrlap é. űrlap). `https://notebooks.azure.com/<user_id>/projects/<project_id>` Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakter ből állhat, és nem lehet [fenntartott projektazonosító.](#reserved-project-ids) Ha nem biztos benne, hogy mit szeretne használni, általános konvenció a projektnév kisméretű változatának használata, ahol a szóközök kötőjelekké alakulnak, például "jegyzetfüzet-projekt" (szükség esetén csonkolva, hogy illeszkedjen a hosszkorláthoz). |
| Nyilvános | Ha be van állítva, lehetővé teszi, hogy bárki, aki a linkkel rendelkezik, hozzáférjen a projekthez. Privát projekt létrehozásakor törölje a program ezt a beállítást. |

A GitHubról származó tárház importálása is importálja az előzményeket. A terminál szabványos Git-parancsaival új módosításokat véglegesíthet, módosításokat lehívhat a GitHubról, és így tovább.

## <a name="clone-a-project"></a>Projekt klónozása

A klónozás létrehoz egy másolatot egy meglévő projektről a saját fiókjában, ahol a projekt bármely jegyzetfüzetét vagy más fájlját futtathatja és módosíthatja. A klónozással saját projektekről is készíthet másolatot, amelyekben kísérleteket vagy más munkát végez anélkül, hogy megzavarná az eredeti projektet.

Projekt klónozása:

1. A **Saját projektek** irányítópulton kattintson a jobb gombbal a kívánt projektre, és válassza a **Klónozás** parancsot (billentyűparancs: c).

    ![Klónozás parancs a projekt helyi menüjében](media/clone-command.png)

1. A **Klónozási projekt** előugró ablakában adja meg a klón nevét és azonosítóját, és adja meg, hogy a klón nyilvános-e. Ezek a beállítások megegyeznek az [új projektbeállításaival.](#create-a-new-project)

    ![Klón projekt előugró ablak](media/clone-project.png)

1. Miután kiválasztotta a **Klónozás** gombot, az Azure Notebooks közvetlenül a másolathoz navigál.

## <a name="next-steps"></a>További lépések

- [Mintajegyzetfüzetek felfedezése](azure-notebooks-samples.md)
- [Útmutató: Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
- [Útmutató: Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
- [Útmutató: Adatfájlok kalvaló megoldása](work-with-project-data-files.md)
- [Útmutató: Adaterőforrások elérése](access-data-resources-jupyter-notebooks.md)
- [Útmutató: Az Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
