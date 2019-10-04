---
title: Az SSH-alagút használata az Azure HDInsight eléréséhez
description: Ismerje meg, hogyan használható az SSH-alagút a Linux-alapú HDInsight-csomópontokon üzemeltetett webes erőforrások biztonságos tallózásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: d976826fe90946697a32c5b1edb9dd323b01cc1c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105473"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Az Apache Ambari webes felhasználói felületének, a JobHistory, a NameNode, az Apache Oozie és más felületének eléréséhez használja az SSH-bújtatást

A HDInsight-fürtök az interneten keresztül biztosítanak hozzáférést az Apache Ambari webes FELÜLETéhez, néhány szolgáltatáshoz azonban szükség van egy SSH-alagútra. Az Apache Oozie szolgáltatás webes felhasználói felülete például nem érhető el az interneten keresztül SSh-alagút nélkül.

## <a name="why-use-an-ssh-tunnel"></a>Miért érdemes SSH-alagutat használni?

A Ambari számos menüje csak SSH-alagúton keresztül működik. Ezek a menük a más csomópont-típusokon, például a feldolgozó csomópontokon futó webhelyeken és szolgáltatásokon alapulnak.

Az alábbi webes felülethez SSH-alagút szükséges:

* JobHistory
* NameNode
* Szálak készletei
* Oozie webes felhasználói felület
* Felhasználói felület HBase Master és naplózása

Ha parancsfájl-műveletek használatával testreszabja a fürtöt, a webszolgáltatások számára elérhetővé tett összes szolgáltatás vagy segédprogram SSH-alagutat igényel. Ha például a Hue műveletet parancsfájl-művelettel telepíti, egy SSH-alagutat kell használnia a Hue webes felhasználói felületének eléréséhez.

> [!IMPORTANT]  
> Ha a virtuális hálózaton keresztül közvetlen hozzáférést biztosít a HDInsight, nem kell SSH-alagutakat használnia. Az HDInsight virtuális hálózaton keresztüli közvetlen elérésére példaként tekintse [meg a HDInsight összekapcsolása](connect-on-premises-network.md) a helyszíni hálózati dokumentumhoz című témakört.

## <a name="what-is-an-ssh-tunnel"></a>Mi az SSH-alagút?

A [Secure Shell (SSH) bújtatás](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) a helyi gépen található portot csatlakoztatja a HDInsight egyik fő csomópontjához. A helyi portra továbbított forgalom a fő csomóponttal létesített SSH-kapcsolatban van átirányítva. A rendszer úgy oldja meg a kérést, mintha a fő csomópontról származik. Ekkor a rendszer visszairányítja a választ az alagúton keresztül a munkaállomásra.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy webböngésző, amely SOCKS5 proxy használatára konfigurálható.

    > [!WARNING]  
    > A Windows Internet-beállításokba beépített SOCKS proxy-támogatás nem támogatja a SOCKS5, és nem működik a jelen dokumentumban ismertetett lépésekkel. A következő böngészők a Windows-proxybeállításokat használják, és jelenleg nem működnek a jelen dokumentumban ismertetett lépésekkel:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > A Google Chrome a Windows-proxy beállításain is alapul. Azonban a SOCKS5 támogató bővítményeket is telepíthet. A [FoxyProxy standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)használatát javasoljuk.

## <a name="usessh"></a>Alagút létrehozása az SSH parancs használatával

Az alábbi parancs használatával hozzon létre egy SSH-alagutat `ssh` a paranccsal. Cserélje `sshuser` le egy SSH-felhasználót a HDInsight-fürtre, `clustername` és cserélje le a nevet a HDInsight-fürt nevére:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Ezzel a paranccsal olyan kapcsolat jön létre, amely a 9876-as helyi portra irányítja át a forgalmat az SSH-val a fürtre. A következő lehetőségek közül választhat:

* **D 9876** – az a helyi port, amely az alagúton keresztül irányítja a forgalmat.
* **C** – az összes adatok tömörítése, mivel a webes forgalom többnyire szöveg.
* **2** – az SSH csak a 2-es verziójú protokoll kipróbálására kényszeríthető.
* **q** -csendes mód.
* Nem kell letiltani a **pszeudo-TTY** kiosztást, mivel csak egy portot továbbít.
* **n** – az stdin olvasásának tiltása, mivel csak egy portot továbbít.
* **N** – ne hajtson végre távoli parancsot, mert csak egy portot továbbít.
* **f** – Futtatás a háttérben.

A parancs befejezése után a rendszer a helyi számítógépen a 9876-es portra eljuttatott forgalmat a fürt fő csomópontjára irányítja.

## <a name="useputty"></a>Alagút létrehozása a PuTTY használatával

A [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty) a Windows rendszerhez készült grafikus SSH-ügyfél. Ha nem ismeri a PuTTY-t, tekintse meg a [Putty dokumentációját](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). A következő lépésekkel hozhat létre SSH-alagutat a PuTTY használatával:

### <a name="create-or-load-a-session"></a>Munkamenet létrehozása vagy betöltése

1. Nyissa meg a PuTTY eszközt, és győződjön meg arról, hogy a bal oldali menüben a **munkamenet** be van jelölve. Ha már mentett egy munkamenetet, válassza ki a munkamenet nevét a **mentett munkamenetek** listából, és válassza a **Betöltés**lehetőséget.

