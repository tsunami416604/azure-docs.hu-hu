---
title: "A felhasználóknak engedélyezik, az Ambari nézetek - Azure HDInsight |} Microsoft Docs"
description: "Hogyan tartományhoz a HDInsight-fürtök az Ambari felhasználók és csoportok engedélyeinek kezelése."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 02761b0e4a25e9aa2312d66bb8c6ab861fb0c3cc
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="authorize-users-for-ambari-views"></a>Az Ambari Views használatának engedélyezése felhasználók számára

[A HDInsight-fürtök tartományhoz](./domain-joined/apache-domain-joined-introduction.md) adja meg a vállalati szintű képességet, beleértve az Azure Active Directory-alapú hitelesítés. Szinkronizálhatja az új felhasználók
<!-- [synchronize new users](hdinsight-sync-aad-users-to-cluster.md) --> added to Azure AD groups that have been provided access to the cluster, allowing those specific users to perform certain actions. Working with users, groups, and permissions in Ambari is supported for both domain-joined HDInsight cluster and standard HDInsight cluster.

Active Directory-felhasználók bejelentkezhetnek a fürtcsomópontokon, a tartományi hitelesítő adataik használatával. A tartományi hitelesítő adataik fürt interakciók más jóváhagyott végpontok, például a Hue, az Ambari nézetek, a ODBC, a JDBC, a PowerShell és a REST API-k hitelesítésére szolgáltatást is alkalmazhatja.

> [!WARNING]
> Ne változtassa meg jelszavát a Linux-alapú HDInsight-fürtök az Ambari figyelő (hdinsightwatchdog). A jelszó módosítása megsérti a Parancsfájlműveletek vagy méretezési műveleteket a fürthöz.

Ha még nem tette meg, kövesse az [ezeket az utasításokat](./domain-joined/apache-domain-joined-configure.md) tartományhoz tartozó új fürt létrehozásához.

## <a name="access-the-ambari-management-page"></a>Hozzáférés az Ambari kezelése lap

Eléréséhez a **Ambari kezelése lap** a a [Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md), keresse meg a  **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Adja meg a fürt rendszergazdai jogosultságú felhasználónevet és jelszót, amelyet a fürt létrehozásakor megadott. Az Ambari irányítópulton, válassza ki, **kezelése az Ambari** alatt a **admin** menüben:

