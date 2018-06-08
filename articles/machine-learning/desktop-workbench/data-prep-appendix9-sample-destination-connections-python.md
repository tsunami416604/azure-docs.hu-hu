---
title: Példa a célok/kimenetek lehetséges az Azure Machine Learning adatok előkészítése |} Microsoft Docs
description: Ez a dokumentum egy egyéni adatok célok/kimenetének létrehozása az Azure Machine Learning adatok előkészítése példák készlete
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 93da07006280b38c9e6539773e6ac22e50e48b57
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831812"
---
# <a name="sample-of-destination-connections-python"></a>A cél-kapcsolatok (Python) minta 
Ahhoz, hogy olvassa el a jelen függelék, olvassa el a [Python bővítési áttekintése](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Excel írása 


Excel írás egy további könyvtár igényel. A bővítési áttekintése hozzáadása új könyvtárak ismertetését. `openpyxl` a könyvtárban, amelyeket hozzá kell adnia van.

Az Excel ablakában írása előtt néhány egyéb módosítások szükség lehet. Adatok előkészítése az használt adattípusok közül nem mindegyiknél támogatott néhány cél formátumban. Például "Error" objektumok esetén ezek nem szerializálható megfelelően Excel. Így az Excelbe írására történt kísérlet, meg kell "Hibaértékek cseréje" átalakító, amely hibák eltávolítja az oszlopot.

Ha minden az előző munkaelem befejeződött, a következő sort az adattábla ír egy lapot a egy Excel-dokumentumban. Adjon hozzá egy átalakítási Adatfolyamblokk (parancsfájl) átalakítási. Egy kifejezés szakaszban adja meg az alábbi kódot.


### <a name="on-windows"></a>Windows rendszeren 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>A macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
