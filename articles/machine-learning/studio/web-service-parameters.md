---
title: Webszolgáltatás-paraméterek – Azure Machine Learning Studio (klasszikus) | Microsoft Docs
description: Hogyan használható az Azure Machine Learning webszolgáltatás-paraméterek a web service elérésekor a modell működésének módosítása céljából.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204409"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás paramétereinek használata

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning webszolgáltatás tegye közzé konfigurálható paraméterek és modulokat tartalmazó kísérlet jön létre. Bizonyos esetekben előfordulhat, hogy módosítani szeretné a modul viselkedését a webszolgáltatás futtatása közben. A *webszolgáltatás paraméterei* lehetővé teszik a feladat elvégzését. 

Gyakori példa az [adatimportálási][reader] modul beállítása, hogy a közzétett webszolgáltatás felhasználója más adatforrást is megadhat a webszolgáltatáshoz való hozzáféréskor. Vagy konfigurálja az [adatexportálási][writer] modult úgy, hogy más célhely is megadható legyen. Néhány példa a [funkció-kivonatolási][feature-hashing] modulhoz tartozó bitek számának módosítására, illetve a [Filter-alapú funkciók kiválasztási][filter-based-feature-selection] moduljának kívánt funkcióinak megváltoztatására. 

Állítsa be a webszolgáltatás-paraméterek, és rendelje azokat egy vagy több modulja paraméter a kísérlet során, és megadhatja, hogy azok a szükséges és választható. A felhasználó a webszolgáltatás majd megadhat értékeket a paraméterekhez, a webszolgáltatás hívásakor. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Állítsa be, és webszolgáltatás-paraméterek használata
A paraméter egy modul melletti ikonra kattintva, majd válassza a "Webes szolgáltatás paraméter beállítása" meghatározhat egy webes szolgáltatás paramétert. Ez létrehoz egy új webes szolgáltatás paramétert, és csatlakoztatja, hogy a modul paraméter. Ezután a web service érhető el, amikor a felhasználó egyik értékét adhatja meg a Web Service paraméterhez, és a modul paramétert alkalmazza a rendszer.

Miután meghatározott egy webes szolgáltatás paraméter, érhető el a kísérletben bármely más modul paraméteréhez. Egy olyan paramétert egy modul társított webszolgáltatási paraméter határozza meg, ha használható a webszolgáltatási paraméter bármely egyéb modult, mindaddig, amíg a paraméter a ugyanolyan típusú értéket vár. Például ha a Web Service paraméter egy numerikus értéket, majd csak használat modul paraméterek egy numerikus értéket várt. A felhasználó beállít egy értéket a Web Service paraméter, akkor lépnek érvénybe az összes társított modul paraméterei.

Eldöntheti, hogy adja meg a Web Service paraméter alapértelmezett értékét. Ha így tesz, a paraméter megadása esetén a felhasználó a webszolgáltatás nem kötelező. Ha nem ad meg alapértelmezett értéket, majd a felhasználó szükséges adjon meg egy értéket, ha a webszolgáltatás.

A webszolgáltatás API-dokumentáció programozott módon megadása a Web Service paraméter, a web service elérésekor a szolgáltatás felhasználó információkat biztosít.

> [!NOTE]
> A klasszikus webszolgáltatások API-dokumentációja az Machine Learning Studio (klasszikus) webszolgáltatási **irányítópultján** található **API-Súgó oldal** hivatkozásán keresztül érhető el. Az új webszolgáltatások API-dokumentációja a webszolgáltatások felhasználására **és** kivágására szolgáló **API** -lapokon elérhető [Azure Machine learning webszolgáltatások](https://services.azureml.net/Quickstart) portálján keresztül érhető el.
> 
> 

## <a name="example"></a>Példa
Tegyük fel például, hogy egy olyan [adatexportálási][writer] modullal kísérletezünk, amely adatokat küld az Azure Blob Storage szolgáltatásnak. Fogunk meghatározni a "Blob elérési útja" nevű Web Service paraméter, amely lehetővé teszi, hogy a szolgáltatás felhasználó elérési útjának módosítása a blob Storage, a szolgáltatás eléréséhez.

1. A Machine Learning Studio (klasszikus) területen kattintson az [adatexportálás][writer] modulra a kiválasztásához. A tulajdonságainak jobb oldalán a kísérletvászonra a Tulajdonságok panelen látható.
2. Adja meg a tárolás típusát:
   
   * Az adja **meg az adat célhelyét**területen válassza az "Azure Blob Storage" lehetőséget.
   * A **kérjük, adja meg a hitelesítés típusát**területen válassza a "fiók" lehetőséget.
   * Írja be a fiók adatait az Azure blob storage. 

3. Kattintson az elérési út jobb oldalán lévő ikonra a **Container paraméterrel kezdődő blobra**. Ez a következőhöz hasonló:
   
   ![Webes szolgáltatás paraméter ikon](./media/web-service-parameters/icon.png)
   
   Válassza ki a "Webes szolgáltatás paraméter beállítása".
   
   A Tulajdonságok ablaktábla alján található **webszolgáltatás-paraméterek** alatt egy bejegyzést adnak hozzá, amelynek a neve "a tárolótól kezdve a blobhoz" nevű elérési út. Ez az a webszolgáltatás-paraméter, amely most már társítva van ehhez az [adatexportálási][writer] modul-paraméterhez.
4. A webszolgáltatás paraméterének átnevezéséhez kattintson a névre, írja be a "blob elérési útja" kifejezést, majd nyomja le az **ENTER** billentyűt. 
5. A webszolgáltatás paraméter alapértelmezett értékének megadásához kattintson a név jobb oldalán látható ikonra, válassza az "alapértelmezett érték megadása" lehetőséget, adjon meg egy értéket (például "container1/output1. csv"), és nyomja le az **ENTER** billentyűt.
   
   ![Webszolgáltatási paraméter](./media/web-service-parameters/parameter.png)
6. Kattintson a **Run** (Futtatás) parancsra. 
7. Kattintson a webszolgáltatás **telepítése** lehetőségre, és válassza a **webszolgáltatás telepítése [klasszikus]** vagy a **webszolgáltatás telepítése [új]** lehetőséget a webszolgáltatás telepítéséhez.

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md). 

A webszolgáltatás felhasználója mostantól megadhat egy új célhelyet az [adatexportálási][writer] modulhoz, amikor a webszolgáltatáshoz fér hozzá.

## <a name="more-information"></a>További információ
Részletesebb példaként tekintse meg a [webszolgáltatási paraméterek](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) bejegyzést a [Machine learning blogban](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

A Machine Learning webszolgáltatások elérésével kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás](consume-web-services.md)használata.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

