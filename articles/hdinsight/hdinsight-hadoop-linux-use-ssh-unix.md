---
title: Az SSH használata a Hadoop–Azure HDInsight rendszerben
description: A HDInsight a Secure Shell (SSH) segítségével érhető el. Ez a dokumentum a HDInsighthoz történő csatlakozásról ad információt Windows, Linux, Unix vagy macOS rendszerű ügyfelekről érkező ssh és scp parancsok használata esetén.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: hadoop parancsok linux rendszerben, hadoop linux parancsok, hadoop macos, ssh hadoop, ssh hadoop fürt
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: a7ba66fae3f2a8426cc333cca109a4c6793d1da2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724323"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Csatlakozhat a HDInsight (az Apache Hadoop) SSH-val

Ismerje meg, hogyan használható [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) biztonságosan kapcsolódhat az Azure HDInsight az Apache Hadoop. 

A HDInsight használhatja a Linux (Ubuntu) rendszert a Hadoop-fürt csomópontjainak operációs rendszereként. Ha egy SSH-ügyfél használatával Linux rendszerű HDInsighthoz csatlakozik, az alábbi táblázatban látható cím-és portinformációkra lesz szükség:

| Cím | Port | A következőhöz csatlakozik: |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Határcsomópont (ML-szolgáltatások a HDInsighton) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Élcsomópont (bármely egyéb fürttípus, ha létezik élcsomópont) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Elsődleges átjárócsomópont |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Másodlagos átjárócsomópont |

