---
title: Vállalati biztonsági csomagfürtök kezelése – Azure HDInsight
description: Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket az Enterprise Security Package csomaggal.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435882"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight-fürtök kezelése vállalati biztonsági csomaggal

Ismerje meg a HDInsight Enterprise Security Package (ESP) felhasználóiés szerepköreit, valamint az ESP-fürtök kezelését.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

A normál fürtöt az Apache Ambari által felügyelt felhasználónévvel kapcsolhatja össze, valamint egy biztonsági `user1@contoso.com`Apache Hadoop fürthöz is tartományfelhasználónév (például: ) segítségével.

1. Nyissa [meg a Visual Studio Code -ot](https://code.visualstudio.com/). Győződjön meg [arról,](../hdinsight-for-vscode.md) hogy a Spark & Hive Tools bővítmény telepítve van.

1. Kövesse a [Fürt csatolása](../hdinsight-for-vscode.md#link-a-cluster) a Visual Studio-kódhoz című lépés lépéseit.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Normál fürtöt az Ambari által felügyelt felhasználónévvel kapcsolhat össze, valamint egy biztonsági hadoop-fürtet is csatolhat tartományfelhasználónév (például: `user1@contoso.com`) használatával.

1. Nyissa meg az IntelliJ IDEA-t. Győződjön meg arról, hogy minden [előfeltétel](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) teljesül.

1. Kövesse az [IntelliJ fürtjének összekapcsolása](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) című lépéseit.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Normál fürtöt az Ambari által felügyelt felhasználónévvel kapcsolhat össze, valamint egy biztonsági hadoop-fürtet is csatolhat tartományfelhasználónév (például: `user1@contoso.com`) használatával.

1. Nyissa meg az Eclipse környezetet. Győződjön meg arról, hogy minden [előfeltétel](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) teljesül.

1. Kövesse a [Fürt csatolása](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) az Eclipse számára című lépés lépéseit.

## <a name="access-the-clusters-with-enterprise-security-package"></a>A fürtök elérése vállalati biztonsági csomaggal

Az Enterprise Security Package (korábbi nevén HDInsight Premium) többfelhasználós hozzáférést biztosít a fürthöz, ahol a hitelesítést az Active Directory és az Apache Ranger és storage ACL-ek (ADLS ACL-ek) végzi. Az engedélyezés biztonságos határokat biztosít több felhasználó között, és csak a kiemelt jogosultságú felhasználók számára teszi lehetővé az adatokhoz való hozzáférést az engedélyezési házirendek alapján.

A biztonság és a felhasználók elkülönítése fontos a VÁLLALATI biztonsági csomaggal rendelkező HDInsight-fürtök esetében. Ezeknek a követelményeknek a teljesítéséhez a vállalati biztonsági csomaggal rendelkező fürtssSH-hozzáférés le van tiltva. Az alábbi táblázat az egyes fürttípusokhoz ajánlott hozzáférési módszereket mutatja be:

|Számítási feladat|Forgatókönyv|Hozzáférési mód|
|--------|--------|-------------|
|Apache Hadoop|Hive – Interaktív feladatok/lekérdezések  |<ul><li>[Beeline](#beeline)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio eszközök](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktív feladatok/lekérdezések, PySpark interaktív|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin és Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio eszközök](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch-forgatókönyvek – Spark submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktív lekérdezés (LLAP)|Interaktív|<ul><li>[Beeline](#beeline)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio eszközök](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Bármelyik|Egyéni alkalmazás telepítése|<ul><li>[Parancsfájl-műveletek](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > A Jupyter nincs telepítve/támogatott az Enterprise Security Package csomagban.

A szabványos API-k használata biztonsági szempontból segítséget nyújt. Ön is kap a következő előnyöket:

- **Kezelés** – A szabványos API-k – Livy, HS2 stb.
- **Naplózás** – Az SSH-val nincs mód a naplózásra, amely az SSH'd a fürthöz. Ez nem lenne így, ha a feladatok szabványos végpontokon keresztül épülnek fel, mivel a felhasználó kontextusában lennének végrehajtva.

### <a name="use-beeline"></a><a name="beeline"></a>A Beeline használata

Telepítse a Beeline-t a gépére, és csatlakozzon a nyilvános interneten keresztül, használja a következő paramétereket:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Ha a Beeline helyileg van telepítve, és egy Azure virtuális hálózaton keresztül csatlakozik, használja a következő paramétereket:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

A headnode teljesen minősített tartománynevének megkereséséhez használja a HDInsight kezelése az Ambari REST API-dokumentum használatával található információkat.

## <a name="users-of-hdinsight-clusters-with-esp"></a>AZ ESP-vel rendelkező HDInsight-fürtök felhasználói

Egy nem ESP HDInsight-fürt két felhasználói fiókkal rendelkezik, amelyek a fürt létrehozása során jönnek létre:

- **Ambari admin**: Ez a fiók is ismert *Hadoop felhasználó* vagy *HTTP felhasználó*. Ezzel a fiókkal lehet bejelentkezni az `https://CLUSTERNAME.azurehdinsight.net`Ambari a . Lekérdezések futtatására is használható Ambari nézeteken, feladatok végrehajtására külső eszközökön (például PowerShellen, Templetononon, Visual Studio) keresztül, és hitelesítheti magát a Hive ODBC illesztőprogrammal és üzletiintelligencia-eszközökkel (például Excel, Power BI vagy Tableau).

Az ESP-vel rendelkező HDInsight-fürtnek az Ambari Admin mellett három új felhasználója is van.

- **Ranger admin**: Ez a fiók a helyi Apache Ranger admin fiók. Ez nem egy active directory tartományi felhasználó. Ezzel a fiókkal házirendeket lehet beállítani, és más felhasználókat vagy delegált rendszergazdákat hozhat (hogy ezek a felhasználók kezelni tudják a házirendeket). Alapértelmezés szerint a felhasználónév *rendszergazda,* a jelszó pedig megegyezik az Ambari rendszergazdai jelszójelszavával. A jelszó frissíthető a Ranger Beállítások lapjáról.

- **Fürtfelügyeleti tartományi felhasználó**: Ez a fiók egy Active Directory tartományi felhasználó, akit hadoop-fürt-rendszergazdaként jelölnek ki, beleértve az Ambari és a Ranger tartományt. A fürt létrehozása során meg kell adnia a felhasználó hitelesítő adatait. Ez a felhasználó a következő jogosultságokkal rendelkezik:
    - Csatlakoztasson gépeket a tartományhoz, és helyezze őket a fürt létrehozása során megadott szervezeti egységbe.
    - Hozzon létre egyszerű szolgáltatásnéveket a szervezeti egységen belül, amelyeket a fürt létrehozása során megadott.
    - Fordított DNS-bejegyzések létrehozása.

    Vegye figyelembe, hogy a többi AD-felhasználó is rendelkezik ezekkel a jogosultságokkal.

    Vannak olyan végpontok a fürtön belül (például Templeton), amelyeket nem a Ranger kezel, és így nem biztonságosak. Ezek a végpontok a fürtfelügyeleti tartományi felhasználó kivételével minden felhasználó számára zárolva vannak.

- **Szabályos**: A fürt létrehozása során több active directorycsoportot is megadhat. Az ezekben a csoportokban lévő felhasználók szinkronizálva vannak a Ranger és az Ambari alkalmazással. Ezek a felhasználók tartományi felhasználók, és csak a Ranger által felügyelt végpontokhoz (például Hiveserver2) férhetnek hozzá. Az összes RBAC-szabályzat és naplózás ezekre a felhasználókra vonatkozik.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>A HDInsight-fürtök és az ESP szerepkörei

A HDInsight enterprise biztonsági csomag a következő szerepkörökkel rendelkezik:

- Fürtfelügyelő
- Fürt operátor
- Szolgáltatás-rendszergazda
- Szolgáltatás operátora
- Fürt felhasználója

**A szerepkörök engedélyeinek megtekintése**

1. Nyissa meg az Ambari Management felhasználói felületét.  Lásd: [Az Ambari Management felhasználói felület megnyitása.](#open-the-ambari-management-ui)
2. A bal oldali menüben válassza a **Szerepek**lehetőséget.
3. Az engedélyek megtekintéséhez jelölje ki a kék kérdőjelet:

    ![ESP HDInsight szerepkörök engedélyei](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Az Ambari Management felhasználói felületének megnyitása

1. Keresse `https://CLUSTERNAME.azurehdinsight.net/` meg, ahol a CLUSTERNAME a fürt neve.
1. Jelentkezzen be az Ambari szolgáltatásba a fürtrendszergazdának a tartomány felhasználónevével és jelszavával.
1. Válassza ki a **rendszergazdai** legördülő menüt a jobb felső sarokban, majd válassza **az Ambari kezelése parancsot.**

    ![ESP HDInsight apache ambari kezelése](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A felhasználói felület így néz ki:

    ![ESP HDInsight Apache Ambari felügyeleti felhasználói felület](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Az Active Directoryból szinkronizált tartományfelhasználók listázása

1. Nyissa meg az Ambari Management felhasználói felületét.  Lásd: [Az Ambari Management felhasználói felület megnyitása.](#open-the-ambari-management-ui)
2. A bal oldali menüben válassza a **Felhasználók**lehetőséget. Az Active Directoryból a HDInsight-fürtre szinkronizált összes felhasználónak látnia kell.

    ![AZ ESP HDInsight Ambari felügyeleti felhasználói felület felhasználói felületének felhasználói felületének felhasználói felületének felhasználói felületének felhasználói felületének felhasználói felületének felhasználói felületének felhasználói felületének felhasználói felülete](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Az Active Directoryból szinkronizált tartománycsoportok felsorolása

1. Nyissa meg az Ambari Management felhasználói felületét.  Lásd: [Az Ambari Management felhasználói felület megnyitása.](#open-the-ambari-management-ui)
2. A bal oldali menüben válassza a **Csoportok**lehetőséget. Az Active Directoryból a HDInsight-fürtre szinkronizált összes csoport megjelenik.

    ![ESP HDInsight Ambari felügyeleti felhasználói felületi listacsoportok](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive-nézetek engedélyeinek konfigurálása

1. Nyissa meg az Ambari Management felhasználói felületét.  Lásd: [Az Ambari Management felhasználói felület megnyitása.](#open-the-ambari-management-ui)
2. A bal oldali menüben válassza a **Nézetek lehetőséget.**
3. A részletek megjelenítéséhez válassza a **HIVE** lehetőséget.

    ![ESP HDInsight Ambari felügyeleti felhasználói felületi struktúranézetek](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. A **Hive-nézetek** konfigurálásához válassza a Hive-nézet hivatkozást.
5. Görgessen le az **Engedélyek** szakaszhoz.

    ![Az ESP HDInsight Ambari felügyeleti felhasználói felületi struktúranézetei konfigurálják az engedélyeket](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Válassza **a Felhasználó hozzáadása** vagy a Csoport **hozzáadása**lehetőséget, majd adja meg a Hive-nézeteket használó felhasználókat vagy csoportokat.

## <a name="configure-users-for-the-roles"></a>Felhasználók konfigurálása a szerepkörökhöz

 A szerepkörök és engedélyeik listájának megtekintéséhez olvassa el a HDInsight-fürtök szerepkörök ESP-vel című témakört.

1. Nyissa meg az Ambari Management felhasználói felületét.  Lásd: [Az Ambari Management felhasználói felület megnyitása.](#open-the-ambari-management-ui)
2. A bal oldali menüben válassza a **Szerepek**lehetőséget.
3. Válassza **a Felhasználó hozzáadása** vagy a Csoport **hozzáadása** lehetőséget, ha a felhasználókat és csoportokat különböző szerepkörökhöz szeretné rendelni.

## <a name="next-steps"></a>További lépések

- A HDInsight-fürt vállalati biztonsági csomaggal történő konfigurálásáról a [HDInsight-fürtök konfigurálása ESP szolgáltatással](apache-domain-joined-configure.md)című témakörben látható.
- A Hive-házirendek konfigurálása és a Hive-lekérdezések futtatása című témakörben [az Apache Hive-házirendek konfigurálása HDInsight-fürtökhez ESP-vel](apache-domain-joined-run-hive.md)című témakörben.
