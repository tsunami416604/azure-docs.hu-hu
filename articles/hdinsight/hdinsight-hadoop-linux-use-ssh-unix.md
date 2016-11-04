---
title: SSH-kulcsok használata a Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben | Microsoft Docs
description: " A Linux-alapú HDInsight a Secure Shell (SSH) segítségével érhető el. Ez a dokumentum információt nyújt az SSH és a HDInsight együttes használatáról Linux-, Unix- vagy OS X-ügyfeleken."
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr

---
# Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben
> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

A [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) lehetővé teszi, hogy egy parancssori felülettel távolról hajtson végre műveleteket a Linux-alapú HDInsight-fürtökön. Ez a dokumentum információt nyújt az SSH és a HDInsight együttes használatáról Linux-, Unix- vagy OS X-ügyfeleken.

> [!NOTE]
> A cikkben ismertetett eljárásokhoz Linux-, Unix- vagy OS X-ügyfelet kell használnia. Ezek a lépések Windows-alapú ügyfélen is végrehajthatók, ha telepített olyan csomagot, amely biztosítja az `ssh` és az `ssh-keygen` parancsot, ilyen például a [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Windowson futó Ubuntu Bash-környezete) című cikkben ismertetett Bash.
> 
> Ha a Windows-alapú ügyfélen nem lett telepítve az SSH, kövesse a [Use SSH with Linux-based HDInsight (Hadoop) from Windows](hdinsight-hadoop-linux-use-ssh-windows.md) (Az SSH és a Linux-alapú HDInsight (Hadoop) együttes használata a Windowsban) című útmutató lépéseit a PuTTY telepítésével és használatával kapcsolatos információkért.
> 
> 

## Előfeltételek
* **ssh-keygen** és **ssh** Linux-, Unix- és OS X-ügyfelekhez. Ezeket a segédprogramokat általában az operációs rendszer biztosítja, vagy a csomagkezelő rendszeren keresztül érhetők el.
* Egy HTML5-támogatással rendelkező modern webböngésző.

VAGY

* [Azure parancssori felület (CLI)](../xplat-cli-install.md).
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## Mi az az SSH?
Az SSH egy olyan eszköz, amellyel beléphet egy távoli kiszolgálóra, és távolról parancsokat hajthat rajta végre. Az SSH a Linux-alapú HDInsight segítségével titkosított kapcsolatot hoz létre a fürt átjárócsomópontjával, és megjelenít egy parancssort, amelybe be lehet írni a parancsokat. A parancsok végrehajtására közvetlenül a kiszolgálón kerül sor.

### SSH-felhasználónév
Az SSH-felhasználónév az a név, amelyet a HDInsight-fürtön a hitelesítéshez használ. Amikor a fürt létrehozása során megad egy SSH-felhasználónevet, ez a felhasználó a fürt összes csomópontján létrejön. Miután a fürt létrejött, ezzel a felhasználónévvel csatlakozhat a HDInsight-fürt átjárócsomópontjaihoz. Ezután az átjárócsomópontokról csatlakozhat az egyes munkavégző csomópontokhoz.

### SSH-jelszó vagy nyilvános kulcs
Az SSH-felhasználó jelszót vagy nyilvános kulcsot is használhat a hitelesítéshez. A jelszó egy Ön által megadott szöveges karakterlánc, míg a nyilvános kulcs egy kriptografikus kulcspár része, amely az Ön egyedi azonosítására szolgál.

A kulcs biztonságosabb, mint a jelszó, azonban a kulcs létrehozásához további lépések szükségesek, és a kulcsot tartalmazó fájlokat biztonságos helyen kell tárolni. Ha bárki hozzáfér a kulcsfájlokhoz, utána a fiókjához is hozzáférhet. Ha a kulcsfájlok elvesznek, nem fog tudni belépni a fiókjába.

Egy kulcspár egy nyilvános kulcsból (amelyet a rendszer elküld a HDInsight-kiszolgálóra) és egy titkos kulcsból (amelyet az ügyfélszámítógép tárol) áll. Amikor az SSH-val a HDInsight-kiszolgálóhoz csatlakozik, az SSH-ügyfél a számítógépén található titkos kulccsal hitelesíti a kiszolgálót.

## SSH-kulcs létrehozása
Támaszkodjon az alábbi információkra, ha a fürtön SSH-kulcsokat szeretne használni. Ha inkább jelszó használatát tervezi, kihagyhatja ezt a szakaszt.

