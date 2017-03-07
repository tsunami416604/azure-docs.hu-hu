---
title: "SSH használata a HDInsight (Hadoop) eszközzel Windows, Linux, Unix vagy OS X rendszerben | Microsoft Docs"
description: " A HDInsight a Secure Shell (SSH) segítségével érhető el. Ez a dokumentum információt nyújt az SSH és a HDInsight együttes használatáról Windows-, Linux-, Unix- vagy OS X-ügyfeleken.."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 4cde035f75bfa3c448f12e9ebf2896b9a54a6873
ms.lasthandoff: 02/28/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-bash-on-windows-10-linux-unix-or-os-x"></a>SSH használata a Bashből a HDInsight (Hadoop) eszközzel Windows 10, Linux, Unix vagy OS X rendszerben

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

A [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) lehetővé teszi, hogy bejelentkezzen egy Linux-alapú HDInsight-fürtre, és parancsokat futtasson egy parancssori felületről. Ez a dokumentum alapvető információkat biztosít az SSH-ról, illetve konkrét információkat az SSH HDInsighttal történő használatáról.

## <a name="what-is-ssh"></a>Mi az az SSH?

Az SSH egy hálózati titkosítási protokoll, amely lehetővé teszi, hogy egy nem védett hálózaton keresztül biztonságosan kommunikáljon egy távoli kiszolgálóval. Az SSH-val biztonságos parancssori bejelentkezés biztosítható egy távoli kiszolgálóra, ebben az esetben egy HDInsight-fürt átjárócsomópontjaira vagy élcsomópontjára.

Az SSH emellett az ügyfél és a HDInsight-fürt közötti hálózati forgalom bújtatására is használható. Az alagút használata lehetővé teszi, hogy hozzáférjen olyan szolgáltatásokhoz a HDInsight-fürtön, amelyek nem csatlakoznak közvetlenül az internetre. Az SSH-bújtatás HDInsighttal való használatával kapcsolatos további információkat [az SSH-bújtatás a HDInsighttal való használatáról szóló cikkben](hdinsight-linux-ambari-ssh-tunnel.md) találhat.

## <a name="ssh-clients"></a>SSH-ügyfelek

Számos operációs rendszer biztosít SSH-ügyfél funkciót az `ssh` és az `scp` parancssori segédprogramon keresztül.

* __ssh__: Általános SSH-ügyfél, amely távoli parancssori munkamenet létesítésére és alagutak létrehozására használható.
* __scp__: Olyan segédprogram, amely fájlokat másol a helyi és a távoli rendszerek között az SSH protokoll használatával.

A Windows 10 évfordulós kiadása fejlesztőeszközként tartalmazza a Basht. Biztosítja a(z) `ssh`, `scp` és egyéb Linux parancsokat. A Bash on Windows 10 használatával kapcsolatos további információkért lásd: [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about).

Ha Windows rendszert használ, és nem fér hozzá a Bash on Windows 10 szolgáltatáshoz, a következő SSH-ügyfeleket javasoljuk:

