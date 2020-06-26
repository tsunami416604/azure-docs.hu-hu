---
title: Könyvtárak hozzáadása és kezelése Apache Sparkhoz az Azure szinapszis Analyticsben
description: Megtudhatja, hogyan veheti fel és kezelheti a Apache Spark által használt könyvtárakat az Azure szinapszis Analyticsben.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5eb67752a57ce7745b1128f5f5eb36414bfe993c
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392607"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Könyvtárak hozzáadása és kezelése Apache Sparkhoz az Azure szinapszis Analyticsben

A Apache Spark számos könyvtártól függ, hogy funkciókat biztosítson. Ezek a kódtárak kiegészíthető vagy lecserélhetők további könyvtárakkal, illetve régebbi verziókkal is.

Python-csomagok a Spark-készlet (előzetes verzió) szintjén adhatók hozzá, a. jar-alapú csomagok pedig a Spark-feladatdefiníció szintjén adhatók hozzá.

## <a name="adding-or-updating-python-libraries"></a>Python-kódtárak hozzáadása vagy frissítése

Apache Spark az Azure szinapszis Analyticsben a teljes anacondas telepítése és további könyvtárak is elérhetők. A teljes kódtárak listája a következő címen érhető el: [Apache Spark Version support](apache-spark-version-support.md).

A Spark-példány indításakor új virtuális környezet jön létre, amely a telepítést használja alapként. Emellett egy *requirements.txt* -fájl (a parancs kimenete) használható a `pip freeze` virtuális környezet frissítésére. A telepítéshez vagy frissítéshez a fájlban felsorolt csomagok letöltése a PyPi a fürt indításakor történik. Ezt a követelményt a rendszer minden alkalommal felhasználja, amikor egy Spark-példányt létrehoznak a Spark-készletből.

> [!IMPORTANT]
>
> - Ha a telepített csomag nagy méretű, vagy hosszú ideig tart a telepítés, ez hatással van a Spark-példány indítási idejére.
> - A fordítási támogatást igénylő csomagok, például a GCC, nem támogatottak.
> - A csomagokat nem lehet leértékelni, csak a Hozzáadás vagy a frissítés lehetőségre.

### <a name="requirements-format"></a>Követelmények formátuma

A következő kódrészlet a követelmények fájl formátumát mutatja. A PyPi-csomag neve a pontos verzióval együtt jelenik meg. Ez a fájl a [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) Reference dokumentációjában ismertetett formátumot követi. Ez a példa egy adott verziót PIN-kódra mutat. Ebben a fájlban a "nem nagyobb, mint" és a "kisebb, mint" verziót is megadhatja.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python-függvénytár felhasználói felülete

A kódtárak hozzáadására szolgáló felhasználói felület a Azure Portal **Apache Spark készlet létrehozása** lapjának **További beállítások** lapján található.

Töltse fel a környezet konfigurációs fájlját a lap **csomagok** területén található fájl-választó használatával.

![Python-kódtárak hozzáadása](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Python-kódtárak hozzáadása")

### <a name="verifying-installed-libraries"></a>Telepített kódtárak ellenőrzése

A következő kód futtatásával ellenőrizheti, hogy telepítve vannak-e a megfelelő kódtárak megfelelő verziói

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark dokumentáció](https://spark.apache.org/docs/2.4.4/)
