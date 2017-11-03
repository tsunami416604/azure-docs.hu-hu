---
title: "Az adatok tudományos feldolgozáshoz Hadoop-fürtök testreszabása |} Microsoft Docs"
description: "Népszerű Python-modulok egyéni Azure HDInsight Hadoop-fürtök elérhetik."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0c115dca-2565-4e7a-9536-6002af5c786a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 522e33b399f2648427464b439bc4405e9e8097cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Az Azure HDInsight Hadoop-fürtök testreszabása a csoportos adatelemzési folyamathoz
Ez a cikk ismerteti hogyan szabhatja testre a HDInsight Hadoop-fürt telepítésével, 64 bites Anaconda (Python 2.7) minden egyes csomóponton, amikor a fürt ki van építve a HDInsight-szolgáltatásként. Azt is bemutatja, hogyan a fürthöz egyéni feladatok küldéséhez headnode eléréséhez. Ez a beállítás lehetővé teszi számos népszerű Python modult, szereplő Anaconda kényelmesen használható felhasználó által megadott funkciókat (UDF), melyet úgy terveztek, a fürt Hive-rekordok feldolgozásához. Ebben a forgatókönyvben használt eljárásokat, lásd: [hogyan elküldeni a Hive-lekérdezések](move-hive-tables.md#submit).

A következő menü hivatkozások témakörök azt ismertetik, hogyan állíthatja be a különböző adatok által használt tudományos környezetekben a [Team adatok tudományos folyamat (TDSP)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

## <a name="customize"></a>Az Azure HDInsight Hadoop-fürt testreszabása
Testreszabott HDInsight Hadoop-fürt létrehozása, indítsa el a naplózás [ **a klasszikus Azure portálon**](https://manage.windowsazure.com/), kattintson a **új** bal alsó sarokban, és adja DATA SERVICES -> HDINSIGHT -> **egyéni létrehozás** elindítani a **a fürt részleteinek** ablak. 

![Munkaterület létrehozása](./media/customize-hadoop-cluster/customize-cluster-img1.png)

A konfiguráció lapon 1 létrehozni a fürt nevét, és fogadja el a más mezők alapértelmezett értékét. Kattintson a nyílra kattintva nyissa meg a következő konfigurációs oldalát. 

![Munkaterület létrehozása](./media/customize-hadoop-cluster/customize-cluster-img1.png)

A konfiguráció lapon 2 bemeneti száma **ADATCSOMÓPONTOKAT**, jelölje be a **régió/virtuális hálózati**, és válassza ki a méretét a **ÁTJÁRÓCSOMÓPONT** és a **ADATCSOMÓPONTON**. Kattintson a nyílra kattintva nyissa meg a következő konfigurációs oldalát.

> [!NOTE]
> A **régió/virtuális hálózati** lehet ugyanaz, mint a területet a storage-fiók, amelyet a HDInsight Hadoop-fürt használni szeretne. Ellenkező esetben a negyedik konfigurálása lapon, a tárfiók nem jelennek meg a legördülő listából a **fióknév**.
> 
> 

![Munkaterület létrehozása](./media/customize-hadoop-cluster/customize-cluster-img3.png)

A konfiguráció lapon 3 adja meg egy felhasználónevet és jelszót a HDInsight Hadoop-fürt. **Ne** válassza ki a *adja meg a Hive/Oozie Metaadattárhoz*. Kattintson a nyílra kattintva nyissa meg a következő konfigurációs oldalát. 

![Munkaterület létrehozása](./media/customize-hadoop-cluster/customize-cluster-img4.png)

A konfiguráció lapon 4 adja meg a tárfiók nevét, a HDInsight Hadoop-fürt alapértelmezett tárolót. Ha *hozzon létre alapértelmezett tároló* a a **alapértelmezett tároló** legördülő listában, egy azonos nevű tárolót, a fürt jön létre. Kattintson a nyílra kattintva nyissa meg az utolsó lap.

![Munkaterület létrehozása](./media/customize-hadoop-cluster/customize-cluster-img5.png)

A végső **Parancsfájlműveletek** konfigurációs lapon kattintson a **parancsfájlművelet hozzáadása** gombra, és töltse ki a szövegmezők tartalma a következő értékekkel.

* **NÉV** -bármilyen karakterlánc, mint a parancsfájlművelet nevét
* **A CSOMÓPONTTÍPUS** – Itt adhatja meg **minden csomópont**
* **PARANCSFÁJL URI azonosítója** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
  * *publicscripts* a tárfiókban lévő nyilvános tárolója 
  * *getgoing* megosztása lehetővé teszi a felhasználók munkahelyi az Azure PowerShell-parancsfájlok használatával
* **PARAMÉTEREK** -(hagyja üresen)

Végül kattintson a pipa jelre indítsa el a testre szabott HDInsight Hadoop-fürt létrehozását. 

![Munkaterület létrehozása](./media/customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Hozzáférés a Hadoop-fürt Átjárócsomópontjához
RDP segítségével próbál hozzáférni a Hadoop-fürt átjárócsomópontjához engedélyeznie kell az Azure-ban Hadoop-fürt távoli eléréséhez. 

1. Jelentkezzen be a [ **a klasszikus Azure portálon**](https://manage.windowsazure.com/), jelölje be **HDInsight** bal oldalán válassza ki a Hadoop-fürt fürtök a listából, kattintson a **konfigurációs** fülre, majd a **távoli engedélyezése** ikonra az oldal alján.
   
    ![Munkaterület létrehozása](./media/customize-hadoop-cluster/enable-remote-access-1.png)
2. Az a **konfigurálása a távoli asztal** ablak, írja be a FELHASZNÁLÓNEVET és a jelszó mező, és válassza ki a távelérés lejárati dátumát. Kattintson a pipa jelre a távoli hozzáférés a Hadoop-fürt átjárócsomópontjához engedélyezése.
   
    ![Munkaterület létrehozása](./media/customize-hadoop-cluster/enable-remote-access-2.png)

> [!NOTE]
> A felhasználónév és jelszó a távoli hozzáféréshez csak a felhasználónév és a Hadoop-fürt létrehozásakor használt jelszó. Ez a hitelesítő adatok külön készletét. Is a távelérés lejárati dátuma nem lehet az aktuális 7 napon belül.
> 
> 

Távelérés engedélyezése után kattintson **CONNECT** az átjárócsomópont be távolról a lap alján. Jelentkezzen be a Hadoop-fürt átjárócsomópontjához a távoli hozzáférés a felhasználó korábban megadott hitelesítő adatok megadásával.

![Munkaterület létrehozása](./media/customize-hadoop-cluster/enable-remote-access-3.png)

A következő lépéseket a speciális elemzési során leképezett a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) , és tartalmazhatnak lépéseket, adatokat áthelyezi HDInsight, majd feldolgozni, és példa, hogy az adatokat az Azure Machine Learning tanulva előkészítésekor.

Lásd: [hogyan elküldeni a Hive-lekérdezések](move-hive-tables.md#submit) útmutatást, amely a felhasználói függvény (UDF), amely a fürt tárolt Hive rekordok feldolgozásához használt a fürt átjárócsomópontjához Anaconda szerepel a Python-modulok eléréséhez.

