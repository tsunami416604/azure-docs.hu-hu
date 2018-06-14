---
title: Egy földrajzi mesterséges Eszközintelligencia virtuális gépet az Azure - kiépítése Azure |} Microsoft Docs
description: Megtudhatja, hogyan lehet kiépíteni egy földrajzi AI virtuális gépet az Azure.
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, az adatok tudományos virtuális gépet, a földrajzi elemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 93dfe6594aeaf45a6905fe8cb55c98dd37cc9599
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408608"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Egy földrajzi mesterséges Eszközintelligencia virtuális gépet az Azure telepítéséhez 

A földrajzi AI adatok tudományos virtuális gép (földrajzi-DSVM) a népszerű kiterjesztése [Azure adatok tudományos virtuális gép](http://aka.ms/dsvm) egyesítése AI és földrajzi analytics kifejezetten konfigurált. A virtuális gép a földrajzi analytics szerint vannak kapcsolva [ArcGIS Pro](https://www.arcgis.com/features/index.html). Az adatok tudományos VM lehetővé teszi, hogy a gyors képzését machine learning modellek, és még a mély tanulási modellek, földrajzi információk van növelést-adatokat. Támogatott a használata Windows 2016 DSVM csak. 

A földrajzi DSVM AI beleértve eszközöket tartalmazza:

- Részletes tanulási népszerű keretrendszerekre GPU-kiadások, például Microsoft kognitív eszközkészlet, TensorFlow, Keras, Caffe2, Chainer; 
- eszközök beszerzése és előre folyamatok kép, szöveges adatok 
- adatbázisok fejlesztési tevékenységek, például Microsoft R Server Developer Edition, Anaconda Python, Python a Jupyter notebookokból és R, Python és R, SQL IDEs eszközei
- ESRI tartozó ArcGIS Pro asztali szoftver, amely használható a földrajzi adatok, az Eszközintelligencia alkalmazásaiból Python és R adapterrel együtt. 


## <a name="create-your-geo-ai-data-science-vm"></a>A földrajzi AI Adattudomány virtuális gép létrehozása

A folyamat során a földrajzi AI adatok tudományos VM példány létrehozásához a rendszer: 


1. Keresse meg a virtuális gépet, a listaelem [Azure-portálon](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Válassza ki a **létrehozása** gombra a varázsló veendő alján.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. A varázsló a földrajzi DSVM létrehozásához használt szükséges **bemenetek** az egyes a **négy lépésben** a jobb oldali ábra számba. Az alábbiakban az egyes lépéseket konfigurálásához szükséges adatokat:



   - **Alapvető beállítások**

      1. **Név**: az létrehozásakor tudományos-kiszolgáló nevét.

      2. **Felhasználónév**: rendszergazdai fiók bejelentkezési azonosító.

      3. **Jelszó**: rendszergazdai fiók jelszavát.

      4. **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki a amelyiken a gép létrehozását és számlázva van.

      5. **Erőforráscsoport**: hozhat létre egy új, vagy használjon egy **üres** meglévő Azure erőforráscsoport, az előfizetésben.

      6. **Hely**: válassza ki a legjobban megfelelő adatközpont. Általában az adatközpont, amely az adatok, vagy a helynév leggyorsabb hálózati hozzáférési legközelebb. Ha kell tennie a GPU mély tanulási, választania kell a helyek, amely a hálózati vezérlő által-sorozat GPU Virtuálisgép-példányok rendelkezik Azure-ban. A helyeket az GPU virtuális gépeket jelenleg: **USA keleti régiója, északi középső Régiójában, déli középső Régiójában, USA nyugati régiója 2. régiója, Észak-Európában, Nyugat-Európában**. A legújabb listája, tekintse meg a [Azure termékek régió lap](https://azure.microsoft.com/regions/services/) , és keressen **NC-sorozat** alatt **számítási**. 


   - **Beállítások**: Válasszon egyet a NC-sorozat GPU virtuálisgép-méret, ha azt tervezi, mély tanulási futtathatnak a földrajzi DSVM a GPU. Ellenkező esetben dönthet úgy, egyet a CPU-alapú példány.  Hozzon létre egy tárfiókot a virtuális gép számára. 
   
   - **Összefoglalás**: Győződjön meg arról, hogy az összes megadott adatok helyesek.

   - **Vásároljon**: kattintson a **megvásárlása** kiépítési elindításához. Hivatkozás a szolgáltatás feltételeit valósul meg. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretéhez számítási túl további díjakat a **mérete** lépés. 

>[!NOTE]
> A kiépítése körülbelül 20-30 percet kell végrehajtani. A kiépítési állapotát az Azure portálon jelenik meg.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Hogyan érhetők el a földrajzi AI adatok tudományos virtuális gép

A virtuális gép létrehozása után készen áll a telepített és előre konfigurált eszközök használatának. Start menü csempék és számos eszközt az asztali ikonok vannak. A rendszergazdai fiók hitelesítő adatait az előző konfigurált használatával történő távoli asztal is **alapjai** szakasz. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>A virtuális gépen telepített ArcGIS Pro használata

A földrajzi DSVM már előre telepített ArcGIS Pro asztali és a környezet előre konfigurálva van a DSVM az összes eszközökről. ArcGIS indításakor megkérdezi a bejelentkezési ArcGIS fiókjába. Ha már rendelkezik ArcGIS fiókkal, és a szoftver licenccel rendelkezik, használhatja a meglévő hitelesítő adatait.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Ellenkező esetben iratkozzon fel új ArcGIS fiók és a licencek vagy futtasson egy [ingyenes próbaverzió](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS ingyenes](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Miután előfizetési egy bármelyik fizetett vagy egy ingyenes próbafiók ArcGIS, engedélyezheti ArcGIS Pro fiókja utasításait követve a [ArcGIS Pro dokumentáció első lépések](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Miután bejelentkezik ArcGIS Pro asztali ArcGIS fiókjához, készen áll az adatok tudományos eszközöket, amelyek telepítése és konfigurálása történik meg a földrajzi elemzés és a gépi tanulási projektek a virtuális Gépen.

## <a name="next-steps"></a>További lépések

A földrajzi AI adatok tudományos virtuális Gépet útmutatással a következő témakörök a használatának megkezdése:

* [A földrajzi AI Adattudomány VM használata](use-geo-ai-dsvm.md)