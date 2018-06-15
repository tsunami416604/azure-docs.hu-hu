---
title: Az SSH tunneling elérni az Azure HDInsight használata |} Microsoft Docs
description: Útmutató az SSH-alagút segítségével biztonságosan keresse meg a Linux-alapú HDInsight-csomópontok webes erőforrásaihoz.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 797538a6d023e1a4b95680057eb0f72489290f40
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311522"
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>SSH Tunneling Ambari webes felhasználói felület, JobHistory, NameNode, Oozie és egyéb web UI eléréséhez használja

A HDInsight-fürtök az Ambari webes felhasználói felület hozzáférést biztosítanak az interneten keresztül, de bizonyos funkciók szükségesek az SSH-alagutat. Például a webes felhasználói felület az Oozie-szolgáltatás nem érhető el az SSh-alagút nélkül az interneten keresztül.

## <a name="why-use-an-ssh-tunnel"></a>Miért érdemes használni az SSH-alagút

A menüket az Ambari számos csak támogatott eszközplatformokon működnek az SSH-alagúton keresztül. Ezek a menük webhelyek és az egyéb csomóponttípusok, például a feldolgozó csomópontok szolgáltatás támaszkodnak.

A következő Web UI SSH-alagút megkövetelése:

* JobHistory
* NameNode
* A szál verem
* Oozie webes felhasználói felület
* A HBase Master és a naplókat a felhasználói felület

A Parancsfájlműveletek segítségével testre szabhatja a fürthöz, ha lehetnek szolgáltatások vagy a segédeszközök telepítése egy webszolgáltatás-bővítmény visszaállítását szükséges SSH-alagutat. Például egy parancsfájl műveletével Hue telepítése SSH-alagutat a a Hue webes felhasználói felület eléréséhez kell használnia.

> [!IMPORTANT]
> Ha a virtuális hálózaton keresztül a HDInsight közvetlen hozzáféréssel rendelkezzenek, nem kell SSH-alagutak használatára. Közvetlenül a virtuális hálózaton keresztül férnek hozzá a HDInsight példáért lásd: a [a helyszíni hálózathoz való csatlakozás HDInsight](connect-on-premises-network.md) dokumentum.

## <a name="what-is-an-ssh-tunnel"></a>Mi az az SSH-alagút

