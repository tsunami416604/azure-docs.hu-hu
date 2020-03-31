---
title: Apache Hive-szabályzatok az Apache Rangerben – Azure HDInsight
description: Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat a Hive-hoz egy Azure HDInsight-szolgáltatásban enterprise security csomaggal.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196926"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Apache Hive-szabályzatok konfigurálása a HDInsightban az Enterprise Security Package csomaggal

Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat az Apache Hive-hoz. Ebben a cikkben két Ranger-házirendet hoz létre a hivesampletable nevű táblához való hozzáférés korlátozása érdekében. A hivesampletable HDInsight-fürtöket tartalmaz. Miután konfigurálta a házirendeket, excel- és ODBC-illesztőprogram használatával csatlakozhat a HIVe-táblákhoz a HDInsightban.

## <a name="prerequisites"></a>Előfeltételek

* HDInsight-fürt vállalati biztonsági csomaggal. Lásd: [HDInsight-fürtök konfigurálása ESP-vel.](apache-domain-joined-configure.md)
* Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, az Excel 2013 Standalone vagy Office 2010 Professional Plus rendszert futtató munkaállomás.

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez
**Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez**

1. Egy böngészőből keresse meg a Ranger `https://CLUSTERNAME.azurehdinsight.net/Ranger/` Admin felhasználói felületet, ahol a CLUSTERNAME a fürt neve.

   > [!NOTE]  
   > A Ranger más hitelesítő adatokat használ, mint az Apache Hadoop-fürt. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, az új InPrivate böngészőablakkal csatlakozhatnak a Ranger Admin felhasználói felülethez.

