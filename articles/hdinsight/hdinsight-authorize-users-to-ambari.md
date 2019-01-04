---
title: Ambari-nézetek – Azure HDInsight használatának engedélyezése a felhasználók
description: ESP engedélyezve-fürtök kezelése a HDInsight az Ambari felhasználói és engedélyeket.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 976bb44ae81a6c002d2503407a4d74d513a76042
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725269"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Az Apache Ambari Views használatának engedélyezése felhasználók számára

[Vállalati biztonsági csomag (ESP) engedélyezve van a HDInsight-fürtök](./domain-joined/apache-domain-joined-introduction.md) adja meg a nagyvállalati szintű funkciókat, beleértve az Azure Active Directory-alapú hitelesítés. Is [új felhasználók szinkronizálása](hdinsight-sync-aad-users-to-cluster.md) hozzá az Azure AD-csoportokat, amelyek adtak meg a fürthöz való hozzáférés lehetővé teszi adott felhasználók bizonyos műveletek elvégzéséhez. Felhasználók, csoportok és az engedélyek használata [Apache Ambari](https://ambari.apache.org/) ESP HDInsight-fürtök mind a standard szintű HDInsight-fürtök esetén támogatott.

Active Directory-felhasználók a fürtcsomópontok tartományi hitelesítő adataik használatával jelentkezhet be. Egyéb engedélyezett végpontokban, például a fürt interakció hitelesítéshez tartományi hitelesítő adataik is használhatják [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell és REST API-k.

> [!WARNING]  
> Ne módosítsa a jelszavát a Linux-alapú HDInsight-fürt Ambari figyelő (hdinsightwatchdog). A jelszó módosítása működésképtelenné válik a parancsfájlműveletekkel vagy a fürt skálázási műveleteket végez.

Ha még nem tette meg, hajtsa végre a [ezek az utasítások](./domain-joined/apache-domain-joined-configure.md) egy új ESP-fürt kiépítéséhez.

## <a name="access-the-ambari-management-page"></a>Az Ambari felügyeleti lap megnyitása

Beolvasásához a **Ambari lapját** a a [Apache Ambari webes Kezelőfelületen](hdinsight-hadoop-manage-ambari.md), keresse meg a **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Adja meg a fürt rendszergazdai felhasználónevet és jelszót, amelyet a fürt létrehozásakor megadott. Az Ambari irányítópultról majd **kezelése az Ambari** alá a **rendszergazdai** menüben:

![Az Ambari kezelése](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Engedélyek megadása az Apache Hive-nézetek

Példányok megtekintése az Ambari tartalmaz [Apache Hive](https://hive.apache.org/) és [Apache TEZ](https://tez.apache.org/), többek között. Legalább egy Hive-nézet példányok hozzáférést, lépjen a **Ambari lapját**.

1. A kezelés lapon, válassza ki a **nézetek** hivatkozásra a **nézetek** a bal oldali menü fejlécének.

    ![Nézetek összekapcsolása](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. A nézetek lapon bontsa ki a **HIVE** sor. Van egy alapértelmezett Hive-nézet létrehozásakor a Hive-szolgáltatás hozzáadása a fürthöz. További Hive view példányok igény szerint is létrehozhat. Egy Hive-nézet kiválasztása:

    ![Nézet – Hive-nézet](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. A nézet lap alján görgessen. Alatt a *engedélyek* szakaszban, a nézet az engedélyek megadását a tartományi felhasználók két lehetősége van:

**Ezekhez a felhasználókhoz engedélyeket** ![engedélyeket a felhasználóknak](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Jogosultság megadása a ezeket a csoportokat** ![jogosultság megadása a ezeket a csoportokat](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. A felhasználó hozzáadásához jelölje be a **felhasználó hozzáadása** gombra.

    * Kezdenie gépelni, a felhasználó nevét, és megjelenik egy legördülő lista korábban definiált nevek.

    ![Felhasználói autocompletes](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Válassza ki, vagy begépelte, a felhasználó nevét. Ezzel a felhasználónévvel, egy új felhasználó hozzáadásához válassza a **új** gombra.

    * A módosítások mentéséhez válassza a **Kék jelölőnégyzet**.

    ![Felhasználó által megadott](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Egy csoport hozzáadásához válassza a **csoport hozzáadása** gombra.

    * Kezdje el beírni a csoport nevét. Egy létező csoport nevének kijelölésekor, és a egy új csoport hozzáadása ugyanaz, mint a felhasználók hozzáadásával.
    * A módosítások mentéséhez válassza a **Kék jelölőnégyzet**.

    ![A megadott csoport](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Felhasználók hozzáadása közvetlenül egy nézet akkor hasznos, ha szeretné engedélyek hozzárendelése a felhasználói számára, hogy a nézet, de szeretné, hogy egy további engedélyekkel rendelkező csoport tagjának lennie. Csökkentheti az adminisztratív terhelést, lehet egyszerűbb engedélyek hozzárendelése a csoportokhoz.

## <a name="grant-permissions-to-apache-tez-views"></a>Az Apache TEZ nézetekhez engedélyek megadása

A [Apache TEZ](https://tez.apache.org/) példányok megtekintése engedélyezése a felhasználók figyelése és az összes Tez-feladatok, által küldött hibakereséséhez [Apache Hive](https://hive.apache.org/) lekérdezések és [Apache Pig](https://pig.apache.org/) parancsfájlokat. Van egy alapértelmezett Tez nézet példányt, amely a fürt létesítése jön létre.

Felhasználók és csoportok hozzárendelése egy Tez nézet példányt, bontsa ki a **TEZ** sor a nézetek lapon korábban leírtak szerint.

![Nézetek - Tez megtekintése](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Felhasználók vagy csoportok hozzáadása, ismételje meg az előző szakaszban 3 – 5.

## <a name="assign-users-to-roles"></a>Felhasználók szerepkörökhöz rendelése

Felhasználók és csoportok, a hozzáférési engedélyek csökkenő sorrendben öt biztonsági szerepkörök állnak rendelkezésre:

* Fürt rendszergazdája
* Fürt operátor
* Szolgáltatás-rendszergazda
* Szolgáltatás-operátor
* Fürt felhasználói

A szerepkörök kezeléséhez nyissa meg a **Ambari lapját**, majd válassza a **szerepkörök** belül hivatkozásra a *fürtök* a bal oldali menü csoport.

![Szerepkörök menü-hivatkozás](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Minden szerepkör engedélyeket listájának megtekintéséhez kattintson a a kék kérdőjel mellett a **szerepkörök** fejlécre a szerepkörök lapon.

![Szerepkörök menü-hivatkozás](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Ezen az oldalon van két különböző nézeteket a felhasználókat és csoportokat a szerepkörök kezeléséhez használhatja: Blokk- és listája.

### <a name="block-view"></a>Blokknézet

A blokk nézet jeleníti meg az egyes szerepkörök a saját sorában, és biztosítja a **szerepköröket hozzárendelni ezeket a felhasználókat** és **szerepköröket hozzárendelni ezeket a csoportokat** lehetőségek az előzőekben leírtak szerint.

![Szerepkörök nézetének blokkolása](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Listanézet

A listanézet kétféle gyors szerkesztési képességeket biztosítja: Felhasználók és csoportok.

* A listanézet felhasználók kategóriáját jeleníti meg az összes olyan felhasználó, lehetővé teszi, hogy a legördülő listában válassza ki a szerepkör minden felhasználóhoz.

    ![Szerepkörök listanézet - felhasználók](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  A listanézet csoportok kategóriáját jeleníti meg, az összes csoport és az egyes csoporthoz hozzárendelt szerepkör. Ebben a példában azokat a csoportokat szinkronizálása a megadott Azure AD-csoportokat a **hozzáférési felhasználói csoport** a fürt tartománybeállítások tulajdonságát. Lásd: [egy HDInsight-fürt létrehozása a engedélyezve ESP](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Szerepkörök listanézet - csoportok](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    A fenti ábrán a "hiveusers" csoport hozzá van rendelve a *fürt felhasználójának* szerepkör. Ez a csak olvasható szerepkör, amely lehetővé teszi a felhasználóknak a megtekintése, de nem módosíthatja a szolgáltatáskonfiguráció és fürtmetrikák csoportnak.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Jelentkezzen be az Ambari csak megtekintési felhasználóként

Hogy rendelt az Azure AD tartományi felhasználói "hiveuser1" engedélyek Tez Hive és a nézetek. Amikor azt indítsa el az Ambari webes felhasználói Felületet, és adja meg a felhasználó tartományi hitelesítő adatok (az Azure AD felhasználónév az e-mail formátumát és jelszó), a rendszer átirányítja a felhasználót az Ambari-nézetek oldalra. Itt a felhasználó kiválaszthat bármely elérhető nézetet. A felhasználó nem látogasson el a többi része a helyen, beleértve az irányítópult, a szolgáltatások, a gazdagépekhez, a riasztások vagy a rendszergazdai oldalakat.

![A nézetek csak a felhasználó](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Jelentkezzen be az Ambari fürt felhasználóként

Mi az Azure AD tartományi felhasználói "hiveuser2" rendelt hozzá a *fürt felhasználójának* szerepkör. Ez a szerepkör érhetik el az irányítópultot és az összes elem menü. A fürt felhasználójának, mint a rendszergazda kevesebb engedélyezett lehetőséggel rendelkezik. Például hiveuser2 megtekintheti az egyes szolgáltatások konfigurációi, de nem szerkesztheti.

![Fürt felhasználói szerepkörrel rendelkező felhasználók](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>További lépések

* [ESP a HDInsight az Apache Hive-házirendek konfigurálása](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md)
* [Az Apache Hive-nézet használata a HDInsight Apache Hadoop-keretrendszerrel](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [A fürt az Azure AD-felhasználók szinkronizálása](hdinsight-sync-aad-users-to-cluster.md)
