---
title: Jegyzetfüzetek használata az Azure Sentinel segítségével a biztonsági vadászathoz
description: Ez a cikk ismerteti, hogyan használhatja a jegyzetfüzetek az Azure Sentinel vadászati képességek használatával.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581837"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>A Jupyter notebookok használata a biztonsági fenyegetések keresésére

Az Azure Sentinel alapja az adattár; nagy teljesítményű lekérdezést, dinamikus sémát és nagy adatkötetekre skáláz. Az Azure Portal és az összes Azure Sentinel-eszköz egy közös API-t használ az adattár eléréséhez. Ugyanez az API is elérhető a külső eszközök, például [a Jupyter](https://jupyter.org/) notebookok és a Python. Bár a portálon számos gyakori feladat hajtható végre, a Jupyter kiterjeszti az adatokkal végezhető műveletek körét. A teljes programozhatóságot a gépi tanuláshoz, vizualizációhoz és adatelemzéshez szükséges könyvtárak hatalmas gyűjteményével kombinálja. Ezek az attribútumok teszik Jupyter ta-kori egy kényszerítő eszköz a biztonsági vizsgálat és a vadászat.

![példa jegyzetfüzet](./media/notebooks/sentinel-notebooks-map.png)

A Jupyter-élményt integráltuk az Azure Portalon, így egyszerűen hozhat létre és futtathat jegyzetfüzeteket az adatok elemzéséhez. A *Kqlmagic* könyvtár biztosítja a kapcsolást, amely lehetővé teszi az Azure Sentinel lekérdezéseit, és közvetlenül a jegyzetfüzeten belül futtathatja őket. A lekérdezések a [Kusto lekérdezési nyelvet](https://kusto.azurewebsites.net/docs/query/index.html)használják. A Microsoft néhány biztonsági elemzője által kifejlesztett jegyzetfüzetek közül több is az Azure Sentinel csomagban van elcsomagolva. Ezek a notebookok egy adott forgatókönyvhöz vannak tervezve, és a hogy használhatók. Mások mintaként szolgálnak, amelyek bemutatják azokat a technikákat és szolgáltatásokat, amelyeket saját jegyzetfüzeteiben másolhat vagy adaptálhat. Más jegyzetfüzetek is importálhatók az Azure Sentinel közösség GitHub.

Az integrált Jupyter-élmény [az Azure Notebookok](https://notebooks.azure.com/) használatával tárolja, osztja meg és hajtja végre a jegyzetfüzeteket. Ezeket a jegyzetfüzeteket helyileg is futtathatja, ha python-környezettel és Jupyter-rel rendelkezik a számítógépen vagy más JupterHub-környezetekben, például az Azure Databricks-ben.

A notebookok két összetevőből állnak:

- Az a böngészőalapú felület, amelyen lekérdezéseket és kódokat ír be és futtat, és ahol a végrehajtás eredményei megjelennek.
- Olyan *kernel,* amely a kód elemzésével és végrehajtásával felelős. 

Az Azure Notebookok ban ez a rendszermag alapértelmezés szerint az Azure *Free Cloud Compute and Storage webhelyen*fut. Ha a jegyzetfüzetek összetett gépi tanulási modelleket vagy vizualizációkat tartalmaznak, fontolja meg a hatékonyabb, dedikált számítási erőforrások, például [az adatelemzési virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM) használatát. A fiókjában lévő jegyzetfüzetek bizalmasak maradnak, kivéve, ha ön úgy dönt, hogy megosztja őket.

Az Azure Sentinel-jegyzetfüzetek számos népszerű Python-kódtárat használnak, például pandákat, matplotlibot, bokeh-t és másokat. Van egy nagyon sok más Python csomagok közül választhat, amely területeken, mint például:

- Képi megjelenítések és grafikák
- Adatfeldolgozás és -elemzés
- Statisztika és számszerű számítástechnika
- Gépi tanulás és mély tanulás

Mi is kiadott néhány nyílt forráskódú Jupyter biztonsági eszközök egy csomag nevű [msticpy](https://github.com/Microsoft/msticpy/). Ez a csomag számos mellékelt jegyzetfüzetben használatos. Az Msticpy eszközöket kifejezetten arra tervezték, hogy segítsenek a vadászathoz és a nyomozáshoz szükséges jegyzetfüzetek létrehozásában, és aktívan dolgozunk az új funkciókon és fejlesztéseken.

A kezdeti notebookok a következők:

- **Irányított vizsgálat – Folyamatriasztások**: Lehetővé teszi a riasztások gyors osztályozását az érintett gazdagépen vagy gazdagépeken végzett tevékenységek elemzésével.
- **Irányított vadászat - Windows host explorer**: Lehetővé teszi, hogy vizsgálja fiók tevékenység, folyamat végrehajtások, hálózati tevékenység, és egyéb események a fogadó.
- **Irányított vadászat – Office365 – Felfedezés:** Gyanús Office 365-tevékenységek rekedhet több Office 365-adathalmazban.

Az [Azure Sentinel Community GitHub-tárház](https://github.com/Azure/Azure-Sentinel) a Microsoft által készített vagy a közösségtől származó jövőbeli Azure Sentinel-jegyzetfüzetek helye.

A jegyzetfüzetek használatához rendelkeznie kell egy Azure Notebooks-fiókkal. További információ: [Rövid útmutató: Jelentkezzen be, és állítsa be](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) a felhasználói azonosítót az Azure Notebooks dokumentációjában. A fiók létrehozásához használhatja a Regisztráció az **Azure Notebooks** szolgáltatáshoz lehetőséget az **Azure Sentinel – Jegyzetfüzetek**parancssávjáról:

> [!div class="mx-imgBorder"]
>![Regisztráció az Azure Notebooks beállításra](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Futtathat egy jegyzetfüzetet közvetlenül az Azure Sentinelből, vagy klónozhatja az összes Azure Sentinel-jegyzetfüzetet egy új Azure Notebooks projektbe.

## <a name="run-a-notebook-from-azure-sentinel"></a>Jegyzetfüzet futtatása az Azure Sentinelből
 
1. Az Azure Portalon keresse meg az **Azure Sentinel** > **fenyegetéskezelési** > **jegyzetfüzeteit,** ahol megtekintheti az Azure Sentinel által biztosított jegyzetfüzeteket. 

2. Válassza ki az egyes jegyzetfüzeteket a leírások, a szükséges adattípusok és adatforrások olvasásához. Példa:
    
    > [!div class="mx-imgBorder"]
    > ![notebook indítása](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Válassza ki a használni kívánt jegyzetfüzetet, majd válassza **a Notebook indítása (előzetes verzió)** lehetőséget a jegyzetfüzet klónozásához és konfigurálásához egy új Azure Notebooks projektbe, amely az Azure Sentinel-munkaterülethez csatlakozik. Amikor a folyamat befejeződött, a jegyzetfüzet megnyílik az Azure Notebooks-ban futtatható.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Az Azure Sentinel-jegyzetfüzetek klónozása egy új Azure Notebooks projektbe

Ez az eljárás létrehoz egy Azure Notebooks projektet, amely tartalmazza az Azure Sentinel-jegyzetfüzeteket. Ezután futtathatja a jegyzetfüzeteket a hogyésként, vagy módosíthatja őket, majd futtathatja őket.

1. Az Azure Portalon keresse meg az **Azure Sentinel** > **fenyegetéskezelési** > **jegyzetfüzeteit,** és válassza a **klónozó jegyzetfüzetek** lehetőséget a parancssávról:
  
    > [!div class="mx-imgBorder"]
    >![Jegyzetfüzetek klónozása beállítás](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Amikor a következő párbeszédpanel jelenik meg, válassza **az Importálás** lehetőséget a GitHub-tártár azure-beli jegyzetfüzet-projektbe való klónozásához. Ha nem rendelkezik meglévő Azure Notebook-fiókkal, a rendszer kéri, hogy hozzon létre egyet, és jelentkezzen be.

   ![Jegyzetfüzet importálása](./media/notebooks/sentinel-notebooks-clone.png)

3. A **GitHub-tárház feltöltése** párbeszédpanelen ne válassza **a Clone rekurzív lehetőséget,** mert ez a beállítás csatolt GitHub-adatra hivatkozik. A projekt nevéhez használja az alapértelmezett nevet, vagy írja be az újat. Ezután kattintson **az Importálás** gombra a GitHub-tartalom klónozásának megkezdéséhez, amely néhány percet is igénybe vehet.

   ![Jegyzetfüzet importálása](./media/notebooks/sentinel-create-project.png)

4. Nyissa meg az imént létrehozott projektet, majd nyissa meg a **Jegyzetfüzetek mappát** a jegyzetfüzetek megtekintéséhez. Példa:

   ![Tártár importálása](./media/notebooks/sentinel-open-notebook1.png)

Ezután futtathatja a jegyzetfüzeteket az Azure Notebooks. Ha vissza szeretne térni ezekhez a jegyzetfüzetekhez az Azure Sentinelből, válassza az **Ugrás a jegyzetfüzetekhez** lehetőséget az **Azure Sentinel – Jegyzetfüzetek**parancssávjáról:

> [!div class="mx-imgBorder"]
>![Ugrás a Jegyzetfüzetek beállításra](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Jegyzetfüzetek használata vadászathoz

Minden jegyzetfüzet végigvezeti a vadászat vagy nyomozás elvégzésének lépésein. A jegyzetfüzet által szükséges könyvtárak és egyéb függőségek telepíthetők magából a jegyzetfüzetből vagy egy egyszerű konfigurációs eljárással. A notebook-projektet az Azure Sentinel-előfizetéshez visszakötést tartalmazó konfiguráció automatikusan kiépítésre kerül az előző lépésekben.

1. Ha még nem rendelkezik az Azure Notebooks, használhatja a **Go to your Notebooks** opciót a parancssáv az **Azure Sentinel – Notebookok:**
    
    > [!div class="mx-imgBorder"]
    >![Ugrás a Jegyzetfüzetek beállításra](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Az Azure Notebooks, válassza a **Saját projektek**, majd a projekt, amely tartalmazza az Azure Sentinel notebookok, és végül a **Jegyzetfüzetek** mappába.
    
2. A jegyzetfüzet megnyitása előtt vegye figyelembe, hogy alapértelmezés szerint a Szabad számítás beállítás van kiválasztva a jegyzetfüzetek futtatásához:
    
   ![jegyzetfüzet kijelölése](./media/notebooks/sentinel-open-notebook2.png)
    
    Ha beállított egy adatelemzési virtuális gépek (DSVM) használata a bevezetőben leírtak szerint, válassza ki a DSVM és hitelesíti megnyitása előtt az első notebook. 

3. Jelöljön ki egy jegyzetfüzetet a megnyitásához.
    
    A jegyzetfüzet első megnyitásakor a rendszer kérheti a rendszer rendszermag-verzió kiválasztását. Ha a rendszer nem kéri, kiválaszthatja a kernel verziót a **Kernel** >  **Change kernel**ből, majd kiválaszthatja a legalább 3.6-os verziót. A kijelölt kernelverzió a jegyzetfüzetablak jobb felső részén jelenik meg:
    
   ![jegyzetfüzet kijelölése](./media/notebooks/sentinel-select-kernel.png)

4. Mielőtt bármilyen módosítást végezne a letöltött jegyzetfüzeten, célszerű másolatot készíteni az eredeti jegyzetfüzetről, és dolgozni a másolaton. Ehhez válassza a **Fájl** > **másolása**lehetőséget. A másolatokon végzett munka lehetővé teszi, hogy biztonságosan frissítsen a jegyzetfüzetek jövőbeli verzióira anélkül, hogy felülírna az adatokat.
    
    Most már készen áll a kijelölt jegyzetfüzet futtatására vagy szerkesztésére.

Ajánlások:

- Az Azure Sentinelben az adatok lekérdezésének rövid bemutatását a fő **jegyzetfüzetek** mappában található [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) jegyzetfüzet című részben találja. 

- További mintajegyzetfüzeteket talál a **Mintajegyzetfüzetek** almappában. Ezek a mintajegyzetfüzetek adatokkal lettek mentve, így könnyebben láthatóak a kívánt kimenet. Javasoljuk, hogy tekintse meg ezeket a notebookokat az [nbviewer programban.](https://nbviewer.jupyter.org/) 

- A **HowTos** mappa olyan jegyzetfüzeteket tartalmaz, amelyek például a következőket tartalmazzák: Alapértelmezett Python-verzió beállítása, DSVM konfigurálása, Azure Sentinel könyvjelzők létrehozása jegyzetfüzetből és egyéb témák.

A megadott jegyzetfüzetek hasznos eszközökként, valamint illusztrációkként és kódmintákként is használhatók a saját jegyzetfüzetek fejlesztéséhez.

Szívesen fogadunk visszajelzést, legyen szó javaslatokról, funkciókra vonatkozó kérésekről, jegyzetfüzetekről, hibajelentésekről vagy fejlesztésekről és fejlesztésekről a meglévő jegyzetfüzetekhez. Lépjen az [Azure Sentinel community GitHubra](https://github.com/Azure/Azure-Sentinel) egy probléma vagy elágazás létrehozásához, és töltsön fel egy hozzájárulást.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kezdheti el használni a Jupyter-jegyzetfüzeteket az Azure Sentinelben. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:

- [Proaktív vadászat a fenyegetésekre](hunting.md)
- [Használjon könyvjelzőket, hogy érdekes információkat mentsen vadászat közben](bookmarks.md)
