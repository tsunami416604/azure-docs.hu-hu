---
title: "Használja az Apache Phoenix és az SQuirreL with Azure HDInsight Windows-alapú |} Microsoft Docs"
description: "Útmutató Apache Phoenix használata a Hdinsightban, és telepítésének és konfigurálásának SQuirreL való kapcsolódáshoz a hdinsight HBase-fürtöt a munkaállomáson."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1a756e98-75c1-44cd-a178-c5119683b7b7
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 64a4c5b158ebe0119f2f0133587a743fd2dbf0ff
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-phoenix-and-squirrel-with-windows-based-hbase-clusters-in-hdinsight"></a>Apache Phoenix és az SQuirreL használata a Hdinsightban Windows-alapú HBase-fürtökkel
Ismerje meg, hogyan használható [Apache Phoenix](http://phoenix.apache.org/) , és hogyan telepítheti és konfigurálhatja a munkaállomásra való kapcsolódáshoz a hdinsight HBase-fürtöt SQuirreL. Phoenix kapcsolatos további információkért lásd: [Phoenix kevesebb mint 15 perc alatt](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). A Phoenix nyelvtan, lásd: [Phoenix nyelvtan](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> A Phoenix fájlverzió-információkat a Hdinsightban, lásd: [What's new in HDInsight által biztosított Hadoop-fürt verziók?](hdinsight-component-versioning.md).
>

> [!IMPORTANT]
> Az ebben a dokumentumban csak a lépések Windows-alapú HDInsight-fürtök. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). Phoenix a Linux-alapú HDInsight használatáról további információért lásd: [használata Apache Phoenix a Linux-alapú HBase a HDInsight clusters](hbase/apache-hbase-phoenix-squirrel-linux.md).
>



