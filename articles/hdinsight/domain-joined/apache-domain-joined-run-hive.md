---
title: HDInsight-szabályzatok konfigurálása a Enterprise Security Package-Azure-ban
description: Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat a Kaptárhoz egy Azure HDInsight-szolgáltatásban Enterprise Security Package használatával.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b0213fc1a96b38b615cbd8b7b6374a6716b9f840
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918183"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Apache Hive-szabályzatok konfigurálása a HDInsightban az Enterprise Security Package csomaggal
Megtudhatja, hogyan konfigurálhatja a Apache Hive Apache Ranger-szabályzatait. Ebben a cikkben két Ranger-házirendet hoz létre a hivesampletable nevű táblához való hozzáférés korlátozása érdekében. A hivesampletable HDInsight-fürtöket tartalmaz. Miután konfigurálta a házirendeket, az Excel és az ODBC-illesztőprogram használatával kapcsolódjon a HDInsight Hive-tábláihoz.

## <a name="prerequisites"></a>Előfeltételek
* Enterprise Security Package-t tartalmazó HDInsight-fürt. Lásd: [HDInsight-fürtök beállítása az ESP-vel](apache-domain-joined-configure.md).
* Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, az Excel 2013 Standalone vagy Office 2010 Professional Plus rendszert futtató munkaállomás.

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez
**Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez**

1. Egy böngészőből csatlakozzon a Ranger felügyeleti felhasználói felületéhez. Az URL-cím: https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > A Ranger eltérő hitelesítő adatokat használ, mint a Apache Hadoop-fürt. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, használja az új InPrivate-böngészőablakot a Ranger felügyeleti felhasználói felületéhez.

