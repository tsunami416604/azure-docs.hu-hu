---
title: Az Azure HDInsight eléréséhez ssh-bújtatás használata
description: Ismerje meg, hogyan használhatja az SSH-alagutat a Linux-alapú HDInsight-csomópontokon tárolt webes erőforrások biztonságos böngészéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991353"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Az Apache Ambari webes felhasználói felületének, a JobHistory, a NameNode, az Apache Oozie és más ui-k eléréséhez SSH bújtatás használata

A HDInsight-fürtök az interneten keresztül biztosítják az Apache Ambari webes felhasználói felületének elérését, de egyes funkciók hoz egy SSH-alagutat. Például az Apache Oozie szolgáltatás webes felhasználói felülete nem érhető el az interneten ssh alagút nélkül.

## <a name="why-use-an-ssh-tunnel"></a>Miért érdemes SSH-alagutat használni?

Az Ambari több menüje csak SSH alagúton keresztül működik. Ezek a menük más csomóponttípusokon, például munkavégző csomópontokon futó webhelyekre és szolgáltatásokra támaszkodnak.

A következő webes felhasználói felülethez SSH-alagút szükséges:

* JobHistory (Feladattörténete)
* Névnode
* Szálhalmok
* Oozie webes felhasználói felület
* HBase főkiszolgáló és naplók felhasználói felülete

Ha parancsfájlműveletek segítségével szabja testre a fürtöt, a webszolgáltatást elérhetővé tett szolgáltatásokat vagy segédprogramokat SSH-alagútra van szükség. Ha például a Hue-t parancsfájlművelettel telepíti, ssh-alagutat kell használnia a Hue webes felhasználói felület eléréséhez.

> [!IMPORTANT]  
> Ha közvetlen hozzáféréssel rendelkezik a HDInsight-hoz egy virtuális hálózaton keresztül, nem kell SSH-alagutakat használnia. A HDInsight virtuális hálózaton keresztüli közvetlen elérésének például a [HDInsight csatlakoztatása a helyszíni hálózati dokumentumhoz](connect-on-premises-network.md) című témakörben található.

## <a name="what-is-an-ssh-tunnel"></a>Mi az az SSH alagút

[A Secure Shell (SSH) bújtatása](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) a helyi számítógép egyik portját a HDInsight egy főcsomópontjához köti. A helyi portra küldött forgalom egy SSH-kapcsolaton keresztül a főcsomóponthoz vezet. A kérelem úgy oldódik meg, mintha a főcsomópontról származna. A válasz ezután az alagúton keresztül visszairányítja a munkaállomásra.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](hdinsight-hadoop-linux-use-ssh-unix.md)

* SOCKS5 proxy használatára konfigurálható webböngésző.

    > [!WARNING]  
    > A Windows internetbeállításaiba beépített SOCKS proxytámogatás nem támogatja a SOCKS5-öt, és nem működik a jelen dokumentumban ismertetett lépésekkel. A következő böngészők a Windows proxybeállításaira támaszkodnak, és jelenleg nem működnek a jelen dokumentumban leírt lépésekkel:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > A Google Chrome a Windows proxybeállításaira is támaszkodik. A SOCKS5-öt támogató bővítményeket azonban telepítheti. Javasoljuk [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Alagút létrehozása az SSH paranccsal

A következő paranccsal ssh-alagutat hozhat létre a `ssh` paranccsal. Cserélje `sshuser` le egy SSH-felhasználóra a `CLUSTERNAME` HDInsight-fürthöz, és cserélje le a HDInsight-fürt nevét:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Ez a parancs olyan kapcsolatot hoz létre, amely a forgalmat a 9876-os helyi portra irányítja a fürthöz SSH-n keresztül. A következő lehetőségek közül választhat:

* **D 9876** - A helyi kikötő, amely a forgalmat az alagúton keresztül irányítja.
* **C** - Tömöríteni az összes adatot, mert a webes forgalom többnyire szöveg.
* **2** - Kényszerítse az SSH-t, hogy csak a protokoll 2-es verzióját próbálja meg.
* **q** - Csendes mód.
* **T** - Tiltsa le a pszeudo-tty allokációt, mivel csak egy portot továbbít.
* **n** - Megakadályozza az STDIN olvasását, mivel csak egy portot továbbít.
* **N** - Ne hajtson végre távoli parancsot, mivel csak egy portot továbbít.
* **f** - Fuss a háttérben.

A parancs befejezése után a helyi számítógépen a 9876-os portra küldött forgalom a fürtfőcsomópontra lesz irányítva.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Alagút létrehozása a PuTTY használatával

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) egy grafikus SSH kliens windows. Ha nem ismeri a PuTTY-t, olvassa el a [PuTTY dokumentációját.](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html) A következő lépésekkel hozzon létre egy SSH-alagutat a PuTTY használatával:

### <a name="create-or-load-a-session"></a>Munkamenet létrehozása vagy betöltése

1. Nyissa meg a PuTTY-t, és győződjön meg **arról,** hogy a Munkamenet a bal oldali menüben van kiválasztva. Ha már mentett egy munkamenetet, jelölje ki a munkamenet nevét a **Mentett munkamenetek** listából, és válassza a **Betöltés**lehetőséget.

