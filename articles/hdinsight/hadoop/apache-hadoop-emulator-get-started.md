---
title: Ismerje meg az Apache Hadoop homokozó, emulátor használatát - Azure HDInsight
description: 'Az Apache Hadoop-ökoszisztéma használatának megkezdéséhez beállíthat egy Hadoop-sandboxot a Hortonworks-ből egy Azure virtuális gépen. '
keywords: hadoop emulátor,hadoop homokozó
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73044770"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Első lépések egy Apache Hadoop homokozóval, egy virtuális gép emulátorával

Ismerje meg, hogyan telepítheti az Apache Hadoop sandbox-ot a Hortonworks-től egy virtuális gépre, hogy megismerje a Hadoop ökoszisztémát. A sandbox helyi fejlesztői környezetet biztosít a Hadoop, a Hadoop Distributed File System (HDFS) és a feladatbeküldés megismeréséhez. Miután már ismeri a Hadoop, elkezdheti használni a Hadoop az Azure-ban egy HDInsight-fürt létrehozásával. Az első lépésekről a [Hadoop a HDInsight on](apache-hadoop-linux-tutorial-get-started.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* [Oracle VirtualBox](https://www.virtualbox.org/). Töltse le és telepítse [innen](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>A virtuális gép letöltése és telepítése

1. Tallózással keresse meg a [Cloudera letöltéseket.](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)

1. Kattintson **a VIRTUALBOX** csoport **Telepítési típus kiválasztása** csoportban a legújabb Hortonworks sandbox letöltéséhez a virtuális gépen. Jelentkezzen be vagy töltse ki a termékérdeklődési űrlapot.

1. A letöltés megkezdéséhez kattintson a **HDP SANDBOX (LATEST)** gombra.

A sandbox beállításával kapcsolatos tudnivalókat a [Sandbox telepítési és telepítési útmutatója című témakörben találja.](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)

Régebbi HDP-verziós sandbox letöltéséhez olvassa el a **Régebbi verziók**című témakörben található hivatkozásokat.

## <a name="start-the-virtual-machine"></a>A virtuális gép indítása

1. Nyissa meg az Oracle VM VirtualBox.Open Oracle VM VirtualBox.
1. A **Fájl** menüben kattintson a **Készülék importálása**parancsra, majd adja meg a Hortonworks sandbox-lemezképet.
1. Válassza ki a Hortonworks sandboxot, kattintson a **Start**gombra, majd a **Normál indítás parancsra.** Miután a virtuális gép befejezte a rendszerindítási folyamatot, megjeleníti a bejelentkezési utasításokat.

    ![virtualbox menedzser normál start](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Nyisson meg egy webböngészőt, `http://127.0.0.1:8888`és keresse meg a megjelenített URL-t (általában ).

## <a name="set-sandbox-passwords"></a>A sandbox-jelszavak beállítása

1. A Hortonworks sandbox lap **első lépések** lépésében válassza a Speciális beállítások **megtekintése lehetőséget.** Az ezen az oldalon található információk segítségével jelentkezzen be a sandboxba az SSH használatával. Használja a megadott nevet és jelszót.

   > [!NOTE]
   > Ha nincs telepítve SSH-ügyfél, használhatja a virtuális gép által biztosított webalapú **http://localhost:4200/** SSH-t a rendszerben.

    Az SSH használatával történő első csatlakozáskor a rendszer kéri a gyökérfiók jelszavának módosítását. Adjon meg egy új jelszót, amelyet az SSH használatával történő bejelentkezéskor használ.

2. Miután bejelentkezett, írja be a következő parancsot:

        ambari-admin-password-reset

    Amikor a rendszer kéri, adjon meg egy jelszót az Ambari rendszergazdai fiókhoz. Ezt az Ambari webes felhasználói felület elérésekor használja a program.

## <a name="use-hive-commands"></a>Hive-parancsok használata

1. Egy SSH-kapcsolatról a sandboxba a következő paranccsal indítsa el a Hive-rendszerhéjat:

        hive
2. A rendszerhéj indítása után a következőkkel tekintse meg a homokozóhoz mellékelt táblázatokat:

        show tables;
3. Az alábbi módon 10 sort `sample_07` kérhet be a táblából:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>További lépések

* [A Visual Studio használata a Hortonworks sandboxsegítségével](../hdinsight-hadoop-emulator-visual-studio.md)

* [Tanulás a kötelek a Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop oktatóanyag - A HDP – Első lépések](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
