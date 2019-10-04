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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5b90ecc1db686b698668b07bd839304b425445ca
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240529"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter-jegyzetfüzetek használata biztonsági fenyegetések vadászatához

Az Azure Sentinel alapja az adattár; egyesíti a nagy teljesítményű lekérdezéseket, a dinamikus sémákat és a nagy adatmennyiségeket. Az Azure Sentinel portál és az összes Azure Sentinel-eszköz közös API-val fér hozzá ehhez az adattárhoz. Ugyanez az API a külső eszközök, például a [Jupyter](https://jupyter.org/) notebookok és a Python számára is elérhető. Habár számos gyakori feladat elvégezhető a portálon, a Jupyter kiterjeszti a hatókörét, hogy mit tehet az adatokkal. A teljes programozást a tárak hatalmas gyűjteményével ötvözi a gépi tanuláshoz, a vizualizációhoz és az adatelemzéshez. Ezek az attribútumok meggyőző eszközt biztosítanak a biztonsági vizsgálathoz és a vadászathoz Jupyter.

![Példa jegyzetfüzetre](./media/notebooks/sentinel-notebooks-map.png)

A Jupyter-élményt az Azure Sentinel-portálon integráljuk, így könnyedén hozhat létre és futtathat jegyzetfüzeteket az adatai elemzéséhez. A *Kqlmagic* -kódtár lehetővé teszi az Azure Sentinel-lekérdezések lekérdezését, és közvetlenül egy jegyzetfüzetben történő futtatását. A lekérdezések a [Kusto lekérdezési nyelvét](https://kusto.azurewebsites.net/docs/query/index.html)használják. Több, a Microsoft által készített biztonsági elemző által fejlesztett jegyzetfüzet is be van csomagolva az Azure Sentinel szolgáltatással. Ezek a jegyzetfüzetek egy adott forgatókönyvhöz készültek, és használhatók a-ként is. Mások mintaként szolgálnak a saját jegyzetfüzetekben való használatra másolható vagy alkalmazkodó technikák és funkciók szemléltetésére. Más jegyzetfüzetek is importálhatók az Azure Sentinel Community GitHubról.

Az integrált Jupyter-élmény a notebookok tárolásához, megosztásához és végrehajtásához [Azure Notebooks](https://notebooks.azure.com/) használ. Ezeket a jegyzetfüzeteket helyileg is futtathatja (ha rendelkezik Python-környezettel és Jupyter a számítógépen) vagy más JupterHub-környezetekben, például Azure Databricks.

A jegyzetfüzetek két összetevővel rendelkeznek:

- a böngészőalapú felület, ahol lekérdezéseket és kódokat írhat be és futtathat, valamint a végrehajtás eredményét.
- a kód elemzéséhez és végrehajtásához felelős *kernel* . 

Azure Notebooks ez a kernel alapértelmezés szerint az Azure *ingyenes felhőalapú számítási és tárolási kapacitását* futtatja. Ha a notebookok összetett gépi tanulási modelleket vagy vizualizációkat tartalmaznak, érdemes nagyobb teljesítményű, dedikált számítási erőforrásokat használni, például [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). A fiókban lévő jegyzetfüzetek csak akkor maradnak magánjellegűek, ha megosztja őket.

Az Azure Sentinel notebookok számos népszerű Python-kódtárat használnak, mint például a pandák, a matplotlib, a bokeh és mások. Számos más Python-csomag közül választhat, amelyek a következőkre terjednek ki:

- vizualizációk és grafikák
- adatfeldolgozás és-elemzés
- statisztikák és numerikus számítástechnika
- gépi tanulás és mély tanulás

A [msticpy](https://github.com/Microsoft/msticpy/)nevű csomagban is megjelent néhány nyílt forráskódú Jupyter biztonsági eszköz. Ezt a csomagot számos mellékelt jegyzetfüzetben használják. A Msticpy Tools kifejezetten segítséget nyújt a jegyzetfüzetek létrehozásához a vadászathoz és a nyomozáshoz, és aktívan dolgozunk az új szolgáltatásokon és újdonságokon.

A kezdeti jegyzetfüzetek a következők:

- **Irányított vizsgálat – riasztások feldolgozása**: Lehetővé teszi a riasztások gyors osztályozását az érintett állomás (ok) tevékenységének elemzésével.
- **Interaktív vadászat – Windows Host Explorer**: Lehetővé teszi a fiókok tevékenységének, a folyamatok végrehajtásának, a hálózati tevékenységeknek és a gazdagépen található egyéb eseményeknek a megismerését.  
- **Interaktív vadászat – Office 365 – felfedezés**: A gyanús Office 365-tevékenységek vadászata több O365-adatkészletben.

Az [Azure Sentinel Community GitHub-tárház](https://github.com/Azure/Azure-Sentinel) a Microsoft által készített vagy a Közösségtől származó jövőbeli Azure Sentinel-jegyzetfüzetek helye.

## <a name="run-a-notebook"></a>Jegyzetfüzet futtatása

A következő példában létrehozunk egy Azure Notebooks projektet az Azure Sentinel portálról, amely a projektet jegyzetfüzetekkel tölti fel. A jegyzetfüzetek használata előtt érdemes lehet másolatot készíteni a jegyzetfüzetről, és dolgozni a másolaton. A másolatok használata lehetővé teszi, hogy biztonságosan frissítse a jegyzetfüzetek jövőbeli verzióit anélkül, hogy felülírja az összes adatát.

1. Az Azure Sentinel Portalon kattintson a navigációs menü **jegyzetfüzetek** elemére. Új Azure Notebooks projekt létrehozásához kattintson az **Azure Sentinel-jegyzetfüzetek klónozása** lehetőségre, vagy a meglévő jegyzetfüzet-projektek megnyitásához kattintson az **Ugrás a jegyzetfüzetekhez**elemre.
  
   ![jegyzetfüzetek kiválasztása](./media/notebooks/sentinel-azure-notebooks-home.png)

2. Ha az előző lépésben az **Azure Sentinel notebookok klónozását** választotta, a következő párbeszédablak jelenik meg. Kattintson az **Importálás** gombra a GitHub-tárháznak a Azure Notebooks projektbe való klónozásához. Ha nem rendelkezik meglévő Azure Notebooks fiókkal, a rendszer felszólítja, hogy hozzon létre egyet, és jelentkezzen be.

   ![Jegyzetfüzet importálása](./media/notebooks/sentinel-notebooks-clone.png)

3. Új projekt létrehozásakor a projektet kell megadnia – használja az alapértelmezett nevet vagy a típust egy újat. Ne vizsgálja a **klónozott rekurzív** beállítást – ez a beállítás a csatolt GitHub-adattárakra vonatkozik. Az **Importálás** gombra kattintva megkezdheti a GitHub-tartalom klónozását, ami eltarthat néhány percig.

   ![Jegyzetfüzet importálása](./media/notebooks/sentinel-create-project.png)

4. Nyissa meg a **jegyzetfüzetek** mappát a jegyzetfüzetek megtekintéséhez. Mindegyik jegyzetfüzet végigvezeti a vadászat vagy a vizsgálat elvégzésének lépésein. A notebookhoz szükséges könyvtárak és egyéb függőségek a jegyzetfüzetből vagy egy egyszerű konfigurációs eljárással is telepíthetők. A notebook-projektet az Azure Sentinel-előfizetéshez kapcsolódó konfiguráció automatikusan az előző lépésekben lesz kiépítve. A jegyzetfüzetek készen állnak az Azure Sentinel Log Analytics munkaterületen való futtatásra.

   ![Adattár importálása](./media/notebooks/sentinel-open-notebook1.png)

5. Nyisson meg egy jegyzetfüzetet. Alapértelmezés szerint az ingyenes számítás a jegyzetfüzetek futtatására van kiválasztva (kiemelve). Ha konfigurált egy DSVM (lásd fentebb), válassza ki a DSVM, és a hitelesítést az első jegyzetfüzet megnyitása előtt. Kattintson egy jegyzetfüzetre a megnyitásához.

   ![Jegyzetfüzet kiválasztása](./media/notebooks/sentinel-open-notebook2.png)

6. A Python verziójának kiválasztása. Amikor először nyit meg egy jegyzetfüzetet, kérheti, hogy válasszon ki egy kernel-verziót. Ha nem, válassza ki az alábbiak szerint használni kívánt kernelt. A Python 3,6-es vagy újabb verziójának a kiválasztott kernelnek kell lennie (a jegyzetfüzet ablakának jobb felső sarkában).

   ![Jegyzetfüzet kiválasztása](./media/notebooks/sentinel-select-kernel.png)

Az Azure Sentinelben lévő adatlekérdezés gyors bevezetéséhez tekintse meg a [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) notebookot a fő jegyzetfüzetek mappában. A **minta-jegyzetfüzetek** almappában további minta-jegyzetfüzeteket is talál. A mintául szolgáló jegyzetfüzetek adatokat mentenek, így könnyebben megtekinthető a kívánt kimenet (azt javasoljuk, hogy tekintse meg őket a [nbviewer](https://nbviewer.jupyter.org/)-ben). A **howtos** mappa tartalmaz egy jegyzetfüzetet, amely leírja például a Python alapértelmezett verziójának beállítását, a DSVM konfigurálását, az Azure Sentinel könyvjelzők létrehozását egy jegyzetfüzetből és más témákból.

Ezek a jegyzetfüzetek a saját jegyzetfüzetek fejlesztéséhez használható hasznos eszközökként, valamint illusztrációként és mintakódként szolgálnak.

Szívesen fogadjuk a visszajelzéseket, legyen szó a javaslatokról, a funkciókról, a közreműködő jegyzetfüzetekről, a hibajelentésekről, valamint a meglévő jegyzetfüzetek fejlesztéséről és kiegészítéseiről. Lépjen az [Azure Sentinel Community githubra](https://github.com/Azure/Azure-Sentinel) , és hozzon létre egy problémát vagy elágazást, és töltsön fel egy hozzájárulást.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kezdheti el a Jupyter notebookok használatát az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [A könyvjelzők használatával érdekes információkat menthet a vadászat során](bookmarks.md)