2. Jelentkezzen be a fürt rendszergazdai tartományi felhasználónevével és jelszavával:

    ![A HDInsight ESP Ranger kezdőlapja](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    A Ranger jelenleg csak a Yarn és a Hive rendszerrel működik.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A HIVEruser1 és a hiveuser2 létrehozásáról a [HDInsight-fürt létrehozása ESP-vel](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)című témakörben talál. A cikkben szereplő két felhasználói fiókot használhatja.

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása

Ebben a szakaszban két Ranger-szabályzatot hoz létre a hivesampletable eléréséhez. Adjon kiválasztási engedélyt a különböző oszlopcsoportokra vonatkozóan. Mindkét felhasználó a [HDInsight-fürt létrehozása ESP-vel](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). A következő szakaszban tesztelheti a két szabályzatot az Excelben.

**Ranger-házirendek létrehozása**

1. Nyissa meg a Ranger felügyeleti felhasználói felületét. Lásd a Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez című részt.
2. Válassza **a CLUSTERNAME_Hive**lehetőséget a **Hive**csoportban. Két előre konfigurált házirendnek kell megjelennie.
3. Válassza **az Új házirend hozzáadása**lehetőséget, majd adja meg a következő értékeket:

    |Tulajdonság |Érték |
    |---|---|
    |Házirend neve|olvasás-hivesampletable-all|
    |Hive-adatbázis|alapértelmezett|
    |tábla|hivesampletable|
    |Hive oszlop|*|
    |Felhasználó kiválasztása|hiveuser1|
    |Engedélyek|Válassza ki|

    ![A HDInsight ESP Ranger Hive házirendek konfigurálása](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Ha egy tartományi felhasználó nem töltődik be a Felhasználó kiválasztása részben, várjon néhány másodpercig, amíg a Ranger szinkronizálódik az AAD-val.

4. A házirend mentéséhez válassza a **Hozzáadás** lehetőséget.

5. Ismételje meg az utolsó két lépést egy másik házirend létrehozásához, amely a következő tulajdonságokkal rendelkezik:

    |Tulajdonság |Érték |
    |---|---|
    |Házirend neve|olvasás-hivesampletable-devicemake|
    |Hive-adatbázis|alapértelmezett|
    |tábla|hivesampletable|
    |Hive oszlop|clientid, devicemake|
    |Felhasználó kiválasztása|hiveuser2|
    |Engedélyek|Válassza ki|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Az utasítások a [Hive ODBC-adatforrás létrehozása](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) című részben találhatók.  

 | Tulajdonság  |Leírás |
 | --- | --- |
 | Adatforrás neve | Adjon nevet az adatforrásának |
 | Gazdagép | Adja meg CLUSTERNAME.azurehdinsight.net. Például: sajatHDICluster.azurehdinsight.net |
 | Port | Használja a **443** számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
 | Adatbázis | Használja az **Alapértelmezett** adatbázist. |
 | Hive Server típusa | Válassza ki a **Hive Server 2** típust |
 | Mechanizmus | Válassza ki az **Azure HDInsight szolgáltatást** |
 | HTTP elérési útja | Hagyja üresen. |
 | Felhasználónév | Írja be a hiveuser1@contoso158.onmicrosoft.com (igen) kifejezést. Frissítse a tartománynevet, ha az más. |
 | Jelszó | Adja meg a hiveuser1 jelszavát. |

Az adatforrás mentése előtt kattintson a **Tesztelés** gombra.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból

Az utolsó szakaszban két házirendet konfigurált.  A hiveuser1 nevű felhasználó az összes oszlopra, míg hiveuser2 csak két oszlopra vonatkozó kiválasztási engedéllyel rendelkezik. Ebben a szakaszban Ön megszemélyesíti a két felhasználót, hogy adatokat importáljon Excel formátumba.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.

1. Az **Adatok** lapon válassza az **Adatok becsatornázása** > **más forrásokból** > **az ODBC-ből** lehetőséget az **ODBC-ből** ablak elindításához.

    ![Adatkapcsolat megnyitása varázsló](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. A legördülő listában jelölje ki az utolsó szakaszban létrehozott adatforrásnevet, majd kattintson az **OK gombra.**

1. Az első használathoz megnyílik egy **ODBC-illesztőprogram-párbeszédpanel.** Válassza a Bal oldali menü **Windows** parancsát. Ezután a Csatlakozás gombra a **Navigátor** ablak megnyitásához válassza a **Csatlakozás** lehetőséget.

1. Várja meg, amíg megnyílik az **Adatbázis és tábla kiválasztása** párbeszédpanel. Ez eltarthat néhány másodpercig.

1. Válassza **a hivesampletable**lehetőséget, majd a **Tovább**gombot.

1. Válassza a **Finish** (Befejezés) elemet.

1. Az **Adatok importálása** párbeszédpanelen módosíthatja vagy megadhatja a lekérdezést. Ehhez válassza a **Tulajdonságok lehetőséget.** Ez eltarthat néhány másodpercig.

1. Válassza a **Definíció** lapot. A parancs szövege:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   A definiált Ranger-házirendek alapján a hiveuser1 az összes oszlopra vonatkozó kiválasztási engedéllyel rendelkezik.  Így ez a lekérdezés működik a hiveuser1 hitelesítő adataival, de ez a lekérdezés nem működik a hiveuser2 hitelesítő adataival.

1. A Kapcsolat tulajdonságai párbeszédpanel bezárásához **kattintson** az OK gombra.

1. Az **Adatok importálása** párbeszédpanel bezárásához kattintson az **OK gombra.**  

1. Írja be újra a hiveuser1 jelszavát, majd kattintson az **OK** gombra. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe. Ha ez megtörtént, 11 adatoszlopot fog látni.

A második házirend (read-hivesampletable-devicemake) teszteléséhez az utolsó szakaszban

1. Adjon hozzá egy új munkalapot az Excelben.
2. Az adatok importálásához kövesse az utolsó eljárást.  Az egyetlen módosítás, amit csinál, hogy a hiveuser2 hitelesítő adatait használja a hiveuser1 hitelesítő adatai helyett. Ez sikertelen, mert a hiveuser2 csak két oszlop megtekintésére jogosult. A következő hibaüzenetnek kell megjelennie:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Az adatok importálásához kövesse ugyanazt az eljárást. Ez alkalommal hiveuser2 hitelesítő adatait használja, és módosítsa a kiválasztási utasítást erről:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    erre:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Ha ez megtörtént, két importált adatoszlopot fog látni.

## <a name="next-steps"></a>További lépések

* A HDInsight-fürt vállalati biztonsági csomaggal történő konfigurálásáról a [HDInsight-fürtök konfigurálása ESP szolgáltatással](apache-domain-joined-configure.md)című témakörben látható.
* A HDInsight-fürtök ESP-vel való kezeléséről a [HDInsight-fürtök kezelése ESP-vel.](apache-domain-joined-manage.md)
* Az SSH-t használó Hive-lekérdezések ESP-vel történő futtatásáról az [SSH használata a HDInsight szolgáltatással(SSH) (SSH használata HDInsightdal) témakörben van.](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)
* A Hive JDBC használatával a Hive-hoz való csatlakozás ról az [Apache Hive-hoz az Azure HDInsight-on a Hive JDBC-illesztőprogram használatával című](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) témakörben
* Az Excel Hadoophoz való csatlakozása a Hive ODBC használatával című témakörben olvashat: [Az Excel csatlakoztatása az Apache Hadoophoz a Microsoft Hive ODBC meghajtóval](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Az Excel Hadoophoz való csatlakozása a Power Query használatával című témakörben olvashat: [Az Excel csatlakoztatása az Apache Hadoophoz a Power Query használatával című](../hadoop/apache-hadoop-connect-excel-power-query.md) témakörben