![Ambari kezelése](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-hive-views"></a>Engedélyezze, hogy a Hive-nézetek

Ambari részeként elérhető példányok megtekintése a Hive és a Tez, többek között. Egy vagy több Hive példányok megtekintése hozzáférést, lépjen a **Ambari kezelése lap**.

1. A kezelés lapon, válassza ki a **nézetek** hivatkozásra a **nézetek** a bal oldali menü fejlécének.

    ![Nézetek hivatkozás](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. A nézetek lapon bontsa ki a **HIVE** sor. Nincs jön létre, amikor a Hive-szolgáltatás hozzáadása a fürthöz egy alapértelmezett Hive nézete. További Hive példányok megtekintése igény szerint is létrehozhat. Hive nézet kiválasztása:

    ![Nézetek - Hive nézete](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. A nézet lap alján görgessen. Az a *engedélyek* szakaszban, két választási lehetősége van a tartományi felhasználók nézetbe rájuk vonatkozó engedélyek megadására:

**Engedélyt ad a felhasználók** ![engedélyt ad a felhasználók](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Ezekhez a csoportokhoz engedélyt** ![engedélyt ezekhez a csoportokhoz](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. Felhasználó hozzáadásához jelölje ki a **felhasználó hozzáadása** gombra.

    * Írjon be a felhasználónevet, és megjelenik a korábban definiált nevek a legördülő listából.

    ![Felhasználói autocompletes](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Válassza ki, vagy a bevitelt, a felhasználó nevét. Ezt a felhasználónevet egy új felhasználó hozzáadásához válassza a **új** gombra.

    * A módosítások mentéséhez, válassza ki a **Kék jelölőnégyzet**.

    ![Felhasználó által megadott](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Csoport hozzáadásához válassza ki a **csoport hozzáadása** gombra.

    * Kezdje el beírni a csoport nevét. A folyamat egy meglévő csoport nevét, vagy egy új csoport hozzáadása ugyanaz, mint a felhasználók hozzáadásával.
    * A módosítások mentéséhez, válassza ki a **Kék jelölőnégyzet**.

    ![A megadott csoport](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Felhasználók hozzáadása közvetlenül egy nézet akkor hasznos, ha szeretné a engedélyek hozzárendelése a felhasználó a fenti nézetben, de szeretné, hogy további engedélyekkel rendelkező csoport tagjának lennie. Csökkenteni az adminisztrációs terhelést, lehet engedélyek hozzárendelése a csoportokat az egyszerűbb.

## <a name="grant-permissions-to-tez-views"></a>Engedélyezze, hogy Tez nézetek

A Tez példányok megtekintése engedélyezése a figyelheti, és minden Tez feladatokhoz, elküldte a Hive-lekérdezéseket és Pig-parancsfájlok hibakeresése. Van egy alapértelmezett Tez nézet példány jön létre, amikor a fürt ki van építve.

Felhasználók és csoportok hozzárendelése a Tez nézet példánya, bontsa ki a **TEZ** korábban leírt sor: a nézetek lapon.

![Nézetek - Tez megtekintése](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Felhasználók vagy csoportok hozzáadása, ismételje meg az előző szakasz 3-5.

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz

A felhasználók és csoportok, a hozzáférési engedélyek csökkenő sorrendben öt biztonsági szerepkörök állnak rendelkezésre:

* A Fürtfelügyelő
* Fürt operátor
* Szolgáltatás-rendszergazda
* Szolgáltatás operátor
* Fürt felhasználói

Szerepkörök kezelése, keresse fel a **Ambari kezelése lap**, majd jelölje be a **szerepkörök** belül hivatkozásra a *fürtök* a bal oldali menü csoport.

![Szerepkörök menü-hivatkozás](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Minden egyes szerepkör számára megadott engedélyek listájának megtekintéséhez kattintson a kék kérdőjel mellett a **szerepkörök** táblázatfejlécet a szerepkörök lapon.

![Szerepkörök menü-hivatkozás](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Ezen a lapon nincsenek szerepkörök a felhasználók és csoportok kezelése segítségével két különböző nézeteket: Block és listáját.

### <a name="block-view"></a>Blokknézet

A blokknézet megjeleníti, amelyben minden egyes szerepkör külön sorban, és a **szerepkörök hozzárendelése ezek a felhasználók** és **szerepkörök hozzárendelése az ezekhez a csoportokhoz** korábban leírt beállítások.

![Szerepkörök letiltása megtekintése](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Listanézet

A lista nézet két kategóriába gyors szerkesztési képességeket biztosít: felhasználók és csoportok.

* A felhasználók kategória a lista nézet megjeleníti az összes felhasználót, hogy lehetővé teszi az egyes felhasználói szerepkör kiválasztása a legördülő listából.

    ![Szerepkörök listanézet - felhasználók](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* A csoportok kategória a listanézet összes csoport és az egyes csoportokhoz rendelt szerepkör jeleníti meg. A jelen példában csoportok listájának szinkronizálása a megadott Azure AD-csoport a **hozzáférés felhasználói csoport** a fürt tartománybeállítások tulajdonsága. Lásd: [hozzon létre HDInsight-fürt](./domain-joined/apache-domain-joined-configure.md#create-an-hdinsight-cluster-in-the-vnet).

    ![Szerepkörök listanézet - csoportok](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    A fenti kép, a "hiveusers" csoport hozzá van rendelve a *fürt felhasználói* szerepkör. A rendszer csak olvasható, és lehetővé teszi a felhasználók az adott csoportok megtekintése, de nem módosíthatja a szolgáltatáskonfiguráció és a fürt metrikák.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Jelentkezzen be az Ambari egy csak megtekintésre

A Microsoft rendelt hozzá az Azure AD tartományi felhasználói "hiveuser1" engedélyek Hive és a Tez nézetet. Ha azt az Ambari webes felhasználói felületének indítása, és írja be a felhasználó tartományi hitelesítő adatok (az Azure AD felhasználói név e-mail formátum, és jelszó), a felhasználót a rendszer átirányítja az Ambari nézetek oldalra. Itt választható ki bármely elérhető nézetet. A felhasználó nem látogasson el a többi része a helyen, beleértve az irányítópult, a szolgáltatások, a gazdagépekhez, a riasztások vagy a felügyeleti lapokat.

![A nézetek csak a felhasználó](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Jelentkezzen be Ambari fürt felhasználóként

Azt az Azure AD tartományi felhasználói "hiveuser2" rendelt-e a *fürt felhasználói* szerepkör. Ez az a szerepe, hozzáférhetnek az irányítópult és az összes menüpont. A fürt felhasználó rendelkezik-e a rendszergazda-nál kevesebb engedélyezett lehetőséget. Például a hiveuser2 egyes szolgáltatások konfigurációi megtekinthetik, de nem szerkeszthetők.

![Fürt felhasználói szerepkörrel rendelkező felhasználó](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>További lépések

* [A tartományhoz csatlakoztatott HDInsight Hive-szabályzatok konfigurálása](./domain-joined/apache-domain-joined-run-hive.md)
* [Tartományhoz csatlakozó HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md)
* [A Hive nézet használata a hadooppal a Hdinsightban](hadoop/apache-hadoop-use-hive-ambari-view.md)

<!-- * [Synchronize Azure AD users to the cluster](hdinsight-sync-aad-users-to-cluster.md) -->
