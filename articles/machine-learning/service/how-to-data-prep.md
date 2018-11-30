---
title: Előkészíti az adatokat a Machine Learning Data Prep SDK-val az Pythonnal – Azure
description: Ismerje meg, hogyan használhatja az Azure Machine Learning Data Prep SDK Pythonhoz készült betölteni az adatokat a különböző formátumok, átalakítja a további használatához és az adatokat írni az eléréséhez a modellek helyét.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446425"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Adatok előkészítése az Azure Machine Learning modellezés

Ebben a cikkben megismerkedhet a használati esetek és az Azure Machine Learning Data Prep SDK egyedi funkcióját. Adat-előkészítés a machine learning-munkafolyamat legfontosabb részét képezi. Valós adatoknak gyakran megszakad, inkonzisztens vagy nem lehet a betanítási adatok jelentős Címellenőrzés és -átalakítási nélkül használható. Hibák és rendellenességeket a nyers adatokat, és új funkciók, amely a problémát próbál megoldani, a pontosság növekszik. Az SDK-t tervezték más közös adatok előkészítő kódtárak partnereknek ismerős eszközökön főbb forgatókönyvek megvalósítását előnyöket kínál, és a többi tárak együttműködés karbantartása során.

Az adatok a Python használatával készítheti elő a [az Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk).

## <a name="azure-machine-learning-data-prep-sdk"></a>Az Azure Machine Learning, Data Prep SDK

A [az Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) egy Python-kódtár által kínált:

* Mentés intelligens időpont-átalakítások, például intelligens csoportosítási, származtatott oszlop szerint példa, automatikus felosztása, az intelligens fájl olvasása és Ragged jobb séma feldolgozása.
* Egy API, amely együttműködik a kis méretű adatok helyi vagy a felhőben, nagy mennyiségű adat **kódmódosítás nélkül, néhány**.
* Az adatok feldolgozását, nem pedig a memóriába streamelési megközelítése révén egyetlen gépen hatékonyabb méretezési képességét.

### <a name="install-the-sdk"></a>Az SDK telepítése

A Python-környezetében, a következő paranccsal telepítse az SDK-t.

```shell
pip install azureml-dataprep
```

A következő kód használatával a csomag importálása.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Példák és referencia

A modulok és az SDK funkcióit kapcsolatos további információkért tekintse meg a [Data Prep SDK referenciadokumentumok](https://aka.ms/data-prep-sdk).

Az alábbi példák kiemelnek néhány egyedi funkcióját az SDK-t, beleértve:

* A fájlok automatikus adattípus észlelése
* Intelligens átalakítások
* Összefoglaló statisztikák
* Kereszt-környezet funkció


#### <a name="automatic-file-type-detection"></a>A fájlok automatikus adattípus észlelése

Használja a `smart_read_file()` függvény használatával töltse be az adatokat a fájl típusa megadása nélkül. Ez a függvény automatikusan felismeri és elemzi a fájl típusa.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>Intelligens átalakítások

Az SDK segítségével felosztása és azokból származtatott oszlopok is példa tanuláshoz és következtetésekhez funkciófejlesztési automatizálásához. Fel, hogy egy mezőt az adatfolyam-objektum nevű `datetime` értékkel `2018-09-15 14:30:00`.

Automatikusan felosztása a `datetime` mezőben a következő függvény hívása.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

A példa a paraméter nem definiálásával automatikusan kettéosztjuk a függvény a `datetime` két új mezők mezőt `datetime_1` és `datetime_2`. Az eredményül kapott értékek a következők `2018-09-15` és `14:30:00`, illetve. Biztosít egy példa a minta lehetőség arra is, és az SDK előre jelezni fog, és hajtsa végre a kívánt átalakítást. Azonos `datetime` objektumot, az alábbi kód létrehoz egy olyan új oszlop `datetime_weekday` a hétköznap, a megadott példa alapján.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>Összefoglaló statisztikák

Létrehozhat egy adatfolyam-egyetlen sor kód rövid összefoglaló statisztikáit. Ez a módszer kényelmesen megtudhatja, hogy az adatokat, és hogyan oszlik.

```python
dataflow.get_profile()
```

Ez a függvény hívása egy adatfolyam-objektum a következő táblázat hasonló kimenet eredményez.

![Összefoglaló statisztikák kimenet](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Több környezetben való kompatibilitása

Az SDK lehetővé teszi az adatfolyam-objektumok szerializálva, és megnyílik az *bármely* Python-környezetet. A környezet, amelyben meg van nyitva, mint a környezet mentési helye eltérő lehet. Ez a funkció lehetővé teszi, hogy a Python-környezetek és gyors integráció az Azure Machine Learning-modellek között áttelepítő.

A következő kód használatával az adatfolyamot objektumok mentése.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

A következő kód használatával nyissa meg újra a csomag bármilyen környezetben és adatfolyam-objektumok listájának lekéréséhez.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Adat-előkészítési folyamat

Részletes példákat és minden egyes előkészítési lépés kód megtekintéséhez használja a következő útmutatókat:

1. [Adatok betöltése](how-to-load-data.md), amely számos formátumú lehet
2. [Átalakítás](how-to-transform-data.md) őket egy használhatóbbá struktúra
3. [Írási](how-to-write-data.md) az adatokat egy olyan helyre, a modellek

![Adatok előkészítési folyamatának](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>További lépések

Tekintse át az [példa notebook](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) az adat-előkészítés az Azure Machine Learning Data Prep SDK használatával.

Az Azure Machine Learning Data Prep SDK [referenciadokumentációt](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).
