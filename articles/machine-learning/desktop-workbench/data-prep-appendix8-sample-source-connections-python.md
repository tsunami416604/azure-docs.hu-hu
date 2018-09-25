---
title: A példában további forrás adatkapcsolatok lehetséges az Azure Machine Learning adat-előkészítési |} A Microsoft Docs
description: Ez a dokumentum biztosít, amelyek az Azure Machine Learning adat-előkészítési lehetőségeinek forrás adatkapcsolatok példái
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ed0e6bc4c506535fcb679fc2b4015a61274a77f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967693"
---
# <a name="sample-of-custom-source-connections-python"></a>Egyéni adatforrás-kapcsolatok (Python) mintája 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Mielőtt, olvassa el a jelen függelék, olvassa el a [Python bővíthetőség áttekintése](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Adatok betöltése a Data.worldről

### <a name="prerequisites"></a>Előfeltételek

#### <a name="register-yourself-at-dataworld"></a>Data.world, regisztrálja magát
Szüksége lesz egy API-jogkivonatot a data.world-webhelyről.

#### <a name="install-dataworld-library"></a>Data.world-erőforrástár telepítése

Nyissa meg az Azure Machine Learning Workbench parancssori felület kiválasztásával **fájl** > **nyílt forráskódú parancssori felületének**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Ezután futtassa `dw configure` a parancssorban, amely kérni fogja a jogkivonatot. Amikor Ön adja meg a jogkivonatot, egy .dw / könyvtár jön létre a kezdőkönyvtárban, és a token ott tárolja.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Most már lehet tudja importálni a data.world-kódtárakat.

#### <a name="load-data-into-data-preparation"></a>Adatok betöltése az adat-előkészítés

Hozzon létre egy Data adatfolyam átalakítása (szkript) átalakítás. A következő parancsfájl használatával az adatok betöltése a Data.worldről.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Az Azure Cosmos DB adatforrás-kapcsolat
Olvassa el az Azure Cosmos DB egy kapcsolatot, például [betöltése az Azure Cosmos DB egy adatforrás-kapcsolatot](data-prep-load-azure-cosmos-db.md)
