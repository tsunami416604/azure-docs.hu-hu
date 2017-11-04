---
title: "Példa további Forráshelyek adatokhoz való kapcsolódást lehetővé az Azure Machine Learning adatok előkészítése |} Microsoft Docs"
description: "Ez a dokumentum biztosít forrás adatok kapcsolat esetén lehetséges az Azure Machine Learning adatok előkészítése"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 9aaf4329b25cb189146949afed89cf15619ba592
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Egyéni adatforrás-kapcsolatok (Python) minta 
Ahhoz, hogy olvassa el a jelen függelék, olvassa el a [Python bővítési áttekintése](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Adatok betöltése az data.world

### <a name="prerequisites"></a>Előfeltételek

#### <a name="register-yourself-at-dataworld"></a>Regisztrálja magát data.world:
Szüksége van egy API-jogkivonatot az data.world webhelyről.

#### <a name="install-dataworld-library"></a>Telepítse a data.world könyvtár

Nyissa meg az Azure Machine Learning-munkaterület parancssori felület kiválasztásával **fájl** > **nyissa meg a parancssori felület**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Ezt követően futtassa `dw configure` a parancssorban, amely bekéri a jogkivonatot. Ha, írja be a jogkivonatot, egy .dw / könyvtár jön létre a kezdőkönyvtár és a token nem tárolja.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Most meg kell tudni data.world könyvtárak importálása.

#### <a name="load-data-into-data-preparation"></a>Adatok betöltése az adatok előkészítése

Hozzon létre egy új, parancsprogram-alapú adatfolyam. A következő parancsfájl segítségével az adatok betöltése az data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Azure Cosmos DB adatok betöltése az adatok előkészítése

Hozzon létre egy új, parancsprogram-alapú adatfolyam, és a következő parancsfájl segítségével az adatok betöltése az Azure Cosmos DB. (A könyvtárak kell előbb telepíteni kell. További információkért lásd az előző referenciadokumentum azt hivatkozó.)

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
