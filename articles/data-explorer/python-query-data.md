---
title: 'Gyors útmutató: Adatok lekérdezése az Azure Data Explorer Python-kódtár használatával'
description: Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan kérdezhet le adatokat az Azure Data Explorerből a Python használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: 4de8f68e0384742cea4ce50ccd23a7455b186893
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048741"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Gyors útmutató: Adatok lekérdezése az Azure Data Explorer Python-kódtár használatával

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer [adatügyfélkódtárat biztosít a Pythonhoz](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Ez a kódtár lehetővé teszi adatok lekérdezését a kódból. Ebben a rövid útmutatóban a *súgófürtben* lévő olyan táblához csatlakozik, amelyet a tanulás elősegítéséhez állítottunk be. Ezután lekérdezi a fürt egy tábláját, és visszaadja az eredményeket.

Ez a rövid útmutató elérhető [Azure Notebookként](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb) is.

## <a name="prerequisites"></a>Előfeltételek

* Vállalati e-mail-fiók, amely az Azure Active Directory (AAD) tagja

* A fejlesztői számítógépen telepített [Python](https://www.python.org/downloads/)

## <a name="install-the-data-library"></a>Az adatkódtár telepítése

Telepítse az *azure-kusto-data* tárat.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Importálási utasítások és állandók hozzáadása

Importáljon osztályokat a kódtárból, valamint a *pandas* adatelemzési kódtárból.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Az alkalmazás hitelesítéséhez az Azure Data Explorer az AAD-bérlő azonosítóját használja. A bérlőazonosító megkereséséhez használja a következő URL-címet úgy, hogy a *YourDomain* kifejezés helyére a saját tartományát írja be.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kattintson erre az URL-címre az eredmények megtekintéséhez; az első sor a következő.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlőazonosító ebben az esetben a következő: `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. A kód futtatása előtt állítsa be az AAD_TENANT_ID értékét.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Most hozza létre a kapcsolati sztringet. Ez a példa eszközhitelesítést használ a fürt eléréséhez. Is [AAD-alkalmazás tanúsítványának](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [AAD alkalmazáskulcsot](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20), és [AAD felhasználójának és jelszavának](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Csatlakozás az Azure Data Explorerhez és lekérdezés végrehajtása

Hajtson végre egy lekérdezést a fürtön, és tárolja a kimenetet egy adatkeretben. Ez a kód futtatásakor a következőhöz hasonló üzenetet adja vissza: *Jelentkezzen be, használja egy webböngészőben nyissa meg a https://microsoft.com/devicelogin , és írja be a kódot a hitelesítéshez F3W4VWZDM*. Kövesse a bejelentkezési lépéseket, majd térjen vissza a következő kódblokk futtatásához.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Adatok feltárása adatkeretben

A bejelentkezési adatok beírása után a lekérdezés eredményeket ad vissza, amelyek egy adatkeretben lesznek tárolva. Az eredményeket ugyanúgy használhatja, mint más adatkereteket.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Meg kell jelennie a StormEvents táblában szereplő első tíz eredménynek.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Az Azure Data Explorer Python-kódtár használata az adatok betöltése](python-ingest-data.md)
