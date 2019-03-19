---
title: A vállalati biztonsági csomaggal – Azure HDInsight az Apache HBase-szabályzatok konfigurálása
description: Ismerje meg, az Apache Ranger-házirendek konfigurálása a hbase-hez az Azure HDInsight vállalati biztonsági csomaggal.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: tutorial
ms.date: 02/01/2019
ms.openlocfilehash: 1421b142fbca83d2de46f52f8390d0c25f22780c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117285"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Oktatóanyag: Az Apache HBase-házirendek konfigurálása a HDInsight vállalati biztonsági csomaggal (előzetes verzió)

Megtudhatja, hogyan konfigurálhatja a vállalati biztonsági csomag (ESP) az Apache HBase-fürtök az Apache Ranger-házirendet. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban létrehozhat két Ranger-házirendek különböző oszlopcsaláddal egy HBase-táblában való hozzáférés korlátozásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Táblázatok HBase-fürt létrehozása
> * Ranger-házirendek tesztelése

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Hozzon létre egy [HDInsight HBase-fürt a vállalati biztonsági csomaggal](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://<ClusterName>.azurehdinsight.net/Ranger/`. Ne felejtse el módosítani `<ClusterName>` a HBase-fürt nevére.

    > [!NOTE]  
    > A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

Látogasson el [egy HDInsight-fürt létrehozása a vállalati biztonsági csomag](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), megtudhatja, hogyan hozhat létre a **sales_user1** és **marketing_user1** tartományi felhasználók. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-hbase-tables-and-import-sample-data"></a>HBase-táblákat hozhat létre és Mintaadatok importálása

Ön SSH-val HBase-fürtökhöz csatlakozhat, és ezután [Apache HBase rendszerhéj](https://hbase.apache.org/0.94/book/shell.html) hozhat létre HBase-táblákat, helyezze be az adatokat, és adatokat kérdezhet le. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>A HBase rendszerhéj használata

1. Az SSH-ból futtassa az alábbi HBase-parancsot:
   
    ```bash
    hbase shell
    ```

2. Hozzon létre egy HBase-tábla `Customers` két oszlopcsaláddal: `Name` és `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Adatok beszúrása:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. A tábla tartalmának megtekintése:
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![HDInsight Hadoop HBase-rendszerhéj](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása

Hozzon létre egy Ranger-házirendet a **sales_user1** és **marketing_user1**.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**. Kattintson a  **\<ClusterName > _hbase** alatt **HBase**.

   ![Apache Ranger rendszergazdai felhasználói felület](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. A **házirendek felsorolása** képernyő jelenik meg a fürt számára létrehozott összes Ranger-házirendet. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend. Kattintson a **új szabályzat hozzáadása**.

    ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Az a **házirend létrehozása** képernyőn, adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  sales_customers_name_contact   |
   |HBase-tábla   |  Ügyfelek |
   |A HBase oszlopcsalád-   |  Kapcsolattartó neve |
   |A HBase oszlop   |  * |
   |Csoport kiválasztása  | |
   |Felhasználó kiválasztása  | sales_user1 |
   |Engedélyek  | Olvasás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * `*` azt jelzi, hogy nulla vagy több karakter előfordulását.
   * `?` azt jelzi, hogy egyetlen karaktert.

   ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

5. Kattintson az **Add New Policy** (Új házirend hozzáadása) lehetőségre, majd adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  marketing_customers_contact   |
   |HBase-tábla   |  Ügyfelek |
   |A HBase oszlopcsalád-   |  Kapcsolattartó |
   |A HBase oszlop   |  * |
   |Csoport kiválasztása  | |
   |Felhasználó kiválasztása  | marketing_user1 |
   |Engedélyek  | Olvasás |

   ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

Konfigurálva, a Ranger-házirendek alapján **sales_user1** is megtekintheti az adatok az oszlopok a `Name` és `Contact` oszlopcsaláddal. A **marketing_user1** csak megtekintheti az adatokat a `Contact` oszlopcsalád.

### <a name="access-data-as-salesuser1"></a>Mint sales_user1 érheti el adatait

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. A következő paranccsal jelentkezzen be a fürthöz:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Módosítsa a kívánt felhasználó használja a kinit parancsot.

   ```bash
   kinit sales_user1
   ```

2. Nyissa meg a hbase rendszerhéjat, és a tábla beolvasása `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Figyelje meg, hogy az értékesítési felhasználói megtekinthetik az összes oszlopot a `Customers` többek között a két oszlop a tábla a `Name` oszlopcsalád-, valamint az öt oszlopa a `Contact` oszlopcsalád.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>Mint marketing_user1 érheti el adatait

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. Jelentkezzen be a következő paranccsal **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Módosítsa a kívánt felhasználó használja a kinit parancsot

   ```bash
   kinit marketing_user1
   ```

2. Nyissa meg a hbase rendszerhéjat, és a tábla beolvasása `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Figyelje meg, hogy a marketing felhasználó csak megtekintheti az öt oszlopára vonatkozó a `Contact` oszlopcsalád.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   ![A Ranger felhasználói felület házirendnaplózása](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje a HBase-fürtöt az alábbi lépéseket követve létrehozott:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az a **keresési** tetején típus **HDInsight**. 
1. Válassza ki **HDInsight-fürtök** alatt **szolgáltatások**.
1. A HDInsight-fürtök megjelenő listában kattintson a **...**  ebben az oktatóanyagban létrehozott fürt mellett. 
1. Kattintson a **Törlés** gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Apache HBase használatának első lépései](../hbase/apache-hbase-tutorial-get-started-linux.md)
