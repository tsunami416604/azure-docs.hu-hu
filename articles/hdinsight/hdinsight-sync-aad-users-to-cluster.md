---
title: Az Azure Active Directory felhasználóinak szinkronizálása a HDInsight-fürttel
description: Szinkronizálja a hitelesített felhasználókat az Azure Active Directoryból egy HDInsight-fürthöz.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744864"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása

[A HDInsight-fürtök enterprise security package (ESP)](hdinsight-domain-joined-introduction.md) erős hitelesítést használhatnak az Azure Active Directory (Azure AD) felhasználóival, valamint *szerepköralapú hozzáférés-vezérlési* (RBAC) házirendeket is használhatnak. Amikor felhasználókat és csoportokat ad hozzá az Azure AD-hez, szinkronizálhatja azokkal a felhasználókkal, akiknek hozzáférésre van szükségük a fürthöz.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [hozzon létre egy HDInsight-fürtöt az Enterprise Security Package csomaggal.](hdinsight-domain-joined-configure.md)

## <a name="add-new-azure-ad-users"></a>Új Azure AD-felhasználók hozzáadása

A gazdagépek megtekintéséhez nyissa meg az Ambari Web felhasználói felületét. Minden csomópont frissül az új felügyelet nélküli frissítési beállításokkal.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az ESP-fürthöz társított Azure AD-könyvtárat.

2. Válassza a bal oldali menü **Minden felhasználó lehetőséget,** majd válassza az **Új felhasználó**lehetőséget.

    ![Az Azure Portal felhasználói és csoportjai](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Töltse ki az új felhasználói űrlapot. Válassza ki a fürtalapú engedélyek hozzárendeléséhez létrehozott csoportokat. Ebben a példában hozzon létre egy "HiveUsers" nevű csoportot, amelyhez új felhasználókat rendelhet hozzá. Az ESP-fürt létrehozásának [példautasítások](hdinsight-domain-joined-configure.md) közé `HiveUsers` `AAD DC Administrators`tartozik két csoport hozzáadása, és a .

    ![Az Azure Portal felhasználói ablaktáblája csoportok kiválasztása](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Kattintson a **Létrehozás** gombra.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Felhasználók szinkronizálása az Apache Ambari REST API-val

Ekkor történik a fürt létrehozása során meghatározott felhasználói csoportok szinkronizálása. A felhasználók szinkronizálása óránként egyszer automatikusan megtörténik. A felhasználók azonnali szinkronizálásához vagy a fürt létrehozása során megadott csoportoktól eltérő csoport szinkronizálásához használja az Ambari REST API-t.

A következő módszer a POST-ot használja az Ambari REST API-val. További információ: [HDInsight-fürtök kezelése az Apache Ambari REST API használatával.](hdinsight-hadoop-manage-ambari-rest-api.md)

1. Az [ssh paranccsal](hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztése a fürt nevének cseréjével, `CLUSTERNAME` majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A hitelesítés után írja be a következő parancsot:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    A válasz így kell kinéznie:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. A szinkronizálási állapot megtekintéséhez hajtson végre egy új `curl` parancsot:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    A válasz így kell kinéznie:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Ez az eredmény azt mutatja, hogy az állapot **TELJES**, egy új felhasználó jött létre, és a felhasználó tagságot kapott. Ebben a példában a felhasználó hozzá van rendelve a "HiveUsers" szinkronizált LDAP-csoporthoz, mivel a felhasználó ugyanahhoz a csoporthoz lett hozzáadva az Azure AD-ben.

    > [!NOTE]  
    > Az előző módszer csak szinkronizálja az Azure AD-csoportok at az **Access felhasználói csoport** tulajdonsága a tartomány beállításait a fürt létrehozása során. További információt a [HDInsight-fürt létrehozása](domain-joined/apache-domain-joined-configure.md)című témakörben talál.

## <a name="verify-the-newly-added-azure-ad-user"></a>Az újonnan hozzáadott Azure AD-felhasználó ellenőrzése

Nyissa meg az [Apache Ambari Web felhasználói felületét,](hdinsight-hadoop-manage-ambari.md) és ellenőrizze, hogy az új Azure AD-felhasználó hozzá lett-e adva. Az Ambari webfelhasználói felület eléréséhez a t **`https://CLUSTERNAME.azurehdinsight.net`** keresve. Adja meg a fürt rendszergazdájának felhasználónevét és jelszavát.

1. Az Ambari irányítópulton válassza az **Ambari kezelése parancsot** a **rendszergazdai** menüalatt.

    ![Apache Ambari műszerfal Ambari kezelése](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. A lap bal oldalán, a **Felhasználó + Csoportkezelés** menücsoport csoportban válassza a **Felhasználók** lehetőséget.

    ![HDInsight felhasználók és csoportok menü](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Az új felhasználónak szerepelnie kell a Felhasználók táblában. A típus beállítása `LDAP` a `Local`helyett.

    ![HDInsight aad felhasználók oldalának áttekintése](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Jelentkezzen be az Ambari-ba új felhasználóként

Amikor az új felhasználó (vagy bármely más tartományi felhasználó) bejelentkezik az Ambariba, a teljes Azure AD-felhasználónevet és a tartományi hitelesítő adatokat használják.  Az Ambari egy felhasználói aliast jelenít meg, amely a felhasználó megjelenítendő neve az Azure AD-ben.
Az új példafelhasználó felhasználóneve `hiveuser3@contoso.com`. Az Ambari, ez az `hiveuser3` új felhasználó jelenik meg, de `hiveuser3@contoso.com`a felhasználó bejelentkezik Ambari a .

## <a name="see-also"></a>Lásd még

* [Az Apache Hive-házirendek konfigurálása a HDInsightban az ESP-vel](hdinsight-domain-joined-run-hive.md)
* [HDInsight-fürtök kezelése ESP-vel](hdinsight-domain-joined-manage.md)
* [Felhasználók engedélyezése az Apache Ambari-ra](hdinsight-authorize-users-to-ambari.md)
