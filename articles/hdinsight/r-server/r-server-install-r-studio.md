---
title: "Az R Server on HDInsight - Azure Rstudióból telepítése |} Microsoft Docs"
description: "Hogyan Rstudióból telepíteni az R Server on HDInsight."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aaf527d6d95b97eff5edcab9040ce08751bb8296
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Az R Server on HDInsight az Rstudióból telepítése

Ez a cikk ismerteti a community (ingyenes) verziójának telepítéséhez [Rstudióból Server](https://www.rstudio.com/products/rstudio-server/) egyéni parancsfájl használata a fürt peremhálózati csomóponton. Rstudióból kiszolgáló távoli ügyfelek számára biztosít egy webböngésző-alapú IDE, és Linux széles körben használható. Van több integrált fejlesztési környezet (IDEs) érhető el, az R ma, beleértve:

- A Microsoft [R Tools for Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) 
- [Rstudióból kiszolgáló](https://www.rstudio.com/products/rstudio-server/) 
- Walware tartozó Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

A HDInsight-fürtök élcsomópont Rstudióból-kiszolgálót telepítse előnye, hogy az teljes IDE élményt nyújt a fejlesztési és az R parancsfájlok végrehajtása az R Server a fürtön. Lehet, hogy ez a konfiguráció jelentős mértékben hatékonyabb, mint az alapértelmezett R konzol használata.

> [!NOTE]
> Az ebben a cikkben ismertetett eljárás csak fontos, ha nem jelölte be telepítse Rstudióból Server közösségi verziót, ha a fürt. Ha a kiépítés során hozzáadott, akkor az eléréséhez kattintson a a **R Server irányítópultok** csempét a az Azure portál bejegyzése a fürtöt, majd a **R Studio Server** csempére. 

Ha inkább az Rstudióból Server kereskedelmileg licencelt Pro verzióját használja, a telepítési utasításokat hajtsa végre az [Rstudióból Server](https://www.rstudio.com/products/rstudio/download-server/).

## <a name="prerequisites"></a>Előfeltételek

* R Server telepített Azure HDInsight-fürtöt. Útmutatásért lásd: [az R Server első lépései a HDInsight-fürtök](r-server-get-started.md).
* Egy SSH-ügyfél. A Linux és Unix megfelelő kiadásának vagy Macintosh-OS X a `ssh` parancs az operációs rendszer által biztosított. A Windows, ajánlott [Cygwin](http://www.redhat.com/services/custom/cygwin/) rendelkező a [OpenSSH beállítás](https://www.youtube.com/watch?v=CwYSvvGaiWU), vagy [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Egyéni parancsfájl használata a fürt Rstudióból telepítése

A lépések a következők:

1. A fürt élcsomópont azonosításához. A HDInsight-fürtök az R Server az alábbiakban az átjárócsomópont és élcsomópont elnevezési konvenciót.
   * Átjárócsomópont`CLUSTERNAME-ssh.azurehdinsight.net`
   * Élcsomópont`CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. SSH-ból az élcsomóponthoz a fürt az elnevezési minta az 1. lépésben megadott használatával. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Miután csatlakozott, vált a gyökér szintű felhasználó a fürtön. Az SSH-munkamenetet a következő paranccsal:

        sudo su -

4. Töltse le a egyéni parancsfájl Rstudióból telepítéséhez. Használja az alábbi parancsot:

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Az egyéni parancsfájl a engedélyeit, és futtassa a parancsfájlt. Az alábbi parancsokat használja:

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. R Server a HDInsight-fürtök létrehozásakor egy SSH-jelszó használata esetén ezt a lépést kihagyhatja, és folytassa a következő. Ha SSH-kulcs inkább a fürt létrehozása, meg kell adni az SSH-felhasználó jelszót. Ezt a jelszót kell Rstudióból történő csatlakozás során. Futtassa az alábbi parancsot:

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. Ha a rendszer kéri **aktuális Kerberos jelszó**, nyomja le az ENTER **ENTER**.  Vegye figyelembe, hogy le kell cserélnie `USERNAME` rendelkező a HDInsight-fürthöz az SSH-felhasználó. Ha a jelszó sikeresen be van állítva, a következő üzenetet kell megjelennie:

        passwd: password updated successfully

    Lépjen ki az SSH-munkamenetet.

8. A fürthöz SSH-alagút létrehozása leképezi `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` a HDInsight-fürt, az ügyfélszámítógépnek. Az SSH-alagút létre kell hoznia egy új böngésző-munkamenet megnyitása előtt.

   * A Linux-ügyfél vagy egy Windows ügyfél [Cygwin](http://www.redhat.com/services/custom/cygwin/)nyisson meg egy terminál-munkamenetet, és használja a következő parancsot:

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Cserélje le **felhasználónév** az SSH-felhasználó a HDInsight-fürtöt, és cserélje le a **CLUSTERNAME** a HDInsight-fürt nevét.
       Használhatja a jelszó helyett egy SSH-kulcs hozzáadásával `-i id_rsa_key`.        
   * Ha a Windows-ügyfél majd a PuTTY használatával

     1. Nyissa meg a PuTTY, és adja meg a kapcsolódási adatokat.
     2. Az a **kategória** a párbeszédpanel bal területen bontsa ki a **kapcsolat**, bontsa ki a **SSH**, majd válassza ki **alagutak**.
     3. A következő információkat biztosítanak a **vezérlő beállításokban SSH port átirányítása** űrlap:

        * **Source port** (Forrásport) - Az ügyfélen az a port, amelyet továbbítani szeretne. Például **8787**.
        * **Cél** -a cél, hogy le kell képezni a helyi ügyfélszámítógépen. Például **localhost:8787**.

            ![SSH-alagút létrehozása](./media/r-server-install-r-studio/createsshtunnel.png "SSH-alagút létrehozása")

     4. Kattintson a **Hozzáadás** vegye fel a beállításokat, majd **nyissa meg a** az SSH-kapcsolat megnyitása.
     5. Amikor a rendszer kéri, jelentkezzen be a kiszolgálóra SSH-munkamenetet létesít, és engedélyezi az alagutat.

9. Nyisson meg egy webböngészőt, és írja be a következő URL-cím az az alagutat a megadott port alapján:

        http://localhost:8787/ 

10. Adja meg az SSH-felhasználónév és jelszó kapcsolódik a fürthöz kéri. Ha a fürt létrehozásakor egy SSH-kulcsot, meg kell adnia az 5. lépésben létrehozott jelszó.

    ![R Studio csatlakozni](./media/r-server-install-r-studio/connecttostudio.png "SSH-alagút létrehozása")

11. Annak megállapítására, hogy a Rstudióból telepítés sikeres volt, a teszt parancsfájl, amely végrehajtja az R-alapú MapReduce és Spark feladatok a fürtön is futtathatja. Töltse le a teszt parancsfájl futtatását Rstudióból, lépjen vissza az SSH-konzolra, és adja meg a következő parancsokat:

    *    A Hadoop fürtök R hozta létre, ha az alábbi parancsot használja:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Ha létrehozott egy Spark-fürt az R, az alábbi parancsot használja:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. Az Rstudióból tekintse meg a letöltött teszt parancsfájl. Kattintson duplán a fájlra a megnyitásához, válassza ki a fájl tartalmát, majd **futtatása**. A kimenetnek kell megjelennie a **konzol** panelen:

   ![A telepítés sikerességének ellenőrzése](./media/r-server-install-r-studio/test-r-script.png "a telepítés sikerességének ellenőrzése")

Egy másik lehetőség lenne, írja be a `source(testhdi.r)` vagy `source(testhdi_spark.r)` a parancsfájl végrehajtása.

## <a name="see-also"></a>Lásd még:

* [Számítási környezet lehetőségek R Server a HDInsight-fürtökön](r-server-compute-contexts.md)
* [Azure Storage lehetőségek a HDInsighton belüli R Server esetében](r-server-storage.md)

