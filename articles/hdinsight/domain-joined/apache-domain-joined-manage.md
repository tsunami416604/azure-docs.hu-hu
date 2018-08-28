---
title: Tartományhoz csatlakoztatott HDInsight-fürtök – Azure kezelése
description: Ismerje meg, hogyan lehet tartományhoz csatlakoztatott HDInsight-fürtök kezelése
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.openlocfilehash: 494049cffe77e23c33528747e04bf96065fac2e2
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051604"
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakoztatott HDInsight-fürtök kezelése
Ismerje meg, a felhasználók és a tartományhoz csatlakoztatott HDInsight és a tartományhoz csatlakoztatott HDInsight-fürtök kezelése a szerepkörök.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Hivatkozás egy normál fürt kezelése az Ambari felhasználónév használatával is, is hivatkozásra a biztonsági hadoop-fürt használatával: tartomány felhasználónév (például: user1@contoso.com).
1. Nyissa meg a parancskatalógust kiválasztásával **CTRL + SHIFT + P**, majd adja meg **HDInsight: egy fürtöt**.

   ![hivatkozás fürt parancs](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Adja meg a HDInsight fürt URL-cím-bemenet felhasználónév -> > be a jelszót a select -> fürttípus -> azt látható sikeres info ellenőrzési át.
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]
   > A társított felhasználónevet és jelszót használják, ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított. 
   
3. Parancs használatával megtekintheti a társított fürt **lista fürt**. Most már küldhet egy parancsfájlt, hogy a társított fürtöt.

   ![a csatolt fürt](./media/apache-domain-joined-manage/linked-cluster.png)

