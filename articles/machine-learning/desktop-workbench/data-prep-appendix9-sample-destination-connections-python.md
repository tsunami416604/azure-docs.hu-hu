---
title: Példa destinations/kimenetekben. lehetséges, az Azure Machine Learning adat-előkészítési |} A Microsoft Docs
description: Ez a dokumentum számos új egyéni adatok destinations/kimenetekben. az Azure Machine Learning adat-előkészítési példái
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
ms.openlocfilehash: 3e827c56caa787ae9fc33119810eea0f268f0120
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958413"
---
# <a name="sample-of-destination-connections-python"></a>A cél kapcsolatok (Python) minta 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Mielőtt, olvassa el a jelen függelék, olvassa el a [Python bővíthetőség áttekintése](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Az Excel írása 


Excel írása szükséges egy további erőforrástár. Új kódtárak hozzáadása a bővíthetőségi áttekintő ismertetését. `openpyxl` a rendszer az erőforrástárban, amelyeket hozzá kell adnia.

Mielőtt Excel ír, akkor lehet szükség, néhány más módosítást. Bizonyos cél formátumban a az adatelőkészítés által használt adatok típusok nem támogatottak. Például ha az "Error" objektum létezik, azok nem szerializálható megfelelően Excel. Ebből kifolyólag próbál meg írni az Excel, birtokában "Hiba értékek lecserélése" átalakító, amely oszlopokat távolít el hibákat.

Ha az összes korábbi munka befejeződése után a következő sort az adattábla ír egy lapot az Excel-dokumentum a. Adjon hozzá egy adatfolyam átalakítása (szkript) átalakítás. Majd adja meg a következő kódot egy kifejezés szakaszban.


### <a name="on-windows"></a>Windows rendszeren 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>A macOS-/ OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
