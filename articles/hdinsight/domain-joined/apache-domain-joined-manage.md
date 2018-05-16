---
title: Tartományhoz csatlakozó HDInsight-fürtök - Azure kezelése
description: A tartományhoz a HDInsight-fürtök kezelése
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 31b58176a75918cf68cda695f27216031670493e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakozó HDInsight-fürtök kezelése
Ismerje meg, a felhasználók és a szerepköröket, a tartományhoz, és a tartományhoz a HDInsight-fürtök kezelése.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Egy normál fürt hivatkozás segítségével felügyelt Ambari felhasználónév, is hivatkozásra a biztonsági hadoop-fürthöz tartományi felhasználónevet használatával (például: user1@contoso.com).
1. Nyissa meg a parancs palettát kiválasztásával **CTRL + SHIFT + P**, majd adja meg **HDInsight: hivatkozás egy fürt**.

   ![hivatkozás fürt parancs](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Adja meg a HDInsight fürt URL -> felhasználónév -> bemeneti be a jelszót válasszon -> fürttípus -> azt látható sikeres info ellenőrzési át.
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]
   > Csatolt felhasználónevet és jelszót használják, ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz kapcsolódó. 
   
3. Megjelenik egy társított fürt paranccsal **lista fürt**. Most is elküldhetik a csatolt fürt parancsfájlt.

   ![csatolt fürt](./media/apache-domain-joined-manage/linked-cluster.png)

