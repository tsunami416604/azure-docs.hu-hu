---
title: A vállalati biztonsági csomaggal – Azure HDInsight Hive-házirendek konfigurálása
description: Ismerje meg, az Apache Ranger-házirendek konfigurálása a Hive a vállalati biztonsági csomaggal egy Azure HDInsight szolgáltatásban.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 43b3e293bf2990c953aeb2947b1113ee30175dee
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632815"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Az Apache Hive-házirendek konfigurálása a HDInsight vállalati biztonsági csomaggal
Ismerje meg, az Apache Hive Apache Ranger-házirendek konfigurálása. Ebben a cikkben két Ranger-házirendet hoz létre a hivesampletable nevű táblához való hozzáférés korlátozása érdekében. A hivesampletable HDInsight-fürtöket tartalmaz. Miután konfigurálta a házirendeket, az Excel és az ODBC-illesztőprogram használatával kapcsolódjon a HDInsight Hive-tábláihoz.

## <a name="prerequisites"></a>Előfeltételek
* Egy vállalati biztonsági csomag rendelkező HDInsight-fürt. Lásd: [konfigurálása HDInsight-fürtök ESP](apache-domain-joined-configure.md).
* Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, az Excel 2013 Standalone vagy Office 2010 Professional Plus rendszert futtató munkaállomás.

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez
**Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez**

1. Egy böngészőből csatlakozzon a Ranger felügyeleti felhasználói felületéhez. Az URL-cím: https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > A Ranger más hitelesítő adatokat használ, mint a Hadoop-fürt. Hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat, új InPrivate-böngészőablak használatával csatlakozzon a Ranger felügyeleti felhasználói felületéhez.
   >
   >
