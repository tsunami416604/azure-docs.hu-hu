---
title: "Példa a célok/kimenetek lehetséges az Azure Machine Learning adatok előkészítése |} Microsoft Docs"
description: "Ez a dokumentum egy egyéni adatok célok/kimenetének létrehozása az Azure Machine Learning adatok előkészítése példák készlete"
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
ms.openlocfilehash: ca9e7d8c318100ba498ee15be15e213905f1e8d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-destination-connections-python"></a>A cél-kapcsolatok (Python) minta 
Ahhoz, hogy olvassa el a jelen függelék, olvassa el a [Python bővítési áttekintése](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Excel írása 


Excel írás egy további könyvtár igényel. A bővítési áttekintése hozzáadása új könyvtárak ismertetését. `openpyxl`a könyvtárban, amelyeket hozzá kell adnia van.

Az Excel ablakában írása előtt néhány egyéb módosítások szükség lehet. Adatok előkészítése az használt adattípusok közül nem mindegyiknél támogatott néhány cél formátumban. Például "Error" objektumok esetén ezek nem szerializálható megfelelően Excel. Így az Excelbe írására történt kísérlet, meg kell "Hibaértékek cseréje" átalakító, amely hibák eltávolítja az oszlopot.

Ha minden az előző munkaelem befejeződött, a következő sort az adattábla ír egy lapot a egy Excel-dokumentumban. Adja hozzá a írási Adatfolyamblokk (parancsfájl) átalakítás. Egy kifejezés szakaszban adja meg az alábbi kódot.


### <a name="on-windows"></a>Windows rendszeren 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>A macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