## <a name="use-sqlline"></a>SQLLine használata
[SQLLine](http://sqlline.sourceforge.net/) egy parancssori segédprogram SQL végrehajtása.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt használhatná SQLLine, az alábbiakkal kell rendelkeznie:

* **A hdinsight HBase-fürtöt**. HBase kiépítési információk fürt esetén lásd: [Ismerkedés az Apache HBase hdinsightban][hdinsight-hbase-get-started].
* **A HBase-fürtöt, a távoli asztal protokollal csatlakozni**. Útmutatásért lásd: [kezelése Hadoop-fürtök a HDInsight a klasszikus Azure portál használatával][hdinsight-manage-portal].

**Ha szeretné tudni, a gazdagép neve**

1. Nyissa meg **Hadoop parancssori** az asztalról.
2. Futtassa a következő parancs használatával beszerezheti a DNS-utótag:

        ipconfig

    Írja le **kapcsolatspecifikus DNS-utótag**. Például *myhbasecluster.f5.internal.cloudapp.net*. Való csatlakozáskor HBase-fürtöt, szüksége lesz a teljes tartománynév használatával Zookeepers egyik való kapcsolódáshoz. Minden egyes HDInsight-fürt 3 Zookeepers rendelkezik. Ezek *zookeeper0*, *zookeeper1*, és *zookeeper2*. A teljes Tartománynevet fogja hasonlót *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**SQLLine használata**

1. Nyissa meg **Hadoop parancssori** az asztalról.
2. Nyissa meg a SQLLine a következő parancsok futtatásával:

        cd %phoenix_home%\bin
        sqlline.py [The FQDN of one of the Zookeepers]

    ![HDInsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

    A mintában használt parancsok:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

További információkért lásd: [SQLLine manuális](http://sqlline.sourceforge.net/#manual) és [Phoenix nyelvtan](http://phoenix.apache.org/language/index.html).

## <a name="use-squirrel"></a>SQuirreL használata
[SQL-ügyfél sQuirreL](http://squirrel-sql.sourceforge.net/) grafikus Java-program hozhat létre, amely lehetővé teszi olyan JDBC-kompatibilis adatbázisokba szerkezete megtekintése, keresse meg a táblázatok adatait, adja ki az SQL-parancsok stb. A HDInsight az Apache Phoenix csatlakozni használható.

Ez a szakasz bemutatja, hogyan telepítéséhez és konfigurálásához SQuirreL VPN-en keresztül a HDInsight HBase-fürtöt való csatlakozáshoz a számítógépen.

### <a name="prerequisites"></a>Előfeltételek
Eljárások végrehajtása előtt az alábbiakkal kell rendelkeznie:

* A DNS virtuális gépekkel egy Azure virtuális hálózatra telepített HBase-fürtöt.  Útmutatásért lásd: [hozzon létre HBase-fürtök Azure virtuális hálózat][hdinsight-hbase-provision-vnet].

* A HBase fürt fürt kapcsolatspecifikus DNS-utótag első. Az RDP a fürthöz, és futtassa az IPConfig.  A DNS-utótag hasonlít:

        myhbase.b7.internal.cloudapp.net
* Töltse le és telepítse [Microsoft Visual Studio Express a Windows asztal](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) a munkaállomáson. Szüksége lesz a csomag létrehozni a tanúsítványt a makecert.  
* Töltse le és telepítse [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) a munkaállomáson.  SQuirreL SQL ügyfélverzió 3.0-s és újabb rendszer JRE 1.6 vagy újabb verziója szükséges.  

### <a name="configure-a-point-to-site-vpn-connection-to-the-azure-virtual-network"></a>Pont – hely típusú VPN-kapcsolat az Azure virtuális hálózat konfigurálva
Nincsenek érintett pont-pont VPN-kapcsolat konfigurálása 3 lépést:

1. [Virtuális hálózat és a dinamikus útválasztó átjáró konfigurálása](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [A tanúsítványok létrehozása](#Create-your-certificates)
3. [A VPN-ügyfél konfigurálása](#Configure-your-VPN-client)

Lásd: [egy Azure virtuális hálózatra egy pont – hely típusú VPN-kapcsolat beállítása](../vpn-gateway/vpn-gateway-point-to-site-create.md) további információt.

#### <a name="configure-a-virtual-network-and-a-dynamic-routing-gateway"></a>Virtuális hálózat és a dinamikus útválasztó átjáró konfigurálása
Biztosítja a HBase-fürtöt egy Azure virtuális hálózatban ellátta (lásd az ebben a szakaszban). A következő lépéssel konfigurálása egy pont – hely kapcsolat.

**A pont – hely kapcsolat konfigurálása**

1. Jelentkezzen be a [a klasszikus Azure portálon][azure-portal].
2. Kattintson a bal oldali **hálózatok**.
3. Kattintson a létrehozott virtuális hálózat (lásd: [rendelkezés HBase clusters Azure virtuális hálózat][hdinsight-hbase-provision-vnet]).
4. Kattintson a **KONFIGURÁLÁSA** a lista elejéről.
5. Az a **pont – hely kapcsolat** szakaszban jelölje be **pont – hely kapcsolat konfigurálása**.
6. Konfigurálása **KEZDŐ IP-** és **CIDR** adhatja meg az IP-címtartományt, amelyből a VPN-ügyfelek a csatlakozáskor IP-címet fog kapni. A tartomány nem lehet átfedésben a helyszíni hálózat és az Azure virtuális hálózat való kapcsolódás található tartományt. Például. Ha a virtuális hálózat 10.0.0.0/20 választotta, az ügyfél-címterület 10.1.0.0/24 választhatja meg. Tekintse meg a [pont – hely kapcsolat] [ vnet-point-to-site-connectivity] olvashat.
7. Kattintson a virtuális hálózati cím szóközöket csoport **átjáró alhálózatának hozzáadása**.
8. Kattintson a **mentése** az oldal alján.
9. Kattintson a **Igen** a módosítás megerősítéséhez. Várjon, amíg a rendszer a módosítást, mielőtt továbblép a következő eljárással befejeződött.

**Dinamikus útválasztó átjáró létrehozásához**

1. A klasszikus Azure portál, kattintson az **IRÁNYÍTÓPULT** az oldal tetején.
2. Kattintson a **ÁTJÁRÓ létrehozása** az oldal alján.
3. Kattintson a **Igen** megerősítéséhez. Várjon, amíg az átjáró létrehozása.
4. Kattintson a **IRÁNYÍTÓPULT** a lista elejéről.  A virtuális hálózati visual ábrázoló diagram jelenik meg:

    ![Azure-beli virtuális hálózat pont-pont virtuális diagramja][img-vnet-diagram]

    Az ábrán látható 0 ügyfélkapcsolatokat. Után a kapcsolat a virtuális hálózathoz, az a szám egy fog frissülni.

#### <a name="create-your-certificates"></a>A tanúsítványok létrehozása
Egy X.509-tanúsítvány létrehozása módja a tanúsítvány-létrehozási eszközzel (makecert.exe), a [Microsoft Visual Studio Express a Windows asztal](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).

**Gyökér önaláírt tanúsítvány létrehozása**

1. A munkaállomáson nyisson meg egy parancssori ablakot.
2. Keresse meg a Visual Studio eszközök mappát.
3. A következő parancsot az alábbi példa létrehoz és egy legfelső szintű tanúsítvány telepítése a munkaállomáson a személyes tanúsítványtárolóban, és is létrehozhat egy megfelelő .cer kiterjesztésű fájlba, amely a klasszikus Azure portál később fogja feltölteni.

        makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

    Nyissa meg azt a könyvtárat, amelyet a .cer fájl található, ahol HBaseVnetVPNRootCertificate-e be a tanúsítvány nevét.

    Ne zárja be a parancssort.  Szüksége lesz rájuk a következő eljárásban.

   > [!NOTE]
   > Mivel létrehozta a főtanúsítványt, amelyből létrejönnek az ügyféltanúsítványok, érdemes exportálni ezt a tanúsítványt a titkos kulcsával együtt és menteni egy olyan biztonságos helyen, ahonnan később helyreállítható.
   >
   >

**Ügyfél-tanúsítvány létrehozása**

* A parancssorból azonos (ez nem lehet ugyanazon a számítógépen, amelyen létrehozta a legfelső szintű tanúsítvány. Az ügyfél-tanúsítványt kell létrejönnie, a legfelső szintű tanúsítványa), a következő parancsot:

          makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

    HBaseVnetVPNRootCertificate a legfelső szintű tanúsítvány neve.  A legfelső szintű tanúsítvány névre van.  

    A legfelső szintű tanúsítvány, mind az ügyfél-tanúsítványt a számítógép személyes tanúsítványtárolójában vannak tárolva. Certmgr.msc segítségével ellenőrizheti.

    ![Azure-beli virtuális hálózat pont-pont VPN-tanúsítványának][img-certificate]

    Az összes, a virtuális hálózathoz csatlakoztatni kívánt számítógépen telepíteni kell egy ügyféltanúsítványt. Ajánlott egyedi ügyféltanúsítványok létrehozása minden olyan számítógéphez, amelyet csatlakoztatni szeretne a virtuális hálózathoz. Az ügyféltanúsítványok exportálásához használja certmgr.msc.

**A klasszikus Azure portálra a legfelső szintű tanúsítvány feltöltése**

1. A klasszikus Azure portál, kattintson az **hálózati** a bal oldalon.
2. Kattintson a virtuális hálózat, amelyen üzembe van helyezve a HBase fürt számára.
3. Kattintson a **tanúsítványok** a lista elejéről.
4. Kattintson a **FELTÖLTÉSE** a lista aljáról, és adja meg a főtanúsítványfájlt előtti eljárásban létrehozott. Várjon, amíg a tanúsítvány lett importálva.
5. Kattintson a **IRÁNYÍTÓPULT** felső.  A virtuális diagram állapotát jeleníti meg.

#### <a name="configure-your-vpn-client"></a>A VPN-ügyfél konfigurálása
**Töltse le, és a VPN-ügyfélcsomag telepítése**

1. Kattintson az IRÁNYÍTÓPULT-oldalon, a virtuális hálózat, a gyors áttekintő területen **a 64 bites ügyfél VPN-csomag** vagy **a 32 bites ügyfél VPN-csomag** munkaállomást az operációs rendszer verziójától függően.
2. Kattintson a **futtatása** a csomag telepítéséhez.
3. Kattintson a biztonsági parancssorba **további információ**, és kattintson a **mégis futtatni**.
4. Kattintson a **Igen** kétszer.

**A csatlakozás VPN hálózathoz**

1. A munkaállomás az asztalon kattintson a tálcán hálózatok ikonjára. A virtuális hálózati név látni fog egy VPN-kapcsolatot.
2. Kattintson a VPN-kapcsolat neve.
3. Kattintson a **Connect** (Csatlakozás) gombra.

**A virtuális Magánhálózati kapcsolat és a tartomány névfeloldás tesztelése**

* A munkaállomás nyisson meg egy parancssort, és egyet a következő nevekre a HBase-fürtöt DNS-utótag ping myhbase.b7.internal.cloudapp.net:

        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        headnode0.myhbase.b7.internal.cloudapp.net
        headnode1.myhbase.b7.internal.cloudapp.net
        workernode0.myhbase.b7.internal.cloudapp.net

### <a name="install-and-configure-squirrel-on-your-workstation"></a>Telepítse és konfigurálja az SQuirreL a munkaállomáson
**SQuirreL telepítése**

1. Töltse le a SQuirreL SQL ügyfél jar-fájlra a [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Megnyitás/Futtatás a jar-fájlra. Van szükség a [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Kattintson a **következő** kétszer.
4. Adjon meg egy elérési utat, ahol írási engedélye, és kattintson **következő**.

  > [!NOTE]
  > Az alapértelmezett telepítési mappa a C:\Program Files\squirrel-sql-3.6 mappában van.  Ahhoz, hogy az elérési út írni, a telepítő meg kell adni a rendszergazdai jogosultság. Nyisson meg egy parancssort rendszergazdaként, keresse meg a Java a bin mappát, és futtassa:
  >
  >     Java.exe-jar [az SQuirreL jar-fájl elérési útja]
5. Kattintson a **OK** létrehozása a célkönyvtárat megerősítéséhez.
6. Az alapértelmezett érték a kiinduló és a Standard csomag telepítéséhez.  Kattintson a **Tovább** gombra.
7. Kattintson a **következő** kétszer, majd **végzett**.

**A Phoenix illesztőprogram telepítése**

A phoenix illesztőprogram jar-fájlra a HBase-fürtöt található. Az elérési út verziók alapján a következőhöz hasonló:

    C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Másolja a munkaállomás alatt [SQuirreL telepítési mappa] / lib elérési utat kell.  A legegyszerűbb módja az, hogy az RDP a fürthöz, és fájlt másolja és illessze be (CTRL + C és CTRL + V) másolja azt a munkaállomás használja.

**A Phoenix illesztőprogram hozzáadása SQuirreL**

1. Nyissa meg a SQuirreL SQL ügyfél a munkaállomáson.
2. Kattintson a **illesztőprogram** a bal oldali lapon.
3. Az a **illesztőprogramok** menüben kattintson a **új illesztőprogram**.
4. Adja meg a következő információkat:

   * **Név**: Phoenix
   * **Példa URL-cím**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
   * **Osztálynév**: org.apache.phoenix.jdbc.PhoenixDriver

     > [!WARNING]
     > Felhasználó összes kisbetű, például URL-címét. Használhatja azokat teljes zookeeper kvórum abban az esetben, ha ezek egyikét nem működik.  A gazdagép neve a következők: zookeeper0, zookeeper1 és zookeeper2.
     >
     >

     ![HDInsight HBase Phoenix SQuirreL illesztőprogram][img-squirrel-driver]
5. Kattintson az **OK** gombra.

**A HBase-fürtöt az alias létrehozása**

1. SQuirreL, kattintson a **aliasok** a bal oldali lapon.
2. Az a **aliasok** menüben kattintson a **új Alias**.
3. Adja meg a következő információkat:

   * **Név**: a HBase-fürtöt, vagy inkább nevek nevét.
   * **Illesztőprogram**: Phoenix.  Ennek egyeznie kell az illesztőprogram az utolsó eljárás létrehozott nevét.
   * **URL-cím**: az URL-címet a rendszer átmásolja az illesztőprogram-konfigurációjáról. Ellenőrizze, hogy a felhasználó minden kisbetű.
   * **Felhasználónév**: szöveg lehet.  Itt adhat meg VPN-kapcsolatot, mert a felhasználónév nem minden használja.
   * **Jelszó**: szöveg lehet.

     ![HDInsight HBase Phoenix SQuirreL illesztőprogram][img-squirrel-alias]
4. Kattintson a **teszt**.
5. Kattintson a **Connect** (Csatlakozás) gombra. Ha a kapcsolat, SQuirreL néz ki:

    ![A HBase Phoenix SQuirreL][img-squirrel]

**Egy teszt futtatásához**

1. Kattintson a **SQL** lap jobb mellett a **objektumok** fülre.
2. Másolja és illessze be a következő kódot:

        CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. A Futtatás gombra.

    ![A HBase Phoenix SQuirreL][img-squirrel-sql]
4. Váltás a **objektumok** fülre.
5. Bontsa ki a alias nevét, és végül **tábla**.  Látni fogja az új táblázat területén.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta rendelkezik Apache Phoenix használata a Hdinsightban.  További tudnivalókért lásd:

* [HDInsight HBase overview][hdinsight-hbase-overview] (A HDInsight HBase áttekintése): A HBase egy Apache, nyílt forráskódú, a Hadoopra épülő NoSQL-adatbázis, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára.
* [Azure virtuális hálózat HBase-fürtök kiépítése][hdinsight-hbase-provision-vnet]: A virtuális hálózati integráció, HBase-fürtökkel telepíthetők az alkalmazások azonos virtuális hálózaton, hogy az alkalmazások közvetlenül kommunikálhatnak a HBase.
* [A HDInsight HBase-replikálás konfigurálása](hbase/apache-hbase-replication.md): HBase-replikálás konfigurálása az Azure két adatközpont között.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:hbase/apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:hbase/apache-hbase-overview.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png
