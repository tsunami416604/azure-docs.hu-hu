---
title: "Tartományhoz csatlakozó HDInsight-fürtök - Azure kezelése |} Microsoft Docs"
description: "A tartományhoz a HDInsight-fürtök kezelése"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 0fc32960fc2f1ae69315dbfd6bfb8c34c4adc0fa
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakozó HDInsight-fürtök kezelése
Ismerje meg, a felhasználók és a szerepköröket, a tartományhoz, és a tartományhoz a HDInsight-fürtök kezelése.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>A tartományhoz a HDInsight-fürtök felhasználók
Egy HDInsight-fürthöz nem csatlakozó tartomány-van két olyan felhasználói fiókot, amely a fürt létrehozása során jönnek létre:

* **Ambari admin**: Ez a fiók akkor is *Hadoop felhasználói* vagy *HTTP felhasználói*. Ez a fiók használható jelentkezhet be az Ambari https://&lt;clustername >. azurehdinsight.net. Is használható lekérdezéseket futtathat az Ambari nézetek, külső eszközök (azaz PowerShell, lépni a Templeton, Visual Studio) keresztül feladatok végrehajtása és a Hive ODBC-illesztővel és az Üzletiintelligencia-eszközök (pl. Excel, Power bi vagy Tableau) a hitelesítést.
* **SSH-felhasználó**: Ez a fiók SSH együtt, és sudo parancsok. A Linux virtuális gépek legfelső szintű engedélyekkel rendelkezik.

A tartományhoz csatlakoztatott HDInsight-fürt három mellett Ambari rendszergazda új felhasználókat és SSH-felhasználó rendelkezik.

* **Pletyka admin**: Ez egy a Apache Pletyka helyi rendszergazdai fiók. Az active directory tartományi felhasználó nincs. Ez a fiók használható házirendek beállítása, és más felhasználók rendszergazdák vagy delegált rendszergazdák (így ezen házirendek kezelése). Alapértelmezés szerint a felhasználónév az *admin* és a jelszó megegyezik az Ambari rendszergazdai jelszavát. A jelszó a Pletyka a beállítások lapon lehet frissíteni.
* **Fürt tartományi felhasználót a rendszergazda**: A fiók egy active directory tartományi felhasználóra, beleértve az Ambari és Pletyka Hadoop fürthöz rendszergazdaként kijelölt legyen. Fürt létrehozása során a felhasználó hitelesítő adatait kell megadnia. A felhasználó rendelkezik-e a következő engedélyekkel:

  * Számítógépek csatlakoztatása a tartományhoz, és helyezze el őket a szervezeti egységet a fürt létrehozása során belül.
  * Hozzon létre szolgáltatásnevekről belül a szervezeti egységet a fürt létrehozása során.
  * Névkeresési DNS-bejegyzéseket létrehozni.

    Megjegyzés: az egyéb AD a felhasználóknak is ezeket a jogokat.

    Nincsenek egyes végpontok a fürtben (például lépni a Templeton) Pletyka által nem kezelt, és ezért nem biztonságos. A végpontok az összes felhasználó számára, kivéve a fürt rendszergazdai tartományi felhasználó van zárolva.
* **Rendszeres**: fürt létrehozása során megadhatja a több active directory-csoportokat. Ebben a csoportokban lévő felhasználók Pletyka és az Ambari lesznek szinkronizálva. Ezek a felhasználók tartományi felhasználók, és hozzáférhetnek csak Pletyka által felügyelt végpontok (például hiveserver2-n). Az RBAC-házirendek és ezek a felhasználók használható naplózási lesz.

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
2. Nyissa meg a HDInsight-fürt egy panelen. Lásd: [listája és megjelenítése fürtök](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
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

## <a name="next-steps"></a>Következő lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használata a tartományhoz csatlakoztatott HDInsight-fürtökön Hive-lekérdezéseket futtat, tekintse meg a [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