> [!NOTE]  
> Cserélje le az `<edgenodename>` elemet az élcsomópont nevére.
>
> Cserélje le a `<clustername>` elemet a fürt nevére.
>
> Ha a fürt élcsomópontot tartalmaz, ajánlott __az élcsomóponthoz mindig__ az SSH segítségével kapcsolódni. Az átjárócsomópontok olyan szolgáltatásokat futtatnak, amelyek kritikus fontosságúak a Hadoop állapota szempontjából. Az élcsomópont csak azt futtatja, amit Ön telepít rá.
>
> Az élcsomópontok használatával kapcsolatban további információért lásd: [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Amikor első alkalommal csatlakozik a HDInsighthoz, az SSH-ügyfél olyan figyelmeztetést jeleníthet meg, amely szerint a gazdaszámítógép nem hitelesíthető. Amikor a rendszer erre felkéri, válassza a „yes” (igen) lehetőséget ahhoz, hogy a gazdaszámítógép felvegye az SSH-ügyfél megbízható kiszolgálókat tartalmazó listába.
>
> Ha korábban már csatlakozott egy ilyen nevű kiszolgálóhoz, előfordulhat, hogy a rendszer figyelmezteti rá, hogy a tárolt gazdaszámítógép-kulcs nem egyezik meg a kiszolgáló gazdaszámítógép-kulcsával. A kiszolgálónévhez tartozó, meglévő bejegyzés eltávolításához tekintse meg az SSH-ügyfél dokumentációját.

## <a name="ssh-clients"></a>SSH-ügyfelek

Az `ssh` és `scp` parancs elérhető a Linux, Unix és macOS rendszerekben. Az `ssh`-ügyfelet általában arra használják, hogy távoli parancssori munkamenetet hozzon létre Linux vagy Unix rendszerben. Az `scp`-ügyfél segítségével biztonságosan másolhat fájlokat a saját ügyfél és a távoli rendszer között.

A Microsoft Windows alapértelmezés szerint nem telepít SSH-ügyfelet. Az `ssh`- és az `scp`-ügyfél az alábbi csomagokban érhető el a Windows rendszerhez:

* OpenSSH-ügyfél (béta): A Fall Creators Update-lépjen a __beállítások__ > __alkalmazások és szolgáltatások__ > __választható funkciók kezelése__  >  __Funkció hozzáadása__ , és válassza ki a __OpenSSH-ügyfél__. 

    > [!NOTE]  
    > Ha az `ssh` és az `scp` parancsok a funkció engedélyezése után sem érhetők el a PowerShellben, jelentkezzen ki, majd jelentkezzen be újra.

* [Bash on Ubuntu on Windows 10-es](https://msdn.microsoft.com/commandline/wsl/about): A `ssh` és `scp` parancs a Bash on Windows parancssori keresztül érhető el.

* [OpenSSH-ügyfél (béta)](https://blogs.msdn.microsoft.com/powershell/2017/12/15/using-the-openssh-beta-in-windows-10-fall-creators-update-and-windows-server-1709/): Ez a Windows 10 Fall Creators Update-ben bevezetett választható szolgáltatás.

* [Az Azure Cloud Shell](../cloud-shell/quickstart.md): A Cloud Shellben a böngészőben a Bash-környezetet biztosít, és biztosítja a `ssh`, `scp`, és egyéb gyakori Linux-parancsok.

* [Git (https://git-scm.com/)](https://git-scm.com/): A `ssh` és `scp` parancs a GitBash parancssorából érhető el.

Számos grafikus SSH-ügyfél is létezik, például a [PuTTY (https://www.chiark.greenend.org.uk/~sgtatham/putty/)](https://www.chiark.greenend.org.uk/~sgtatham/putty/) és a [MobaXterm (https://mobaxterm.mobatek.net/)](https://mobaxterm.mobatek.net/). Bár ezek az ügyfelek használhatók a HDInsighthoz történő kapcsolódáshoz, a kapcsolódás folyamata más, mint az `ssh` segédprogram használatakor. További információt az Ön által használt grafikus ügyfél dokumentációjában talál.

## <a id="sshkey"></a>Hitelesítés: SSH kulcsok

Az SSH-kulcsok [nyilvános kulcsú titkosítással](https://en.wikipedia.org/wiki/Public-key_cryptography) hitelesítik az SSH-munkameneteket. Az SSH-kulcsok biztonságosabbak a jelszavaknál, és egyszerű módszert kínálnak a Hadoop-fürt biztonságos elérésére.

Ha az SSH-fiókja kulccsal van védve, az ügyfélnek meg kell adnia az egyező titkos kulcsot, amikor csatlakozik:

* A legtöbb ügyfél konfigurálható __alapértelmezett kulcs__ használatára. Az `ssh`-ügyfél például a titkos kulcsot a `~/.ssh/id_rsa` helyen keresi Linux- és Unix-környezetekben.

* Megadhatja __egy titkos kulcs elérési útját__. Az `ssh`-ügyfél esetében az `-i` paraméterrel adható meg a titkos kulcs elérési útja. Például: `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Ha __több titkos kulccsal__ rendelkezik, amelyeket különböző kiszolgálókhoz használ, fontolja meg az olyan segédprogramok használatát, mint például az [ssh-ügynök (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). Az `ssh-agent` segédprogram segítségével automatikusan választhatja ki a kulcsot az SSH-munkamenet megkezdéséhez.

> [!IMPORTANT]  
> Ha jelszóval védi a titkos kulcsot, a kulcs használatakor be kell írnia a jelszót. Az `ssh-agent` és hasonló segédprogramokkal a kényelmes használat érdekében gyorsítótárazhatja a jelszót.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az `ssh-keygen` paranccsal hozhat létre nyilvános- és titkoskulcs-fájlokat. A következő parancs egy 2048 bites RSA-kulcspárt hoz létre, amely a HDInsighttal használható:

    ssh-keygen -t rsa -b 2048

A kulcs létrehozása során a rendszer információk megadását kéri. Például a kulcsok tárolási helyét kell megadnia, vagy azt, hogy használ-e jelszót. A folyamat befejezése után két fájl jön létre; egy nyilvános kulcs és egy titkos kulcs.

* A __nyilvános kulccsal__ hozható létre a HDInsight-fürt. A nyilvános kulcs kiterjesztése `.pub`.

* A __titkos kulccsal__ hitelesíthető az ügyfél a HDInsight-fürtön.

> [!IMPORTANT]  
> A kulcsokat jelszóval védheti. Ez a jelszó lényegében egy kód a titkos kulcson. Ilyen esetekben még ha valaki meg is szerzi a titkos kulcsát, a jelszóra is szüksége van a kulcs használatához.

### <a name="create-hdinsight-using-the-public-key"></a>HDInsight létrehozása a nyilvános kulccsal

| Létrehozási metódus | A nyilvános kulcs használata |
| ------- | ------- |
| **Azure Portal** | Törölje a __Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor__ jelölőnégyzet jelölését, majd válassza a __Nyilvános kulcs__ elemet az SSH-hitelesítés típusaként. Végül válassza ki a nyilvános kulcs fájlját, vagy illessze be a fájl szöveges tartalmát a __Nyilvános SSH-kulcs__ mezőbe.</br>![Nyilvános SSH-kulcs párbeszédpanel a HDInsight-fürt létrehozásakor](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | A `New-AzureRmHdinsightCluster` parancsmag `-SshPublicKey` paraméterével illesztheti be a nyilvános kulcs tartalmát sztringként.|
| **Az Azure klasszikus parancssori felület** | Az `azure hdinsight cluster create` parancs `--sshPublicKey` paraméterével illesztheti be a nyilvános kulcs tartalmát sztringként. |
| **Resource Manager-sablon** | Az SSH-kulcsok sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) fájlban lévő `publicKeys` elemmel illeszthetők be a kulcsok az Azure-ba a fürt létrehozásakor. |

## <a id="sshpassword"></a>Hitelesítés: Jelszó

Az SSH-fiókok jelszóval védhetők. Amikor SSH-fiókkal csatlakozik a HDInsighthoz, a rendszer jelszót kér.

> [!WARNING]  
> A Microsoft nem javasolja jelszavas hitelesítés használatát az SSH-hoz. A jelszavakat ki lehet találni, és védtelenek a találgatásos támadásokkal szemben. Ehelyett azt javasoljuk, hogy használjon [SSH-kulcsokat a hitelesítéshez](#sshkey).

> [!IMPORTANT]  
> Az SSH-fiók jelszava 70 nappal a HDInsight-fürt létrehozása után jár le. Ha a jelszó lejár, a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentumban foglaltak szerint változtathatja meg.

### <a name="create-hdinsight-using-a-password"></a>HDInsight létrehozása jelszóval

| Létrehozási metódus | Jelszó megadása |
| --------------- | ---------------- |
| **Azure Portal** | Alapértelmezés szerint az SSH-felhasználói fióknak ugyanaz a jelszava, mint a fürt bejelentkezési fiókjának. Ha más jelszót szeretne használni, törölje a __Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor__ jelölőnégyzet jelölését, majd írja be a jelszót az __SSH-jelszó__ mezőbe.</br>![SSH-jelszó párbeszédpanel a HDInsight-fürt létrehozásakor](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | A `New-AzureRmHdinsightCluster` parancsmag `--SshCredential` paraméterével illessze be az SSH-felhasználói fiók nevét és jelszavát tartalmazó `PSCredential` objektumot. |
| **Az Azure klasszikus parancssori felület** | Az `azure hdinsight cluster create` parancs `--sshPassword` paraméterével adja meg a jelszó értékét. |
| **Resource Manager-sablon** | A jelszavak sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) fájlban lévő `linuxOperatingSystemProfile` elemmel illeszthető be az SSH-fióknév és -jelszó az Azure-ba a fürt létrehozásakor.|

### <a name="change-the-ssh-password"></a>Az SSH-jelszó módosítása

Az SSH-felhasználói fiók jelszavának módosításával kapcsolatos információért tekintse meg a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentum __Jelszavak módosítása__ szakaszát.

## <a id="domainjoined"></a>Hitelesítés: Tartományhoz csatlakoztatott HDInsight

Ha __tartományhoz csatlakoztatott HDInsight-fürtöt__ használ, a `kinit` parancsot kell használnia a helyi SSH-felhasználóval való csatlakozás után. Ez a parancs tartományi felhasználónevet és jelszót kér, és hitelesíti a munkamenetet a fürttel társított Azure Active Directory-tartománnyal.

Emellett engedélyezheti a Kerberos-hitelesítést minden egyes tartományhoz csatlakoztatott csomóponton (pl. átjárócsomóponton, határcsomóponton) annak érdekében, hogy ssh-kapcsolat jöjjön létre a tartományi fiók használatával. Ehhez szerkessze az sshd config fájlt:
```bash
sudo vi /etc/ssh/sshd_config
```
távolítsa el a megjegyzéseket, a `KerberosAuthentication` értékét pedig állítsa a következőre: `yes`

```bash
sudo service sshd restart
```

Ha bármikor ellenőrizni szeretné, hogy a Kerberos-hitelesítés sikeres volt-e, használja a `klist` parancsot.

További információkat itt talál: [Tartományhoz csatlakoztatott HDInsight konfigurálása](./domain-joined/apache-domain-joined-configure.md).

## <a name="connect-to-nodes"></a>Csatlakozás csomópontokhoz

Az átjárócsomópontokhoz és az élcsomóponthoz (ha van) az interneten, a 22-es és a 23-as porton lehet hozzáférni.

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

A csatlakozás után a parancssor megváltozik, és megjeleníti az SSH-felhasználónevet és a csomópontot, amelyhez csatlakozik. Ha például az `sshuser` felhasználóként csatlakozik az elsődleges átjárócsomóponthoz, a parancssor az `sshuser@hn0-clustername:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Csatlakozás a feldolgozó és az Apache Zookeeper-csomópontok

A feldolgozó és Zookeeper-csomópontok nem közvetlenül az internetről, hanem a fürt átjáró- vagy élcsomópontjain keresztül érhetők el. A következő általános lépésekkel csatlakozhat más csomópontokhoz:

1. Az SSH-val csatlakozzon egy átjáró- vagy élcsomóponthoz:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Az átjáró- vagy élcsomópont felé fennálló SSH-kapcsolatból használja az `ssh` parancsot a fürt egyik munkavégző csomópontjához való csatlakozáshoz:

        ssh sshuser@wn0-myhdi

    A csomópont nevének listájának lekéréséhez tekintse meg a [kezelése a HDInsight az Apache Ambari REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentumot.

Ha az SSH-fiókot __jelszó__ védi, a kapcsolódáshoz adja meg a jelszót.

Ha az SSH-fiókot __SSH-kulcsok__ védik, győződjön meg róla, hogy az SSH-továbbítás engedélyezve van az ügyfélen.

> [!NOTE]  
> A fürtben lévő összes csomópont közvetlen elérésének másik módja, ha a HDInsightot Azure virtuális hálózatra telepíti. Ezután csatlakoztathatja a távoli gépet ugyanehhez a virtuális hálózathoz, és közvetlenül érheti el a fürtben lévő összes csomópontot.
>
> További információ: [Virtuális hálózat használata HDInsighttal](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>SSH-ügynöktovábbítás konfigurálása

> [!IMPORTANT]  
> A következő lépések Linux vagy UNIX-alapú rendszert feltételeznek, és a Windows 10-en futó Bash-környezet esetén működnek. Ha a lépések nem működnek a rendszerén, lehet, hogy át kell tekintenie az SSH-ügyfél dokumentációját.

1. Egy szövegszerkesztővel nyissa meg a `~/.ssh/config` fájlt. Ha a fájl nem létezik, létrehozhatja a parancssoron az `touch ~/.ssh/config` karakterlánc beírásával.

2. Adja hozzá a következő szöveget az `config` fájlhoz.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Cserélje le a __Gazda__ információit azon csomópont címére, amelyet SSH-val csatlakoztat. Az előző példa az élcsomópontot használja. Ez a bejegyzés konfigurálja az SSH-ügynöktovábbítást az adott csomópont számára.

3. Tesztelje az SSH-ügynöktovábbítást a terminálból a következő parancs segítségével:

        echo "$SSH_AUTH_SOCK"

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Ha a parancs nem ad vissza semmit, a(z) `ssh-agent` nem fut. További tudnivalókért lásd az ügynök indítási szkriptjeire vonatkozó részt a [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Az ssh-agent és az ssh együttes használata) című cikkben vagy az SSH-ügyfél dokumentációját.

4. Ha meggyőződött róla, hogy az **ssh-agent** fut, a következő segítségével adja hozzá a titkos SSH-kulcsot az ügynökhöz:

        ssh-add ~/.ssh/id_rsa

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
> * [Az Azure Storage-et használó HDInsight](hdinsight-hadoop-use-blob-storage.md).
>
> * [A HDInsight segítségével az Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>További lépések

* [SSH-alagútkezelés használata a HDInsighttal](hdinsight-linux-ambari-ssh-tunnel.md)
* [Virtuális hálózat használata a HDInsighttal](hdinsight-extend-hadoop-virtual-network.md)
* [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node)
