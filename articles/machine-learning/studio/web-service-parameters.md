---
title: Webszolgáltatás paraméterei
titleSuffix: ML Studio (classic) - Azure
description: A Azure Machine Learning webszolgáltatás paramétereinek használata a modell működésének módosításához a webszolgáltatáshoz való hozzáféréskor.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: c274579e756b3c22920023d68501981d5b4a8b01
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84705865"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás paramétereinek használata

A Azure Machine Learning webszolgáltatás egy olyan kísérlet közzétételével jön létre, amely konfigurálható paraméterekkel rendelkező modulokat tartalmaz. Bizonyos esetekben érdemes lehet módosítani a modul működését, amíg a webszolgáltatás fut. A *webszolgáltatás paraméterei* lehetővé teszik a feladat elvégzését. 

Gyakori példa az [adatimportálási][reader] modul beállítása, hogy a közzétett webszolgáltatás felhasználója más adatforrást is megadhat a webszolgáltatáshoz való hozzáféréskor. Vagy konfigurálja az [adatexportálási][writer] modult úgy, hogy más célhely is megadható legyen. Néhány példa a [funkció-kivonatolási][feature-hashing] modulhoz tartozó bitek számának módosítására, illetve a [Filter-alapú funkciók kiválasztási][filter-based-feature-selection] moduljának kívánt funkcióinak megváltoztatására. 

Beállíthatja a webszolgáltatás paramétereit, és társíthatja azokat egy vagy több modul-paraméterrel a kísérletben, és megadhatja, hogy kötelező vagy opcionális-e. A webszolgáltatás felhasználója ezután megadhatja a paraméterek értékeit, amikor meghívja a webszolgáltatást. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Webszolgáltatás paramétereinek beállítása és használata
A webszolgáltatás paramétereit úgy definiálhatja, hogy rákattint a paraméter melletti ikonra a modulhoz, és kiválasztja a "Set as Web Service paraméter" lehetőséget. Ez létrehoz egy új webszolgáltatás-paramétert, és csatlakoztatja azt a modul paraméteréhez. Ezt követően a webszolgáltatáshoz való hozzáféréskor a felhasználó megadhatja a webszolgáltatás paraméter értékét, és a modul paraméterére lesz alkalmazva.

Ha meghatároz egy webszolgáltatás-paramétert, a kísérletben elérhető bármely más modul paramétere. Ha egy modulhoz egy paraméterhez társított webszolgáltatás-paramétert ad meg, akkor ugyanazt a webszolgáltatás-paramétert használhatja bármely más modulhoz, feltéve, hogy a paraméter ugyanolyan típusú értéket vár. Ha például a webszolgáltatás paraméter egy numerikus érték, akkor csak numerikus értéket várható modul-paraméterekhez használhatja. Ha a felhasználó beállítja a webszolgáltatás paraméter értékét, a rendszer az összes társított modul paraméterére alkalmazza.

Eldöntheti, hogy megadhat-e alapértelmezett értéket a webszolgáltatás paraméterhez. Ha így tesz, a paraméter nem kötelező a webszolgáltatás felhasználója számára. Ha nem ad meg alapértelmezett értéket, a felhasználónak értéket kell megadnia a webszolgáltatás elérésekor.

A webszolgáltatás API-dokumentációja a webszolgáltatás azon felhasználója számára nyújt információt, amely a webszolgáltatás paraméterének programozott módon való megadását mutatja be a webszolgáltatáshoz való hozzáférés során.

> [!NOTE]
> A klasszikus webszolgáltatások API-dokumentációja az Machine Learning Studio (klasszikus) webszolgáltatási **irányítópultján** található **API-Súgó oldal** hivatkozásán keresztül érhető el. Az új webszolgáltatások API-dokumentációja a webszolgáltatások felhasználására **és** kivágására szolgáló **API** -lapokon elérhető [Azure Machine learning webszolgáltatások](https://services.azureml.net/Quickstart) portálján keresztül érhető el.
> 
> 

## <a name="example"></a>Példa
Tegyük fel például, hogy egy olyan [adatexportálási][writer] modullal kísérletezünk, amely adatokat küld az Azure Blob Storage szolgáltatásnak. Definiálunk egy "blob Path" nevű webszolgáltatás-paramétert, amely lehetővé teszi, hogy a webszolgáltatás-felhasználó módosítsa a blob Storage elérési útját a szolgáltatás elérésekor.

1. A Machine Learning Studio (klasszikus) területen kattintson az [adatexportálás][writer] modulra a kiválasztásához. A tulajdonságok a kísérlet vászontól jobbra látható Tulajdonságok ablaktáblán jelennek meg.
2. Adja meg a tárolási típust:
   
   * Az adja **meg az adat célhelyét**területen válassza az "Azure Blob Storage" lehetőséget.
   * A **kérjük, adja meg a hitelesítés típusát**területen válassza a "fiók" lehetőséget.
   * Adja meg az Azure Blob Storage-fiók adatait. 

3. Kattintson az elérési út jobb oldalán lévő ikonra a **Container paraméterrel kezdődő blobra**. A következőképpen néz ki:
   
   ![Webszolgáltatás paraméterének ikonja](./media/web-service-parameters/icon.png)
   
   Válassza a "Set as Web Service paraméter" lehetőséget.
   
   A Tulajdonságok ablaktábla alján található **webszolgáltatás-paraméterek** alatt egy bejegyzést adnak hozzá, amelynek a neve "a tárolótól kezdve a blobhoz" nevű elérési út. Ez az a webszolgáltatás-paraméter, amely most már társítva van ehhez az [adatexportálási][writer] modul-paraméterhez.
4. A webszolgáltatás paraméterének átnevezéséhez kattintson a névre, írja be a "blob elérési útja" kifejezést, majd nyomja le az **ENTER** billentyűt. 
5. A webszolgáltatás paraméter alapértelmezett értékének megadásához kattintson a név jobb oldalán látható ikonra, válassza az "alapértelmezett érték megadása" lehetőséget, adjon meg egy értéket (például "container1/output1.csv"), és nyomja le az **ENTER** billentyűt.
   
   ![Webszolgáltatás paraméter](./media/web-service-parameters/parameter.png)
6. Kattintson a **Futtatás**gombra. 
7. Kattintson a webszolgáltatás **telepítése** lehetőségre, és válassza a **webszolgáltatás telepítése [klasszikus]** vagy a **webszolgáltatás telepítése [új]** lehetőséget a webszolgáltatás telepítéséhez.

> [!NOTE] 
> Új webszolgáltatás telepítéséhez megfelelő engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a webszolgáltatást telepíti. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md). 

A webszolgáltatás felhasználója mostantól megadhat egy új célhelyet az [adatexportálási][writer] modulhoz, amikor a webszolgáltatáshoz fér hozzá.

## <a name="more-information"></a>További információ
Részletesebb példaként tekintse meg a [webszolgáltatási paraméterek](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) bejegyzést a [Machine learning blogban](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

A Machine Learning webszolgáltatások elérésével kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás](consume-web-services.md)használata.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