1. Ha még nem rendelkezik mentett munkamenettel, adja meg a kapcsolat adatait:

    |Tulajdonság |Érték |
    |---|---|
    |Állomásnév (vagy IP-cím)|A HDInsight-fürt SSH-címe. Például: **mycluster-ssh.azurehdinsight.net**.|
    |Port|22|
    |Kapcsolattípus|SSH|

1. **Mentés** kiválasztása

    ![A HDInsight gittmunkamenetet hoz létre](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. A párbeszédpanel **bal oldalán** lévő Kategória csoportban bontsa ki a **Kapcsolat**, az **SSH**csomópontot, majd válassza az **Alagutak**lehetőséget.

1. Adja meg a következő információkat az **SSH-porttovábbítást vezérlő beállítások** űrlapról:

    |Tulajdonság |Érték |
    |---|---|
    |Forrásport|Az ügyfél továbbítandó portja. **Például: 9876**.|
    |Cél|A HDInsight-fürt SSH-címe. Például: **mycluster-ssh.azurehdinsight.net**.|
    |Dinamikus|Engedélyezi a dinamikus SOCKS proxy útválasztást.|

    ![PuTTY konfigurációs bújtatási beállításai](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. A beállítások hozzáadásához válassza a **Hozzáadás** lehetőséget, majd az SSH-kapcsolat megnyitásához válassza a **Megnyitás** gombot.

1. Amikor a rendszer kéri, jelentkezzen be a kiszolgálóra.

## <a name="use-the-tunnel-from-your-browser"></a>Az alagút használata a böngészőből

> [!IMPORTANT]  
> Az ebben a szakaszban leírt lépések a Mozilla FireFox böngészőt használják, mivel ugyanazokat a proxybeállításokat biztosítják minden platformon. Más modern böngészők, mint például a Google Chrome, szükség lehet egy kiterjesztés, mint a FoxyProxy dolgozni az alagútban.

1. Állítsa be a böngészőt úgy, hogy **a localhost-ot** és a **tunnel SOCKS v5** proxyként való létrehozásakor használt portot használja. Így néznek ki a Firefox beállításai. Ha a 9876-tól eltérő portot használt, módosítsa a portot a használt portra:

    ![firefox böngésző proxy beállításai](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > A **Távoli DNS** kiválasztása a HDInsight-fürt használatával feloldja a DNS-kérelmeket. Ez a beállítás a fürt főcsomópontja alapján oldja fel a DNS-t.

2. Ellenőrizze, hogy az alagút működik-e, ha felkeresi a webhelyet, például [https://www.whatismyip.com/](https://www.whatismyip.com/)a. A visszaadott IP-címet a Microsoft Azure adatközpontnak kell használnia.

## <a name="verify-with-ambari-web-ui"></a>Igazolás az Ambari webes felhasználói felületével

A fürt létrehozása után az alábbi lépésekkel ellenőrizze, hogy az Ambari webről hozzáférhet-e a szolgáltatás webes felhasználói felületéhez:

1. Nyissa meg a `http://headnodehost:8080` URL-címet a böngészőben. A `headnodehost` cím az alagúton keresztül kerül elküldésre a fürthöz, és feloldódik arra a fő csomópontra, amelyen az Ambari fut. Amikor a rendszer kéri, adja meg a fürt rendszergazdai felhasználónevét (rendszergazda) és jelszavát. Az Ambari webes felhasználói felülete másodszor is rákérdezhet. Ha igen, adja meg újra az információt.

   > [!NOTE]  
   > Amikor a `http://headnodehost:8080` címet használja a fürthöz való csatlakozáshoz, az alagúton keresztül csatlakozik. A kommunikáció https helyett az SSH-alagút használatával védett. Ha HTTPS protokollon keresztül `https://clustername.azurehdinsight.net`szeretne `clustername` csatlakozni az interneten keresztül, használja a használatát, ahol a fürt neve.

2. Az Ambari Web felhasználói felületén válassza a HDFS elemet a lap bal oldalán található listából.

    ![Apache Ambari hdfs szolgáltatás kiválasztva](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Amikor megjelennek a HDFS szolgáltatás adatai, válassza a **Gyorshivatkozások**lehetőséget. Megjelenik a fürtfőcsomópontok listája. Jelölje ki az egyik főcsomópontot, majd válassza a **NameNode felhasználói felületet.**

    ![Kép a Gyorshivatkozások menü kibontott](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Ha a Gyorshivatkozások lehetőséget __választja,__ várakozásjelzőt kaphat. Ez a feltétel akkor fordulhat elő, ha lassú az internetkapcsolat. Várjon egy-két percet, amíg az adatok a kiszolgálóról érkeznek, majd próbálkozzon újra a listával.
    >
    > A **Gyorshivatkozások** menü egyes bejegyzéseit a képernyő jobb oldala vághatja le. Ha igen, bontsa ki a menüt az egérrel, és a jobb nyílbillentyűvel görgetje jobbra a képernyőt a menü többi részének megtekintéséhez.

4. Az alábbihoz hasonló oldal jelenik meg:

    ![A Hadoop NameNode felhasználói felületének képe](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Figyelje meg az oldal URL-címét; hasonlónak kell `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`lennie a . Ez az URI a csomópont belső, teljesen minősített tartománynevét (FQDN) használja, és csak SSH-alagút használata esetén érhető el.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az SSH-alagutak létrehozását és használatát, tekintse meg az alábbi dokumentumban az Ambari használatának egyéb módjait:

* [HDInsight-fürtök kezelése az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
