---
title: Az ELŐREJELZÉSsel rendelkező gépi tanulási modellek pontszáma
description: Megtudhatja, hogyan szerzi be a gépi tanulási modelleket a szinapszis SQL-ben a T-SQL ELŐREJELZÉSi függvény használatával.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d28f37e6020e3a3165b012548868a3ec798651c2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294376"
---
# <a name="score-machine-learning-models-with-predict"></a>Az ELŐREJELZÉSsel rendelkező gépi tanulási modellek pontszáma

A szinapszis SQL lehetővé teszi a gépi tanulási modellek a jól ismert T-SQL nyelv használatával való kiértékelését. A T-SQL [előrejelzéssel](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)megadhatja a már meglévő gépi tanulási modelleket, és az adattárház biztonságos határain belül is megszerezheti azokat. A PREDIKTÍV függvény [ONNX (Open neurális hálózati Exchange)](https://onnx.ai/) modellt és adatokat használ bemenetként. Ez a szolgáltatás kiküszöböli az értékes adattárházon kívüli adatáthelyezés lépéseit. Az informatikai szakemberek számára lehetővé teszi, hogy az ismerős T-SQL-felülettel könnyedén üzembe helyezhetik a gépi tanulási modelleket, valamint hogy zökkenőmentesen működjenek együtt a feladataik megfelelő keretrendszerével dolgozó adatszakértőkkel.

> [!NOTE]
> Ez a funkció jelenleg nem támogatott az SQL igény szerinti használata esetén.

A funkció használatához a modellt a szinapszis SQL-en kívül kell tanítani. A modell felépítése után töltse be az adattárházba, és a T-SQL előrejelzési szintaxissal szerzi be az adatokból származó elemzéseket.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>A modell betanítása

A szinapszis SQL egy előre betanított modellt vár. Tartsa szem előtt a következő tényezőket, amikor betanít egy gépi tanulási modellt, amely az előrejelzések a szinapszis SQL-ben való végrehajtására szolgál.

- A szinapszis SQL csak a ONNX formátumot támogatja. A ONNX egy nyílt forráskódú modell formátuma, amely lehetővé teszi a különböző keretrendszerek közötti modellek cseréjét az együttműködés lehetővé tételéhez. A meglévő modelleket ONNX formátumra konvertálhatja olyan keretrendszerek használatával, amelyek natív módon támogatják vagy a csomagok átalakítását is lehetővé teszik. A [sklearn-Onnx](https://github.com/onnx/sklearn-onnx) csomag például a scikit-Learn modellek konvertálása Onnx. A [ONNX GitHub-adattár](https://github.com/onnx/tutorials#converting-to-onnx-format) a támogatott keretrendszerek és példák listáját tartalmazza.

   Ha az [automatikus ml](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) -t használja a betanításhoz, ügyeljen arra, hogy a *enable_onnx_compatible_models* paraméter értéke TRUE (igaz) legyen, hogy Onnx formátumú modellt hozzon létre. Az [automatizált Machine learning notebookon](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) látható egy példa arra, hogyan használható a AUTOML a ONNX formátumú gépi tanulási modell létrehozásához.

- A bemeneti adatok a következő adattípusokat támogatják:
    - int, bigint, valós, float
    - char, varchar, nvarchar

- A pontozási adatgyűjtési adatként a betanítási adatként megegyező formátumban kell lennie. Az előrejelzés nem támogatja az összetett adattípusokat, például a többdimenziós tömböket. Így a képzéshez győződjön meg arról, hogy a modell minden bemenete megfelel a pontozási tábla egyetlen oszlopának, ahelyett, hogy egyetlen, az összes bemenetet tartalmazó tömböt kellene átadni.

- Győződjön meg arról, hogy a modell bemenetei és adattípusai egyeznek az új előrejelzési adatok oszlopainak neveivel és adattípusaival. A különböző nyílt forráskódú eszközök online elérhetővé tételével egy ONNX-modell megjelenítése további segítséget nyújthat a hibakeresésben.

## <a name="loading-the-model"></a>A modell betöltése

A modellt egy szinapszis SQL felhasználói táblában tárolja hexadecimális karakterláncként. Az azonosító és a Leírás további oszlopokat is hozzáadhat a modell táblában a modell azonosításához. Használja a varbinary (max) értéket a modell oszlop adattípusa szerint. Az alábbi példa egy olyan táblázatra mutat be példát, amely a modellek tárolására használható:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Miután a modell át lett konvertálva egy hexadecimális sztringre, és megadta a tábla definícióját, a [másolási paranccsal](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) vagy a (z) használatával töltse be a modellt a szinapszis SQL-táblába. A következő mintakód a Másolás parancs használatával tölti be a modellt.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>A modell pontozása

Ha a modell és az adat betöltődik az adattárházba, a **T-SQL előrejelzési** függvénnyel szerzi be a modellt. Győződjön meg arról, hogy az új bemeneti adatok formátuma megegyezik a modell létrehozásához használt képzési adatokkal. A T-SQL előrejelzés két bemenetet vesz igénybe: modell és új pontozási bemeneti adat, és új oszlopokat hoz létre a kimenethez. A modell változóként, literálal vagy skaláris sub_query is megadható. A [WITH common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15) használatával megadhatja az adatparaméter elnevezett eredményhalmaz értékét.

Az alábbi példa egy példaként szolgáló lekérdezést mutat be az előrejelzési függvény használatával. Egy további, a *pontszám* és az adattípusú *lebegőpontos* oszlop jön létre, amely tartalmazza az előrejelzés eredményeit. Az összes bemeneti adatoszlop, valamint a kimeneti előrejelzési oszlopok is elérhetők a SELECT utasítással való megjelenítéshez. További részletek: [előrejelzés (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Következő lépések

További információ a PREDIKTÍV függvényről: [előrejelzés (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).
