---
title: Az SSH használata a Hadoop–Azure HDInsight rendszerben
description: A HDInsight a Secure Shell (SSH) segítségével érhető el. Ez a dokumentum a HDInsighthoz történő csatlakozásról ad információt Windows, Linux, Unix vagy macOS rendszerű ügyfelekről érkező ssh és scp parancsok használata esetén.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 31e85876d60ae6fcd8f3b29633506d698a323acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272434"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val

Ismerje meg, hogyan csatlakozhat biztonságos [rendszerhéj (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) segítségével az Apache Hadoophoz az Azure HDInsight szolgáltatásban. A virtuális hálózaton keresztüli csatlakozásról az [Azure HDInsight virtuális hálózati architektúrájában](./hdinsight-virtual-network-architecture.md) és [az Azure HDInsight-fürtök virtuális hálózati üzembe helyezésének megtervezése című témakörben](./hdinsight-plan-virtual-network-deployment.md)talál további információt.

Az alábbi táblázat az Okates-ügyféllel a HDInsighthoz való csatlakozáshoz szükséges cím- és portadatokat tartalmazza:

| Cím | Port | A következőhöz csatlakozik: |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Elsődleges átjárócsomópont |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Másodlagos átjárócsomópont |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | peremhálózati csomópont (ML-szolgáltatások a HDInsight-on) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | élcsomópont (bármely más fürttípus, ha létezik peremcsomópont) |

Cserélje le a `<clustername>` elemet a fürt nevére. Cserélje le az `<edgenodename>` elemet az élcsomópont nevére.

Ha a fürt élcsomópontot tartalmaz, ajánlott __az élcsomóponthoz mindig__ az SSH segítségével kapcsolódni. Az átjárócsomópontok olyan szolgáltatásokat futtatnak, amelyek kritikus fontosságúak a Hadoop állapota szempontjából. Az élcsomópont csak azt futtatja, amit Ön telepít rá. Az élcsomópontok használatával kapcsolatban további információért lásd: [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Amikor első alkalommal csatlakozik a HDInsighthoz, az SSH-ügyfél olyan figyelmeztetést jeleníthet meg, amely szerint a gazdaszámítógép nem hitelesíthető. Amikor a rendszer erre felkéri, válassza a „yes” (igen) lehetőséget ahhoz, hogy a gazdaszámítógép felvegye az SSH-ügyfél megbízható kiszolgálókat tartalmazó listába.
>
> Ha korábban már csatlakozott egy ilyen nevű kiszolgálóhoz, előfordulhat, hogy a rendszer figyelmezteti rá, hogy a tárolt gazdaszámítógép-kulcs nem egyezik meg a kiszolgáló gazdaszámítógép-kulcsával. A kiszolgálónévhez tartozó, meglévő bejegyzés eltávolításához tekintse meg az SSH-ügyfél dokumentációját.

## <a name="ssh-clients"></a>SSH-ügyfelek

Az `ssh` és `scp` parancs elérhető a Linux, Unix és macOS rendszerekben. Az `ssh`-ügyfelet általában arra használják, hogy távoli parancssori munkamenetet hozzon létre Linux vagy Unix rendszerben. Az `scp`-ügyfél segítségével biztonságosan másolhat fájlokat a saját ügyfél és a távoli rendszer között.

A Microsoft Windows alapértelmezés szerint nem telepít SSH-ügyfeleket. Az `ssh`- és az `scp`-ügyfél az alábbi csomagokban érhető el a Windows rendszerhez:

* [OpenSSH ügyfél](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse). Ez az ügyfél egy opcionális funkció, amelyet a Windows 10 Fall Creators frissítés vezet be.

* [Bash az Ubuntu-n a Windows 10 rendszeren](https://docs.microsoft.com/windows/wsl/about).

* [Az Azure Cloud Shell](../cloud-shell/quickstart.md). A Cloud Shell bash környezetet biztosít a böngészőben.

* [Git](https://git-scm.com/).

Vannak is több grafikus SSH ügyfelek, mint a [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) és [MobaXterm](https://mobaxterm.mobatek.net/). Bár ezek az ügyfelek használhatók a HDInsighthoz történő kapcsolódáshoz, a kapcsolódás folyamata más, mint az `ssh` segédprogram használatakor. További információt a használt grafikus ügyfél dokumentációjában talál.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Hitelesítés: SSH-kulcsok

Az [SSH-kulcsok nyilvános kulcsú titkosítást](https://en.wikipedia.org/wiki/Public-key_cryptography) használnak az SSH-munkamenetek hitelesítéséhez. Az SSH-kulcsok biztonságosabbak a jelszavaknál, és egyszerű módszert kínálnak a Hadoop-fürt biztonságos elérésére.

Ha az SSH-fiókja kulccsal van védve, az ügyfélnek meg kell adnia az egyező titkos kulcsot, amikor csatlakozik:

* A legtöbb ügyfél konfigurálható __alapértelmezett kulcs__ használatára. Az `ssh`-ügyfél például a titkos kulcsot a `~/.ssh/id_rsa` helyen keresi Linux- és Unix-környezetekben.

* Megadhatja __egy titkos kulcs elérési útját__. Az `ssh`-ügyfél esetében az `-i` paraméterrel adható meg a titkos kulcs elérési útja. Például: `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Ha __több titkos kulccsal__ rendelkezik, amelyeket különböző kiszolgálókhoz használ, fontolja meg az olyan segédprogramok használatát, mint például az [ssh-ügynök (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). Az `ssh-agent` segédprogram segítségével automatikusan választhatja ki a kulcsot az SSH-munkamenet megkezdéséhez.

> [!IMPORTANT]  
> Ha jelszóval védi a titkos kulcsot, a kulcs használatakor be kell írnia a jelszót. Az `ssh-agent` és hasonló segédprogramokkal a kényelmes használat érdekében gyorsítótárazhatja a jelszót.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az `ssh-keygen` paranccsal hozhat létre nyilvános- és titkoskulcs-fájlokat. A következő parancs egy 2048 bites RSA-kulcspárt hoz létre, amely a HDInsighttal használható:

    ssh-keygen -t rsa -b 2048

A kulcs létrehozási folyamata során a rendszer információt kér. Például a kulcsok tárolási helyét kell megadnia, vagy azt, hogy használ-e jelszót. A folyamat befejezése után két fájl jön létre; egy nyilvános kulcs és egy titkos kulcs.

* A __nyilvános kulccsal__ hozható létre a HDInsight-fürt. A nyilvános kulcs kiterjesztése `.pub`.

* A __titkos kulccsal__ hitelesíthető az ügyfél a HDInsight-fürtön.

> [!IMPORTANT]  
> A kulcsokat jelszóval védheti. Ez a jelszó lényegében egy kód a titkos kulcson. Ilyen esetekben még ha valaki meg is szerzi a titkos kulcsát, a jelszóra is szüksége van a kulcs használatához.

### <a name="create-hdinsight-using-the-public-key"></a>HDInsight létrehozása a nyilvános kulccsal

| Létrehozási metódus | A nyilvános kulcs használata |
| ------- | ------- |
| Azure portál | Törölje a jelet __a Fürt bejelentkezési jelszavának használata az SSH-hoz jelölőnégyzetből,__ majd válassza a __Nyilvános kulcs__ ssh-hitelesítési típusként lehetőséget. Végül válassza ki a nyilvános kulcs fájlját, vagy illessze be a fájl szöveges tartalmát a __Nyilvános SSH-kulcs__ mezőbe.</br>![Nyilvános SSH-kulcs párbeszédpanel a HDInsight-fürt létrehozásakor](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | Használja `-SshPublicKey` a [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag paraméterét, és adja át a nyilvános kulcs tartalmát karakterláncként.|
| Azure CLI | Használja `--sshPublicKey` az [az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) parancs paraméterét, és adja át a nyilvános kulcs tartalmát karakterláncként. |
| Resource Manager-sablon | Az SSH-kulcsok sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) fájlban lévő `publicKeys` elemmel illeszthetők be a kulcsok az Azure-ba a fürt létrehozásakor. |

## <a name="authentication-password"></a>Hitelesítés: Jelszó

Az SSH-fiókok jelszóval védhetők. Amikor ssh-val csatlakozik a HDInsighthoz, a rendszer kéri a jelszó megadását.

> [!WARNING]  
> A Microsoft nem javasolja jelszavas hitelesítés használatát az SSH-hoz. A jelszavakat ki lehet találni, és védtelenek a találgatásos támadásokkal szemben. Ehelyett azt javasoljuk, hogy használjon [SSH-kulcsokat a hitelesítéshez](#sshkey).

> [!IMPORTANT]  
> Az SSH-fiók jelszava 70 nappal a HDInsight-fürt létrehozása után jár le. Ha a jelszó lejár, a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentumban foglaltak szerint változtathatja meg.

### <a name="create-hdinsight-using-a-password"></a>HDInsight létrehozása jelszóval

| Létrehozási metódus | Jelszó megadása |
| --------------- | ---------------- |
| Azure portál | Alapértelmezés szerint az SSH-felhasználói fióknak ugyanaz a jelszava, mint a fürt bejelentkezési fiókjának. Másik jelszó használatához törölje a jelet __az SSH fürtbejelentkezési jelszavának használata__jelölőnégyzetből, majd írja be a jelszót az __SSH jelszó__ mezőbe.</br>![SSH-jelszó párbeszédpanel a HDInsight-fürt létrehozásakor](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | Használja `--SshCredential` a [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag paraméterét, `PSCredential` és adja át az SSH felhasználói fiók nevét és jelszavát tartalmazó objektumot. |
| Azure CLI | Használja `--sshPassword` az [az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) parancs paraméterét, és adja meg a jelszó értékét. |
| Resource Manager-sablon | A jelszavak sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) fájlban lévő `linuxOperatingSystemProfile` elemmel illeszthető be az SSH-fióknév és -jelszó az Azure-ba a fürt létrehozásakor.|

### <a name="change-the-ssh-password"></a>Az SSH-jelszó módosítása

Az SSH-felhasználói fiók jelszavának módosításával kapcsolatos információért tekintse meg a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentum __Jelszavak módosítása__ szakaszát.

## <a name="authentication-domain-joined-hdinsight"></a>Hitelesítési tartomány csatlakozott a HDInsighthoz

Ha __tartományhoz csatlakozott HDInsight-fürtöt__használ, az `kinit` SSH helyi felhasználóhoz való csatlakozás után a parancsot kell használnia. Ez a parancs tartományi felhasználónevet és jelszót kér, és hitelesíti a munkamenetet a fürttel társított Azure Active Directory-tartománnyal.

A Kerberos-hitelesítést engedélyezheti minden tartományhoz csatlakozó csomóponton (például főcsomópont, peremcsomópont) a tartományi fiók használatával. Ehhez szerkessze az sshd config fájlt:

```bash
sudo vi /etc/ssh/sshd_config
```

távolítsa el a megjegyzéseket, a `KerberosAuthentication` értékét pedig állítsa a következőre: `yes`

```bash
sudo service sshd restart
```

A `klist` parancs segítségével ellenőrizze, hogy a Kerberos-hitelesítés sikeres volt-e.

További információkat itt talál: [Tartományhoz csatlakoztatott HDInsight konfigurálása](./domain-joined/apache-domain-joined-configure.md).

## <a name="connect-to-nodes"></a>Csatlakozás csomópontokhoz

A fő csomópontok és a peremhálózati csomópont (ha van ilyen) a 22-es és a 23-as porton keresztül érhető el az interneten keresztül.

* Az __átjárócsomópontokhoz__ való csatlakozás során az elsődleges átjárócsomóponthoz való csatlakozáshoz használja a __22-es__ portot, a másodlagos átjárócsomóponthoz való csatlakozáshoz pedig a __23-as__ portot. A használandó teljes tartománynév a `clustername-ssh.azurehdinsight.net`, ahol a `clustername` a fürt neve.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* Az __élcsomóponthoz__ való csatlakozáskor használja a 22-es portot. A teljes tartománynév az `edgenodename.clustername-ssh.azurehdinsight.net`, ahol az `edgenodename` az élcsomópont létrehozásakor megadott név. A `clustername` a fürt neve.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Az előző példák azt feltételezik, hogy jelszavas hitelesítést használ, vagy automatikus tanúsítványalapú hitelesítés történik. Ha SSH-kulcspárt használ a hitelesítéshez, és a tanúsítvány használata nem automatikus, az `-i` paraméterrel adja meg a titkos kulcsot. Például: `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

A csatlakozást követően a kérdés megváltozik, jelezve az SSH felhasználónevét és azt a csomópontot, amelyhez csatlakozik. Ha például az `sshuser` felhasználóként csatlakozik az elsődleges átjárócsomóponthoz, a parancssor az `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Csatlakozás feldolgozó- és Apache Zookeeper-csomópontokhoz

A munkavégző csomópontok és zookeeper csomópontok nem érhetők el közvetlenül az internetről. hanem a fürt átjáró- vagy élcsomópontjain keresztül érhetők el. A következő általános lépésekkel csatlakozhat más csomópontokhoz:

1. Az SSH-val csatlakozzon egy átjáró- vagy élcsomóponthoz:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Az átjáró- vagy élcsomópont felé fennálló SSH-kapcsolatból használja az `ssh` parancsot a fürt egyik munkavégző csomópontjához való csatlakozáshoz:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    A csomópontnevek listájának beolvasásához tekintse meg a [HDInsight kezelése az Apache Ambari REST API-dokumentum használatával](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) című témakört.

Ha az SSH-fiókot __jelszó__ védi, a kapcsolódáshoz adja meg a jelszót.

Ha az SSH-fiókot __SSH-kulcsok__ védik, győződjön meg róla, hogy az SSH-továbbítás engedélyezve van az ügyfélen.

> [!NOTE]  
> A fürtben lévő összes csomópont közvetlen elérésének másik módja, ha a HDInsightot Azure virtuális hálózatra telepíti. Ezután csatlakoztathatja a távoli gépet ugyanehhez a virtuális hálózathoz, és közvetlenül érheti el a fürtben lévő összes csomópontot.
>
> További információ: [A HDInsight virtuális hálózatának megtervezése.](hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-ssh-agent-forwarding"></a>SSH-ügynöktovábbítás konfigurálása

> [!IMPORTANT]  
> A következő lépések Linux vagy UNIX-alapú rendszert feltételeznek, és a Windows 10-en futó Bash-környezet esetén működnek. Ha a lépések nem működnek a rendszerén, lehet, hogy át kell tekintenie az SSH-ügyfél dokumentációját.

1. Egy szövegszerkesztővel nyissa meg a `~/.ssh/config` fájlt. Ha a fájl nem létezik, létrehozhatja a parancssoron az `touch ~/.ssh/config` karakterlánc beírásával.

2. Adja hozzá a következő szöveget az `config` fájlhoz.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Cserélje le a __Gazda__ információit azon csomópont címére, amelyet SSH-val csatlakoztat. Az előző példa az élcsomópontot használja. Ez a bejegyzés konfigurálja az SSH-ügynöktovábbítást az adott csomópont számára.

3. Tesztelje az SSH-ügynöktovábbítást a terminálból a következő parancs segítségével:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Ha semmit sem `ssh-agent` ad vissza, akkor nem fut. További tudnivalókért lásd az ügynök indítási szkriptjeire vonatkozó részt a [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Az ssh-agent és az ssh együttes használata) című cikkben vagy az SSH-ügyfél dokumentációját.

4. Miután ellenőrizte, hogy az **ssh-agent** fut, az alábbi módon adja hozzá az SSH személyes kulcsát az ügynökhöz:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Ha a titkos kulcsot egy másik fájl tárolja, a `~/.ssh/id_rsa` részt cserélje ki a fájl elérési útjára.

5. Csatlakozzon a fürt élcsomópontjához vagy átjárócsomópontjaihoz SSH-val. Ezután az SSH-paranccsal csatlakozzon egy feldolgozó vagy Zookeeper-csomóponthoz. A kapcsolat létrejön a továbbított kulccsal.

## <a name="copy-files"></a>Fájlok másolása

Az `scp` segédprogrammal fájlokat másolhat a fürt egyes csomópontjairól más csomópontokra. A következő paranccsal például a `test.txt` könyvtárat a helyi rendszerről az elsődleges átjárócsomópontra másolhatja:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Mivel nincs megadva elérési út a `:` után, a fájl az `sshuser` kezdőkönyvtárba kerül.

A következő parancs a `test.txt` fájlt az elsődleges átjárócsomóponton található `sshuser` kezdőkönyvtárból a helyi rendszerre másolja:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]  
> Az `scp` csak a fürt egyes csomópontjainak fájlrendszeréhez képes hozzáférni. Nem használható a fürt HDFS-kompatibilis tárolójában tárolt adatok eléréséhez.
>
> Ha egy SSH-munkamenetből kíván feltölteni egy használni kívánt erőforrást, használja az `scp` segédprogramot. Például töltsön fel egy Python-szkriptet, majd futtassa a szkriptet egy SSH-munkamenetből.
>
> Adatok közvetlenül a HDFS-kompatibilis tárolóba való betöltésével kapcsolatos információkat a következő dokumentumokban talál:
>
> * [HDInsight az Azure Storage használatával.](hdinsight-hadoop-use-blob-storage.md)
>
> * [HDInsight az Azure Data Lake Storage használatával.](hdinsight-hadoop-use-data-lake-store.md)

## <a name="next-steps"></a>További lépések

* [SSH-alagútkezelés használata a HDInsighttal](hdinsight-linux-ambari-ssh-tunnel.md)
* [Virtuális hálózat tervezése a HDInsight segítségével](hdinsight-plan-virtual-network-deployment.md)
* [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node)