2. Jelentkezzen be a fürt rendszergazdai tartományi felhasználónevével és jelszavával:

    ![HDInsight ESP Ranger kezdőlapja](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    A Ranger jelenleg csak a Yarn és a Hive rendszerrel működik.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása
Lásd: [egy HDInsight-fürt létrehozása a ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp), hiveuser1 és egy hiveuser2 létrehozásával kapcsolatos információkat. Ebben az oktatóanyagban használja a két felhasználói fiókokat.

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása
Ebben a szakaszban két Ranger-házirendet a hivesampletable eléréséhez hozzon létre. Adjon kiválasztási engedélyt a különböző oszlopcsoportokra vonatkozóan. Mindkét felhasználó használatával létrehozott [egy HDInsight-fürt létrehozása a ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). A következő szakaszban a két házirendet Excelben fogja tesztelni.

**Ranger-házirendek létrehozása**

1. Nyissa meg a Ranger felügyeleti felhasználói felületét. Lásd a [Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez](#connect-to-apache-ranager-admin-ui) című részt.
2. A **Hive** alatt kattintson a **&lt;ClusterName>_hive** elemre. Két előre konfigurált házirendnek kell megjelennie.
3. Kattintson az **Új házirend hozzáadása** gombra, majd adja meg a következő értékeket:

   * Házirend neve: read-hivesampletable-all
   * Hive-adatbázis: alapértelmezett
   * tábla: hivesampletable
   * Hive-oszlop: *
   * Felhasználó kiválasztása: hiveuser1
   * Engedélyek: kiválasztás

     ![HDInsight ESP Ranger Hive-házirendjének konfigurálása](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Ha egy tartományi felhasználó nem töltődik be a Felhasználó kiválasztása részben, várjon néhány másodpercig, amíg a Ranger szinkronizálódik az AAD-val.
     >
     >
4. A házirend mentéséhez kattintson a **Hozzáadás** gombra.
5. Ismételje meg az utolsó két lépést egy másik házirend létrehozásához, amely a következő tulajdonságokkal rendelkezik:

   * Házirend neve: read-hivesampletable-devicemake
   * Hive-adatbázis: alapértelmezett
   * tábla: hivesampletable
   * Hive-oszlop: clientid devicemake
   * Felhasználó kiválasztása: hiveuser2
   * Engedélyek: kiválasztás

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása
Az utasítások a [Hive ODBC-adatforrás létrehozása](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) című részben találhatók.  

 | Tulajdonság  |Leírás |
 | --- | --- |
 | Adatforrás neve | Adjon nevet az adatforrásának |
 | Gazdagép | Írja be a következő kifejezést: &lt;HDInsightClusterName>.azurehdinsight.net. Például: sajatHDICluster.azurehdinsight.net |
 | Port | Használja a **443** számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
 | Adatbázis | Használja az **Alapértelmezett** adatbázist. |
 | Hive Server típusa | Válassza ki a **Hive Server 2** típust |
 | Mechanizmus | Válassza ki az **Azure HDInsight szolgáltatást** |
 | HTTP elérési útja | Hagyja üresen. |
 | Felhasználónév | Írja be a hiveuser1@contoso158.onmicrosoft.com (igen) kifejezést. Ha eltérő, frissítse a tartománynevet. |
 | Jelszó | Adja meg a hiveuser1 jelszavát. |

Az adatforrás mentése előtt kattintson a **Tesztelés** gombra.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból
Az utolsó szakaszban két házirendet konfigurált.  A hiveuser1 nevű felhasználó az összes oszlopra, míg hiveuser2 csak két oszlopra vonatkozó kiválasztási engedéllyel rendelkezik. Ebben a szakaszban Ön megszemélyesíti a két felhasználót, hogy adatokat importáljon Excel formátumba.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.
2. Az **Adatok** lapon kattintson az **Egyéb adatforrásokból** elemre, majd kattintson az **Adatkapcsolat varázslóból** elemre, hogy elindítsa az **Adatkapcsolat varázslót**.

    ![Adatkapcsolat varázsló megnyitása] [img-hdi-simbahiveodbc.excel.dataconnection]
3. Válassza ki az **ODBC DSN** adatforrást, majd kattintson a **Tovább** gombra.
4. Az ODBC-adatforrások közül válassza ki az előző lépésben létrehozott adatforrásnevet, majd kattintson a **Tovább** gombra.
5. Írja be újból a jelszót a fürthöz, a varázslóban, és kattintson a **OK**. Várja meg, amíg megnyílik az **Adatbázis és tábla kiválasztása** párbeszédpanel. Ez eltarthat néhány másodpercig.
6. Válassza ki a **hivesampletable** táblát, majd kattintson a **Tovább** gombra.
7. Kattintson a **Finish** (Befejezés) gombra.
8. Az **Adatok importálása** párbeszédpanelen módosíthatja vagy megadhatja a lekérdezést. Ehhez kattintson a **Tulajdonságok** elemre. Ez eltarthat néhány másodpercig.
9. Kattintson a **Definíció** fülre. A parancs szövege a következő:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   A definiált Ranger-házirendek alapján a hiveuser1 az összes oszlopra vonatkozó kiválasztási engedéllyel rendelkezik.  Ez a lekérdezés hiveuser1 hitelesítő adataival működik, tehát, de ez a lekérdezés nem működik a hiveuser2 hitelesítő adataival.

   ![Kapcsolat tulajdonságai] [img-hdi-simbahiveodbc-excel-connectionproperties]
10. A kapcsolat tulajdonságai párbeszédpanel bezárásához kattintson az **OK** gombra.
11. Az **Adatok importálása** párbeszédpanel bezárásához kattintson az **OK** gombra.  
12. Írja be újra a hiveuser1 jelszavát, majd kattintson az **OK** gombra. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe. Amikor kész van, 11 adatoszlopnak kell megjelennie.

A második (read-hivesampletable-devicemake) házirend tesztelése, az előző szakaszban létrehozott

1. Adjon hozzá egy új munkalapot az Excelben.
2. Az adatok importálásához kövesse az utolsó eljárást.  Az egyetlen változás választja ki, hogy helyett a hiveuser1 hiveuser2 hitelesítő adatait használja. Ez nem sikerül, mert hiveuser2 csak két oszlop megtekintésére jogosult. A következő hibaüzenetnek kell megjelennie:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Az adatok importálásához kövesse ugyanazt az eljárást. Ez alkalommal hiveuser2 hitelesítő adatait használja, és módosítsa a kiválasztási utasítást erről:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    erre:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Amikor kész van, két importáltadat-oszlopnak kell megjelennie.

## <a name="next-steps"></a>További lépések
* A vállalati biztonsági csomaggal egy HDInsight-fürt konfigurálásához lásd: [konfigurálása HDInsight-fürtök ESP](apache-domain-joined-configure.md).
* ESP HDInsight fürtök kezeléséhhez lásd: [kezelése a HDInsight-fürtök ESP](apache-domain-joined-manage.md).
* Az SSH használata a HDInsight-fürtök ESP Hive-lekérdezések futtatásához, tekintse meg a [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* A Hive JDBC segítségével történő Hive-csatlakoztatáshoz olvassa el a [Csatlakozás a Hive-hoz az Azure HDInsight rendszerben a Hive JDBC-illesztőprogrammal](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) című részt
* Ha az Excelt a Hive ODBC segítségével szeretné a Hadoophoz csatlakoztatni, olvassa el [Az Excel csatlakoztatása a Hadoophoz a Microsoft Hive ODBC-meghajtó segítségével](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) című részt
* Ha az Excelt a Power Query segítségével szeretné a Hadoophoz csatlakoztatni, olvassa el [Az Excel csatlakoztatása a Hadoophoz a Power Query segítségével](../hadoop/apache-hadoop-connect-excel-power-query.md) című részt
