---
title: LDAP-szinkronizálás a Rangerben és az Apache Ambariban az Azure HDInsightban
description: Cím zsinat a LDAP szinkronizál -ban Őrszem és Ambari és beszerez általános irányelvek.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463218"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-szinkronizálás a Rangerben és az Apache Ambariban az Azure HDInsightban

A HDInsight Enterprise Security Package (ESP) fürtök rangert használnak az engedélyezéshez. Az Apache Ambari és a Ranger egymástól függetlenül szinkronizálja a felhasználókat és a csoportokat, és egy kicsit másképp működik. Ez a cikk célja, hogy foglalkozzon az LDAP-szinkronizálás ranger és Ambari.

## <a name="general-guidelines"></a>Általános irányelvek

* A fürtöket mindig csoportoksegítségével telepítse.
* Ahelyett, hogy az Ambari és a Ranger csoportszűrőit módosítaná, próbálja meg kezelni ezeket az Azure AD-ben, és egymásba ágyazott csoportok használatával hozza be a szükséges felhasználókat.
* A felhasználó szinkronizálása után a rendszer akkor sem távolítja el, ha a felhasználó nem tagja a csoportoknak.
* Ha közvetlenül kell módosítania az LDAP-szűrőket, először használja a felhasználói felületet, mivel az bizonyos érvényesítéseket tartalmaz.

## <a name="users-are-synced-separately"></a>A felhasználók szinkronizálása külön-külön

Az Ambari és a Ranger nem osztják meg a felhasználói adatbázist, mert két különböző célt szolgálnak. Ha a felhasználónak az Ambari felhasználói felületét kell használnia, akkor a felhasználót szinkronizálni kell az Ambari-val. Ha a felhasználó nincs szinkronizálva ambari, Ambari UI / API elutasítja, de más részein a rendszer működni fog (ezek által őrzött Ranger vagy Resource Manager, és nem Ambari). Ha azt szeretné, hogy a felhasználó egy Ranger-szabályzat, majd szinkronizálja a felhasználót ranger.

Biztonságos fürt telepítésekor a csoporttagok (az összes alcsoport és azok tagjai) az Ambari és a Ranger között is szinkronizálódnak.When a secure cluster is deployed, group members are sync transitively (all the algroups and their members) to both ambari and Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Ambari felhasználói szinkronizálás és konfiguráció

A fő csomópontokból óránként futtat `/opt/startup_scripts/start_ambari_ldap_sync.py`egy cron-feladatot a felhasználói szinkronizálás ütemezéséhez. A cron feladat meghívja az Ambari rest API-kat a szinkronizálás végrehajtásához. A parancsfájl elküldi a szinkronizálandó felhasználók és csoportok listáját (mivel a felhasználók nem tartoznak a megadott csoportokhoz, mindkettő külön van megadva). Az Ambari a sAMAccountName-t felhasználónévként és a csoport összes tagjaként szinkronizálja, tranzitív módon.

A naplókat a `/var/log/ambari-server/ambari-server.log`ban kell lennie. További információ: [Ambari naplózási szint konfigurálása](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

A Data Lake-fürtökben a felhasználó létrehozása utáni hook a szinkronizált felhasználók kezdőmappáinak létrehozására szolgál, és a kezdőmappák tulajdonosaiként vannak beállítva. Ha a felhasználó nincs megfelelően szinkronizálva az Ambari-val, akkor a felhasználó nak hibákkal kell szembenéznie az átmeneti és egyéb ideiglenes mappák elérésében.

### <a name="update-groups-to-be-synced-to-ambari"></a>Az Ambari-val szinkronizálandó csoportok frissítése

Ha nem tudja kezelni a csoporttagságokat az Azure AD-ben, két lehetősége van:

* Egyszeri szinkronizálás végrehajtása az [LDAP-felhasználók és -csoportok szinkronizálása](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)című alkalommal teljesebb mértékben. Amikor a csoporttagság megváltozik, újra el kell végeznie ezt a szinkronizálást.

* Írjon egy cron feladatot, hívja meg az [Ambari API-t rendszeresen](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) az új csoportokkal.

## <a name="ranger-user-sync-and-configuration"></a>Ranger felhasználó szinkronizálása és konfigurálása

A Ranger beépített szinkronizálási motorral rendelkezik, amely óránként fut a felhasználók szinkronizálásához. Nem osztja meg a felhasználói adatbázist az Ambari-val. A HDInsight úgy konfigurálja a keresési szűrőt, hogy szinkronizálja a rendszergazdai felhasználót, a figyelőt és a fürt létrehozása során megadott csoporttagokat. A csoport tagok lesz szinkronizált tranzitíven:

* Tiltsa le a növekményes szinkronizálást.
* Felhasználói csoport szinkronizálási leképezésének engedélyezése.
* Adja meg a keresési szűrőt a tranzitív csoport tagok felvételéhez.
* Szinkronizálás sAMAccountName a felhasználók és a név attribútum csoportok.

### <a name="group-or-incremental-sync"></a>Csoport vagy növekményes szinkronizálás

A Ranger támogatja a csoportszinkronizálási beállítást, de a felhasználói szűrővel való metszéspontként működik. Nem egy unió a csoporttagságok és a felhasználói szűrő között. A Ranger csoportszinkronizálási szűrőjének tipikus használati esete - csoportszűrő: (dn=clusteradmingroup), felhasználói szűrő: (city=seattle).

A növekményes szinkronizálás csak a már szinkronizált felhasználóknál működik (az első alkalommal). A növekményes nem szinkronizálja a csoportokhoz a kezdeti szinkronizálás után hozzáadott új felhasználókat.

### <a name="update-ranger-sync-filter"></a>A Ranger-szinkronizálási szűrő frissítése

Az LDAP-szűrő az Ambari felhasználói felületén, a Ranger felhasználószinkronizáláskonfigurációs szakaszban található. A meglévő szűrő a `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`képernyőn jelenik meg. Győződjön meg arról, hogy predikátumot `net ads` ad hozzá a végén, és tesztelje a szűrőt a keresési parancs vagy az ldp.exe vagy valami hasonló használatával.

## <a name="ranger-user-sync-logs"></a>Ranger felhasználói szinkronizálási naplók

Ranger felhasználói szinkronizálás történhet ki bármelyik headnodes. A naplók a `/var/log/ranger/usersync/usersync.log`. A naplók részletességének növeléséhez tegye a következő lépéseket:

1. Jelentkezzen be az Ambariba.
1. Nyissa meg a Ranger konfigurációs szakaszt.
1. Nyissa meg a Speciális **usersync-log4j szakaszt.**
1. Változtassa `log4j.rootLogger` meg `DEBUG` a szintet (Változás `log4j.rootLogger = DEBUG,logFile,FilterLog`után meg kell kinéznie ).
1. Mentse a konfigurációt, és indítsa újra a rangert.

## <a name="next-steps"></a>További lépések

* [Hitelesítési problémák az Azure HDInsightban](./domain-joined-authentication-issues.md)
* [Azure AD-felhasználók szinkronizálása HDInsight-fürttel](../hdinsight-sync-aad-users-to-cluster.md)
