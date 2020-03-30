---
title: Webszolgáltatás-paraméterek – Azure Machine Learning Studio (klasszikus) | Microsoft dokumentumok
description: Az Azure Machine Learning webszolgáltatás paramétereinek használata a modell viselkedésének módosításához a webszolgáltatás elérésekor.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204409"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Az Azure Machine Learning Studio (klasszikus) webszolgáltatás-paramétereinek használata

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning webszolgáltatás egy konfigurálható paraméterekkel rendelkező modulokat tartalmazó kísérlet közzétételével jön létre. Bizonyos esetekben előfordulhat, hogy módosítani szeretné a modul viselkedését, miközben a webszolgáltatás fut. *A webszolgáltatás paraméterei* lehetővé teszik a feladat elvégzését. 

Gyakori példa az [Adatok importálása][reader] modul beállítása, hogy a közzétett webszolgáltatás felhasználója másik adatforrást adjon meg a webszolgáltatás elérésekor. Vagy konfigurálja az [Adatok exportálása][writer] modult, hogy egy másik cél megadható. További példák például a [funkciókivonat-kivonatoló][feature-hashing] modul biteinek számának módosítása vagy a [szűrőalapú szolgáltatásválasztó][filter-based-feature-selection] modul kívánt szolgáltatásainak száma. 

Beállíthatja a webszolgáltatás paramétereit, és társíthatja őket egy vagy több modulparaméterrel a kísérletben, és megadhatja, hogy kötelezőek-e vagy sem. A webszolgáltatás felhasználója ezután értékeket adhat meg ezekhez a paraméterekhez, amikor a webszolgáltatást hívják. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Webszolgáltatás-paraméterek beállítása és használata
A webszolgáltatás-paramétert úgy határozhatja meg, hogy a modul paramétere melletti ikonra kattint, és a "Beállítás webszolgáltatás-paraméterként" lehetőséget választja. Ez létrehoz egy új webszolgáltatás-paramétert, és csatlakoztatja azt a modulparaméterhez. Ezután a webszolgáltatás elérésekor a felhasználó megadhatja a webszolgáltatás-paraméter értékét, és alkalmazza azt a modulparaméterre.

Miután definiálta a webszolgáltatás-paramétert, az elérhető vé válik a kísérlet bármely más modulparamétere számára. Ha egy modul paraméteréhez társított webszolgáltatás-paramétert ad meg, akkor ugyanazt a webszolgáltatás-paramétert használhatja bármely más modulhoz, feltéve, hogy a paraméter ugyanazt az értéket várja. Ha például a Webszolgáltatás paramétere egy numerikus érték, akkor csak olyan modulparaméterekhez használható, amelyek numerikus értéket várnak. Amikor a felhasználó beállít egy értéket a WebService Paraméterhez, a rendszer az összes társított modulparaméterre alkalmazza.

Eldöntheti, hogy megad-e alapértelmezett értéket a webszolgáltatás-paraméterhez. Ha igen, akkor a paraméter nem kötelező a felhasználó számára a webszolgáltatás. Ha nem ad meg alapértelmezett értéket, akkor a felhasználónak meg kell adnia egy értéket a webszolgáltatás elérésekor.

A webszolgáltatás API-dokumentációja információkat tartalmaz a webszolgáltatás felhasználója számára arról, hogyan adja meg a webszolgáltatás paraméterét programozott módon a webszolgáltatás elérésekor.

> [!NOTE]
> A klasszikus webszolgáltatás API-dokumentációját a Machine Learning Studio (klasszikus) **irányítópultjának** **irányítópultja API-súgóhivatkozása** biztosítja. Az új webszolgáltatás API-dokumentációját az [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) portálon keresztül biztosítják a **felhasználás** és a **Swagger API-lapok** on your web service.
> 
> 

## <a name="example"></a>Példa
Tegyük fel például, hogy van egy kísérletünk egy [Adatexportálási][writer] modullal, amely adatokat küld az Azure blob storage-ba. Definiálunk egy "Blob elérési út" nevű webszolgáltatás-paramétert, amely lehetővé teszi a webszolgáltatás felhasználója számára, hogy a szolgáltatás elérésekor módosítsa a blobstorage elérési útját.

1. A Machine Learning Studio (klasszikus) alkalmazásban kattintson az [Adatok exportálása][writer] modulra a kijelöléséhez. Tulajdonságai a kísérletvászon tól jobbra lévő Tulajdonságok ablaktáblában jelennek meg.
2. Adja meg a tároló típusát:
   
   * A **Kérjük, adja meg az adatcél lehetőséget,** válassza az "Azure Blob Storage" lehetőséget.
   * A **Kérjük, adja meg a hitelesítés típusát,** válassza a "Fiók" lehetőséget.
   * Adja meg az Azure blob storage fiókadatait. 

3. Kattintson a **tárolóparaméterrel kezdődő elérési út**jobb oldalán található ikonra. A következőképpen néz ki:
   
   ![A Webszolgáltatás paramétere ikon](./media/web-service-parameters/icon.png)
   
   Válassza a "Beállítás webszolgáltatás-paraméterként" lehetőséget.
   
   A tulajdonságtábla alatt található **Webszolgáltatás-paraméterek** csoportban egy bejegyzés kerül hozzáadásra a "Tárolóval kezdődő elérési út a blobhoz" névvel. Ez az a webszolgáltatás-paraméter, amely most ehhez az [Adatexportálás][writer] modul paraméterhez van társítva.
4. A webszolgáltatás paraméterének átnevezéséhez kattintson a névre, írja be a "Blob elérési út" kifejezést, és nyomja le az **Enter billentyűt.** 
5. A Webszolgáltatás paraméter alapértelmezett értékének megadásához kattintson a névtől jobbra lévő ikonra, válassza az "Alapértelmezett érték megadása" lehetőséget, adjon meg egy értéket (például "container1/output1.csv"), és nyomja le az **Enter billentyűt.**
   
   ![Webszolgáltatás paramétere](./media/web-service-parameters/parameter.png)
6. Kattintson a **Futtatás gombra.** 
7. Kattintson **a Webszolgáltatás telepítése gombra,** és válassza **a Webszolgáltatás [Klasszikus] telepítése** vagy az **[Új] webszolgáltatás telepítése lehetőséget** a webszolgáltatás központi telepítéséhez.

> [!NOTE] 
> Új webszolgáltatás üzembe helyezéséhez megfelelő engedélyekkel kell rendelkeznie abban az előfizetésben, amelyre a webszolgáltatást telepítette. További információt a [Webszolgáltatás kezelése az Azure Machine Learning WebServices portálon című témakörben talál.](manage-new-webservice.md) 

A webszolgáltatás felhasználója most már megadhat egy új célt az [Adatok exportálása][writer] modulhoz a webszolgáltatás elérésekor.

## <a name="more-information"></a>További információ
Részletesebb példát a Machine Learning blog [webszolgáltatás-paraméterek](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) című bejegyzésében [talál.](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)

A Machine Learning webszolgáltatások eléréséről az [Azure Machine Learning webszolgáltatás felhasználása](consume-web-services.md)című témakörben talál további információt.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

