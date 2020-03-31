---
title: A & az írási kísérletfájljainak mentési helye
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy hová mentheti a kísérlet bemeneti fájljait, és hol írhat kimeneti fájlokat a tárolási korlátozási hibák és a kísérlet késésének megelőzése érdekében.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078431"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Hol lehet fájlokat menteni és írni az Azure Machine Learning-kísérletekhez?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogy hová kell menteni a bemeneti fájlokat, és hol kell írni a kimeneti fájlokat a kísérletekből a tárolási korlát hibák és a kísérlet késésének megelőzése érdekében.

A [betanításindításakor egy számítási cél,](how-to-set-up-training-targets.md)el vannak különítve a külső környezetekben. E terv célja a kísérlet reprodukálhatóságának és hordozhatóságának biztosítása. Ha ugyanazt a parancsfájlt kétszer futtatja, ugyanazon vagy egy másik számítási célon, ugyanazokat az eredményeket kapja. Ezzel a kialakítással állapot nélküli számítási erőforrásokként kezelheti a számítási célokat, amelyek mindegyike nem kapcsolódik a befejezésután futó feladatokhoz.

## <a name="where-to-save-input-files"></a>A bemeneti fájlok mentésének helye

Mielőtt kísérletet kezdeményezhetne egy számítási célon vagy a helyi gépen, gondoskodnia kell arról, hogy a szükséges fájlok elérhetők legyenek az adott számítási cél számára, például a függőségi fájlok és az adatfájlok, amelyeket a kódnak futtatnia kell.

Az Azure Machine Learning a teljes parancsfájl-mappa másolásával futtatja a képzési parancsfájlokat a célszámítási környezetbe, majd pillanatképet készít. A kísérletek pillanatképeinél a tárhelykorlát 300 MB és/vagy 2000 fájl.

Ezért a következőket ajánljuk:

* **Fájlok tárolása egy Azure Machine [Learning-adattárban.](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)** Ez megakadályozza a kísérlet késési problémák, és az okkal rendelkezik az adatok elérése egy távoli számítási cél, ami azt jelenti, hitelesítés és szerelés az Azure Machine Learning által kezelt. További információ arról, hogy miként adhat meg egy adattárat forráskönyvtárként, és fájlokat tölthet fel az adattárba az [adattárból származó Access-adatok című](how-to-access-data.md) cikkben.

* **Ha csak néhány adatfájlra és függőségi parancsfájlra van szüksége, és nem tudja használni az adattárat,** helyezze a fájlokat ugyanabba a mappába, mint a betanítási parancsfájl. Adja meg ezt `source_directory` a mappát közvetlenül a betanítási parancsfájlban, vagy a betanítási parancsfájlt meghívja kódot.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>A kísérletpillanatképek tárolási korlátai

A kísérletek, az Azure Machine Learning automatikusan elkészíti a kísérlet pillanatképét a kód alapján a könyvtárat, amelyet a futtatás konfigurálásakor. Ez a maximális korlát 300 MB és / vagy 2000 fájlokat. Ha túllépi ezt a korlátot, a következő hibaüzenet jelenik meg:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

A hiba megoldásához tárolja a kísérleti fájlokat egy adattárban. Ha nem tud használni egy adattár, az alábbi táblázat ban lehetséges alternatív megoldásokat kínál.

Kísérlet&nbsp;leírása|Tárolási korlát megoldás
---|---
Kevesebb, mint 2000 fájl, & nem tudnak adattancentrikust használni| Pillanatképméret-korlát felülbírálása <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Ez a fájlok számától és méretétől függően több percet is igénybe vehet.
Adott parancsfájlkönyvtárat kell használnia| Készítsen `.amlignore` olyan fájlt, amely kizárja azokat a fájlokat a kísérlet pillanatképéből, amelyek nem részei a forráskódnak. Adja hozzá a `.amlignore` fájlneveket a fájlhoz, és helyezze a betanítási parancsfájllal azonos könyvtárba. A `.amlignore` fájl ugyanazt a [szintaxist és mintázatot](https://git-scm.com/docs/gitignore) használja, mint a `.gitignore` fájl.
Folyamat|Minden lépéshez használjon másik alkönyvtárat
Jupyter-notebookok| Hozzon `.amlignore` létre egy fájlt, vagy helyezze át a jegyzetfüzetet egy új, üres alkönyvtárba, és futtassa újra a kódot.

## <a name="where-to-write-files"></a>Hol írjon fájlokat

A betanítási kísérletek elkülönítése miatt a futtatások során végrehajtott fájlok módosításai nem feltétlenül maradnak meg a környezeten kívül. Ha a parancsfájl módosítja a helyi számítási fájlokat, a módosítások nem maradnak meg a következő kísérlet futtatásakor, és nem lesznek automatikusan propagálva az ügyfélgépre. Ezért az első kísérlet során végrehajtott módosítások nem érintik és nem is befolyásolhatják a második ban végrehajtottakat.

A módosítások írásakor azt javasoljuk, hogy fájlokat írjon egy Azure Machine Learning-adattárba. Lásd: [Access-adatok az adattárakból.](how-to-access-data.md)

Ha nincs szüksége adattárra, írjon `./outputs` fájlokat `./logs` az és/vagy mappába.

>[!Important]
> Két mappa, *kimenetek* és *naplók,* különleges bánásmódban részesülnek az Azure Machine Learning. A betanítás során,`./outputs` amikor`./logs` fájlokat ír a mappákba és mappákba, a fájlok automatikusan feltöltődnek a futtatási előzményekbe, így a futtatás befejezése után hozzáférhet hozzájuk.

* **Kimenet, például állapotüzenetek vagy pontozási eredmények** `./outputs` esetén írjon fájlokat a mappába, így a futtatási előzményekben összetevőkként maradnak meg. Vegye figyelembe a mappába írt fájlok számát és méretét, mivel a tartalom feltöltésekor késés léphet fel az előzmények futtatásához. Ha a késés aggodalomra ad okot, a fájlok írása egy adattárba ajánlott.

* **Ha az írott fájlt naplóként szeretné menteni a futtatási előzményekben,** írjon fájlokat a mappába. `./logs` A naplók feltöltése valós időben történik, így ez a módszer alkalmas élő frissítések távoli futtatásból történő streamelésére.

## <a name="next-steps"></a>További lépések

* További információ az [adattárakból származó adatok eléréséről.](how-to-access-data.md)

* További információ [a képzési célok beállításáról.](how-to-set-up-training-targets.md)