1. Nyisson meg egy terminál-munkamenetet, és az alábbi parancs használatával nézze meg, hogy rendelkezik-e létező SSH-kulcsokkal:
   
        ls -al ~/.ssh
   
    Nézze meg, hogy szerepelnek-e a következő fájlok a könyvtárlistában. Ezek a nyilvános SSH-kulcsok gyakori nevei.
   
   * id\_dsa.pub
   * id\_ecdsa.pub
   * id\_ed25519.pub
   * id\_rsa.pub
2. Ha nem szeretné a létező fájlokat használni, vagy nincsenek SSH-kulcsai, az alábbi megadásával hozhat létre egy új fájlt:
   
        ssh-keygen -t rsa
   
    A rendszer a következő információkat fogja kérni:
   
   * A fájl helye – az alapértelmezett hely a ~/.ssh/id\_rsa.
   * Egy hozzáférési kód – a rendszer ennek az ismételt megadását fogja kérni.
     
     > [!NOTE]
     > Határozottan javasoljuk, hogy a kulcshoz egy biztonságos hozzáférési kódot használjon. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.
     > 
     > 
     
     A parancs futtatásának befejeztével két új fájl jön létre: a titkos kulcs (például **id\_rsa**) és a nyilvános kulcs (például **id\_rsa.pub**).

## Linux-alapú HDInsight-fürt létrehozása
Egy Linux-alapú HDInsight-fürt létrehozásakor meg kell adnia az előzőleg létrehozott nyilvános kulcsot. Linux-, Unix- vagy OS X-ügyfelekről kétféle módon hozhat létre HDInsight-fürtöt:

* **Azure Portal** – A fürt létrehozásához egy webes portált használ.
* **Azure parancssori felület (CLI) Mac, Linux és Windows rendszerekhez** – A fürtöt parancssori parancsok segítségével hozza létre.

Mindkét módszerhez egy jelszó vagy egy nyilvános kulcs megadása szükséges. A Linux-alapú HDInsight-fürtök létrehozására vonatkozó teljes körű információkat a [Provision Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú HDInsight-fürtök kiépítése) című témakör tartalmazza.

### Azure Portal
Amikor az [Azure Portal][preview-portal] segítségével hoz létre egy Linux-alapú HDInsight-fürtöt, meg kell adnia egy **SSH USER NAME** (SSH-FELHASZNÁLÓNÉV) megadása szükséges, valamint választania kell egy **PASSWORD** (JELSZÓ) vagy egy **SSH PUBLIC KEY** (SSH NYILVÁNOS KULCS) megadása között.

Ha az **SSH PUBLIC KEY** megadását választja, a nyilvános kulcsot (amit a **.pub** kiterjesztésű fájl tartalmaz) bemásolhatja az **SSH PublicKey** mezőbe, vagy a **Select a file** (Jelöljön ki fájlt) lehetőséggel megkeresheti és kiválaszthatja a nyilvános kulcs fájlját.

