---
title: Adatimportálás a tervezőbe
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan importálhat különböző adatforrásokból származó adatok Azure Machine Learning designerbe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 15fca48327c46480546764be1b2ab40c1635e874
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985612"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Az adatimportálás Azure Machine Learning designerbe

Ebből a cikkből megtudhatja, hogyan importálhat saját adatait a Designerben egyéni megoldások létrehozásához. Az adatgyűjtés kétféleképpen importálható a tervezőbe: 

* **Azure Machine learning adatkészletek** – [adatkészletek](concept-data.md#datasets) regisztrálása a Azure Machine Learningban az adatkezelést segítő speciális funkciók engedélyezéséhez.
* **Adatmodul importálása** – az adatok [importálása](algorithm-module-reference/import-data.md) modul használatával közvetlenül férhet hozzá az online adatforrásokból származó adatokhoz.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning adatkészletek használata

Azt javasoljuk, hogy [adatkészletek](concept-data.md#datasets) használatával importálja az adatokat a tervezőbe. Amikor regisztrál egy adatkészletet, teljes mértékben kihasználhatja a fejlett adatszolgáltatások előnyeit, például a [verziószámozást és a nyomon követést](how-to-version-track-datasets.md) és [az adatfigyelést](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Adatkészlet regisztrálása

A meglévő adatkészleteket [programozott módon az SDK-val,](how-to-create-register-datasets.md#datasets-sdk) vagy [vizuálisan Azure Machine learning Studióban](how-to-connect-data-ui.md#create-datasets)is.

Bármely tervező modul kimenetét adatkészletként is regisztrálhatja.

1. Válassza ki azt a modult, amely a regisztrálni kívánt adatokat kiírja.

1. A Tulajdonságok ablaktáblán válassza a **kimenetek + naplók**  >  **regisztrálása adatkészletet**.

    ![Az adatkészlet regisztrálása lehetőségre való váltást bemutató képernyőkép](media/how-to-designer-import-data/register-dataset-designer.png)

Ha a modul kimeneti adatokat táblázatos formátumban adja meg, akkor a kimenetet **fájl-adatkészlet** vagy **táblázatos adatkészletként**kell regisztrálnia.

 - A **fájl adatkészlete** fájl adatkészletként regisztrálja a modul kimeneti mappáját. A kimeneti mappa tartalmaz egy adatfájlt és egy olyan metataget, amelyet a Designer belsőleg használ. Válassza ezt a lehetőséget, ha továbbra is használni szeretné a regisztrált adatkészletet a tervezőben. 

 - A **táblázatos adatkészlet** csak a modul kimeneti adatfájlját regisztrálja táblázatos adatkészletként. Ezt a formátumot más eszközök is könnyen felhasználják, például automatizált Machine Learning vagy a Python SDK-ban. Akkor válassza ezt a lehetőséget, ha a regisztrált adatkészletet a tervezőn kívül szeretné használni.  



### <a name="use-a-dataset"></a>Adatkészlet használata

A regisztrált adatkészletek a modul palettáján, az **adatkészletek**területen találhatók. Adatkészlet használatához húzza azt a folyamat vászonra. Ezután kapcsolja össze az adatkészlet kimeneti portját a vászon más moduljaival. 

![A mentett adatkészletek helyét bemutató képernyőkép a tervező palettán](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> A tervező támogatja az [adatkészlet verziószámozását](how-to-version-track-datasets.md). Határozza meg az adatkészlet verzióját az adatkészlet moduljának tulajdonságok paneljén.


## <a name="import-data-using-the-import-data-module"></a>Adatimportálás az Adatimportálási modul használatával

Habár azt javasoljuk, hogy adatkészletek használatával importálja az adatokat, az [adatimportálás](algorithm-module-reference/import-data.md) modult is használhatja. Az Adatimportálási modul kihagyja az adathalmaz regisztrálását Azure Machine Learning és közvetlenül egy [adattárból](concept-data.md#datastores) vagy http URL-címről importálja az adatokat.

Az adatok importálása modul használatával kapcsolatos részletes információkért lásd az [adatok importálása – hivatkozás lapot](algorithm-module-reference/import-data.md).

> [!NOTE]
> Ha az adatkészlet túl sok oszlopot tartalmaz, a következő hibaüzenet jelenhet meg: "az érvényesítés a méret korlátozása miatt meghiúsult". Ennek elkerüléséhez [regisztrálja az adatkészletet az adatkészletek kezelőfelületén](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Támogatott források

Ez a szakasz a tervező által támogatott adatforrásokat sorolja fel. Az adatok egy adattárból vagy egy [táblázatos adatkészletből](how-to-create-register-datasets.md#dataset-types)származnak a tervezőbe.

### <a name="datastore-sources"></a>Adattár-források
A támogatott adattár-források listáját lásd: az [Azure Storage-szolgáltatásokban tárolt adathozzáférés](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Táblázatos adatkészlet forrásai

A Designer a következő forrásokból létrehozott táblázatos adatkészleteket támogatja:
 * Tagolt fájlok
 * JSON-fájlok
 * Parquet-fájlok
 * SQL-lekérdezések

## <a name="data-types"></a>Adattípusok

A tervező belsőleg felismeri a következő adattípusokat:

* Sztring
* Egész szám
* Tizedesjegy
* Logikai
* Dátum

A tervező egy belső adattípust használ a modulok közötti adatátvitelhez. Az adatokat adattábla formátumba explicit módon konvertálhatja az [átalakítás adatkészletbe](algorithm-module-reference/convert-to-dataset.md) modul használatával. Minden olyan modul, amely a belső formátumtól eltérő formátumot fogad el, a következő modulba való továbbítás előtt visszafogja az adatátalakítást.

## <a name="data-constraints"></a>Adatkorlátozások

A tervezőben lévő modulokat a számítási cél mérete korlátozza. Nagyobb adatkészletek esetén nagyobb Azure Machine Learning számítási erőforrást kell használnia. További információ a Azure Machine Learning számítási feladatokról: [Mik a számítási célok a Azure Machine Learningban?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Virtuális hálózatban tárolt adathozzáférés

Ha a munkaterület virtuális hálózatban található, további konfigurációs lépéseket kell végrehajtania a tervezőben lévő adatok megjelenítéséhez. További információ az adattárolók és adatkészletek virtuális hálózatban való használatáról: [Azure Machine learning Studio használata Azure-beli virtuális hálózaton](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megismerheti a tervezői alapismereteket [: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md).