2. Jelentkezzen be a fürt rendszergazdai tartományi felhasználónevével és jelszavával:

    ![HDInsight ESP Ranger kezdőlapja](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    A Ranger jelenleg csak a Yarn és a Hive rendszerrel működik.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása
A hiveruser1 és a hiveuser2 létrehozásával kapcsolatos információkért tekintse meg [a HDInsight-fürt ESP-vel történő létrehozását](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)ismertető témakört. Ebben a cikkben a két felhasználói fiókot használja.

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása
Ebben a szakaszban két Ranger-szabályzatot hoz létre a hivesampletable eléréséhez. Adjon kiválasztási engedélyt a különböző oszlopcsoportokra vonatkozóan. Mindkét felhasználó a HDInsight- [fürt és az ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)együttes használatával lett létrehozva. A következő szakaszban a két házirendet Excelben fogja tesztelni.

**Ranger-házirendek létrehozása**

1. Nyissa meg a Ranger felügyeleti felhasználói felületét. Lásd: Kapcsolódás az Apache Ranger felügyeleti felhasználói felületéhez.
2. A **Hive** alatt kattintson a **&lt;ClusterName>_hive** elemre. Két előre konfigurált házirendnek kell megjelennie.
3. Kattintson az **Új házirend hozzáadása** gombra, majd adja meg a következő értékeket:

   * Házirend neve: read-hivesampletable-all
   * Hive-adatbázis: alapértelmezett
   * tábla: hivesampletable
   * Hive-oszlop: *
   * Felhasználó kiválasztása: hiveuser1
   * Engedélyek: kiválasztás

     ![HDInsight ESP Ranger-struktúra házirend konfigurálása](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

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
 | Felhasználónév | Írja be a hiveuser1@contoso158.onmicrosoft.com (igen) kifejezést. Ha az eltérő, frissítse a tartománynevet. |
 | Windows 10 | Adja meg a hiveuser1 jelszavát. |

Az adatforrás mentése előtt kattintson a **Tesztelés** gombra.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból
Az utolsó szakaszban két házirendet konfigurált.  A hiveuser1 nevű felhasználó az összes oszlopra, míg hiveuser2 csak két oszlopra vonatkozó kiválasztási engedéllyel rendelkezik. Ebben a szakaszban Ön megszemélyesíti a két felhasználót, hogy adatokat importáljon Excel formátumba.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.
2. Az **Adatok** lapon kattintson az **Egyéb adatforrásokból** elemre, majd kattintson az **Adatkapcsolat varázslóból** elemre, hogy elindítsa az **Adatkapcsolat varázslót**.

    ![Adatkapcsolat varázsló megnyitása] [img-hdi-simbahiveodbc.excel.dataconnection]
3. Válassza ki az **ODBC DSN** adatforrást, majd kattintson a **Tovább** gombra.
4. Az ODBC-adatforrások közül válassza ki az előző lépésben létrehozott adatforrásnevet, majd kattintson a **Tovább** gombra.
5. Írja be újra a fürthöz tartozó jelszót a varázslóban, majd kattintson az **OK**gombra. Várja meg, amíg megnyílik az **Adatbázis és tábla kiválasztása** párbeszédpanel. Ez eltarthat néhány másodpercig.
6. Válassza ki a **hivesampletable** táblát, majd kattintson a **Tovább** gombra.
7. Kattintson a **Finish** (Befejezés) gombra.
8. Az **Adatok importálása** párbeszédpanelen módosíthatja vagy megadhatja a lekérdezést. Ehhez kattintson a **Tulajdonságok** elemre. Ez eltarthat néhány másodpercig.
9. Kattintson a **Definíció** fülre. A parancs szövege a következő:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   A definiált Ranger-házirendek alapján a hiveuser1 az összes oszlopra vonatkozó kiválasztási engedéllyel rendelkezik.  Így ez a lekérdezés a hiveuser1 hitelesítő adataival működik, de ez a lekérdezés nem működik a hiveuser2 hitelesítő adataival.

   ![Kapcsolat tulajdonságai] [img-hdi-simbahiveodbc-excel-connectionproperties]
10. A kapcsolat tulajdonságai párbeszédpanel bezárásához kattintson az **OK** gombra.
11. Az **Adatok importálása** párbeszédpanel bezárásához kattintson az **OK** gombra.  
12. Írja be újra a hiveuser1 jelszavát, majd kattintson az **OK** gombra. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe. Amikor kész van, 11 adatoszlopnak kell megjelennie.

A második szabályzat (read-hivesampletable-devicemake) teszteléséhez, amelyet az utolsó szakaszban hozott létre

1. Adjon hozzá egy új munkalapot az Excelben.
2. Az adatok importálásához kövesse az utolsó eljárást.  Az egyetlen változás, hogy a hiveuser2 hitelesítő adatokat használja a hiveuser1 helyett. Ez a művelet sikertelen, mert a hiveuser2 csak két oszlop megjelenítésére jogosult. A következő hibaüzenetnek kell megjelennie:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Az adatok importálásához kövesse ugyanazt az eljárást. Ez alkalommal hiveuser2 hitelesítő adatait használja, és módosítsa a kiválasztási utasítást erről:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    erre:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Amikor kész van, két importáltadat-oszlopnak kell megjelennie.

## <a name="next-steps"></a>További lépések
* A HDInsight-fürtök Enterprise Security Package-vel való konfigurálásával kapcsolatban lásd: [HDInsight-fürtök beállítása az ESP-vel](apache-domain-joined-configure.md).
* Az ESP-vel rendelkező HDInsight-fürtök kezelésével kapcsolatban lásd: [HDInsight-fürtök kezelése az ESP-vel](apache-domain-joined-manage.md).
* A HDInsight-fürtök SSH-val való futtatásához az ESP használatával: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Ha a kaptárt a méhkas JDBC használatával Apache Hive szeretné csatlakoztatni, tekintse meg a következőt: [Csatlakozás az Azure HDInsight-hez a kaptár JDBC-illesztő](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Az Excel és a Hadoop összekapcsolása a kaptár ODBC használatával: [az Excel csatlakoztatása Apache Hadoop a Microsoft kaptár ODBC-meghajtóval](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Az Excel és a Hadoop összekapcsolása Power Query használatával: az [Excel csatlakoztatása Apache Hadoophoz a Power Query használatával](../hadoop/apache-hadoop-connect-excel-power-query.md)