[Secure Shell (SSH) bújtatás](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) egy portot a helyi számítógép csatlakozik egy HDInsight átjárócsomópontjához. A helyi portra küldött forgalmat az SSH-kapcsolat az átjárócsomóponthoz keresztül történik. A kérelem meg van oldva, mintha az átjárócsomópont származna. A válasz majd áthalad vissza az alagutat a munkaállomáson.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. A legtöbb operációs rendszer adja meg egy SSH-ügyfél keresztül a `ssh` parancsot. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy olyan webböngésző, beállítható úgy, hogy egy SOCKS5 proxy használatára.

    > [!WARNING]
    > A Windows Internet beállítások épített SOCKS proxy támogatási SOCKS5 nem támogatja, és nem működik a jelen dokumentumban leírt lépések. Az alábbi böngészők Windows proxybeállítások alapulnak, és a jelen dokumentumban leírt lépések jelenleg nem működnek:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome is használ a Windows proxybeállításokat. Bővítmények SOCKS5 támogató is telepítheti. Ajánlott [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Hozzon létre egy alagúton, az SSH-parancs használatával

Használja a következő paranccsal egy SSH-alagút használatával a `ssh` parancsot. Cserélje le **sshuser** az SSH-felhasználó a HDInsight-fürtöt, és cserélje le a **clustername** nevű, a HDInsight-fürt:

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Ez a parancs kapcsolatot hoz létre, amely irányítja a forgalmat helyi port 9876 a fürthöz az SSH-n keresztül. A következő lehetőségek közül választhat:

* **D 9876** -a helyi portot, amelyet a irányítja a forgalmat az alagúton keresztül.
* **C** -tömörítése minden adatot, mert a webes forgalom leginkább szöveget.
* **2** -force SSH protokoll 2-es verzióját kipróbálásához.
* **a q** -csendes mód.
* **T** -pszeudo-tty kiosztását, tiltsa le, mivel csak továbbít egy portot.
* **n** -megakadályozása STDIN, olvasása, mivel csak továbbít egy portot.
* **N** -nem hajtható végre távoli parancsot, mert csak továbbít egy portot.
* **f** -fusson a háttérben.

Ha a parancs a helyi számítógépen 9876 portra küldi továbbítódik a head fürtcsomóponton.

## <a name="useputty"></a>A PuTTY használatával-alagút létrehozása

[A puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) egy grafikus SSH-ügyfél Windows. Használja a PuTTY SSH-alagút létrehozásához tegye a következőket:

1. Nyissa meg a PuTTY, és adja meg a kapcsolódási adatokat. Ha nem ismeri a PuTTY-e, tekintse meg a [dokumentáció PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. Az a **kategória** a párbeszédpanel bal területen bontsa ki a **kapcsolat**, bontsa ki a **SSH**, majd válassza ki **alagutak**.

3. A következő információkat biztosítanak a **vezérlő beállításokban SSH port átirányítása** űrlap:
   
   * **Source port** (Forrásport) - Az ügyfélen az a port, amelyet továbbítani szeretne. Például **9876**.

   * **Cél** -az SSH a Linux-alapú HDInsight-fürt címét. Például: **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** (Dinamikus) - Lehetővé teszi a dinamikus SOCKS proxy útválasztást.
     
     ![beállítások tunneling képe](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Kattintson a **Hozzáadás** vegye fel a beállításokat, majd **nyissa meg a** az SSH-kapcsolat megnyitása.

5. Amikor a rendszer kéri, jelentkezzen be a kiszolgálóra.

## <a name="use-the-tunnel-from-your-browser"></a>Használja az alagút a böngészőből

> [!IMPORTANT]
> A jelen szakaszban szereplő lépéseket a Mozilla FireFox böngésző használja, minden platformon lehetővé teszi a proxybeállításokat. Egyéb modern böngésző támogatja, például a Google Chrome, például az alagutat használható FoxyProxy bővítmény lehet szükség.

1. Használja a böngészőben **localhost** és mikor Ön által használt porthoz létrehozása az alagutat, mint egy **SOCKS v5** proxy. Ez hogyan meg a Firefox beállításait. Ha egy másik portra, mint 9876, módosítsa a port azonos:
   
    ![a Firefox beállításai](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Kiválasztása **távoli DNS** oldja fel a tartománynévrendszer (DNS) kéréseket a HDInsight-fürt használatával. Ez a beállítás használata a fürt átjárócsomópontjához DNS oldja fel.

2. Győződjön meg arról, hogy működik-e az alagutat a webhely felkeresésével [ http://www.whatismyip.com/ ](http://www.whatismyip.com/). Kell lennie az IP-cím adott vissza a Microsoft Azure datacenter használják.

## <a name="verify-with-ambari-web-ui"></a>Ellenőrizze a következővel Ambari webes felhasználói felület

A fürt létrehozása után az alábbi lépések segítségével győződjön meg arról, hogy elérhető szolgáltatás web UI az Ambari webes:

1. A böngészőben nyissa meg a http://headnodehost:8080. A `headnodehost` cím a fürthöz, és hárítsa el az átjárócsomóponthoz, hogy fut-Ambari az alagúton keresztül zajlik. Amikor a rendszer kéri, adja meg a rendszergazdai felhasználónevet (rendszergazda) és a jelszót a fürt számára. Kérheti még egyszer az Ambari webes felhasználói felület által. Ha igen, írja be újra az adatokat.

   > [!NOTE]
   > Használatakor a http://headnodehost:8080 a cím, csatlakozzon a fürthöz, az alagúton keresztül kapcsolódik. Kommunikációs használatával lett biztonságossá téve az SSH-alagút HTTPS kapcsolat helyett. Az interneten, HTTPS-kapcsolaton keresztül csatlakozni, használja a https://clustername.azurehdinsight.net, ahol **clustername** a fürt neve.

2. Az Ambari webes felhasználói felületén jelölje ki a lap bal oldali listában a HDFS.

    ![A kiválasztott HDFS kép](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Ha a HDFS-szolgáltatás információk jelennek meg, válassza ki a **Gyorshivatkozások**. A fürt átjárócsomópontokkal listája jelenik meg. Az átjárócsomópontokkal egyikét, majd válassza ki és **NameNode felhasználói felület**.

    ![A kibontott QuickLinks menü Kép](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Ha bejelöli __Gyorshivatkozások__, várjon, amíg mutató kaphat. Ez az állapot akkor fordulhat elő, ha lassú internetkapcsolattal rendelkezik. Várjon egy percet, amíg a kiszolgálótól érkező adatokat, és próbálkozzon újra a listában.
   >
   > Az egyes bejegyzések a **Gyorshivatkozások** menü előfordulhat, hogy elszigeteli a képernyő jobb szélén. Ha igen, bontsa ki a menüt, az egér használatával, és görgessen a képernyő jobb a menü további részeinek megjelenítéséhez a jobbra mutató nyílra kulcs használatával.

4. Az alábbi képen hasonló lap jelenik meg:

    ![A NameNode felhasználói felület képe](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Figyelje meg ezen a lapon; URL-címe meg kell hasonló **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Ezt az URI a csomópont a belső teljesen minősített tartománynevét (FQDN) használja, és csak elérhető az SSH-alagút használatakor.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan létrehozhat és használhat SSH-alagút, tekintse meg a következő dokumentumban más Ambari használatának módjai:

* [A HDInsight-fürtök kezelése az Ambari segítségével](hdinsight-hadoop-manage-ambari.md)

Az SSH és a HDInsight együttes használatával további információkért lásd: [az SSH a Hdinsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