1. Ha még nem rendelkezik mentett munkamenettel, adja meg a kapcsolati adatait:
    * **Állomásnév (vagy IP-cím)** – a HDInsight-fürthöz tartozó SSH-cím. Például: **mycluster-SSH.azurehdinsight.net**
    * **Port** -22
    * **Kapcsolattípus** – SSH

1. Válassza ki **mentése**

    ![HDInsight-munkamenet létrehozása](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. A párbeszédpanel bal oldalán található **Kategória** szakaszban bontsa ki a **kapcsolatok**csomópontot, bontsa ki az **SSH**elemet, majd válassza az **alagutak**lehetőséget.

1. Adja meg az alábbi információkat az **SSH-port továbbítási formáját vezérlő beállításokhoz** :

   * **Source port** (Forrásport) - Az ügyfélen az a port, amelyet továbbítani szeretne. Például **9876**.

   * **Cél** – a HDInsight-fürthöz tartozó SSH-címet. Például: **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** (Dinamikus) - Lehetővé teszi a dinamikus SOCKS proxy útválasztást.

     ![Putty konfigurációs bújtatási beállítások](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. A beállítások hozzáadásához válassza a **Hozzáadás** lehetőséget, majd kattintson a **Megnyitás** lehetőségre egy SSH-kapcsolatok megnyitásához.

1. Ha a rendszer kéri, jelentkezzen be a kiszolgálóra.

## <a name="use-the-tunnel-from-your-browser"></a>Az alagút használata a böngészőben

> [!IMPORTANT]  
> Az ebben a szakaszban szereplő lépések a Mozilla FireFox böngészőt használják, mivel ugyanazokat a proxybeállításokat biztosítja az összes platformon. Más modern böngészők, például a Google Chrome, olyan bővítményt igényelhetnek, mint például a FoxyProxy, hogy működjenek az alagúton.

1. Konfigurálja a böngészőt a **localhost** és az alagút a **SOCKS V5** proxyként való létrehozásakor használt port használatára. A Firefox beállításai így néznek ki. Ha a 9876-nál eltérő portot használt, módosítsa a portot a használthoz:

    ![a Firefox böngésző proxybeállításait](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > A **távoli DNS** lehetőség kiválasztásával a rendszer a DNS-kérelmeket a HDInsight-fürt használatával oldja fel. Ez a beállítás a fürt fő csomópontjának használatával oldja fel a DNS-t.

2. Ellenőrizze, hogy az alagút működik-e úgy, hogy [https://www.whatismyip.com/](https://www.whatismyip.com/)meglátogat egy webhelyet, például:. A visszaadott IP-címet a Microsoft Azure adatközpontnak kell használnia.

## <a name="verify-with-ambari-web-ui"></a>Ellenőrzés Ambari webes felhasználói felülettel

A fürt létrejötte után a következő lépésekkel ellenőrizheti, hogy elérhető-e a szolgáltatás webes felülete a Ambari web használatával:

1. A böngészőben nyissa `http://headnodehost:8080`meg a következőt:. A `headnodehost` rendszer az alagúton keresztül továbbítja a-címeket a fürthöz, és az Ambari-on futó fő csomópontra oldja fel. Ha a rendszer kéri, adja meg a fürt rendszergazdai felhasználónevét és jelszavát. A Ambari webes felhasználói felülete másodszor is kérheti a kérést. Ha igen, adja meg újra az adatokat.

   > [!NOTE]  
   > Ha a `http://headnodehost:8080` címeket a fürthöz való csatlakozáshoz használja, az alagúton keresztül csatlakozik. A kommunikáció a HTTPS-kapcsolat helyett az SSH-alagút használatával védett. Ha HTTPS- `https://clustername.azurehdinsight.net`kapcsolaton keresztül szeretne csatlakozni az interneten keresztül `clustername` , akkor a a (z), ahol a a fürt neve.

2. A Ambari webes felhasználói felületén válassza a HDFS lehetőséget a lap bal oldalán található listából.

    ![Apache Ambari hdfs szolgáltatás kiválasztva](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Ha megjelenik a HDFS szolgáltatás adatai, válassza a **gyors hivatkozások**lehetőséget. Megjelenik a fürt fő csomópontjainak listája. Válassza ki az egyik fő csomópontot, majd válassza a **NameNode felhasználói felület**lehetőséget.

    ![A gyors menü kibontott képe](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Ha a __gyors hivatkozások__lehetőséget választja, akkor a várakozási mutatót is elérheti. Ez az állapot akkor fordulhat elő, ha lassú internetkapcsolattal rendelkezik. Várjon egy percet vagy kettőt, amíg az adatok beérkeznek a kiszolgálóról, majd próbálja megismételni a listát.
    >
    > Előfordulhat, hogy a **gyors hivatkozások** menü egyes bejegyzéseinek kivágása a képernyő jobb oldalán lehetséges. Ha igen, bontsa ki a menüt az egérrel, és a jobbra mutató nyílra kattintva görgessen a képernyő jobb oldalára, és tekintse meg a menü hátralévő részét.

4. A következő képhez hasonló oldal jelenik meg:

    ![A Hadoop NameNode felhasználói felületének képe](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Figyelje meg az oldal URL-címét; a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`következőhöz hasonlónak kell lennie:. Ez az URI a csomópont belső teljesen minősített tartománynevét (FQDN) használja, és csak SSH-alagút használata esetén érhető el.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre és használhat egy SSH-alagutat, tekintse meg a következő dokumentumot a Ambari használatának egyéb módjaihoz:

* [HDInsight-fürtök kezelése az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
