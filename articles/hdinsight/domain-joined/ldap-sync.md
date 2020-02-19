---
title: LDAP-szinkronizálás a Ranger és az Apache Ambari az Azure HDInsight
description: Foglalkozzon az LDAP-szinkronizálással a Ranger-és Ambari, és adjon meg általános irányelveket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463218"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-szinkronizálás a Ranger és az Apache Ambari az Azure HDInsight

A HDInsight Enterprise Security Package (ESP) fürtök a Rangert használják az engedélyezéshez. Az Apache Ambari és a Ranger egymástól függetlenül szinkronizálja a felhasználókat és a csoportokat, és egy kicsit másképp működik. Ebből a cikkből megtudhatja, hogyan kell kezelni az LDAP-szinkronizálást a Rangerben és a Ambari.

## <a name="general-guidelines"></a>Általános irányelvek

* A fürtöket mindig a csoportokkal telepítse.
* A Ambari és a Rangerben lévő csoportok szűrőinek módosítása helyett próbálja meg felügyelni ezeket az Azure AD-ben, és használjon beágyazott csoportokat a szükséges felhasználók bevonásához.
* A felhasználó szinkronizálása után a rendszer nem távolítja el, még akkor sem, ha a felhasználó nem tagja a csoportnak.
* Ha közvetlenül módosítania kell az LDAP-szűrőket, először a felhasználói felületet kell használnia, mivel néhány érvényesítést tartalmaz.

## <a name="users-are-synced-separately"></a>A felhasználók szinkronizálása külön történik

A Ambari és a Ranger nem osztja meg a felhasználói adatbázist, mert két különböző célt szolgálnak. Ha a felhasználónak a Ambari felhasználói felületét kell használnia, a felhasználónak szinkronizálnia kell a Ambari. Ha a felhasználó nincs szinkronizálva a Ambari, a Ambari UI/API elutasítja, de a rendszer más részei is működni fognak (a Ranger vagy a Resource Manager által védett, és nem Ambari). Ha azt szeretné, hogy a felhasználó egy Ranger-szabályzatba kerüljön, szinkronizálja a felhasználót a Rangervel.

Biztonságos fürt telepítésekor a csoporttagok szinkronizálva vannak a Ambari és a Ranger tranzitívnak (az összes alcsoporttal és azok tagjaival). 

## <a name="ambari-user-sync-and-configuration"></a>Ambari-felhasználó szinkronizálása és konfigurálása

A fő csomópontok egy cron-feladatot (`/opt/startup_scripts/start_ambari_ldap_sync.py`) futtatnak óránként, hogy ütemezik a felhasználói szinkronizálást. A cron-feladatok meghívja a Ambari REST API-kat a szinkronizálás végrehajtásához. A parancsfájl elküldi a szinkronizálandó felhasználók és csoportok listáját (mivel előfordulhat, hogy a felhasználók nem tartoznak a megadott csoportokhoz, mindkettő külön van megadva). A Ambari a sAMAccountName a Felhasználónév és az összes csoporttagok tranzitívnak szinkronizálja.

A naplóknak `/var/log/ambari-server/ambari-server.log`kell lennie. További információ: a [Ambari naplózási szintjének konfigurálása](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Data Lake-fürtökön a felhasználó létrehozás utáni hookja a szinkronizált felhasználók otthoni mappáinak létrehozására szolgál, amelyek a Kezdőlap mappák tulajdonosaként vannak beállítva. Ha a felhasználó nincs megfelelően szinkronizálva a Ambari, akkor a felhasználó az átmeneti és az egyéb ideiglenes mappák elérésével kapcsolatos hibákba ütközne.

### <a name="update-groups-to-be-synced-to-ambari"></a>A Ambari szinkronizálandó csoportok frissítése

Ha nem tudja kezelni a csoportok tagságait az Azure AD-ben, két lehetőség közül választhat:

* Hajtson végre egy egyszeri szinkronizálást az [LDAP-felhasználók és-csoportok szinkronizálása](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)című részben leírtak szerint. Ha a csoporttagság megváltozik, újra kell végeznie ezt a szinkronizálást.

* Írjon egy cron-feladatot, és rendszeresen hívja meg a [AMBARI API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) -t az új csoportokkal.

## <a name="ranger-user-sync-and-configuration"></a>A Ranger felhasználói szinkronizálása és konfigurálása

A Ranger egy beépített szinkronizálási motorral rendelkezik, amely óránként fut a felhasználók szinkronizálásához. Nem osztja meg a felhasználói adatbázist a Ambari. A HDInsight úgy konfigurálja a keresési szűrőt, hogy szinkronizálja a rendszergazda felhasználót, a watchdog felhasználót és a csoportnak a fürt létrehozásakor megadott tagjait. A csoporttagok szinkronizálva lesznek a tranzitívnak:

* A növekményes szinkronizálás letiltása.
* A felhasználói csoport szinkronizálási térképének engedélyezése.
* Adja meg a keresési szűrőt a tranzitív csoporttagok belefoglalásához.
* A csoportok felhasználóinak és sAMAccountName szinkronizálása.

### <a name="group-or-incremental-sync"></a>Csoport-vagy növekményes szinkronizálás

A Ranger támogatja a csoportos szinkronizálási lehetőséget, de a felhasználói szűrővel való metszéspontként működik. Nem a csoporttagság és a felhasználói szűrő közötti Unió. A Ranger csoport-szinkronizálási szűrő tipikus használati esete a-Group Filter: (DN = clusteradmingroup), felhasználói szűrő: (város = Seattle).

A növekményes szinkronizálás csak azokra a felhasználókra működik, akik már szinkronizálva vannak (az első alkalommal). A növekményes szinkronizálás nem szinkronizálja a csoportokhoz hozzáadott új felhasználókat a kezdeti szinkronizálás után.

### <a name="update-ranger-sync-filter"></a>A Ranger szinkronizálási szűrő frissítése

Az LDAP-szűrő a Ambari felhasználói FELÜLETén, a Ranger felhasználó – szinkronizálás konfigurálása szakaszban található. A meglévő szűrő a `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`formában jelenik meg. Győződjön meg arról, hogy a predikátumot a végén adja hozzá, majd tesztelje a szűrőt `net ads` keresési parancs vagy az Ldp. exe használatával, vagy valamilyen hasonló módon.

## <a name="ranger-user-sync-logs"></a>A Ranger felhasználói szinkronizálási naplói

A Ranger felhasználói szinkronizálása a átjárócsomópontokkal közül bármelyikből kiléphet. A naplók `/var/log/ranger/usersync/usersync.log`találhatók. A naplók részletességének növeléséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Ambari.
1. Lépjen a Ranger-konfiguráció szakaszra.
1. Lépjen a speciális **usersync-log4j** szakaszra.
1. Módosítsa a `log4j.rootLogger` `DEBUG` szintre (a módosítás után a következőhöz hasonlóan kell kinéznie: `log4j.rootLogger = DEBUG,logFile,FilterLog`).
1. Mentse a konfigurációt, és indítsa újra a Rangert.

## <a name="next-steps"></a>Következő lépések

* [Hitelesítési problémák az Azure HDInsight](./domain-joined-authentication-issues.md)
* [Azure AD-felhasználók szinkronizálása HDInsight-fürttel](../hdinsight-sync-aad-users-to-cluster.md)