4. Is megszüntetheti a fürt által bevitelével **HDInsight: a fürt leválasztása** a parancskatalógus.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Hivatkozás egy normál fürt kezelése az Ambari felhasználónév használatával is, is hivatkozásra a biztonsági hadoop-fürt használatával: tartomány felhasználónév (például: user1@contoso.com). 
1. Kattintson a **egy fürtöt** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Adja meg **fürt neve**, **felhasználónév** és **jelszó**. Ellenőrizze a felhasználónevet és jelszót, ha a hitelesítési hiba van szüksége. Szükség esetén adja meg a Tárfiókot, a Tárfiók kulcsát, majd kiválaszthatja a tárolót a Storage-tárolóból. A bal oldali fában a storage Explorer van tárolással
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított a társított storage-kulcs, a felhasználónevet és jelszót használunk.
   > ![az intellij-ben a Storage Explorerben](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Láthatja, hogy a társított fürt **HDInsight** csomópont, ha a bemeneti adatokat megfelelőek. Ehhez a fürthöz társított alkalmazás most már küldhet.

   ![a csatolt fürt](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Fürt is megszüntetheti **Azure Explorer**.
   
   ![nem összekapcsolt fürt](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Hivatkozás egy normál fürt kezelése az Ambari felhasználónév használatával is, is hivatkozásra a biztonsági hadoop-fürt használatával: tartomány felhasználónév (például: user1@contoso.com).
1. Kattintson a **egy fürtöt** a **Azure Explorer**.

   ![hivatkozás fürt helyi menü](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Adja meg **fürtnév**, **felhasználónév** és **jelszó**, majd kattintson az OK gombra kattintva a fürtöt. Szükség esetén adja meg a Tárfiókot, a Tárfiók kulcsát, és válassza ki a tároló dolgozhat a bal oldali fanézetben a storage Explorer
   
   ![hivatkozás fürt párbeszédpanel](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított a társított storage-kulcs, a felhasználónevet és jelszót használunk.
   > ![az eclipse-ben a Storage Explorerben](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Láthatja, hogy a társított fürt **HDInsight** OK gombra kattintunk, ha a bemeneti adatokat a megfelelő csomópont. Ehhez a fürthöz társított alkalmazás most már küldhet.

   ![a csatolt fürt](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Fürt is megszüntetheti **Azure Explorer**.
   
   ![nem összekapcsolt fürt](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Hozzáférés a vállalati biztonsági csomag-fürtöket.

Vállalati biztonsági csomag (korábbi nevén HDInsight prémium szintű) a fürt, ahol hitelesítési végzi el az Active Directory és az Apache Ranger-és tárolási hozzáférés-vezérlési listák (ACL-ek ADLS) engedélyezése több felhasználó hozzáférést biztosít. Engedélyezési nyújt biztonságos határok több felhasználó használ, és lehetővé teszi, hogy csak a kiemelt jogosultságú felhasználók férnek hozzá az adatokhoz, a hitelesítési házirendek alapján.

Biztonság és a felhasználó elkülönítési fontosak egy HDInsight-fürt a vállalati biztonsági csomaggal. Ezek a követelmények teljesítéséhez, vállalati biztonsági csomaggal a fürthöz SSH-hozzáférés le van tiltva. Az alábbi táblázat az egyes fürttípus ajánlott hozzáférési metódusokat:

|Számítási feladat|Forgatókönyv|Hozzáférési mód|
|--------|--------|-------------|
|Hadoop|Hive – interaktív lekérdezések és feladatok |<ul><li>[Beeline](#beeline)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power bi-ban](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Interaktív feladatok és lekérdezésekkel, a PySpark interaktív|<ul><li>[Beeline](#beeline)</li><li>[A Zeppelin a Livyvel](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power bi-ban](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Batch-forgatókönyv – a Spark-submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Az interaktív lekérdezések (LLAP)|Interaktív|<ul><li>[Beeline](#beeline)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power bi-ban](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Bármelyik|Egyéni alkalmazás telepítése|<ul><li>[A Parancsfájlműveletek](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]
   > A vállalati biztonsági csomag telepítve van és támogatott Jupyter nem áll.

A standard API-k használatával segít a biztonság szempontjából. Emellett a következő előnyöket kap:

1.  **Felügyeleti** – a kód kezelheti és automatizálhatja a feladatokat a standard API-k használatával – Livy, HS2 stb.
2.  **Naplózási** – az ssh-val, nincs lehetőség a naplózása, hogy mely felhasználók SSH volt a fürthöz. Ez nem arra az esetre, ha a feladatok épített standard végpontokat keresztül azok lenne, felhasználó környezetében hajtja végre. 



### <a name="beeline"></a>A Beeline használata 
A gépén, telepítse a Beeline és a nyilvános interneten keresztül csatlakozni, használja a következő paramétereket: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Ha a Beeline helyben telepítve van, és egy Azure virtuális hálózaton keresztül, használja a következő paramétereket: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Egy átjárócsomópontjával teljesen minősített tartományneve megkereséséhez használja a a az Ambari REST API-dokumentumok kezelése HDInsight az adatokat.














## <a name="users-of-domain-joined-hdinsight-clusters"></a>Felhasználók tartományhoz csatlakoztatott HDInsight-fürtök
Egy HDInsight-fürtöt, amely a nem tartományhoz csatlakoztatott két felhasználói fiókot a fürt létrehozása során létrehozott rendelkezik:

* **Az Ambari rendszergazdai**: Ez a fiók akkor is *Hadoop-felhasználóra* vagy *HTTP-felhasználó*. Ez a fiók segítségével jelentkezzen be az Ambari, https://&lt;clustername >. azurehdinsight.NET formátumban van. Is használható lekérdezéseket futtathat az Ambari-nézetek, hajtsa végre a külső eszközöket (például PowerShell, templeton eszközön keresztül végzett, a Visual Studio)-feladatok és a Hive ODBC-illesztőt és az Üzletiintelligencia-eszközökkel (például az Excel, a Power bi vagy a Tableau) a hitelesítéshez.

A tartományhoz csatlakoztatott HDInsight-fürt Ambari rendszergazda mellett három új felhasználó rendelkezik

* **Ranger felügyeleti**: A fiók az Apache Ranger helyi rendszergazdai fiók legyen. Akkor sem az active directory tartományi felhasználó. Ez a fiók használható beállítási házirendek, és győződjön meg arról, más felhasználók rendszergazdák vagy a meghatalmazott rendszergazdák (úgy, hogy ezek a felhasználók kezelhetik a szabályzatok). Alapértelmezés szerint a felhasználónevet a *rendszergazdai* és a jelszó nem ugyanaz, mint az Ambari rendszergazdai jelszót. A jelszó (Ranger beállítások) lapján lehet frissíteni.
* **Fürt rendszergazdai tartományi felhasználó**: ezt a fiókot az active directory tartományi felhasználó megjelölte a Hadoop-fürt többek között az Ambari és a Ranger felügyeleti. A fürt létrehozásakor meg kell adni a felhasználó hitelesítő adatait. Ez a felhasználó rendelkezik a következő jogosultságokat:

  * Gépek csatlakoztatása a tartományhoz, és helyezze el őket a szervezeti Egységet a fürt létrehozásakor megadott belül.
  * A fürt létrehozásakor megadott szervezeti Egységben lévő egyszerű szolgáltatások létrehozása.
  * Fordított DNS-bejegyzéseket létrehozni.

    Vegye figyelembe, hogy a többi AD-felhasználók is ezeket a jogosultságokat.

    Vannak bizonyos végpontok a fürtben (például templeton eszközön keresztül végzett) amely Ranger által nem kezelt, és ezért nem biztonságos. Ezek a végpontok le az összes felhasználó számára, kivéve a fürt rendszergazdai tartományi felhasználó zárolva vannak.
* **Rendszeres**: fürt létrehozása során, adjon meg több active directory-csoportokat. A megadott csoportokban szereplő felhasználók Ranger és az Ambari vannak szinkronizálva. Ezek a felhasználók tartományi felhasználók, és csak Ranger által felügyelt végpontok (például hiveserver2-n) birtokában. Az RBAC-házirendeket és a naplózási fogja ezeket a felhasználókat a alkalmazni kell.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakoztatott HDInsight-fürtök szerepkörök
Tartományhoz csatlakoztatott HDInsight a következő szerepkörök rendelkeznek:

* Fürt rendszergazdája
* Fürt operátor
* Szolgáltatás-rendszergazda
* Szolgáltatás-operátor
* Fürt felhasználói

**Ezek a szerepkörök engedélyeinek megtekintéséhez**

1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari felügyeleti felhasználói Felületét](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **szerepkörök**.
3. Kattintson a kék kérdőjel az engedélyek megtekintéséhez:

    ![Tartományhoz csatlakoztatott HDInsight-szerepköri engedélyek](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Nyissa meg az Ambari felhasználói felület kezelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürt. Lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Kattintson a **irányítópult** Ambari megnyitásához a felső menüben.
4. Jelentkezzen be az Ambari a fürt rendszergazdai tartományi felhasználónevével és jelszavával.
5. Kattintson a **rendszergazdai** felső legördülő menüben kattintson a jobb sarokban található, és kattintson **kezelése az Ambari**.

    ![Tartományhoz csatlakoztatott HDInsight kezelése az Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A felhasználói felület hasonlóan néz ki:

    ![Tartományhoz csatlakoztatott HDInsight az Ambari felügyeleti felhasználói Felületét](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>A tartományi felhasználók az Active Directoryból szinkronizált listázása
1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari felügyeleti felhasználói Felületét](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **felhasználók**. A HDInsight-fürt az Active Directoryból szinkronizált az összes felhasználó látni.

    ![Tartományhoz csatlakoztatott HDInsight az Ambari management UI felhasználók listázása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>A tartományi csoportokat az Active Directoryból szinkronizált
1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari felügyeleti felhasználói Felületét](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **csoportok**. A HDInsight-fürt az Active Directoryból szinkronizált az összes csoportot látni.

    ![Tartományhoz csatlakoztatott HDInsight az Ambari management UI csoportok listázása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive-nézetek-engedélyek konfigurálása
1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari felügyeleti felhasználói Felületét](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **nézetek**.
3. Kattintson a **HIVE** részleteinek megjelenítéséhez.

    ![Tartományhoz csatlakoztatott HDInsight az Ambari management UI Hive-nézetek](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Kattintson a **Hive-nézet** hivatkozás Hive-nézetek konfigurálásához.
5. Görgessen le a **engedélyek** szakaszban.

    ![Tartományhoz csatlakoztatott HDInsight az Ambari management UI Hive-nézetek-engedélyek konfigurálása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Kattintson a **felhasználó hozzáadása** vagy **csoport hozzáadása**, majd adja meg a felhasználókat vagy csoportokat, amelyek a Hive-nézetek használatával.

## <a name="configure-users-for-the-roles"></a>A felhasználók a szerepkörök konfigurálása
 Szerepkörök és a rájuk vonatkozó engedélyek listáját lásd: [szerepkörök a tartományhoz csatlakoztatott HDInsight-fürtök](#roles-of-domain---joined-hdinsight-clusters).

1. Nyissa meg az Ambari felügyeleti felhasználói Felületét.  Lásd: [nyissa meg az Ambari felügyeleti felhasználói Felületét](#open-the-ambari-management-ui).
2. A bal oldali menüben kattintson a **szerepkörök**.
3. Kattintson a **felhasználó hozzáadása** vagy **csoport hozzáadása** felhasználók és csoportok hozzárendelése különböző szerepköröket.

## <a name="next-steps"></a>További lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
