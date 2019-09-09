---
title: HDInsight-fürtök kezelése nagyvállalati biztonsági nagyvállalattal – Azure
description: Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket Enterprise Security Packageokkal.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: acf11b8107855c6f087f63f592cdbbad3766795e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811026"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight-fürtök kezelése Enterprise Security Package
Megismerheti a HDInsight Enterprise Security Package (ESP) felhasználóit és szerepköreit, valamint az ESP-fürtök kezelését.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Az Apache Ambari Managed username használatával összekapcsolhat egy normál fürtöt, és a tartomány felhasználóneve (például: `user1@contoso.com`) használatával is összekapcsolhatja a biztonsági Apache Hadoop fürtöt.
1. Nyissa meg a parancssort a **CTRL + SHIFT + P billentyűkombinációval**, **majd írja be a HDInsight: Fürt**csatolása.

   ![fürt csatolása parancs](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Adja meg a HDInsight-fürt URL-címét – > bemeneti felhasználónevet – > bemeneti jelszó – > válassza a fürt típusa lehetőséget – > Ez a siker információit jeleníti meg, ha az ellenőrzés
   
   ![fürt csatolása párbeszédpanel](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > A rendszer a csatolt felhasználónevet és jelszót használja, ha a fürt mind az Azure-előfizetésben jelentkezett be, mind a fürthöz csatlakoztatva van. 
   
3. Egy csatolt fürtöt a parancssori **fürt**használatával tekinthet meg. Most elküldhet egy parancsfájlt ehhez a csatolt fürthöz.

   ![csatolt fürt](./media/apache-domain-joined-manage/linked-cluster.png "csatolt fürt")

4. A fürtöket a HDInsight bevitelével **is leválaszthatja: Fürt** leválasztása a parancssorból.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

A Ambari Managed username használatával összekapcsolhat egy normál fürtöt, és a tartomány felhasználóneve (például: `user1@contoso.com`) használatával is összekapcsolhatja a biztonsági Hadoop-fürtöt. 
1. Kattintson **a fürt csatolása** az **Azure Explorerben**elemre.

   ![fürt helyi menüjének csatolása IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Adja meg a **fürt nevét**, a **felhasználónevet** és a **jelszót**. Ha hitelesítési hiba van, ellenőrizze a felhasználónevet és a jelszót. Igény szerint vegye fel a Storage-fiókot, a tárolási kulcsot, majd válasszon ki egy tárolót a tárolóból. A tároló adatai a bal oldali fában található Storage Explorerben
   
   ![fürt csatolása párbeszédpanel IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > A csatolt Storage-kulcsot, a felhasználónevet és a jelszót használjuk, ha a fürt az Azure-előfizetésben is be van jelentkezve, és egy fürthöz kapcsolódik.
   > ![Storage Explorer a IntelliJ-ben](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Ha a bemeneti adatok helyesek, a **HDInsight** csomópontban láthat egy csatolt fürtöt. Most elküldheti az alkalmazást ehhez a csatolt fürthöz.

   ![csatolt fürt IntelliJ](./media/apache-domain-joined-manage/linked-cluster-intellij.png "csatolt fürt IntelliJ]")

4. A fürtöket az **Azure Explorerben**is leválaszthatja.
   
   ![nem összekapcsolt fürt IntelliJ](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

A Ambari Managed username használatával összekapcsolhat egy normál fürtöt, és a tartomány felhasználóneve (például: `user1@contoso.com`) használatával is összekapcsolhatja a biztonsági Hadoop-fürtöt.
1. Kattintson **a fürt csatolása** az **Azure Explorerben**elemre.

   ![fürt helyi menüjének összekapcsolása](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Adja meg a **fürt nevét**, a **felhasználónevet** és a **jelszót**, majd kattintson az OK gombra a fürt csatolásához. Szükség esetén megadhatja a Storage-fiókot, a Storage-kulcsot, majd kiválaszthatja a Storage Explorer tárolási tárolóját a bal oldali fanézetben való működéshez.
   
   ![fürt csatolása párbeszédpanel Eclipse](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > A csatolt Storage-kulcsot, a felhasználónevet és a jelszót használjuk, ha a fürt az Azure-előfizetésben is be van jelentkezve, és egy fürthöz kapcsolódik.
   > ![Storage Explorer az Eclipse-ben](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Ha a bemeneti adatok helyesek, az OK gombra kattintás után megtekintheti a csatolt fürtöt a **HDInsight** -csomópontban. Most elküldheti az alkalmazást ehhez a csatolt fürthöz.

   ![csatolt fürt Eclipse](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. A fürtöket az **Azure Explorerben**is leválaszthatja.
   
   ![leválasztási fürt Eclipse](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>A fürtök elérése Enterprise Security Packagesal.

Enterprise Security Package (korábbi nevén HDInsight Premium) többfelhasználós hozzáférést biztosít a fürthöz, ahol a hitelesítés az Apache Ranger és a tárolási ACL-ek (ADLS ACL-ek) által végzett Active Directory és engedélyezéssel történik. Az engedélyezés több felhasználó számára biztosít biztonságos határokat, és lehetővé teszi, hogy csak a jogosultsággal rendelkező felhasználók férhessenek hozzá az adataihoz az engedélyezési házirendek alapján.

A biztonság és a felhasználók elkülönítése a HDInsight-fürtök esetében fontos a Enterprise Security Package. A követelmények teljesítése érdekében az SSH-hozzáférés a fürthöz Enterprise Security Package blokkolva van. Az alábbi táblázat az egyes fürtök ajánlott hozzáférési módszereit mutatja be:

|Számítási feladat|Forgatókönyv|Hozzáférési módszer|
|--------|--------|-------------|
|Apache Hadoop|Struktúra – interaktív feladatok/lekérdezések  |<ul><li>[Beeline](#beeline)</li><li>[Struktúra nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktív feladatok/lekérdezések, interaktív PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin és Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Struktúra nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch-forgatókönyvek – Spark beküldés, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktív lekérdezés (LLAP)|Interaktív|<ul><li>[Beeline](#beeline)</li><li>[Struktúra nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|Egyéni alkalmazás telepítése|<ul><li>[Parancsfájlok műveletei](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > A Jupyter nincs telepítve/nem támogatott a Enterprise Security Packageban.

A standard API-k használata a biztonsági szempontból nyújt segítséget. Emellett a következő előnyöket veheti igénybe:

1.  **Felügyelet** – kezelheti a kódot, és automatizálhatja a feladatokat standard API-k használatával – LIVY, HS2 stb.
2.  **Auditálás** – SSH-val nem lehet naplózni, hogy mely felhasználók SSH-t kívánnak a fürtön. Ez nem lehetséges, ha a feladatokat standard végpontokon keresztül készíti el, mivel azok a felhasználó kontextusában lesznek végrehajtva. 



### <a name="beeline"></a>A Beeline használata 
Telepítse a Beeline szolgáltatást a gépére, és kapcsolódjon a nyilvános interneten keresztül, használja a következő paramétereket: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Ha helyileg telepítette a telepítést, és egy Azure-Virtual Network kapcsolódik, használja a következő paramétereket: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

A átjárócsomóponthoz teljes tartománynevének megkereséséhez használja a HDInsight kezelése a Ambari REST API dokumentum segítségével című témakörben található információkat.














## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP-vel rendelkező HDInsight-fürtök felhasználói
A nem ESP-alapú HDInsight-fürtök két, a fürt létrehozása során létrehozott felhasználói fiókkal rendelkeznek:

* **Ambari-rendszergazda**: Ezt a fiókot *Hadoop-felhasználóként* vagy *http-felhasználóként*is nevezzük. Ez a fiók használható a Ambari-be való bejelentkezéshez a&lt;következő helyen: https://clustername >. azurehdinsight. net. Emellett a Ambari-nézeteken végzett lekérdezések futtatására, a feladatok külső eszközökön (például a PowerShell, a Templeton, a Visual Studio) keresztül történő végrehajtására, valamint a kaptár ODBC-illesztővel és BI-eszközökkel való hitelesítésére is használható (például Excel, Power BI vagy tabló).

Az ESP-vel rendelkező HDInsight-fürtök három új felhasználóval rendelkeznek a Ambari-rendszergazda mellett.

* **Ranger rendszergazdája**:  Ez a fiók a helyi Apache Ranger-rendszergazdai fiók. Nem Active Directory tartományi felhasználó. Ezzel a fiókkal házirendeket állíthat be, és más felhasználók rendszergazdáit vagy delegált rendszergazdáit is elvégezheti (így a felhasználók kezelhetik a szabályzatokat). Alapértelmezés szerint a Felhasználónév a *rendszergazda* , és a jelszó megegyezik a Ambari rendszergazdai jelszavával. A jelszót a Ranger beállítások lapján lehet frissíteni.
* **Fürt rendszergazdai tartományának felhasználója**: Ez a fiók egy Active Directory tartományi felhasználó, amely Hadoop-fürtként van kijelölve, beleértve a Ambari és a Rangert is. A fürt létrehozása során meg kell adnia a felhasználó hitelesítő adatait. Ez a felhasználó a következő jogosultságokkal rendelkezik:

  * Csatlakoztassa a gépeket a tartományhoz, és helyezze őket a fürt létrehozása során megadott szervezeti egységbe.
  * Hozzon létre egyszerű szolgáltatásokat a fürt létrehozása során megadott szervezeti egységen belül.
  * Fordított DNS-bejegyzések létrehozása.

    Vegye figyelembe, hogy a többi AD-felhasználó is rendelkezik ezekkel a jogosultságokkal.

    A fürtön belül vannak olyan végpontok (például Templeton), amelyeket nem a Ranger felügyel, ezért nem biztonságos. Ezek a végpontok minden felhasználó számára le vannak zárva, kivéve a fürt rendszergazdai tartományi felhasználóját.
* **Normál**: A fürt létrehozása során több Active Directory-csoportot is megadhat. A csoportok felhasználói szinkronizálva vannak a Ranger és a Ambari között. Ezek a felhasználók tartományi felhasználók, és csak a Ranger által felügyelt végpontokhoz férnek hozzá (például Hiveserver2). Az összes RBAC szabályzat és naplózás a felhasználókra érvényes lesz.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Az ESP-vel rendelkező HDInsight-fürtök szerepkörei
A HDInsight Enterprise Security Package a következő szerepkörökkel rendelkezik:

* Fürt rendszergazdája
* Fürt operátora
* Szolgáltatás-rendszergazda
* Szolgáltatás operátora
* Fürt felhasználója

**A szerepkörök engedélyeinek megtekintéséhez**

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **szerepkörök**elemre.
3. Kattintson a kék kérdőjelre az engedélyek megtekintéséhez:

    ![ESP HDInsight-szerepkörök engedélyei](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>A Ambari-kezelés felhasználói felületének megnyitása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürtöt.
3. A felső menüben kattintson az **irányítópult** elemre a Ambari megnyitásához.
4. Jelentkezzen be a Ambari a fürt rendszergazdai tartományának felhasználónevével és jelszavával.
5. Kattintson a jobb felső sarokban található **rendszergazda** legördülő menüre, majd kattintson a **Ambari kezelése**lehetőségre.

    ![ESP-HDInsight a Ambari kezelése](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A felhasználói felület a következőképpen néz ki:

    ![ESP-HDInsight Ambari-kezelési felhasználói felülete](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>A Active Directoryról szinkronizált tartományi felhasználók listázása
1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **felhasználók**elemre. A Active Directoryról a HDInsight-fürtre szinkronizált összes felhasználót látnia kell.

    ![ESP-HDInsight Ambari-kezelési felhasználói felületének listájának felhasználói](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>A Active Directory szinkronizált tartományi csoportok listázása
1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **csoportok**elemre. A Active Directoryról a HDInsight-fürtre szinkronizált összes csoportot látnia kell.

    ![ESP HDInsight Ambari felügyeleti felhasználói felületi csoportok listája](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>A kaptár-nézetek engedélyeinek konfigurálása
1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **nézetek**elemre.
3. A részletek megjelenítéséhez kattintson a **struktúra** elemre.

    ![ESP HDInsight Ambari-kezelő felhasználói felületi struktúrájának nézetei](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Kattintson a **kaptár nézet** hivatkozásra a kaptár nézeteinek konfigurálásához.
5. Görgessen le az **engedélyek** szakaszhoz.

    ![ESP HDInsight Ambari-kezelő felhasználói felületi struktúrájának nézeteire vonatkozó engedélyek konfigurálása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Kattintson a **felhasználó hozzáadása** vagy a **Csoport hozzáadása**elemre, majd adja meg azokat a felhasználókat vagy csoportokat, akik használhatják a kaptár nézeteket.

## <a name="configure-users-for-the-roles"></a>Felhasználók konfigurálása a szerepkörökhöz
 A szerepkörök és a hozzájuk tartozó engedélyek listájának megtekintéséhez lásd: HDInsight-fürtök, ESP-vel.

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **szerepkörök**elemre.
3. A felhasználók és csoportok különböző szerepkörökhöz való hozzárendeléséhez kattintson a **felhasználó hozzáadása** vagy a **Csoport hozzáadása** elemre.

## <a name="next-steps"></a>További lépések
* A HDInsight-fürtök Enterprise Security Package-vel való konfigurálásával kapcsolatban lásd: [HDInsight-fürtök beállítása az ESP-vel](apache-domain-joined-configure.md).
* A kaptár-házirendek konfigurálásához és a kaptár-lekérdezések futtatásához lásd: [Apache Hive házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md).
