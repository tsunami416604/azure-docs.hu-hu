---
title: Azure Machine Learning előzetes verziójú funkciók elméleti áttekintése |} Microsoft Docs
description: Az előzetes verziójú funkciók (például előfizetések, fiókok, munkaterületek, projektek) az Azure Machine Learning elméleti áttekintését.
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning---concepts"></a>Az Azure Machine Learning - fogalmak

Ez a cikk határozza meg, és az Azure Machine Learning használatához tudnia kell fogalmakat ismerteti. 

![Hierarchia fogalmak](media/overview-general-concepts/hierarchy.png)

- **Előfizetés:** Azure-előfizetés hozzáférést biztosít az Azure-erőforrások. Azure Machine Learning mélyen integrált számítási, tárolási, és sok más Azure-erőforrások és szolgáltatások, mert a munkaterület szükséges, hogy minden felhasználó rendelkezik-e egy érvényes Azure-előfizetéssel való hozzáférés. Felhasználók is kell rendelkeznie az adott előfizetésen belüli erőforrások létrehozásához szükséges engedélyekkel.


- **Kísérletezhet fiók:** kísérletezhet fiók egy Azure-erőforrás szükséges Azure ML és számlázási vehicle. A munkaterületek, amelyek viszont a projekteket tartalmazó tartalmaz. Felvehet több felhasználót, néven _munkaállomásokat_, kísérletezhet fiókhoz. Ahhoz, hogy Azure ML munkaterület kísérletek futtatásához rendelkeznie kell egy olyan kísérletezhet fiókot. 


- **Modellhez tartozó felügyeleti fiókja** egy modell felügyeleti fiókja is egy Azure-modellek kezelése az Azure ML által igényelt erőforrás. Használhatja modellek és jegyzékfájlokban, indexelése webszolgáltatások létrehozása és központi telepítésére, helyileg vagy a felhőben. Azure ML más számlázási vehicle egyben.


- **Munkaterület:** munkaterület összetevő az elsődleges megosztására és az Azure ml együttműködés. Projektek munkaterület belül vannak csoportosítva. A munkaterület aztán megoszthat a kísérleti fiókhoz hozzáadott több felhasználóval rendelkező.


- **A projekt:** az Azure Machine Learning, a projekt az összes a végrehajtás alatt egy probléma megoldására logikai tárolója. Egyetlen, a helyi lemezen lévő mappára vannak leképezve, és tetszőleges számú fájl vagy almappa adható hozzájuk. A projekt is lehet társítva van egy Git-tárház verziókezelő és együttműködés.  

- **Kísérlet:** Azure ml, egy kísérlet egy vagy több forrás kódot (oka) t, amely egy-egy belépési pont hajthatók végre. Feladatokhoz, mint az adatfeldolgozást, a szolgáltatás mérnöki csapathoz, a tanítási modell vagy a modell kiértékelése tartalmazhat. Jelenleg Azure ML támogatja a Python, vagy csak a PySpark kísérleteket.


- **Modell:** az Azure Machine Learning modellek tartalmazó gépi tanulási kísérlet termékre vonatkoznak. -E receptet, hogy helyesen alkalmazni adatokat, ha előre jelzett értékek generálásához. Modellek lehet teszt- vagy éles környezetben telepített, és új adatok pontozása használják. Egyszer, éles környezetben modellek figyelhetők a teljesítmény- és adatok eltéréseket, és szükség szerint retrained. 

- **Számítási cél:** egy számítási célja a számítási erőforrással határozzák meg a kísérletek végrehajtása. Lehet, hogy a helyi számítógép (Windows vagy macOS), a helyi számítógépen vagy a Linux virtuális gép futó Azure vagy egy HDInsight Spark-fürt Docker-tároló.


- **Futtatás:** a kísérleti szolgáltatás futtató számítási célja egy kísérlet végrehajtás élettartamát határozza meg. Az Azure ML minden egyes információk automatikusan rögzíti, és egy adott kísérlet futtatási előzményei formájában teljes előzményeit mutatja be.

- **Környezet:** az Azure Machine Learning egy olyan környezetben egy adott számítási erőforrás központi telepítésére és felügyeletére a modellek használt jelöli. Lehet, hogy a helyi számítógépen, az Azure-on Linux virtuális gép vagy az Azure Tárolószolgáltatásban, attól függően, hogy a környezet és a konfigurációs rendszert futtató Kubernetes fürtre. A modell egy Docker-tároló futtató ezekben a környezetekben üzemeltetett és a REST API-végpont elérhető.


- **Felügyelt modell:** modell kezelése lehetővé teszi modellek webszolgáltatásként telepítése, a modellek különböző verzióinak kezelése és a Teljesítményfigyelő és metrikákat. Az Azure Machine Learning modell felügyeleti fiók felügyelt modellek vannak regisztrálva.

- **Jegyzékfájlokban:** a modell rendszer modell éles környezetben telepíti, ha a jegyzékfájl, amely képes névtérkiszolgálóit modell, a függőségeket, a pontozási parancsfájl, a mintaadatok és a séma tartalmazza. A jegyzékfájl egy Docker-tároló lemezkép létrehozásához használt módszereivel. Modell eszköz használatával automatikusan előállítja a jegyzékfájlban, különböző verziói létrehozása és kezelése a jegyzékfájlban. 


- **Képek:** jegyzékfájlokban segítségével létre (és újbóli létrehozása) Docker-lemezképeket. Indexelése Docker-lemezképek létrehozása a felhőben, a helyi gépen, vagy IoT-eszközök futtatásához a rugalmasságot. Lemezképek, és modellek új adatok pontozása szükséges összes függőséget tartalmaz. 

- **Szolgáltatások:** modell kezelése lehetővé teszi a modellek webszolgáltatásként telepítését. A webservice programot, és a függőségek lemezkép be vannak ágyazva. Minden webszolgáltatás olyan tároló, a lemezkép készen áll szolgáltatási kérelmek egy adott URL-címe alapján. A webszolgáltatás egy központi telepítésnél számítanak.
