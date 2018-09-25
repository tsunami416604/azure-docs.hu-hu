---
title: Csatlakozás az Azure Cosmos DB adatforrásként az Azure Machine Learning Workbench alkalmazásban |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan csatlakozhat az Azure Cosmos DB az Azure Machine Learning Workbench használatával a
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9c4ea529e8ca6dbb9b7321dc24468fad93435705
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948196"
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Csatlakozás az Azure Cosmos DB adatforrásként

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Ez a cikk tartalmaz egy python minta lehetővé teszi, hogy csatlakozzon az Azure Machine Learning Workbench a Cosmos DB-hez.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Azure Cosmos DB-adatok betöltése az adat-előkészítés

Hozzon létre egy új, parancsprogram-alapú adatfolyamot, és ezután használja a következő szkriptet az Azure Cosmos DB az adatok betöltéséhez. 

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

## <a name="other-data-source-connections"></a>Más adatforrás-kapcsolatok
Más minták, olvassa el a [például további forráshelyeket adatkapcsolatok](data-prep-appendix8-sample-source-connections-python.md)