* [Git for Windows](https://git-for-windows.github.io/): Biztosítja az `ssh` és az `scp` parancssori segédprogramot.
* [Cygwin](https://cygwin.com/): Biztosítja az `ssh` és az `scp` parancssori segédprogramot.

> [!NOTE]
> A dokumentum lépései azt feltételezik, hogy hozzáféréssel rendelkezik az `ssh` parancshoz. Ha például puTTY vagy MobaXterm ügyfelet használ, tekintse át a termék dokumentációját az egyenértékű parancsért és paraméterekért.

## <a name="ssh-authentication"></a>SSH-hitelesítés

Az SSH-kapcsolatot jelszóval vagy [nyilvános kulcsú titkosítással (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography) lehet hitelesíteni. A kulcs használata a legbiztonságosabb megoldás, mivel nincs kitéve annyi támadásnak, mint a jelszavak. A kulcsok létrehozása és kezelése azonban bonyolultabb, mint a jelszavak használata.

A nyilvános kulcsú titkosítás használatának része egy _nyilvános_ és egy _titkos_ kulcs alkotta pár létrehozása.

* A **nyilvános kulcsot** a rendszer a HDInsight-fürt csomópontjaiba tölti be, vagy bármely más szolgáltatásba, amelyet nyilvános kulcsú titkosítással kíván használni.

* A **titkos kulccsal** igazolja a személyazonosságát a HDInsight-fürt felé, amikor bejelentkezik egy SSH-ügyféllel. Védje a titkos kulcsot. Ne ossza meg senkivel.

    További biztonsági intézkedésként létrehozhat egy jelszót a titkos kulcshoz. Ha hozzáférési kódot használ, meg kell adnia az SSH-val való hitelesítéskor.

### <a name="create-a-public-and-private-key"></a>Nyilvános és titkos kulcs létrehozása

Az `ssh-keygen` segédprogram a legegyszerűbb módszer a HDInsighthoz használni kívánt nyilvános és titkos kulcspár létrehozására. Egy parancssorból a következő paranccsal hozzon létre egy új kulcspárt a HDInsighthoz:

> [!NOTE]
> Amennyiben grafikus SSH-ügyfelet használ, például a MobaXTermöt vagy a puTTYt, tekintse át az ügyfél dokumentációját a kulcsok létrehozásával kapcsolatos útmutatásért.

    ssh-keygen -t rsa -b 2048

A rendszer a következő információk megadását kéri:

* A fájl helye: Az alapértelmezett hely a `~/.ssh/id_rsa`.

* Jelszó (nem kötelező): Ha megad egy jelszót, azt a HDInsight-fürtön való hitelesítéskor újból meg kell adnia.

> [!IMPORTANT]
> Ez a titkos kulcs jelszava. Minden alkalommal, amikor a titkos kulcsot hitelesítéshez használja, a kulcs használatához először meg kell adnia a jelszót. Ha valaki megszerzi a titkos kulcsot, nem fogja tudni használni a jelszó nélkül.
>
> Ha elfelejti jelszavát, nincs lehetőség a visszaállítására vagy a helyreállítására.

A parancs futtatásának befejeződésekor két új fájl jön létre:

* __id\_rsa__: Ez a fájl tartalmazza a titkos kulcsot.

    > [!WARNING]
    > Korlátozza a hozzáférést ehhez a fájlhoz, hogy megakadályozza a nyilvános kulcs által védett szolgáltatásokhoz való jogosulatlan hozzáférést.

* __id\_rsa.pub__: Ez a fájl tartalmazza a nyilvános kulcsot. Ezt a fájlt HDInsight-fürtök létrehozásakor használja.

    > [!NOTE]
    > Nem számít, hogy ki fér hozzá a _nyilvános_ kulcshoz. A nyilvános kulcsot önmagában csak a titkos kulcs igazolására lehet használni. Az olyan szolgáltatások, mint az SSH-kiszolgáló a személyazonosság igazolására használják a nyilvános kulcsot, amikor a titkos kulccsal végez hitelesítést.

## <a name="configure-ssh-on-hdinsight"></a>Az SSH konfigurálása a HDInsight-on

Amikor létrehoz egy Linux-alapú HDInsight-fürtöt, meg kell adnia egy _SSH-felhasználónevet_, valamint egy _jelszót_ vagy egy _nyilvános kulcsot_. A fürt létrehozása során a rendszer ezt az információt bejelentkezési adatok létrehozására használja a HDInsight-fürtcsomópontokon. A jelszót vagy nyilvános kulcsot a rendszer a felhasználói fiók védelmére használja.

Az SSH a fürt létrehozása során történő konfigurálásával kapcsolatos további információkat a következő dokumentumokban talál:

* [HDInsight létrehozása az Azure Portalon](hdinsight-hadoop-create-linux-clusters-portal.md)
* [HDInsight létrehozása az Azure parancssori felülettel](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [HDInsight létrehozása az Azure PowerShell-lel](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [HDInsight létrehozása Azure Resource Manager-sablonokkal](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [HDInsight létrehozása a .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [HDInsight létrehozása a REST használatával](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>További SSH-felhasználók

Bár a fürt létrehozása után lehetőség van további SSH-felhasználók hozzáadására, ez nem ajánlott.

* Új SSH-felhasználókat kell hozzáadni a fürt egyes csomópontjaihoz.

* Az új SSH-felhasználók ugyanolyan hozzáféréssel rendelkeznek a HDInsighthoz, mint az alapértelmezett felhasználó. A HDInsightban nem lehet korlátozni az adatokhoz vagy feladatokhoz való hozzáférést az SSH-felhasználói fiókok alapján.

A hozzáférés felhasználónkénti korlátozása csak tartományhoz csatlakoztatott HDInsight-fürtön érhető el. A tartományhoz csatlakoztatott HDInsight-fürt Active Directoryval szabályozza a fürterőforrásokhoz való hozzáférést.

Tartományhoz csatlakoztatott HDInsight-fürt használata lehetővé teszi az Active Directoryval való hitelesítést az SSH-val való csatlakozás után. Több felhasználó csatlakozhat SSH-val, majd a csatlakozás után hitelesítést végezhet az Active Directory-fiókjával. További információért lásd a [Tartományhoz csatlakoztatott HDInsight](#domainjoined) című szakaszt.

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a> Csatlakozás a HDInsight-hoz

Bár a HDInsight-fürt összes csomópontja futtatja az SSH-kiszolgálót, nyilvános interneten keresztül csak az átjárócsomópontokhoz vagy az élcsomópontokhoz lehet csatlakozni.

* Az _átjárócsomópontokhoz_ való csatlakozáshoz használja a `CLUSTERNAME-ssh.azurehdinsight.net` karakterláncot, ahol a __CLUSTERNAME__ a HDInsight-fürt neve. A 22-es port (az SSH alapértelmezett portja) az elsődleges átjárócsomóponthoz csatlakozik. A 23-as port a másodlagos átjárócsomóponthoz csatlakozik.

* Egy _élcsomóponthoz_ való csatlakozáshoz használja az `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net` karakterláncot, ahol az __EDGENAME__ az élcsomópont neve, és a __CLUSTERNAME__ a HDInsight-fürt neve. Az élcsomóponthoz való csatlakozáskor használja a 22-es portot.

Az alábbi példák azt mutatják be, hogyan lehet csatlakozni a __myhdi__ nevű fürt átjárócsomópontjaihoz és élcsomópontjához az __sshuser__ SSH-felhasználónévvel. Az élcsomópont neve __myedge__.

| Elvégzendő művelet | Használandó karakterlánc |
| ----- | ----- |
| Csatlakozás az elsődleges átjárócsomóponthoz | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Csatlakozás a másodlagos átjárócsomóponthoz | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Csatlakozás az élcsomóponthoz | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Ha jelszóval védi az SSH-fiókot, a rendszer kérni fogja a jelszó megadását.

Ha nyilvános kulccsal védi az SSH-fiókot, előfordulhat, hogy meg kell adnia a megfelelő titkos kulcs elérési útját az `-i` kapcsolóval. Az alábbi példa az `-i` kapcsoló használatát mutatja be:

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Csatlakozás más csomópontokhoz

A munkavégző csomópontok és a Zookeeper-csomópontok nem érhetők el közvetlenül a fürtön kívülről, de a fürt átjárócsomópontjaiból vagy élcsomópontjaiból el lehet őket érni. A következő általános lépésekkel csatlakozhat más csomópontokhoz:

1. Az SSH-val csatlakozzon egy átjáró- vagy élcsomóponthoz:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Az átjáró- vagy élcsomópont felé fennálló SSH-kapcsolatból használja az `ssh` parancsot a fürt egyik munkavégző csomópontjához való csatlakozáshoz:

        ssh sshuser@wn0-myhdi

    A fürtben található munkavégző csomópontok listájának lekéréséhez tekintse meg a [HDInsight az Ambari REST API-val való kezelését](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) ismertető dokumentumban lévő példát a fürtcsomópontok teljes tartománynevének lekérésére.

Ha az SSH-fiókot jelszó védi, a rendszer megkéri, hogy adja meg a jelszót, és létrejön a kapcsolat.

Ha a felhasználói fiók hitelesítéséhez SSH-kulcsot használ, győződjön meg arról, hogy a helyi környezet SSH-ügynöktovábbításra van konfigurálva.

> [!IMPORTANT]
> Az alábbi lépések Linux-/UNIX-alapú rendszert feltételeznek, és a Bash on Windows 10 esetén működnek. Ha a lépések nem működnek a rendszerén, lehet, hogy át kell tekintenie az SSH-ügyfél dokumentációját.

1. Egy szövegszerkesztővel nyissa meg a `~/.ssh/config` fájlt. Ha a fájl nem létezik, létrehozhatja a parancssoron az `touch ~/.ssh/config` karakterlánc beírásával.

2. Adja a következőket a fájlhoz. Cserélje le a *CLUSTERNAME* kifejezést a HDInsight-fürt nevére.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Ez a bejegyzés konfigurálja az SSH-ügynöktovábbítást a HDInsight-fürt számára.

3. Tesztelje az SSH-ügynöktovábbítást a terminálból a következő parancs segítségével:

        echo "$SSH_AUTH_SOCK"

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Ha a parancs nem ad vissza semmit, a(z) `ssh-agent` nem fut. Az ügynök indítási parancsfájljaival kapcsolatos információkért tekintse meg a [Using ssh-agent with ssh (Az ssh-agent és az ssh együttes használata – http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) című cikket, vagy olvassa el az SSH-ügynök dokumentációját az `ssh-agent` telepítésének és konfigurálásának részletes lépéseiért.

4. Ha meggyőződött róla, hogy az **ssh-agent** fut, a következő segítségével adja hozzá a titkos SSH-kulcsot az ügynökhöz:

        ssh-add ~/.ssh/id_rsa

    Ha a titkos kulcsot egy másik fájl tárolja, a `~/.ssh/id_rsa` részt cserélje ki a fájl elérési útjára.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>Tartományhoz csatlakoztatott HDInsight

[A tartományhoz csatlakozó HDInsight](hdinsight-domain-joined-introduction.md) a Kerberost és a Hadoopot integrálja a HDInsightban. Mivel az SSH-felhasználó nem Active Directory tartományi felhasználó, nem futtathat Hadoop-parancsokat, amíg nem végez hitelesítést az Active Directoryval. Kövesse az alábbi lépéseket az SSH-munkamenet Active Directoryval történő hitelesítéséhez:

1. Csatlakozás SSH-val egy tartományhoz csatlakozó HDInsight-fürthöz. Például az alábbi parancs egy __myhdi__ nevű HDInsight-fürthöz csatlakozik egy __sshuser__ nevű SSH-fiókkal.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Tartományi felhasználóval és jelszóval történő hitelesítéshez használja a következő parancsot:

        kinit

     Ha a rendszer kéri, adjon meg egy tartományi felhasználónevet és a tartományi felhasználó jelszavát.

    A tartományi felhasználók tartományhoz csatlakoztatott HDInsight-fürtökhöz való konfigurálásáról további információt a [tartományhoz csatlakoztatott HDInisight-fürtök konfigurálásával kapcsolatos](hdinsight-domain-joined-configure.md) cikkben talál.

A `kinit` paranccsal végzett hitelesítés után már használhatja az olyan Hadoop-parancsokat, mint a `hdfs dfs -ls /` vagy a `hive`.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>SSH-bújtatás

Az SSH-val helyi kérések, például webes kérések bújtatását is elvégezheti a HDInsight-fürthöz. A kérést a rendszer továbbítja a fürt felé, majd feloldja a fürtön belül.

> [!IMPORTANT]
> Az SSH-alagút előfeltétele annak, hogy el tudja érni néhány Hadoop-szolgáltatás webes felhasználói felületét. Például a Feladatelőzmények felhasználói felület és az Erőforrás-kezelő felhasználói felület is csak SSH-alagúton keresztül érhető el.

Az SSH-alagutak létrehozásával és használatával kapcsolatos további információkat [Az Ambari webes felhasználói felület, a JobHistory, a NameNode, az Oozie és egyéb webes felhasználói felületek SSH-bújtatással való eléréséről szóló cikkben](hdinsight-linux-ambari-ssh-tunnel.md) találhat.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett az SSH-kulccsal végzett hitelesítésről, arról is tájékozódhat, hogyan használhatja a MapReduce-t a HDInsight-alapú Hadoopban.

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

