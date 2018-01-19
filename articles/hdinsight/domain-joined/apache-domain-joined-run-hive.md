---
title: "Tartományhoz csatlakoztatott HDInsight - Azure Hive szabályzatok konfigurálására |} Microsoft Docs"
description: "Információk ...."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 35a74ffb6a30fe2ae7db686be5b6774800ce37b1
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>A tartományhoz csatlakoztatott HDInsight Hive-szabályzatok konfigurálása
Útmutató ahhoz, hogyan lehet az Apache Ranger-házirendeket a Hive számára konfigurálni. Ebben a cikkben két Ranger-házirendet hoz létre a hivesampletable nevű táblához való hozzáférés korlátozása érdekében. A hivesampletable HDInsight-fürtöket tartalmaz. Miután konfigurálta a házirendeket, az Excel és az ODBC-illesztőprogram használatával kapcsolódjon a HDInsight Hive-tábláihoz.

## <a name="prerequisites"></a>Előfeltételek
* Tartományhoz csatlakoztatott HDInsight-fürt. Lásd a [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md) című részt.
* Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, az Excel 2013 Standalone vagy Office 2010 Professional Plus rendszert futtató munkaállomás.

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez
**Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez**

1. Egy böngészőből csatlakozzon a Ranger felügyeleti felhasználói felületéhez. Az URL-cím: https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > A Ranger más hitelesítő adatokat használ, mint a Hadoop-fürt. Gyorsítótárazott hitelesítő adatok használata Hadoop böngészők megakadályozásához használja az új InPrivate-böngészőablakot a Pletyka felügyeleti felhasználói felület való kapcsolódáshoz.
   >
   >
2. Jelentkezzen be a fürt rendszergazdai tartományi felhasználónevével és jelszavával:

    ![HDInsight-tartományhoz csatlakoztatott Ranger kezdőlapja](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    A Ranger jelenleg csak a Yarn és a Hive rendszerrel működik.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása
A [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md#optional-create-ad-users-and-groups) című részben létrehozott egy hiveuser1 és egy hiveuser2 nevű felhasználót. Ebben az oktatóanyagban két felhasználói fiókot használja.

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása
Ebben a szakaszban hozzon létre két Pletyka házirendeket hivesampletable elérése. Adjon kiválasztási engedélyt a különböző oszlopcsoportokra vonatkozóan. Mindkét felhasználó a [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md#optional-create-ad-users-and-groups) című részben lett létrehozva.  A következő szakaszban a két házirendet Excelben fogja tesztelni.

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

     ![HDInsight-tartományhoz csatlakoztatott Ranger Hive-házirendjének konfigurálása](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

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
 | Felhasználónév | Adja meg hiveuser1@contoso158.onmicrosoft.com. Frissítse a tartomány nevét, ha más. |
 | Jelszó | Adja meg a hiveuser1 jelszavát. |

Az adatforrás mentése előtt kattintson a **Tesztelés** gombra.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból
Az utolsó szakaszban két házirendet konfigurált.  A hiveuser1 nevű felhasználó az összes oszlopra, míg hiveuser2 csak két oszlopra vonatkozó kiválasztási engedéllyel rendelkezik. Ebben a szakaszban Ön megszemélyesíti a két felhasználót, hogy adatokat importáljon Excel formátumba.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.
2. Az **Adatok** lapon kattintson az **Egyéb adatforrásokból** elemre, majd kattintson az **Adatkapcsolat varázslóból** elemre, hogy elindítsa az **Adatkapcsolat varázslót**.

    ![Adatkapcsolat varázsló megnyitása] [img-hdi-simbahiveodbc.excel.dataconnection]
3. Válassza ki az **ODBC DSN** adatforrást, majd kattintson a **Tovább** gombra.
4. Az ODBC-adatforrások közül válassza ki az előző lépésben létrehozott adatforrásnevet, majd kattintson a **Tovább** gombra.
5. Írja be újból a jelszót a varázslóban a fürthöz, és kattintson a **OK**. Várja meg, amíg megnyílik az **Adatbázis és tábla kiválasztása** párbeszédpanel. Ez eltarthat néhány másodpercig.
6. Válassza ki a **hivesampletable** táblát, majd kattintson a **Tovább** gombra.
7. Kattintson a **Finish** (Befejezés) gombra.
8. Az **Adatok importálása** párbeszédpanelen módosíthatja vagy megadhatja a lekérdezést. Ehhez kattintson a **Tulajdonságok** elemre. Ez eltarthat néhány másodpercig.
9. Kattintson a **Definíció** fülre. A parancs szövege a következő:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   A definiált Ranger-házirendek alapján a hiveuser1 az összes oszlopra vonatkozó kiválasztási engedéllyel rendelkezik.  Így működik, ez a lekérdezés hiveuser1 tartozó hitelesítő adatokkal, de ez a lekérdezés nem tud együttműködni hiveuser2 tartozó hitelesítő adatokat.

   ![Kapcsolat tulajdonságai] [img-hdi-simbahiveodbc-excel-connectionproperties]
10. A kapcsolat tulajdonságai párbeszédpanel bezárásához kattintson az **OK** gombra.
11. Az **Adatok importálása** párbeszédpanel bezárásához kattintson az **OK** gombra.  
12. Írja be újra a hiveuser1 jelszavát, majd kattintson az **OK** gombra. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe. Amikor kész van, 11 adatoszlopnak kell megjelennie.

Az utolsó szakaszban létrehozott tesztszabályzat a második (olvasás-hivesampletable-devicemake),

1. Adjon hozzá egy új munkalapot az Excelben.
2. Az adatok importálásához kövesse az utolsó eljárást.  Mindössze annyi a változás, akkor ellenőrizze, hogy hiveuser2 tartozó hitelesítő adatok használata helyett hiveuser1 meg. Ez nem sikerül, mert hiveuser2 csak jogosult két oszlopok megjelenítéséhez. A következő hibaüzenetnek kell megjelennie:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Az adatok importálásához kövesse ugyanazt az eljárást. Ez alkalommal hiveuser2 hitelesítő adatait használja, és módosítsa a kiválasztási utasítást erről:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    erre:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Amikor kész van, két importáltadat-oszlopnak kell megjelennie.

## <a name="next-steps"></a>További lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* Tartományhoz csatlakozó HDInsight fürt kezelése, lásd: [kezelése tartományhoz a HDInsight-fürtök](apache-domain-joined-manage.md).
* Az SSH használata a tartományhoz csatlakoztatott HDInsight-fürtökön Hive-lekérdezéseket futtat, tekintse meg a [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* A Hive JDBC segítségével történő Hive-csatlakoztatáshoz olvassa el a [Csatlakozás a Hive-hoz az Azure HDInsight rendszerben a Hive JDBC-illesztőprogrammal](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) című részt
* Ha az Excelt a Hive ODBC segítségével szeretné a Hadoophoz csatlakoztatni, olvassa el [Az Excel csatlakoztatása a Hadoophoz a Microsoft Hive ODBC-meghajtó segítségével](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) című részt
* Ha az Excelt a Power Query segítségével szeretné a Hadoophoz csatlakoztatni, olvassa el [Az Excel csatlakoztatása a Hadoophoz a Power Query segítségével](../hadoop/apache-hadoop-connect-excel-power-query.md) című részt