4. Is megszüntetheti a fürt által beírása **HDInsight: egy fürt leválasztása** parancs palettáról.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Egy normál fürt hivatkozás segítségével felügyelt Ambari felhasználónév, is hivatkozásra a biztonsági hadoop-fürthöz tartományi felhasználónevet használatával (például: user1@contoso.com). 
1. Kattintson a **fürt hivatkozás** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Adja meg **a fürt neve**, **felhasználónév** és **jelszó**. Ellenőrizze a felhasználónevet és jelszót, ha a hitelesítési hiba van szükség. Szükség esetén adja hozzá a Tárfiók, Biztonságitár-kulcs, majd jelöljön ki egy tárolót a tároló. A bal oldali fában Tártallózó tárolással kapcsolatos van
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz kapcsolódó csatolt biztonságitár-kulcs, felhasználónevet és jelszót használunk.
   > ![az IntelliJ Tártallózó](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Láthatja, hogy a társított fürt **HDInsight** csomópont, ha a bemeneti adatokat megfelelőek. Most is elküldhetik a csatolt fürthöz kérelmet.

   ![csatolt fürt](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Is megszüntetheti a fürtöt **Azure Explorer**.
   
   ![a fürt megszüntetése](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Egy normál fürt hivatkozás segítségével felügyelt Ambari felhasználónév, is hivatkozásra a biztonsági hadoop-fürthöz tartományi felhasználónevet használatával (például: user1@contoso.com).
1. Kattintson a **fürt hivatkozás** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Adja meg **fürtnév**, **felhasználónév** és **jelszó**, majd kattintson az OK gombra, ha szeretné kapcsolni a fürtöt. Megadhatja a Tárfiókot, a kulcs, és válassza ki a bal oldali fanézetben működéséhez Tártallózó a tároló
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz kapcsolódó csatolt biztonságitár-kulcs, felhasználónevet és jelszót használunk.
   > ![a Tártallózó az eclipse-ben](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Láthatja, hogy a társított fürt **HDInsight** csomópont után az OK gombra kattintás, ha a bemeneti adatokat megfelelőek. Most is elküldhetik a csatolt fürthöz kérelmet.

   ![csatolt fürt](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Is megszüntetheti a fürtöt **Azure Explorer**.
   
   ![a fürt megszüntetése](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Hozzáférés a vállalati biztonsági csomag fürtön.

A vállalati biztonsági csomag (korábbi nevén HDInsight prémium) a fürt, ahol hitelesítési végezhető el az Active Directory és az engedélyezés Apache Pletyka és a tároló hozzáférés-vezérlési listák (ACL-ek ADLS) több felhasználói hozzáférést biztosít. Engedélyezési biztosít a biztonságos határok több felhasználó használ, és lehetővé teszi, hogy csak jogosultsággal rendelkező felhasználók férhetnek hozzá az adatokhoz, a hitelesítési házirendek alapján.

Biztonság és a felhasználó elkülönítési fontosak a HDInsight-fürthöz, a vállalati biztonsági csomag. Ezek a követelmények teljesítéséhez, a vállalati biztonsági csomag fürt SSH-hozzáférését blokkolja. Az alábbi táblázat a minden egyes fürttípus ajánlott hozzáférési metódusokat:

|Számítási feladat|Eset|Hozzáférési módszer|
|--------|--------|-------------|
|Hadoop|Hive – interaktív feladatok/lekérdezések |<ul><li>[Beeline](#beeline)</li><li>[Hive nézete](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power bi-ban](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Interaktív feladatok/lekérdezések, interaktív PySpark|<ul><li>[Beeline](#beeline)</li><li>[A Livy Zeppelin](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive nézete](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power bi-ban](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Kötegelt forgatókönyvek – Spark küldés, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktív lekérdezés (LLAP)|Interaktív|<ul><li>[Beeline](#beeline)</li><li>[Hive nézete](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power bi-ban](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Bármely|Egyéni alkalmazás telepítése|<ul><li>[A Parancsfájlműveletek](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]
   > Jupyter nincs telepítve/támogatja a vállalati biztonsági csomag.

Standard API-val segíti a biztonsági szempontból. Ezenkívül töltse le a következő előnyöket biztosítja:

1.  **Felügyeleti** – kezelheti a kódot és a szabványos API-k használatával feladatok automatizálása – Livy, HS2 stb.
2.  **Naplózási** – az SSH, nincs mód annak vizsgálatához, mely felhasználók SSH volt a fürthöz. Ez nem tudnák arra az esetre, amikor a feladatok össze keresztül szabványos végpontot, azok akkor hajtható végre felhasználó környezetében. 



### <a name="beeline"></a>Beeline használata 
Beeline telepítése a számítógépre, és a nyilvános interneten keresztül, az alábbi paraméterekkel: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Ha helyileg telepített Beeline, és az Azure virtuális hálózaton keresztül csatlakozni, használja a következő paramétereket: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

A teljesen minősített tartománynevét egy headnode található, olvassa el az Ambari REST API-dokumentumok kezeléséhez HDInsight.














## <a name="users-of-domain-joined-hdinsight-clusters"></a>A tartományhoz a HDInsight-fürtök felhasználók
Egy HDInsight-fürthöz nem csatlakozó tartomány-van két olyan felhasználói fiókot, amely a fürt létrehozása során jönnek létre:

* **Ambari admin**: Ez a fiók akkor is *Hadoop felhasználói* vagy *HTTP felhasználói*. Ez a fiók használható jelentkezhet be az Ambari https://&lt;clustername >. azurehdinsight.net. Is használható lekérdezéseket futtathat az Ambari nézetek, külső eszközök (például PowerShell, lépni a Templeton, Visual Studio) keresztül feladatok végrehajtása és a Hive ODBC-illesztővel és az Üzletiintelligencia-eszközök (például az Excel, Power bi vagy Tableau) a hitelesítést.

A tartományhoz csatlakoztatott HDInsight-fürt rendelkezik három új felhasználók mellett Ambari rendszergazdával.

* **Pletyka admin**: Ez egy a Apache Pletyka helyi rendszergazdai fiók. Az active directory tartományi felhasználó nincs. Ez a fiók használható házirendek beállítása, és más felhasználók rendszergazdák vagy delegált rendszergazdák (így ezen házirendek kezelése). Alapértelmezés szerint a felhasználónév az *admin* és a jelszó megegyezik az Ambari rendszergazdai jelszavát. A jelszó a Pletyka a beállítások lapon lehet frissíteni.
* **Fürt tartományi felhasználót a rendszergazda**: A fiók egy active directory tartományi felhasználóra, beleértve az Ambari és Pletyka Hadoop fürthöz rendszergazdaként kijelölt legyen. Fürt létrehozása során a felhasználó hitelesítő adatait kell megadnia. A felhasználó rendelkezik-e a következő engedélyekkel:

  * Számítógépek csatlakoztatása a tartományhoz, és helyezze el őket a szervezeti egységet a fürt létrehozása során belül.
  * Hozzon létre szolgáltatásnevekről belül a szervezeti egységet a fürt létrehozása során.
  * Névkeresési DNS-bejegyzéseket létrehozni.

    Megjegyzés: az egyéb AD a felhasználóknak is ezeket a jogokat.

    Nincsenek egyes végpontok a fürtben (például lépni a Templeton) Pletyka által nem kezelt, és ezért nem biztonságos. A végpontok az összes felhasználó számára, kivéve a fürt rendszergazdai tartományi felhasználó van zárolva.
* **Rendszeres**: fürt létrehozása során megadhatja a több active directory-csoportokat. Ebben a csoportokban lévő felhasználók Pletyka és az Ambari vannak szinkronizálva. Ezek a felhasználók tartományi felhasználók, és csak Pletyka által felügyelt végpontok (például hiveserver2-n) hozzáféréssel rendelkeznek. Az RBAC-házirendek és ezek a felhasználók használható naplózási lesz.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>A tartományhoz a HDInsight-fürtök szerepkörök
Tartományhoz csatlakozó HDInsight rendelkezik a következő szerepkörök:

* A Fürtfelügyelő
* Fürt operátor
* Szolgáltatás-rendszergazda
* Szolgáltatás operátor
* Fürt felhasználói

**Ezek a szerepkörök engedélyeinek megtekintéséhez**

1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari kezelőfelület](#open-the-ambari-management-ui).
2. Kattintson a bal oldali menü **szerepkörök**.
3. Kattintson a kék kérdőjel az engedélyek megtekintéséhez:

    ![HDInsight szerepkörök engedélyekkel a tartományhoz](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Nyissa meg az Ambari kezelőfelület

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürthöz. Lásd: [listája és megjelenítése fürtök](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Kattintson a **irányítópult** a felső menüben Ambari megnyitásához.
4. Jelentkezzen be az Ambari segítségével a fürt rendszergazdai tartományi felhasználónevet és jelszót.
5. Kattintson a **Admin** legördülő menü felső sarokban található jobbra, és kattintson a **kezelése az Ambari**.

    ![Tartományhoz csatlakozó HDInsight kezelése az Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A felhasználói felület néz ki:

    ![Tartományhoz csatlakozó HDInsight Ambari kezelőfelület](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>A tartományi felhasználók az Active Directoryból szinkronizált felsorolása
1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari kezelőfelület](#open-the-ambari-management-ui).
2. Kattintson a bal oldali menü **felhasználók**. Ekkor megjelenik a HDInsight-fürthöz az Active Directoryból szinkronizált felhasználók.

    ![Tartományhoz csatlakozó HDInsight Ambari felügyeleti felhasználói felület felhasználók listázása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Az Active Directoryból szinkronizált csoportok listázása
1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari kezelőfelület](#open-the-ambari-management-ui).
2. Kattintson a bal oldali menü **csoportok**. Ekkor megjelenik az összes, a a HDInsight-fürthöz az Active Directoryból szinkronizált csoportok.

    ![Tartományhoz csatlakozó HDInsight Ambari felügyeleti felhasználói felület listázza a csoportokat](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Nézetek Hive-engedélyek konfigurálása
1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari kezelőfelület](#open-the-ambari-management-ui).
2. Kattintson a bal oldali menü **nézetek**.
3. Kattintson a **HIVE** a részletek megjelenítéséhez.

    ![HDInsight Ambari felügyeleti tartományhoz felhasználói felület Hive-nézetek](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Kattintson a **Hive View** hivatkozás Hive nézetek konfigurálásához.
5. Görgessen le a **engedélyek** szakasz.

    ![HDInsight Ambari felügyeleti tartományhoz felhasználói felület Hive nézetek engedélyek konfigurálása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Kattintson a **felhasználó hozzáadása** vagy **csoport hozzáadása**, és adja meg a felhasználókat vagy csoportokat, amelyek a Hive-nézetek használatával.

## <a name="configure-users-for-the-roles"></a>A szerepkörök a felhasználók konfigurálása
 Szerepkörök és a rájuk vonatkozó engedélyek listájának megtekintéséhez lásd: [szerepkörök a tartományhoz csatlakoztatott HDInsight-fürtök](#roles-of-domain---joined-hdinsight-clusters).

1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari kezelőfelület](#open-the-ambari-management-ui).
2. Kattintson a bal oldali menü **szerepkörök**.
3. Kattintson a **felhasználó hozzáadása** vagy **csoport hozzáadása** felhasználók és csoportok hozzárendelése különböző szerepeknek.

## <a name="next-steps"></a>További lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
