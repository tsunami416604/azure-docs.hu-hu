---
title: Tárak hozzáadása és kezelése az Apache Sparkhoz az Azure Synapse Analytics szolgáltatásban
description: Ismerje meg, hogyan vehet fel és kezelhet az Apache Spark által használt tárakat az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427834"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Tárak hozzáadása és kezelése az Apache Sparkhoz az Azure Synapse Analytics szolgáltatásban

Az Apache Spark számos könyvtártól függ a funkciók biztosításához. Ezek a könyvtárak bővíthetők vagy helyettesíthetők további könyvtárakkal vagy a régebbiek frissített verzióival.

Python-csomagok a Spark-készlet (előzetes verzió) szintjén adhatók hozzá, és a .jar alapú csomagok a Spark feladatdefiníciós szintjén adhatók hozzá.

## <a name="adding-or-updating-python-libraries"></a>Python-kódtárak hozzáadása vagy frissítése

Az Apache Spark az Azure Synapse Analytics teljes Anacondas telepítésével és további kódtárakkal rendelkezik. A teljes könyvtárak listája megtalálható az [Apache Spark verziótámogatás.](apache-spark-version-support.md)

Amikor egy Spark-példány elindul, egy új virtuális környezet jön létre ezzel a telepítéssel, mint az alap. Ezenkívül a *requirements.txt* fájl (a `pip freeze` parancs kimenete) használható a virtuális környezet frissítéséhez. Az ebben a fájlban telepítési vagy frissítésre szánt csomagok a fürt indításakor töltődnek le a PyPi alkalmazásról. Ez a követelményfájl minden alkalommal használatos, amikor egy Spark-példány jön létre aspark-készletből.

> [!IMPORTANT]
>
> - Ha a telepíti a csomagot nagy, vagy hosszú időt vesz igénybe a telepítés, ez hatással van a Spark-példány indítási ideje.
> - A telepítéskor fordítói támogatást igénylő csomagok, például a GCC, nem támogatottak.
> - A csomagok nem minősíthetők vissza, csak hozzáadhatók vagy frissíthetők.

### <a name="requirements-format"></a>Követelmények formátuma

A következő kódrészlet a követelményfájl formátumát mutatja be. A PyPi csomag neve a pontos verzióval együtt jelenik meg. Ez a fájl a [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) referenciadokumentációban leírt formátumot követi. Ez a példa egy adott verziót tűz ki. Ebben a fájlban megadhatja a "nem nagyobb" és a "kisebb, mint" verziókat is.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python-könyvtár felhasználói felülete

A tárak hozzáadásához a felhasználói felület az Azure Portalon az **Apache Spark-készlet létrehozása** lap További **beállítások** lapján található.

Töltse fel a környezet konfigurációs fájlját a lap **Csomagok** szakaszában található fájlkijelölő vel.

![Python-tárak hozzáadása](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Python-tárak hozzáadása")

## <a name="next-steps"></a>További lépések

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark dokumentáció](https://spark.apache.org/docs/2.4.4/)
