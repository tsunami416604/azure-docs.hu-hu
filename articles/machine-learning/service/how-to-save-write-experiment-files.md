---
title: Megakadályozza a storage korlátozásaival, és kísérletezzen a bemeneti és kimeneti könyvtárak késés
description: Ebből a cikkből megtudhatja, hol kísérlet bemeneti fájlok mentése, és a kimeneti fájlok tárolási korlátozás hibák megelőzése, és kísérletezzen a késés helyének.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 1f9199b5bae0c82cd46750d8ef5522a0d3579671
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595284"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Amennyiben mentéséhez és írhat fájlokat az Azure Machine Learning kísérleteket

Ebből a cikkből megtudhatja, hová szeretné menteni a bemeneti fájlokat, és hol lehet írni a kimeneti fájlok tárolására elkerülése érdekében a kísérletekből korlátozza a hibákat, és kísérletezzen a késés.

Képzési indítása futtatásakor egy [számítási célt](how-to-set-up-training-targets.md), azok el különítve a külső környezetekben. Ez a kialakítás célja annak biztosítása érdekében megismételhetősége és hordozhatóság a kísérlet. Ha ugyanezt a szkriptet kétszer futtatja, az ugyanazon vagy másik számítási célt, ugyanazt az eredményt kapja. Ezzel a kialakítással affinitás nélküli után befejezi a futó feladatokat, hogy az állapot nélküli számítási erőforrásként számítási célnak is kezelheti.

## <a name="where-to-save-input-files"></a>Bemeneti fájlok mentésének helyét

Előtt is végrehajthatja, kísérlet egy számítási célnak vagy a helyi gépen, győződjön meg róla, hogy elérhetők-e a szükséges fájlokat a számítási célnak, például a függőségi fájlokról és az adatfájlokat a kódot kell futtatni.

Az Azure Machine Learning betanítási szkriptekhez fut a teljes parancsfájl mappa másolása a cél számítási környezetet, és ezután egy pillanatképet. A kísérlet a pillanatképek tárolási korlátot 300 MB és/vagy 2000 fájlok.

Ebből kifolyólag javasoljuk:

* **A fájlok tárolására az Azure Machine Learning [adattárolója](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Ez megakadályozza, hogy a kísérletben késési problémák, és az előnnyel jár, egy távoli számítási célt, ami azt jelenti, hogy a hitelesítés és a csatlakoztatáshoz szükséges Azure Machine Learning szolgáltatás által felügyelt származó adatok egységes elérésével. További információ egy adattár-t a forráskönyvtár adja meg, és fájlokat tölthet fel az adattárhoz, az a [érheti el adatait a adattárainak](how-to-access-data.md) cikk.

* **Ha csak néhány adatfájlok kell, és függőségi parancsfájlokat, és nem használhat olyan adattárolón** a fájlokat helyezze a tanítási szkriptet mappa könyvtárába. Ez a mappa, adja meg a `source_directory` közvetlenül a tanítási szkriptet, illetve a kódot, amely meghívja a tanítási szkriptet.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Tárolási korlátok kísérlet pillanatképek

Kísérletek, az Azure Machine Learning automatikusan egy kísérlet pillanatkép, a kód a könyvtárat, akkor javasoljuk, hogy a Futtatás konfigurálásakor alapján lehetővé teszi. Ez esetében összesen 300 MB és/vagy 2000 fájlokat. Ha túllépi ezt a korlátot, akkor a következő hiba jelenik meg:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Ez a hiba elhárításához a adattárolót kísérlet fájlokat tárolja. Ha egy adattár, nem használhatja az alábbi táblázat a lehetséges alternatív megoldásokat kínál.

Kísérlet&nbsp;leírása|Tárolómegoldás korlát
---|---
Kevesebb mint 2000 fájlok & adattárolót nem használható.| A pillanatkép-méretkorlát felülbírálása <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Ez eltarthat néhány percig, attól függően, a szám és a fájlok mérete.
Adott szkript könyvtárat kell használnia.| Győződjön meg arról, egy `.amlignore` fájlok kizárása a kísérlet pillanatkép, amely nem tartozik a forráskód fájlt. Adja hozzá a fájlneveket, a `.amlignore` fájlt, és helyezze a tanítási szkriptet ugyanabban a címtárban. A `.amlignore` fájl ugyanazokkal a [szintaxist és a minták](https://git-scm.com/docs/gitignore) , egy `.gitignore` fájlt.
Folyamat|Használjon egy másik alkönyvtárban mindegyik lépéshez
Jupyter-notebookok| Hozzon létre egy `.amlignore` fájlt vagy a jegyzetfüzet helyezhetik át egy új, üres alkönyvtárat, és újra futtathatja a kódot.

## <a name="where-to-write-files"></a>Hol fájlok írása

Képzési kísérletek elkülönítése miatt a módosítások fájlra, amelynek a futtatása során nem feltétlenül rögzíti a környezeten kívül. Ha a parancsfájl a fájlok helyi számítási módosítja, futtassa a következő kísérlethez nem rögzíti a módosításokat, és azok még nem érvényesülnek vissza az ügyfélszámítógép automatikusan. Ezért futtatása az első kísérlet során elvégzett módosítások nem, és nem érinti azokat a második.

Módosítások írásakor, javasoljuk, hogy írása egy Azure Machine Learning-adattárhoz. Lásd: [érheti el adatait a adattárainak](how-to-access-data.md).

Egy adattároló nem feltétlenül szükséges, ha a fájlok írása a `./outputs` és/vagy `./logs` mappát.

>[!Important]
> Két mappa *kimenete* és *naplók*, speciális kezelés az Azure Machine Learning által. Képzés, amikor fájlok írása során`./outputs` és`./logs` mappák, a fájlok automatikusan fel kell tölteni a futtatási előzmények, így azokhoz való hozzáférés a Futtatás befejeződése után.

* **Például az állapotüzenetek vagy pontozási eredményeinek kimeneti** fájlok írása a `./outputs` mappában, így azok megmaradnak, a futtatási előzmények összetevők. Kell szem előtt tartva, és az ebbe a mappába írt fájlok mérete, a késés akkor fordulhat elő, ha a tartalmat töltenek fel a futtatási előzmények. Késés problémát jelent, ha egy adattár a fájlok írása ajánlott.

* **Írt fájlt mentse őket a futtatási előzmények naplóinak** fájlok `./logs` mappát. A naplók valós időben lesznek feltöltve, ezért ezt a módszert, ami a streaming élő frissítéseket egy távoli, futtassa a megfelelő.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [az adattárolók származó adatok egységes elérésével](how-to-access-data.md).

* Tudjon meg többet [képzési célok beállítása hogyan](how-to-set-up-training-targets.md).
