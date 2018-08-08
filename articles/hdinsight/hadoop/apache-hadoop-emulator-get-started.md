---
title: Ismerje meg, a Hadoop-tesztkörnyezet - emulátor – Azure HDInsight használatával
description: 'A Hadoop-ökoszisztéma használatáról tanulási indításához állíthat be egy Hadoop-tesztkörnyezet a hortonworks által biztosított-beli virtuális gépen. '
keywords: hadoop-emulátorban, a hadoop-tesztkörnyezet
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: jasonh
ms.openlocfilehash: 6a2a81f89e86a75dd56283526713b88cdfd21569
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598712"
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Ismerkedés a Hadoop-tesztkörnyezet, egy-egy virtuális gépen emulátoron

Ismerje meg, hogyan lehet telepíteni a Hadoop-tesztkörnyezet Hortonworks megismerheti a Hadoop-ökoszisztéma virtuális gépen. A védőfal megismerheti a Hadoop, a Hadoop elosztott fájlrendszer (HDFS) és a feladat elküldése helyi fejlesztési környezetet biztosít. Ha már ismeri a Hadoop, elkezdheti az Azure-beli Hadoop használata egy HDInsight-fürtöt. Az első lépésekről további információkért lásd: [Hadoop on HDInsight használatának első lépései](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Előfeltételek
* [Oracle VirtualBox](https://www.virtualbox.org/). Töltse le és telepítse azt a [Itt](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Töltse le és telepítse a virtuális gép
1. Keresse meg a [Hortonworks letölti](http://hortonworks.com/downloads/#sandbox).

2. Kattintson a **töltse le a VIRTUALBOX** töltheti le a legújabb hortonworks – tesztkörnyezet egy virtuális gépen. Azure a Hortonworks közreműködésével regisztrálni a letöltés megkezdése előtt kéri. Töltse le a hálózat sebességétől függően egy-két órát vesz igénybe.
   
    ![Hivatkozás képe, töltse le a VirtualBox hortonworks – tesztkörnyezet](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Az azonos weblapon kattintson a **virtuális Box importálás** hivatkozásra a telepítési utasításokat a virtuális gépet tartalmazó PDF-fájl letöltéséhez.

Töltse le egy régebbi HDP verzió védőfal, bontsa ki az archívum:

![Archív hortonworks – tesztkörnyezet](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>A virtuális gép elindítása

1. Nyissa meg a virtuális gép VirtualBox Oracle.
2. Az a **fájl** menüben kattintson a **importálás berendezés**, és adja meg a Hortonworks Sandbox kép.
1. Válassza ki a Hortonworks Sandbox, kattintson **Start**, majd **indítsa el a normál**. Miután a virtuális gép a rendszerindítási folyamat befejeződött, bejelentkezési utasításokat jeleníti meg.
   
    ![Normál indítása](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Nyisson meg egy webböngészőt, és keresse meg az URL-cím jelenik meg (általában http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>A védőfal jelszavak beállítása

1. Az a **Ismerkedés** . lépés a Hortonworks Sandbox oldala, válassza a **nézet speciális beállítások**. Ezen az oldalon az információk segítségével jelentkezzen be az SSH-val tesztkörnyezetben. Használja a megadott felhasználónévvel és jelszóval.
   
   > [!NOTE]
   > Ha nem rendelkezik egy SSH-ügyfél telepítve van, a webes az SSH-val, a virtuális gép által megadott **http://localhost:4200/**.
   > 
   
    Az első csatlakozáskor SSH,-val kéri a rendszergazdai fiók jelszavának módosítása. Adjon meg egy új jelszót, amelyet használhat az SSH-bejelentkezéskor.

2. Miután bejelentkezett, adja meg a következő parancsot:
   
        ambari-admin-password-reset
   
    Amikor a rendszer kéri, adjon meg egy jelszót az Ambari rendszergazdai fiókkal. Ez használatos az Ambari webes Kezelőfelületen használatakor.

## <a name="use-hive-commands"></a>Hive-parancsokkal

1. Az SSH-kapcsolatot a védőfal a következő parancs használatával indítsa el a Hive-rendszerhéj:
   
        hive
2. A rendszerhéj megkezdése után használja az alábbi a táblák a védőfal által biztosított:
   
        show tables;
3. Használja a következő 10 sorait beolvasni a `sample_07` tábla:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>További lépések
* [Ismerje meg, hogyan használható a Visual Studio a hortonworks – tesztkörnyezet](../hdinsight-hadoop-emulator-visual-studio.md)
* [Tanulási a köteleknek, a hortonworks – tesztkörnyezet](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop oktatóanyag – HDP – első lépések](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

