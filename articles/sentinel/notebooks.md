---
title: Az Azure Sentinel jegyzetfüzetek használatával történő vadászati képességei | Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatók a jegyzetfüzetek az Azure Sentinel vadászati képességeivel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2019
ms.author: rkarlin
ms.openlocfilehash: 1b78f6f0773e114a4dda536213e2684d8fbd706d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483273"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter-jegyzetfüzetek használata biztonsági fenyegetések vadászatához

Az Azure Sentinel alapja az adattár; egyesíti a nagy teljesítményű lekérdezéseket, a dinamikus sémákat és a nagy adatmennyiségeket. A Azure Portal és az összes Azure Sentinel-eszköz közös API-t használ az adattár eléréséhez. Ugyanez az API a külső eszközök, például a [Jupyter](https://jupyter.org/) notebookok és a Python számára is elérhető. Habár számos gyakori feladat elvégezhető a portálon, a Jupyter kiterjeszti a hatókörét, hogy mit tehet az adatokkal. A teljes programozást a tárak hatalmas gyűjteményével ötvözi a gépi tanuláshoz, a vizualizációhoz és az adatelemzéshez. Ezek az attribútumok meggyőző eszközt biztosítanak a biztonsági vizsgálathoz és a vadászathoz Jupyter.

![Példa jegyzetfüzetre](./media/notebooks/sentinel-notebooks-map.png)

Integráltuk a Jupyter-élményt a Azure Portalba, így könnyedén hozhat létre és futtathat jegyzetfüzeteket az adatai elemzéséhez. A *Kqlmagic* -kódtár lehetővé teszi az Azure Sentinel-lekérdezések lekérdezését, és közvetlenül egy jegyzetfüzetben történő futtatását. A lekérdezések a [Kusto lekérdezési nyelvét](https://kusto.azurewebsites.net/docs/query/index.html)használják. Több, a Microsoft által készített biztonsági elemző által fejlesztett jegyzetfüzet is be van csomagolva az Azure Sentinel szolgáltatással. Ezek a jegyzetfüzetek egy adott forgatókönyvhöz készültek, és használhatók a-ként is. Mások mintaként szolgálnak a saját jegyzetfüzetekben való használatra másolható vagy alkalmazkodó technikák és funkciók szemléltetésére. Más jegyzetfüzetek is importálhatók az Azure Sentinel Community GitHubról.

Az integrált Jupyter-élmény a notebookok tárolásához, megosztásához és végrehajtásához [Azure Notebooks](https://notebooks.azure.com/) használ. Ezeket a jegyzetfüzeteket helyileg is futtathatja, ha Python-környezettel és Jupyter rendelkezik a számítógépen, illetve más JupterHub-környezetekben, például Azure Databricks.

A jegyzetfüzetek két összetevővel rendelkeznek:

- A böngészőalapú felület, ahol lekérdezéseket és kódokat írhat be és futtathat, valamint a végrehajtás eredményét.
- A kód elemzéséhez és végrehajtásához felelős *kernel* . 

A Azure Notebooks alapértelmezés szerint ez a kernel az Azure *ingyenes Felhőbeli számítási és tárolási kapacitását*futtatja. Ha a notebookok összetett gépi tanulási modelleket vagy vizualizációkat tartalmaznak, érdemes lehet nagyobb teljesítményű, dedikált számítási erőforrásokat használni, mint például az [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). A fiókban lévő jegyzetfüzetek csak akkor maradnak magánjellegűek, ha megosztja őket.

Az Azure Sentinel notebookok számos népszerű Python-kódtárat használnak, mint például a pandák, a matplotlib, a bokeh és mások. Számos más Python-csomag közül választhat, amelyek többek között a következő területekre terjednek ki:

- Vizualizációk és grafikák
- Adatfeldolgozás és-elemzés
- Statisztikák és numerikus számítástechnika
- Gépi tanulás és mély tanulás

A [msticpy](https://github.com/Microsoft/msticpy/)nevű csomagban is megjelent néhány nyílt forráskódú Jupyter biztonsági eszköz. Ezt a csomagot számos mellékelt jegyzetfüzetben használják. A Msticpy Tools kifejezetten segítséget nyújt a jegyzetfüzetek létrehozásához a vadászathoz és a nyomozáshoz, és aktívan dolgozunk az új szolgáltatásokon és újdonságokon.

A kezdeti jegyzetfüzetek a következők:

- **Irányított vizsgálat – riasztások feldolgozása**: lehetővé teszi a riasztások gyors osztályozását az érintett gazdagépen vagy gazdagépeken található tevékenységek elemzésével.
- **Irányított vadászat – Windows Host Explorer**: lehetővé teszi a fiókok tevékenységének, a folyamatok végrehajtásának, a hálózati tevékenységeknek és a gazdagépen található egyéb eseményeknek a megismerését.
- **Interaktív vadászat – Office 365**: a gyanús Office 365-tevékenységek felkutatása több Office 365-adatkészletben.

Az [Azure Sentinel Community GitHub-tárház](https://github.com/Azure/Azure-Sentinel) a Microsoft által készített vagy a Közösségtől származó jövőbeli Azure Sentinel-jegyzetfüzetek helye.

A jegyzetfüzetek használatához Azure Notebooks fiókkal kell rendelkeznie. További információ: gyors útmutató [: bejelentkezés és felhasználói azonosító beállítása](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) a Azure Notebooks dokumentációjában. Ennek a fióknak a létrehozásához használhatja az **Azure Sentinel-jegyzetfüzetek**parancssorában a **Azure Notebooks regisztráció a következőre** lehetőséget:

> [!div class="mx-imgBorder"]
>![regisztráció a Azure Notebooks lehetőségre](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Az Azure Sentinelből közvetlenül is futtathat egy jegyzetfüzetet, vagy az összes Azure Sentinel-jegyzetfüzetet egy új Azure Notebooks projektbe klónozott.

## <a name="run-a-notebook-from-azure-sentinel"></a>Jegyzetfüzet futtatása az Azure Sentinelből
 
1. A Azure Portal navigáljon az **Azure sentinel** > **veszélyforrások kezelése** > **jegyzetfüzetekhez**, ahol láthatja az Azure Sentinel által biztosított jegyzetfüzeteket. 

2. Válassza az egyéni jegyzetfüzetek lehetőséget a leírások, a szükséges adattípusok és az adatforrások olvasásához. Például:
    
    > [!div class="mx-imgBorder"]
    > ![notebook elindítása](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Válassza ki a használni kívánt jegyzetfüzetet, majd válassza a **Jegyzetfüzet indítása (előzetes verzió)** lehetőséget a jegyzetfüzet klónozásához és konfigurálásához egy olyan új Azure Notebooks projekthez, amely csatlakozik az Azure Sentinel-munkaterülethez. Ha a folyamat befejeződött, a jegyzetfüzet a futtatásához Azure Notebooks belül nyílik meg.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel-jegyzetfüzetek klónozása új Azure Notebooks-projektbe

Ez az eljárás létrehoz egy Azure Notebooks projektet az Ön számára, amely tartalmazza az Azure Sentinel-jegyzetfüzeteket. Ezután futtathatja a jegyzetfüzeteket, vagy módosíthatja azokat, majd futtathatja őket.

1. A Azure Portal navigáljon az **Azure Sentinel** > **veszélyforrások kezelése** > **jegyzetfüzetek** elemre, majd válassza a parancssorból a **jegyzetfüzetek klónozása** elemet:
  
    > [!div class="mx-imgBorder"]
    >![klónozott jegyzetfüzetek lehetőség](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Amikor megjelenik a következő párbeszédpanel, válassza az **Importálás** lehetőséget a GitHub-tárháznak a Azure Notebooks projektbe való klónozásához. Ha nem rendelkezik meglévő Azure Notebooks fiókkal, a rendszer felszólítja, hogy hozzon létre egyet, és jelentkezzen be.

   ![Jegyzetfüzet importálása](./media/notebooks/sentinel-notebooks-clone.png)

3. A **GitHub-adattár feltöltése** párbeszédpanelen ne válassza a **klónozás rekurzív** módon beállítást, mert ez a beállítás a csatolt GitHub-adattárakra hivatkozik. A projekt neve mezőben használja az alapértelmezett nevet vagy a típust egy újat. Ezután kattintson az **Importálás** gombra a GitHub-tartalom klónozásának megkezdéséhez, ami eltarthat néhány percig.

   ![Jegyzetfüzet importálása](./media/notebooks/sentinel-create-project.png)

4. Nyissa meg az imént létrehozott projektet, majd nyissa meg a **jegyzetfüzetek** mappát a jegyzetfüzetek megtekintéséhez. Például:

   ![Adattár importálása](./media/notebooks/sentinel-open-notebook1.png)

Ezután a jegyzetfüzeteket Azure Notebooksról futtathatja. Ha vissza szeretne térni ehhez a jegyzetfüzetekhez az Azure Sentinelből, válassza az **Azure Sentinel-jegyzetfüzetek**menüsávjának **Ugrás a jegyzetfüzetekhez** parancsát:

> [!div class="mx-imgBorder"]
>![nyissa meg a jegyzetfüzetek lehetőséget](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Jegyzetfüzetek használata a vadászathoz

Mindegyik jegyzetfüzet végigvezeti a vadászat vagy a vizsgálat elvégzésének lépésein. A notebookhoz szükséges könyvtárak és egyéb függőségek a jegyzetfüzetből vagy egy egyszerű konfigurációs eljárással is telepíthetők. A notebook-projektet az Azure Sentinel-előfizetéshez kapcsolódó konfiguráció automatikusan az előző lépésekben lesz kiépítve.

1. Ha még nem Azure Notebooks, használhatja az **Azure Sentinel-jegyzetfüzetek**menüsávjának a **jegyzetfüzetek** használata lehetőségét:
    
    > [!div class="mx-imgBorder"]
    >![nyissa meg a jegyzetfüzetek lehetőséget](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Azure Notebooks válassza a **saját projektek**elemet, majd az Azure Sentinel-jegyzetfüzeteket tartalmazó projektet, végül pedig a **jegyzetfüzetek** mappát.
    
2. Mielőtt megnyit egy jegyzetfüzetet, vegye figyelembe, hogy alapértelmezés szerint a rendszer a jegyzetfüzetek futtatásához az ingyenes számítás lehetőséget választotta:
    
   ![Jegyzetfüzet kiválasztása](./media/notebooks/sentinel-open-notebook2.png)
    
    Ha úgy konfigurált egy adatelemzési Virtual Machines (DSVM), amelyet a bevezetésben ismertetett módon használ, válassza ki a DSVM és a hitelesítést az első jegyzetfüzet megnyitása előtt. 

3. Válasszon ki egy jegyzetfüzetet a megnyitásához.
    
    Amikor először nyit meg egy jegyzetfüzetet, a rendszer kérni fogja, hogy válasszon ki egy kernel-verziót. Ha a rendszer nem kéri, válassza ki a kernel **verzióját a kernel >  ** **change kernel**elemre, majd válasszon egy legalább 3,6-es verziót. A kiválasztott kernel-verzió a jegyzetfüzet ablakának jobb felső sarkában jelenik meg:
    
   ![Jegyzetfüzet kiválasztása](./media/notebooks/sentinel-select-kernel.png)

4. Mielőtt bármilyen módosítást hajt végre a letöltött jegyzetfüzetben, érdemes lehet másolatot készíteni az eredeti jegyzetfüzetről, és dolgozni a másolaton. Ehhez válassza a **fájl** > **másolat készítése**lehetőséget. A másolatok használata lehetővé teszi, hogy biztonságosan frissítse a jegyzetfüzetek jövőbeli verzióit anélkül, hogy felülírja az összes adatát.
    
    Most már készen áll a kijelölt jegyzetfüzet futtatására vagy szerkesztésére.

Javaslatok

- Az Azure Sentinelben lévő adatlekérdezés gyors bevezetéséhez tekintse meg a [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) notebookot a fő **jegyzetfüzetek** mappában. 

- A **minta-jegyzetfüzetek** almappában további minta-jegyzetfüzeteket talál. Ezek a minta-jegyzetfüzetek adatokat mentettek, így könnyebben megtekinthető a kívánt kimenet. Javasoljuk, hogy tekintse meg ezeket a jegyzetfüzeteket a [nbviewer](https://nbviewer.jupyter.org/)-ben. 

- A **howtos** mappa tartalmaz egy jegyzetfüzetet, amely leírja például a Python alapértelmezett verziójának beállítását, a DSVM konfigurálását, az Azure Sentinel könyvjelzők létrehozását egy jegyzetfüzetből és más témákból.

A megadott jegyzetfüzetek a saját jegyzetfüzetek fejlesztéséhez használható hasznos eszközökként, valamint illusztrációként és mintakódként is szolgálnak.

Szívesen fogadjuk a visszajelzéseket, legyen szó a javaslatokról, a funkciókról, a közreműködő jegyzetfüzetekről, a hibajelentésekről, valamint a meglévő jegyzetfüzetek fejlesztéséről és kiegészítéseiről. Lépjen az [Azure Sentinel Community githubra](https://github.com/Azure/Azure-Sentinel) , és hozzon létre egy problémát vagy elágazást, és töltsön fel egy hozzájárulást.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan kezdheti el a Jupyter notebookok használatát az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [A könyvjelzők használatával érdekes információkat menthet a vadászat során](bookmarks.md)
