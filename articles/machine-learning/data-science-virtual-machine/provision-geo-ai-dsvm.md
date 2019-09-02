---
title: 'Gyors útmutató: Geo mesterséges intelligencia beállítása'
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan hozhat létre, és a földrajzi mesterséges Intelligencia adatelemző virtuális gép konfigurálása. A földrajzi mesterséges Intelligencia adatelemzési virtuális gépet használó földrajzi mesterséges Intelligencia és gépi Tanulási megoldások létrehozásához szükséges eszközöket biztosít.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/05/2018
ms.openlocfilehash: 7afeec3f71cd1af30093801fedabf3f0357ae3d0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208060"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Gyors útmutató: Az Azure-beli geo mesterséges intelligencia-beli virtuális gép beállítása 

A Geo AI Data Science Virtual Machine (Geo-DSVM) a népszerű [Azure-Data Science Virtual Machine](https://aka.ms/dsvm) kiterjesztése, amely kifejezetten az AI és a térinformatikai elemzések összevonására van konfigurálva. A térinformatikai elemzés, a virtuális gép működteti [ArcGIS Pro](https://www.arcgis.com/features/index.html). A Data Science Virtual Machine (DSVM) lehetővé teszi a gépi tanulási és még mélyebb tanulási modellek gyors betanítását. A modellek fejlesztéséhez a földrajzi adatokkal dúsított adatokat használ. A Geo-DSVM csak a Windows 2016 DSVM támogatott. 

A Geo-DSVM található AI-eszközök közé a következők tartoznak:

- Népszerű mélyreható tanulási keretrendszerek (például a Microsoft Cognitive Toolkit, a TensorFlow, a kerasz, a Caffe2 és a Chainer) GPU-kiadásai
- Képek és szöveges adatok beolvasására és előfeldolgozására szolgáló eszközök
- Eszközök olyan fejlesztési tevékenységekhez, mint például a Microsoft Machine Learning Server Developer Edition, a anaconda Python, a Python és az R Jupyter notebookok, ide-k a Python és az R-hez, valamint az SQL-adatbázisokhoz
- ArcGIS Pro Desktop szoftvert az ESRI-ből, valamint a Python és az R felületek használatával, amelyek a térinformatikai adatokkal dolgozhatnak az AI-alkalmazásokból
 

## <a name="create-your-geo-ai-data-science-vm"></a>A földrajzi mesterséges intelligenciát Használó adatelemzési virtuális gép létrehozása

A Geo AI Data Science VM példányának létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)virtuális gép listáját.
1. **Hozzon létre** egy varázslót a lenti létrehozás elem kiválasztásával:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. A varázsló a négy lépés mindegyikének bemenetét igényli. A bemenettel kapcsolatos részletes információkért tekintse meg a következő szakaszt.

### <a name="wizard-details"></a>Varázsló részletei ###

**Alapvető beállítások**:

- **Név**: Neve az adatelemzési kiszolgálót hoz létre.
    
- **Felhasználónév**: Rendszergazdai fiók bejelentkezési azonosítója.
    
- **Jelszó**: Rendszergazdai fiók jelszava.
    
- **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.
    
- **Erőforráscsoport**: Létrehozhat egy újat, vagy egy **üres** meglévő Azure-erőforráscsoportot is használhat az előfizetésében.
    
- **Hely**: Válassza ki a legmegfelelőbb adatközpontot. Általában ez az, amely a legtöbb adattal rendelkezik, vagy amely a leggyorsabb hálózati hozzáféréshez legközelebb esik a fizikai helyhez. Ha a GPU-ra kiterjedő mély tanulást szeretne futtatni, ki kell választania az Azure-ban található egyik olyan helyet, amely NC sorozatú GPU VM-példányokkal rendelkezik. Jelenleg ezek a helyszínek a következők: USA keleti régiója, USA északi középső régiója, USA déli középső régiója, USA 2. nyugati régiója, **Észak-Európa, Nyugat-Európa**. A legfrissebb listán tekintse meg az [Azure-termékek régiónként](https://azure.microsoft.com/regions/services/) lapot, és keresse meg az **NC-sorozatot** a **számítás**területen. 
    
    
**Beállítások**: Válassza ki az egyik NC sorozatú GPU virtuálisgép-méretet, ha mély tanulást szeretne futtatni a földrajzi DSVM található GPU-val. Ellenkező esetben kiválaszthatja a CPU-alapú példányok egyikét. Hozzon létre egy tárfiókot a virtuális géphez. 
       
**Összefoglalás**: Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt.
    
**Vásároljon**: A létesítési folyamat elindításához kattintson a **vásárlás**gombra. Egy hivatkozást a szolgáltatás feltételeit. A virtuális gép nem rendelkezik további költségekkel a **méret** lépésben kiválasztott kiszolgáló méretére vonatkozó számítási díjakon túl. 
 
 >[!NOTE]
 > A kiépítés körülbelül 20 – 30 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>A földrajzi mesterséges Intelligencia adatelemző virtuális gép elérése

 A virtuális gép létrehozása után készen áll arra, hogy megkezdje a telepített és előre konfigurált eszközök használatát. Számos eszközhöz a Start menü csempéi és asztali ikonjai tartoznak. A virtuális gépet a távoli asztal szolgáltatással érheti el az **alapok** szakaszban konfigurált rendszergazdai fiók hitelesítő adataival.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>ArcGIS Pro segítségével telepítve a virtuális gépen

A ArcGIS Pro Desktop előre telepítve van a Geo-DSVM, és a környezet előre konfigurálva van, hogy működjön a DSVM összes eszközével. A ArcGIS indításakor a rendszer kéri a ArcGIS-fiók hitelesítő adatait. Ha már rendelkezik az ArcGIS-fiókkal, és rendelkezik a szoftver licencét, használhatja a meglévő hitelesítő adatait.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Ellenkező esetben regisztrálhat egy új ArcGIS-fiókot és-licencet, vagy kérheti az [ingyenes próbaverziót](https://www.arcgis.com/features/free-trial.html). 

![Az ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

A standard ArcGIS-fiókra vagy ingyenes próbaverzióra való regisztráció után a [ArcGIS Pro használatának első](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)lépéseivel kapcsolatos utasításokat követve engedélyezheti a ArcGIS Pro-t a fiókjához.

Miután bejelentkezett a ArcGIS Pro Desktopba a ArcGIS-fiókkal, készen áll arra, hogy a térinformatikai elemzési és gépi tanulási projektek számára a virtuális gépen telepített és konfigurált adatelemzési eszközöket használja.

## <a name="next-steps"></a>További lépések

A Geo AI Data Science VM használatának megkezdése a következő erőforrás útmutatásával:

* [A földrajzi mesterséges Intelligencia adatelemzési virtuális gép használata](use-geo-ai-dsvm.md)