![A nyilvános kulcsot kérő űrlap képe](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [!NOTE]
> A kulcsfájl egy egyszerű szövegfájl. A tartalma az alábbihoz hasonlóan néz ki:
> 
> ```
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```
> 
> 

Bejelentkezési adatokat hoz létre az adott felhasználó számára az Ön által megadott jelszó vagy nyilvános kulcs használatával.

### Azure parancssori felület Mac, Linux és Windows rendszerekhez
Az [Azure parancssori felület (CLI) Mac, Linux és Windows rendszerekhez](../xplat-cli-install.md) lehetővé teszi, hogy az `azure hdinsight cluster create` paranccsal egy új fürtöt hozzon létre.

További információk a parancs használatáról: [Provision Hadoop Linux clusters in HDInsight using custom options](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök kiépítése a HDInsightban egyéni beállításokkal).

## Csatlakozás egy Linux-alapú HDInsight-fürthöz
Egy terminál-munkamenetben az SSH paranccsal, a cím és a felhasználónév megadásával csatlakozhat az átjárócsomóponthoz:

* **SSH-cím** – A fürtökhöz kétféle címmel lehet csatlakozni az SSH segítségével:
  
  * **Csatlakozás az átjárócsomóponthoz**: a fürt neve, amelyet az **-ssh.azurehdinsight.net** karakterlánc követ. Például: **mycluster-ssh.azurehdinsight.net**.
  * **Csatlakozás az élcsomóponthoz**: ha a fürt az R Server a HDInsight-on, a fürt egy élcsomópontot is tartalmazni fog, amely az **RServer.CLUSTERNAME.ssh.azurehdinsight.net** használatával érhető el, ahol a **CLUSTERNAME** a fürt nevét jelöli.
* **Felhasználónév** – A fürt létrehozásakor megadott SSH-felhasználónév.

A következő példa **me** felhasználóként csatlakozik a **mycluster** elsődleges átjárócsomóponthoz:

    ssh me@mycluster-ssh.azurehdinsight.net

Ha a felhasználói fiókhoz jelszót használt, a rendszer elkéri a jelszót.

Ha egy hozzáférési kóddal védett SSH-kulcsot használt, a rendszer a hozzáférési kódot kéri el. Más esetekben az SSH megpróbálja automatikusan elvégezni a hitelesítést az ügyfélen található helyi titkos kulcsok egyikével.

> [!NOTE]
> Ha az SSH nem tudja automatikusan kiválasztani a helyes titkos kulcsot a hitelesítéshez, használja az **-i** paramétert, és adja meg a titkos kulcs elérési útját. Az alábbi példa a `~/.ssh/id_rsa` helyről tölti be a titkos kulcsot:
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`
> 
> 

Ha az átjárócsomópont címének megadásával csatlakozik, és nincs megadva a port, az SSH alapértelmezés szerint a 22-es portot használja, és ezzel a HDInsight-fürt elsődleges átjárócsomópontjával létesít kapcsolatot. A 23-as port használata esetén a másodlagos átjárócsomóponthoz fog csatlakozni. Az átjárócsomópontokkal kapcsolatos további információkat lásd: [Availability and reliability of Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (A Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsightban).

### Csatlakozás a munkavégző csomópontokhoz
A munkavégző csomópontok az Azure adatközponton kívülről nem érhetők el közvetlenül, de SSH-val elérhetők a fürt átjárócsomópontjából.

Ha a felhasználói fiók hitelesítéséhez SSH-kulcsot használ, az alábbi lépéseket kell végrehajtania az ügyfélen:

1. Egy szövegszerkesztővel nyissa meg a `~/.ssh/config` fájlt. Ha a fájl nem létezik, létrehozhatja a terminálban a `touch ~/.ssh/config` beírásával.
2. Adja a következőket a fájlhoz. Cserélje le a *CLUSTERNAME* kifejezést a HDInsight-fürt nevére.
   
        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes
   
    Ez konfigurálja az SSH-ügynöktovábbítást a HDInsight-fürt számára.
3. Tesztelje az SSH-ügynöktovábbítást a terminálból a következő parancs segítségével:
   
        echo "$SSH_AUTH_SOCK"
   
    A kapott információ az alábbihoz hasonlóan fog kinézni:
   
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
   
    Ha nem kap semmit, az azt jelenti, hogy az **ssh-agent** nem fut. Olvassa el az operációs rendszere dokumentációjában az **ssh-agent** telepítésének és konfigurálásának részletes lépéseit, vagy tekintse meg a [Using ssh-agent with ssh](http://mah.everybody.org/docs/ssh) (Az ssh-agent és az ssh együttes használata) című cikket.
4. Ha meggyőződött róla, hogy az **ssh-agent** fut, a következő segítségével adja hozzá a titkos SSH-kulcsot az ügynökhöz:
   
        ssh-add ~/.ssh/id_rsa
   
    Ha a titkos kulcsot egy másik fájl tárolja, a `~/.ssh/id_rsa` részt cserélje ki a fájl elérési útjára.

A következő lépésekkel csatlakozzon a fürt munkavégző csomópontjaihoz.

> [!IMPORTANT]
> Ha a felhasználói fiók hitelesítéséhez SSH-kulcsot használ, az előző lépések végrehajtásával ellenőrizze, hogy az ügynöktovábbítás működik-e.
> 
> 

1. Az előzőleg leírtak szerint csatlakozzon az SSH segítségével a HDInsight-fürthöz.
2. A csatlakozás után a következő paranccsal kérje le a fürtben található csomópontok listáját. Cserélje le az *ADMINPASSWORD* elemet a fürt rendszergazdai fiókjának jelszavára. Cserélje le a *CLUSTERNAME* elemet a fürt nevére.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    Ez JSON formátumban ad meg információkat a fürt csomópontjaihoz, beleértve a `host_name` elemet is, amely tartalmazza az egyes csomópontok teljes tartománynevét (FQDN-jét). A következőkben egy példa látható a **curl** parancs által visszaadott `host_name` bejegyzésre:
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"
3. Amikor már van egy listája azokról a munkavégző csomópontokról, amelyekhez csatlakozni szeretne, használja az SSH-munkamenet következő parancsát a kiszolgálón a munkavégző csomópont csatlakoztatásához:
   
        ssh USERNAME@FQDN
   
    Cserélje le a *USERNAME* karakterláncot az SSH felhasználónevére, és az *FQDN* karakterláncot a munkavégző csomópont FQDN-jére. Például: `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
   
   > [!NOTE]
   > Ha jelszóval hitelesíti az SSH-munkamenetet, a rendszer ismét elkéri a jelszót. Ha SSH-kulcsot használ, a kapcsolat további kérések nélkül létrejön.
   > 
   > 
4. A munkamenet létrehozása után a terminál parancssora `username@hn#-clustername` értékről `username@wk#-clustername` értékre változik annak jelzéséhez, hogy a munkavégző csomópont csatlakoztatva van. Innentől a futtatott parancsok a munkavégző csomóponton fognak futni.
5. Amikor már nem szeretne több műveletet végrehajtani a munkavégző csomóponton, az `exit` paranccsal zárja be a munkamenetét. Ezzel visszatér a `username@hn#-clustername` parancssorhoz.

## További fiókok hozzáadása
1. Hozzon létre egy új nyilvános és egy új titkos kulcsot az új felhasználói fiókhoz az [SSH-kulcs létrehozása](#create-an-ssh-key-optional) szakaszban leírt módon.
   
   > [!NOTE]
   > A titkos kulcsot vagy egy olyan ügyfélen kell létrehozni, amellyel a felhasználó a fürthöz fog csatlakozni, vagy pedig a létrehozása után biztonságosan továbbítani kell egy ilyen ügyfélre.
   > 
   > 
2. Egy SSH-munkamenetből adjon új felhasználót a fürthöz a következő paranccsal:
   
        sudo adduser --disabled-password <username>
   
    Ez létrehoz egy új felhasználói fiókot, de letiltja a jelszó-hitelesítést.
3. Hozza létre a kulcs tárolására szolgáló könyvtárat és fájlokat a következő parancsokkal:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys
4. Amikor megnyílik a nano szerkesztő, másolja be a nyilvános kulcs a tartalmát az új felhasználói fiókhoz. A **Ctrl-X** billentyűparanccsal mentse a fájlt, és lépjen ki a szerkesztőből.
   
    ![A nano szerkesztő képe egy példakulccsal](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)
5. A következő paranccsal módosítsa az .ssh mappa és a benne található tartalmak tulajdonosát az új felhasználói fiókra:
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh
6. Mostantól az új felhasználói fiókkal és titkos kulccsal is tud hitelesítést végezni a kiszolgálón.

## <a id="tunnel"></a>SSH-alagútkezelés
Az SSH-val helyi kérések, például webes kérések bújtatását is elvégezheti a HDInsight-fürthöz. A rendszer ilyenkor úgy irányítja a kérést a kért erőforráshoz, mintha a HDInsight-fürt átjárócsomópontból származna.

> [!IMPORTANT]
> Az SSH-alagút előfeltétele annak, hogy el tudja érni néhány Hadoop-szolgáltatás webes felhasználói felületét. Például a Feladatelőzmények felhasználói felület és az Erőforrás-kezelő felhasználói felület is csak SSH-alagúton keresztül érhető el.
> 
> 

Az SSH-alagutak létrehozásáról és használatáról szóló további információkért lásd: [Use SSH Tunneling to access Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie, and other web UI's](hdinsight-linux-ambari-ssh-tunnel.md) (Az Ambari webes felhasználói felület, a ResourceManager, a JobHistory, a NameNode, az Oozie és egyéb webes felhasználói felületek elérése SSH-alagútkezeléssel).

## Következő lépések
Most, hogy megismerkedett az SSH-kulccsal végzett hitelesítésről, arról is tájékozódhat, hogyan használhatja a MapReduce-t a HDInsight-alapú Hadoopban.

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Sep16_HO4-->


