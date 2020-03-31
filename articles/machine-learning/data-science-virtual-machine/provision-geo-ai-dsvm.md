---
title: 'Rövid útmutató: Geo AI-adatelemzési virtuális gép létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Konfiguráljon és hozzon létre egy Geo AI Data Science virtuális gépet az Azure-ban térinformatikai elemzéshez és gépi tanuláshoz.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525889"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Rövid útmutató: Geo mesterséges intelligenciával foglalkozó virtuális gép beállítása az Azure-ban 

A Geo AI Data Science virtuális gép (Geo-DSVM) a népszerű [Azure Data Science virtuális gép](https://aka.ms/dsvm) kiterjesztése, amely speciálisan az AI és a térinformatikai elemzés kombinálására van konfigurálva. A virtuális gép térinformatikai elemzését az [ArcGIS Pro](https://www.arcgis.com/features/index.html)működteti. Az adatelemzési virtuális gép (DSVM) lehetővé teszi a gépi tanulás és még a mélytanulási modellek gyors betanítását. Ezeknek a modelleknek a fejlesztéséhez földrajzi adatokkal bővített adatokat használ. A Geo-DSVM csak Windows 2016 DSVM esetén támogatott. 

A Geo-DSVM-ben található AI-eszközök a következők:

- A népszerű mélytanulási keretrendszerek, például a Microsoft Cognitive Toolkit, a TensorFlow, a Keras, a Caffe2 és a Chainer GPU-kiadásai
- Eszközök a kép- és szöveges adatok beszerzéséhez és előfeldolgozásához
- Fejlesztési eszközök, például Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter notebookok Pythonhoz és R-hez, IdEs python és R, valamint SQL-adatbázisok
- ArcGIS Pro asztali szoftver esri, valamint a Python és R interfészek, amelyek képesek együttműködni a térinformatikai adatokat a AI alkalmazások
 

## <a name="create-your-geo-ai-data-science-vm"></a>A Geo AI Data Science virtuális gép létrehozása

A Geo AI Data Science virtuális gép egy példányának létrehozásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a virtuális gép listáját az [Azure Portalon.](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)
1. A varázsló létrehozásához válassza a **létrehozás** lehetőséget az alján alul:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. A varázsló nak mind a négy lépéshez szüksége van a bevitelre. A bevitelről a következő szakaszban talál részletes tájékoztatást.

### <a name="wizard-details"></a>A varázsló adatai ###

**Alapok:**

- **Név**: A létrehozási adatelemzési kiszolgáló neve.
    
- **Felhasználónév**: Rendszergazdai fiók bejelentkezési azonosítója.
    
- **Jelszó**: Rendszergazdai fiók jelszava.
    
- **Előfizetés:** Ha egynél több előfizetéssel rendelkezik, válassza ki azt, amelyen a gépet létre kell hozni és ki számlázni szeretné.
    
- **Erőforráscsoport:** Létrehozhat egy újat, vagy használhat egy **üres** meglévő Azure-erőforráscsoportot az előfizetésében.
    
- **Hely**: Válassza ki a legmegfelelőbb adatközpontot. Általában ez az, amely a legtöbb adatot, vagy hogy a legközelebb a fizikai helyét a leggyorsabb hálózati hozzáférés. Ha azt tervezi, hogy a mély tanulás a GPU-n, ki kell választania az egyik helyen az Azure-ban, amely nc-sorozatú GPU virtuálisgép-példányok. Jelenleg ezek a helyek: **USA keleti régiója, USA északi középső régiója, USA déli középső régiója 2, Észak-Európa, Nyugat-Európa**. A legújabb lista, ellenőrizze az [Azure-termékek régió szerint](https://azure.microsoft.com/regions/services/) lapon, és keresse meg az **NC-sorozat** a **Számítás**. 
    
    
**Beállítások:** Válassza ki az NC sorozatú GPU virtuálisgép-méretek egyikét, ha a Geo DSVM GPU-ján szeretné futtatni a mélytanulást. Ellenkező esetben kiválaszthatja a CPU-alapú példányok egyikét. Hozzon létre egy tárfiókot a virtuális gép. 
       
**Összegzés:** Ellenőrizze, hogy a megadott összes adat helyes-e.
    
**Vásárlás**: A kiépítési folyamat elindításához kattintson a **Vásárlás**gombra. A szolgáltatás feltételeire mutató hivatkozás ta- A virtuális gép nem rendelkezik semmilyen további díjak at a számítási díjak a kiszolgáló mérete a **méret** lépésben kiválasztott. 
 
 >[!NOTE]
 > A kiépítés körülbelül 20-30 percet vesz igénybe. A kiépítés állapota megjelenik az Azure Portalon.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>A Geo AI adatelemzési virtuális gép elérése

 A virtuális gép létrehozása után készen áll a telepített és előre konfigurált eszközök használatára. Vannak Start menü csempe és asztali ikonok sok az eszközök. A virtuális gép távoli asztalon érhető el az **Alapjak** szakaszban konfigurált rendszergazdai fiók hitelesítő adataival.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>A virtuális gépbe telepített ArcGIS Pro használata

A Geo-DSVM-en az ArcGIS Pro asztal előre telepítve van, és a környezet előre konfigurálva van, hogy működjön együtt a DSVM összes eszközével. Az ArcGIS indításakor a rendszer kéri az ArcGIS-fiók hitelesítő adatainak megadását. Ha már rendelkezik ArcGIS-fiókkal, és licenccel rendelkezik a szoftverhez, használhatja a meglévő hitelesítő adatokat.  

![Ív-GIS-bejelentkezés](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Ellenkező esetben regisztrálhat egy új ArcGIS-fiókot és licencet, vagy [ingyenes próbaverziót](https://www.arcgis.com/features/free-trial.html)kaphat. 

![ArcGIS-mentes próbaverzió](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Miután feliratkozott egy szabványos ArcGIS-fiókra vagy egy ingyenes próbaverzióra, engedélyezheti az ArcGIS Pro-t a fiókjához az [ArcGIS Pro első](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)lépései című útmutató utasításainak követésével.

Miután arcgis-fiókján keresztül bejelentkezett az ArcGIS Pro asztalra, készen áll a virtuális gépen telepített és konfigurált adatelemzési eszközök használatára a térinformatikai elemzési és gépi tanulási projektekhez.

## <a name="next-steps"></a>További lépések

Kezdje el használni a Geo AI Data Science virtuális gép útmutatást a következő erőforrás:

* [A Geo AI Data Science virtuális gép használata](use-geo-ai-dsvm.md)
