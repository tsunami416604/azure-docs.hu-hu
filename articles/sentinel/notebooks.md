---
title: Notebookok használatával az Azure-on Előzetesben Sentinel-vadászat képességek |} A Microsoft Docs
description: Ez a cikk ismerteti a notebookok használata az Azure-Sentinel vadászat képességeket.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ae9d52e4a26825e4318a6afb8aadc86ac29fa2b3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677833"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>A biztonsági fenyegetések hunt Jupyter notebookok használata

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure-Sentinel alapjai az adattár; amely ötvözi az nagy teljesítményű, a dinamikus sémák és skálázását követve rugalmasan méretezhető, nagy adatkötetek lekérdezéséhez. Az Azure Sentinel-portál és az összes Azure Sentinel-eszközök az adattár eléréséhez használja egy közös API-t. Az azonos API-t is érhető el a külső eszközök például [Jupyter](https://jupyter.org/) jegyzetfüzetek és a Python használatával. Számos gyakori feladatok a portálon is végrehajtható, míg Jupyter mire képes az adatok a hatóköre terjeszti ki. Teljes programozhatóság kombinálja-kódtárak a machine learning, megjelenítési és elemzési hatalmas gyűjteménye. Ezek az attribútumok győződjön meg arról, a Jupyter egy biztonsági vizsgálati és vadászat meggyőző eszköz.

![Példa notebook](./media/notebooks/sentinel-nb-mapandtimeline.png)

Már integráltuk a Jupyter élményt a Azure Sentinel-portálra, így könnyen hozhat létre, és hajtsa végre az adatok elemzéséhez jegyzetfüzetekkel. A *Kqlmagic* kódtár biztosítja az összekötő, amely lehetővé teszi lekérdezések igénybe Azure Sentinel- és futtathatja őket közvetlenül egy jegyzetfüzetet belül. Lekérdezések használata a [Kusto-lekérdezés nyelvi](https://kusto.azurewebsites.net/docs/query/index.html). Több notebookokat, a Microsoft biztonsági adatelemzők által fejlesztett Azure Sentinel-vannak csomagolva. Ezeket a notebookokat néhány egy adott forgatókönyv épülnek, és használható – van. Mások célja és minták bemutatják a módszerek és szolgáltatások, amelyek másolhatja, illetve a saját notebookok használata igazíthatja. Más notebookok is importálja az Azure Sentinel-Közösség GitHub.

Az integrált Jupyter-felületet használja [Azure notebookok](https://notebooks.azure.com/) szeretné tárolni, megosztás, és hajtsa végre a jegyzetfüzetet. Is futtathatja ezeket a notebookokat helyileg (Ha rendelkezik egy Python-környezetet és a Jupyter a számítógépen) vagy más JupterHub környezetekben, például az Azure Databricks.

Notebookok két összetevőből állnak:

- a böngészőalapú felület, ahol adja meg, és futtassa a lekérdezéseket és kódot, és hol jelennek meg a végrehajtási eredményeit.
- egy *kernel* feladata, hogy elemzés és a kultúrának végrehajtása. 

Az Azure-ban futtatja a kernel Azure Notebookokat, *ingyenes felhőalapú számítási és tárolási* alapértelmezés szerint. Ha a notebookok összetett gépi tanulási modelleket vagy a Vizualizációk akkor érdemes megfontolni nagyobb teljesítményű, dedikált számítási erőforrásokat például [adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Notebookok fiókjában található titkos őrzi meg, ha úgy dönt, hogy megosztja őket.

Az Azure-Sentinel notebookok használata számos népszerű Python-kódtárakat például pandas, matplotlib, bokeh és mások. Nincsenek nagyon sok egyéb Python-csomagokat, választhat, például kiterjedő területek:

- Vizualizációk és képek
- adatok feldolgozása és elemzése
- statisztikák és numerikus számítástechnika
- gépi tanulási és deep learning

Néhány nyílt forráskódú Jupyter biztonsági eszközökkel nevű csomagot is kibocsátottunk [msticpy](https://github.com/Microsoft/msticpy/). Ez a csomag számos, a csomagban foglalt notebookok használatos. Msticpy eszközök tervezett kifejezetten vadászat-jegyzetfüzeteket létrehozásával és a vizsgálat, és folyamatosan aktívan dolgozunk az új szolgáltatásait és fejlesztéseit.

A kezdeti notebookok a következők:

- **Interaktív vizsgálati - folyamat riasztások**: Gyorsan osztályozhatja a riasztásokat az érintett gazdagép(ek) a tevékenység elemzésével lehetővé teszi.
- **Interaktív vadászat – Windows-állomás explorer**: Lehetővé teszi fióktevékenység, folyamat-végrehajtás, hálózati tevékenységek és egyéb események tallózása a gazdagépen.  
- **Interaktív vadászat – Office 365-felfedezése**: Hunt több O365 adatkészletek gyanús Office 365-tevékenysége számára.

A [Azure Sentinel-Közösség GitHub-adattár](https://github.com/Azure/Azure-Sentinel) minden jövőbeli Azure Sentinel-jegyzetfüzetek helyét a Microsoft által készített, vagy a Közösségtől származó.

## <a name="run-a-notebook"></a>-Jegyzetfüzet futtatása

A következő példában létrehozunk egy Azure-jegyzetfüzetek projekt Sentinel-Azure portal, a projekt a notebookok feltöltése. Ezeket a notebookokat használatához, egy célszerű másolatot készít a notebook és a példányon. Dolgozunk a másolatok biztonságosan frissítse notebookok későbbi verzióiban az adatok felülírása nélkül teszi lehetővé.

1. Az Azure-Sentinel-portálon kattintson a **notebookok** a navigációs menü. Hozzon létre egy új Azure-jegyzetfüzetek projektet, kattintson a **Klónozás Azure Sentinel-jegyzetfüzetek** , nyissa meg a meglévő jegyzetfüzeteket-projektek, kattintson vagy **nyissa meg a notebookok**.
  
   ![Válassza ki a notebookok](./media/notebooks/sentinel-az-notebooks-home.png)

2. Ha úgy döntött **Klónozás Azure Sentinel-jegyzetfüzetek** az előző lépésben, az alábbi párbeszédablak jelenik meg. Kattintson a **importálás** , klónozza a GitHub-adattárat a Azure notebookok projektbe. Ha egy meglévő Azure-jegyzetfüzetek fiók nem rendelkezik, akkor hozzon létre egyet, és jelentkezzen be a kéri.

   ![Importálás notebook](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Új projekt létrehozásakor kell adja a projektnek, – használja az alapértelmezett név vagy típus egy újat. Ne ellenőrizze a **Klónozás rekurzív módon** lehetőséget – társított GitHub-adattárak hivatkozik ezt a beállítást. Kattintson a **importálás** elindítja a Klónozás a GitHub-tartalom, ami eltarthat néhány percig.

   ![Importálás notebook](./media/notebooks/sentinel-create-nb-project.png)

4. Nyissa meg a **notebookok** mappában a jegyzetfüzet. Minden egyes notebook végigvezeti egy hunt vagy egy vizsgálatot végző lépéseit. Könyvtárak és egyéb függőségek, a notebook számára szükséges a notebookból magát vagy egy egyszerű konfigurálás eljárás keresztül telepíthető. Az előző lépésekben a rendszer automatikusan üzembe konfigurációt, amely összeköt a notebook projekt vissza az Azure Sentinel-előfizetését. A jegyzetfüzetek futtatásához az Azure Sentinel-Log Analytics-munkaterület készen áll.

   ![Tárház importálása](./media/notebooks/sentinel-open-notebook1.png)

5. Nyissa meg a Notebook. Ingyenes számítási van alapértelmezettként kiválasztva a (kiemelve) jegyzetfüzetek futtatásához. Ha konfigurálta a dsvm-hez használandó (lásd fent), válassza ki a dsvm-hez, és az első jegyzetfüzet Megnyitás előtt hitelesítést. Kattintson a notebook megnyitásához.

   ![notebook kiválasztása](./media/notebooks/sentinel-open-notebook2.png)

6. A Python-verzió kiválasztása. A notebook első megnyitásakor kérheti, hogy válassza ki a kernel verziója. Ha nem, válassza ki a kernel használja az alábbiak szerint. A Python 3.6-os vagy újabb verzióját kell lennie a kiválasztott kernel (felső, a Jegyzetfüzet-ablak jobb).

   ![notebook kiválasztása](./media/notebooks/sentinel-select-kernel.png)

Amely röviden ismerteti az Azure Sentinel-adatok lekérdezése a, tekintse meg a [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) jegyzetfüzet a fő notebookok mappában. További mintafüzetek megtalálható a **Mintafüzetek** almappába. A mintafüzetek mentett adatokat, így könnyebben tekintse meg a kívánt kimeneti (javasoljuk, hogy tekinti meg őket a [nbviewer](https://nbviewer.jupyter.org/)). A **HowTos** mappa tartalmazza a notebookok leíró, például: beállítás alapértelmezett Python-verzió, konfigurál egy adatelemző virtuális GÉPET, egy jegyzetfüzetet, és más témák létrehozása az Azure Sentinel-könyvjelzők.

Ezeket a notebookokat készültek, mindkét hasznos eszközök és illusztrációk és kódmintákkal, amelyek saját jegyzetfüzetek fejlesztését is használhatja.

Elküldheti visszajelzését, javaslatok, szolgáltatások, a kérelmek viszonyított notebookok, a hibajelentést, vagy a fejlesztései és a meglévő jegyzetfüzeteket a Hozzáadás-e. Nyissa meg a [Azure Sentinel-Közösség GitHub](https://github.com/Azure/Azure-Sentinel) hozzon létre egy problémára vagy egy elágazás, és töltse fel a hozzájárulását.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedhetett az Azure-Sentinel Jupyter notebookok használatának megkezdéséhez. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Proaktív módon hunt fenyegetések](hunting.md)
- [Könyvjelzőkkel mentése közben vadászat hasznos információkat nyerhet ki](bookmarks.md)