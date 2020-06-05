---
title: PySpark interaktív környezet az Azure HDInsight-eszközökkel
description: Megtudhatja, hogyan hozhat létre és küldhet el lekérdezéseket és parancsfájlokat a Visual Studio Code-hoz készült Azure HDInsight Tools használatával.
keywords: VScode, Azure HDInsight-eszközök, struktúra, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktív struktúra, interaktív lekérdezés
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: deeb80ba74385574d62348ddc2ca3b99a7637541
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417259"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A PySpark interaktív környezet beállítása a Visual Studio Code-hoz

A következő lépések bemutatják, hogyan állíthatja be a PySpark interaktív környezetét a VSCode-ben. Ez a lépés csak a nem Windows rendszerű felhasználók számára érhető el.

A **Python/pip** parancs használatával virtuális környezetet építhet ki a saját elérési útjában. Ha más verziót szeretne használni, manuálisan kell módosítania a **Python/pip** parancs alapértelmezett verzióját. További részletek: [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Telepítse a [Pythont](https://www.python.org/downloads/) és a [pip](https://pip.pypa.io/en/stable/installing/)-et.

   * Telepítse a Pythont a alkalmazásból [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * A pip telepítése [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (ha nem a Python-telepítésből van telepítve).
   * Ellenőrizze, hogy a Python és a pip telepítése sikeresen megtörtént-e az alábbi parancsokkal. (Nem kötelező)

        ![A Python pip Version parancsának keresése](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Javasoljuk, hogy a macOS alapértelmezett verziójának használata helyett manuálisan telepítse a Pythont.

2. Telepítse a **virtualenv** az alábbi parancs futtatásával.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Egyéb csomagok

Ha Linux rendszeren az alábbi hibaüzenet jelenik meg, akkor a következő két parancs futtatásával telepítse a szükséges csomagokat.

   ![A Pythonhoz készült libkrb5-csomag telepítése](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Indítsa újra a VSCode, majd térjen vissza a VSCode-szerkesztőhöz, és futtassa a **Spark: PySPark Interactive** parancsot.

## <a name="next-steps"></a>Következő lépések

### <a name="demo"></a>Bemutató

* HDInsight a VS Code-hoz: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [A Visual Studio Code-hoz készült Azure HDInsight Tool használata](hdinsight-for-vscode.md)
* [A Azure Toolkit for IntelliJ használata Apache Spark Scala-alkalmazások létrehozásához és elküldéséhez](spark/apache-spark-intellij-tool-plugin.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
