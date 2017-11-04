---
title: "Ismerje meg, a Hadoop védőfalak - emulátor - Azure HDInsight használata |} Microsoft Docs"
description: "A Hadoop ökoszisztémájának használatával megtanulni elindításához állíthat be egy Hadoop védőfal a Hortonworks Azure virtuális géphez. "
keywords: "hadoop-emulátor, hadoop védőfal"
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 77973ac4224e439377f011a77106534b325b97f9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Ismerkedés a Hadoop védőfalat, az emulátor egy virtuális gépen

Ismerje meg, a Hadoop védőfal Hortonworks telepítéséről további információt a Hadoop ökoszisztémájának virtuális gépen. A védőfal Hadoop, a Hadoop elosztott fájlrendszerrel (HDFS) és a feladat elküldése helyi fejlesztési környezetet biztosít. Ha ismeri a Hadoop, megkezdheti a Hadoop használatával az Azure HDInsight-fürtök létrehozásával. Első lépések a további információkért lásd: [beolvasása használatába a HDInsight Hadoop](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Előfeltételek
* [Oracle VirtualBox](https://www.virtualbox.org/). Töltse le és telepítse azt [Itt](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Töltse le és telepítse a virtuális gép
1. Keresse meg a [Hortonworks letölti](http://hortonworks.com/downloads/#sandbox).

2. Kattintson a **töltse le a VIRTUALBOX** letölteni a legújabb Hortonworks védőfal a virtuális gép. A letöltés megkezdése előtt Hortonworks regisztrálása kéri. Töltse le a hálózat sebességétől függően egy-két órát vesz igénybe.
   
    ![Hivatkozás kép VirtualBox a Hortonworks védőfal letöltése](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. A weblapon, kattintson a **virtuális párbeszédpanel importálás** a virtuális gép telepítési utasításokat tartalmazó PDF-fájl letöltésére mutató hivatkozás.

Töltse le egy régebbi HDP verzió védőfal, bontsa ki az archív:

![Hortonworks védőfal archív](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>A virtuális gép elindítása

1. Nyissa meg az Oracle VM VirtualBox.
2. A a **fájl** menüben kattintson **importálási készülék**, és adja meg a Hortonworks védőfal kép.
1. Jelölje ki azt a Hortonworks védőfal **Start**, majd **normál Start**. Miután a virtuális gép a rendszerindítási folyamat befejeződött, bejelentkezési utasítások jeleníti meg.
   
    ![Normál indítása](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Nyisson meg egy webböngészőt, és keresse meg az URL-cím jelenik meg (általában http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>A védőfal jelszavak beállítása

1. Az a **Ismerkedés** a Hortonworks védőfal lapon jelölje be a lépés **nézet speciális beállítások**. Olvassa el ezen a lapon jelentkezzen be a védőfal SSH használatával. Használja a megadott felhasználónévvel és jelszóval.
   
   > [!NOTE]
   > Ha nincs telepítve egy SSH-ügyfél, használhatja a webalapú SSH, a virtuális gép által megadott **http://localhost:4200 /**.
   > 
   
    Az SSH-létesítsen első alkalommal kéri a rendszergazdafiók jelszavának módosítása. Adjon meg egy új jelszót, amelyet használhat, amikor bejelentkezik az SSH használatával.

2. Miután bejelentkezett, adja meg a következő parancsot:
   
        ambari-admin-password-reset
   
    Amikor a rendszer kéri, adja meg egy jelszót a Ambari rendszergazdai fiók. Ez használható az Ambari webes felhasználói felületén elérésekor.

## <a name="use-hive-commands"></a>Hive-parancsok használata

1. Az SSH-kapcsolatot a védőfal alkalmazás a Hive rendszerhéjat indítsa el a következő parancsot:
   
        hive
2. Amikor a rendszerhéj elindult, használja a következő a táblák, a védőfal által biztosított megtekintése:
   
        show tables;
3. Használja a következő 10 sorát beolvasni a `sample_07` tábla:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Következő lépések
* [Ismerje meg a Visual Studio használata a Hortonworks védőfal](../hdinsight-hadoop-emulator-visual-studio.md)
* [Az a Hortonworks védőfal drótkötelek tanulási](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop oktatóanyag – első lépések HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

