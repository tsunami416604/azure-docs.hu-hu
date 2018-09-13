---
title: Az Azure Machine Learning előzetes verziójának funkcióit fogalmi áttekintése |} A Microsoft Docs
description: Az előzetes verziójú funkciók az Azure Machine Learning, például az előfizetések, fiókok, munkaterületek, projektek fogalmi áttekintése.
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: f63b9c077e64b642adfd8c7eed5026563eb6319a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644984"
---
# <a name="azure-machine-learning---concepts"></a>Az Azure Machine Learning - fogalmak

Ez a cikk határozza meg, és a fogalmakat kell tudni az Azure Machine Learning használatát ismerteti. 

![Hierarchia fogalmak](media/overview-general-concepts/hierarchy.png)

- **Előfizetés:** Azure-előfizetés az Azure-erőforrások hozzáférést biztosít. Az Azure Machine Learning számítási, tárolási, és számos egyéb Azure-erőforrások és szolgáltatások mélyen integrált, mert a Workbench, hogy minden felhasználó rendelkezik-e egy érvényes Azure-előfizetés hozzáférést igényel. Felhasználók adott előfizetésen belüli erőforrások létrehozásához szükséges engedélyekkel is rendelkeznie kell.


- **Kísérletezési fiók:** Kísérletezési fiók egy Azure-erőforrás Azure gépi tanulás és a egy számlázási jármű szükséges. A munkaterületek, projektek viszont tartalmazó tartalmazza. Felvehet több felhasználót, néven _munkaállomások_, egy Kísérletezési fiókhoz. Kísérletezési fiók hozzáféréssel kell rendelkeznie futtathat kísérleteket az Azure Machine Learning Workbench használatával. 


- **Modellkezelési fiók** modellkezelési fiók is egy Azure-erőforrást az Azure Machine Learning modellek kezeléséhez szükséges. Regisztrálja a modellt és a jegyzékek, tárolóalapú webalkalmazás-szolgáltatások létrehozásához és üzembe helyezése, helyileg vagy a felhőben használhatja azt. Egyéb számlázási megfelel az Azure ML egyben.


- **Munkaterület:** munkaterület az elsődleges megosztása és együttműködés az Azure ML-összetevőt. A munkaterületen belül a projektek szerint vannak csoportosítva. Egy munkaterület a Kísérletezési fiókhoz hozzáadott több felhasználó majd megosztható.


- **A projekt:** az Azure Machine Learning, a projekt a probléma megoldása érdekében végzett munka logikai tárolója. Egyetlen, a helyi lemezen lévő mappára vannak leképezve, és tetszőleges számú fájl vagy almappa adható hozzájuk. Egy projektet is lehet társítva egy Git-tárház a forráskezelés és az együttműködés.  

- **Kísérlet:** az Azure gépi tanulás, egy kísérlet egy vagy több kódot forrásfájl(ok), amely egy adott belépési pontot hajthatók végre. Feladatokhoz, mint az adatbetöltés, az funkciófejlesztési, a modell betanítása vagy a modell értékelése tartalmazhat. Jelenleg Azure ML támogatja a Python, vagy csak a PySpark kísérleteket.


- **Modell:** az Azure Machine Learning modellek tekintse meg a machine learning-kísérletből szorzatát. Receptek azok, amelyek adatokra alkalmazott megfelelően, ha előre jelzett értékek létrehozásához. Modellek lehet üzembe helyezett tesztelési és éles környezetekben, és az új adatok pontozásához használják. Egyszer éles környezetben modellek figyelhetők a teljesítmény és az eltéréseket, és szükség szerint retrained. 

- **Számítási célnak:** egy számítási célnak a számítási erőforrás határozzák meg a kísérletek végrehajtása. A helyi számítógép (Windows vagy macOS), a Docker-tárolót a helyi számítógépen vagy a Linux rendszerű virtuális gép futtatása az Azure- vagy egy HDInsight Spark-fürt is lehet.


- **Futtatás:** a Kísérletezési szolgáltatással hoztam létre egy Futtatás egy kísérlet-végrehajtás, a számítási célt élettartamát határozza meg. Az Azure Machine Learning információt az egyes futtatások automatikusan rögzíti, és megadja az egy adott kísérlet a futtatási előzmények az űrlap teljes előzményeit.

- **Környezet:** az Azure Machine Learning-környezet azt jelzi, egy adott számítási erőforrás, amely központi telepítésére és a modellek felügyeletére szolgál. Lehet, hogy a helyi számítógépen, Linux rendszerű virtuális gép az Azure-ban vagy egy környezet és a konfiguráció függően az Azure Container Service-ben futtatott Kubernetes-fürt. A modell ezekben a környezetekben futó Docker-tárolóban üzemeltetett, és a egy REST API-végpont néven jelenik meg.


- **Kezelt modell:** Modellkezelési lehetővé teszi a modellek webszolgáltatásként üzembe helyezése, a modellek különféle verzióit kezelheti, és azok teljesítményére és metrikák figyelése. Kezelt modellek Azure Machine Learning Modellkezelési fiókkal van regisztrálva.

- **Jegyzékek:** a Modellkezelés rendszer éles környezetbe helyez üzembe egy modellt, amikor a jegyzékfájl, amely is magában foglalja a modell, a függőségeket, a pontozó szkript, a mintaadatok és a séma tartalmazza. A jegyzékfájl a recept, egy Docker-tároló rendszerképét létrehozásához használt. Modellkezelés használatával, a jegyzékek automatikus létrehozása, hozzon létre a különböző verziók, és ezeket a jegyzékek kezelése. 


- **Képek:** jegyzékek segítségével hozzon létre (és újbóli létrehozása) Docker-rendszerképeket. Tárolóalapú Docker-rendszerképek rugalmasan futtathatja őket a felhőben, a helyi gépek vagy a IoT-eszköz létrehozása. Képek önálló, és tartalmazza a modellek az új adatok pontozásához szükséges összes függőséget. 

- **Szolgáltatások:** Modellkezelési lehetővé teszi, hogy helyezhet üzembe modelleket webszolgáltatásként. A webszolgáltatás és függőségei vannak beágyazva rendszerképben. Minden webszolgáltatás olyan tárolók, a lemezkép készen áll a szolgáltatáskéréseket a megadott URL-cím alapján. Egy webes szolgáltatás akkor számít, egyetlen telepítést.
