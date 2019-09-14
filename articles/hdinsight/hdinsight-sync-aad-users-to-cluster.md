---
title: Azure Active Directory-felhasználók szinkronizálása egy fürttel – Azure HDInsight
description: Hitelesített felhasználók szinkronizálása Azure Active Directoryról egy HDInsight-fürtre.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9a7008ca7967135209b8fc2c341998570a2679be
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960846"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása

[Enterprise Security Package (ESP) HDInsight-fürtök](hdinsight-domain-joined-introduction.md) erős hitelesítést használhatnak Azure Active Directory (Azure ad) felhasználókkal, valamint *szerepköralapú hozzáférés-vezérlési* (RBAC) házirendek használatával. Amikor felhasználókat és csoportokat AD hozzá az Azure AD-hez, szinkronizálhatja azokat a felhasználókat, akiknek a fürthöz hozzáféréssel kell rendelkezniük.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [hozzon létre egy HDInsight-fürtöt Enterprise Security Package](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Új Azure AD-felhasználók hozzáadása

A gazdagépek megtekintéséhez nyissa meg a Ambari webes felhasználói felületét. Az egyes csomópontok új felügyelet nélküli frissítési beállításokkal lesznek frissítve.

1. A [Azure Portal](https://portal.azure.com)navigáljon az ESP-fürthöz társított Azure ad-címtárhoz.

2. Válassza a **minden felhasználó** lehetőséget a bal oldali menüben, majd válassza az **új felhasználó**lehetőséget.

    ![Minden felhasználó ablaktábla](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Fejezze be az új felhasználói űrlapot. Válassza ki a fürtön alapuló engedélyek hozzárendeléséhez létrehozott csoportokat. Ebben a példában hozzon létre egy "HiveUsers" nevű csoportot, amelyhez új felhasználókat rendelhet hozzá. Az ESP `HiveUsers` -fürtök létrehozására [vonatkozó példa](hdinsight-domain-joined-configure.md) a következő két csoport hozzáadását `AAD DC Administrators`tartalmazza: és.

    ![Új felhasználó ablaktábla](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Kattintson a **Létrehozás** gombra.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>A felhasználók szinkronizálása az Apache Ambari REST API használatával

A fürt létrehozási folyamata során megadott felhasználói csoportok szinkronizálása folyamatban van. A felhasználó-szinkronizálás óránként egyszer automatikusan megtörténik. A felhasználók azonnali szinkronizálásához, vagy a fürt létrehozásakor megadott csoportokon kívüli csoportok szinkronizálásához használja a Ambari REST API.

A következő metódus a POST és a Ambari REST API használja. További információ: HDInsight- [fürtök kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Csatlakozzon az SSH-val a fürthöz](hdinsight-hadoop-linux-use-ssh-unix.md). A Azure Portal a fürt áttekintés paneljén válassza a **Secure Shell (SSH)** gombot.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. Másolja a megjelenített `ssh` parancsot, és illessze be az SSH-ügyfélbe. Ha a rendszer kéri, adja meg az ssh-felhasználó jelszavát.

3. A hitelesítés után írja be a következő parancsot:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    A válasznak így kell kinéznie:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. A szinkronizálás állapotának megtekintéséhez hajtson végre egy `curl` új parancsot:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    A válasznak így kell kinéznie:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Ez az eredmény azt mutatja, hogy az állapot **elkészült**, egy új felhasználó lett létrehozva, és a felhasználó hozzá lett rendelve egy tagsághoz. Ebben a példában a felhasználó hozzá van rendelve az "HiveUsers" szinkronizált LDAP-csoporthoz, mivel a felhasználó hozzá lett adva ugyanahhoz a csoporthoz az Azure AD-ben.

> [!NOTE]  
> Az előző metódus csak a tartományi beállítások **hozzáférés felhasználói csoport** tulajdonságában megadott Azure ad-csoportokat szinkronizálja a fürt létrehozásakor. További információt a HDInsight- [fürt létrehozása](domain-joined/apache-domain-joined-configure.md)című témakörben talál.

## <a name="verify-the-newly-added-azure-ad-user"></a>Az újonnan hozzáadott Azure AD-felhasználó ellenőrzése

Nyissa meg az [Apache Ambari webes felületét](hdinsight-hadoop-manage-ambari.md) annak ellenőrzéséhez, hogy az új Azure ad-felhasználó hozzá lett-e adva. Az Ambari webes Kezelőfelületen eléréséhez megkeresve **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Adja meg a fürt rendszergazdai felhasználónevét és jelszavát.

1. A Ambari Irányítópultján kattintson a **felügyelet Ambari** elemre a **rendszergazda** menüben.

    ![Ambari kezelése](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. A lap bal oldalán, a **felhasználó + csoport kezelése** menü csoportjában válassza a **felhasználók** lehetőséget.

    ![Felhasználók menüelem](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Az új felhasználónak szerepelnie kell a felhasználók táblában. A típus nem értékre `LDAP` van állítva `Local`.

    ![Felhasználók oldal](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Jelentkezzen be a Ambari új felhasználóként

Amikor az új felhasználó (vagy bármely más tartományi felhasználó) bejelentkezik a Ambari-ba, a teljes Azure AD-felhasználónevet és tartományi hitelesítő adatokat használják.  A Ambari megjelenít egy felhasználói aliast, amely a felhasználó megjelenítendő neve az Azure AD-ben. Az új példában szereplő felhasználó neve `hiveuser3@contoso.com`. A Ambari-ben ez az új felhasználó úgy `hiveuser3` jelenik meg, ahogy a felhasználó bejelentkezik a `hiveuser3@contoso.com`Ambari-ba.

## <a name="see-also"></a>Lásd még

* [Apache Hive házirendek konfigurálása a HDInsight-ben ESP-vel](hdinsight-domain-joined-run-hive.md)
* [HDInsight-fürtök az ESP-vel való kezelése](hdinsight-domain-joined-manage.md)
* [Felhasználók engedélyezése az Apache Ambari](hdinsight-authorize-users-to-ambari.md)
