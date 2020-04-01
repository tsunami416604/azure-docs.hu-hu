---
title: Az Apache HBase & vállalati biztonsági csomag – Azure HDInsight
description: Oktatóanyag – Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat a HBase számára az Azure HDInsight vállalati biztonsági csomaggal.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 89e9faeb3c67d0fd0c57adea3a3f69ec5438e3a0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73044647"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Oktatóanyag: Az Apache HBase-házirendek konfigurálása a HDInsightban vállalati biztonsági csomaggal

Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat az Enterprise Security Package (ESP) Apache HBase fürtökhöz. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-szabályzatot hoz létre a HBase-tábla különböző oszlopcsaládokhoz való hozzáférésének korlátozására.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Táblázatok létrehozása HBase-fürtben
> * Ranger-házirendek tesztelése

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

* Hozzon létre egy [HDInsight HBase fürtöt vállalati biztonsági csomaggal.](apache-domain-joined-configure-using-azure-adds.md)

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://<ClusterName>.azurehdinsight.net/Ranger/`. Ne felejtsen el a HBase-fürt nevére váltani. `<ClusterName>`

    > [!NOTE]  
    > A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A [HDInsight-fürt létrehozása vállalati biztonsági csomaggal](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)című, **a sales_user1** és a **marketing_user1** tartományi felhasználók létrehozásácímű. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-hbase-tables-and-import-sample-data"></a>HBase-táblák létrehozása és mintaadatok importálása

Az SSH segítségével csatlakozhat a HBase-fürtökhöz, majd [az Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) segítségével HBase táblákat hozhat létre, adatokat szúrhat be és lekérdezési adatokat. További információ: [SSH használata a HDInsight segítségével.](../hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="to-use-the-hbase-shell"></a>A HBase rendszerhéj használata

1. Az SSH-ból futtassa az alábbi HBase-parancsot:
   
    ```bash
    hbase shell
    ```

2. Hozzon létre `Customers` egy HBase táblát `Name` `Contact`kétoszlopos családokkal: és .

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
4. A táblázat tartalmának megtekintése:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![HDInsight Hadoop HBase rendszerhéj kimenet](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása

Ranger-szabályzat létrehozása **sales_user1** és **marketing_user1**számára.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**. Kattintson ** \<a Fürtnév>_hbase** csoportban a **HBase**csoportban.

   ![HDInsight Apache Ranger felügyeleti felhasználói felülete](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. A **Szabályzatok listája** képernyőn megjelenik a fürthöz létrehozott összes Ranger-szabályzat. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend. Kattintson **az Új házirend hozzáadása gombra.**

    ![Apache Ranger HBase házirendek listája](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. A **Házirend létrehozása** képernyőn adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  sales_customers_name_contact   |
   |HBase tábla   |  Ügyfelek |
   |HBase oszlopcsalád   |  Név, Kapcsolattartó |
   |HBase oszlop   |  * |
   |Csoport kiválasztása  | |
   |Felhasználó kiválasztása  | sales_user1 |
   |Engedélyek  | Olvasás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * `*`a karakterek nulla vagy több előfordulását jelzi.
   * `?`egyetlen karaktert jelöl.

   ![Az Apache Ranger-szabályzat értékesítést hoz létre](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

5. Kattintson az **Add New Policy** (Új házirend hozzáadása) lehetőségre, majd adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  marketing_customers_contact   |
   |HBase tábla   |  Ügyfelek |
   |HBase oszlopcsalád   |  Kapcsolattartó |
   |HBase oszlop   |  * |
   |Csoport kiválasztása  | |
   |Felhasználó kiválasztása  | marketing_user1 |
   |Engedélyek  | Olvasás |

   ![Apache Ranger-szabályzat létrehozása marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

A Ranger-házirendek konfigurálása alapján **sales_user1** megtekintheti az oszlopok `Name` `Contact` összes adatát mind az oszlopcsaládokban. A **marketing_user1** csak az `Contact` oszlopcsalád adatait tekintheti meg.

### <a name="access-data-as-sales_user1"></a>Adatok elérése sales_user1

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. A következő paranccsal jelentkezzen be a fürtbe:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. A kinit paranccsal a kívánt felhasználó környezetére válthat.

   ```bash
   kinit sales_user1
   ```

2. Nyissa meg a HBase rendszerhéjat, és szkalegezsd be az asztalt `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Figyelje meg, hogy az értékesítési `Customers` felhasználó megtekintheti a `Name` tábla összes oszlopát, beleértve az `Contact` oszlopcsalád két oszlopát, valamint az oszlopcsalád öt oszlopát.

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

### <a name="access-data-as-marketing_user1"></a>Adatok elérése marketing_user1

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. A következő paranccsal marketing_user1 jelentkezzen **be:**

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. A kinit paranccsal válthat a kívánt felhasználó környezetére

   ```bash
   kinit marketing_user1
   ```

1. Nyissa meg a HBase rendszerhéjat, és szkacátyszol a táblázatban: `Customers`

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Figyelje meg, hogy a marketingfelhasználó `Contact` csak az oszlopcsalád öt oszlopát tekintheti meg.

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

1. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   ![HDInsight-felelős felhasználói felület házirend-naplózása](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépésekkel létrehozott HBase-fürtöt:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. A felső **keresőmezőbe** írja be a **HDInsight kifejezést.** 
1. Válassza a **HDInsight-fürtök et a** **Szolgáltatások csoportban.**
1. A megjelenő HDInsight-fürtök listájában kattintson az oktatóanyaghoz létrehozott fürt melletti **...** elemre. 
1. Kattintson a **Törlés** gombra. Kattintson **az Igen gombra.**

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés az Apache HBase-lel](../hbase/apache-hbase-tutorial-get-started-linux.md)
