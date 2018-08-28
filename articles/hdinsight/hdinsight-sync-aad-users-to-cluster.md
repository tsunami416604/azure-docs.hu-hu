---
title: Azure HDInsight - fürthöz az Azure Active Directory-felhasználók szinkronizálása
description: Egy fürtön az Azure Active Directory hitelesített felhasználók szinkronizálását.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/19/2018
ms.openlocfilehash: 7e002a43c774bd1a6df9cfe46207ddebd02284b3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43104223"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Egy HDInsight-fürtön az Azure Active Directory-felhasználók szinkronizálása

[Tartományhoz csatlakoztatott HDInsight-fürtök](hdinsight-domain-joined-introduction.md) is erős hitelesítés használata a felhasználók Azure Active Directory (Azure AD), valamint használja *szerepköralapú hozzáférés-vezérlés* (RBAC) házirendeket. Az Azure AD-felhasználók és csoportok hozzáadásakor, szinkronizálhatja a felhasználókat, akiknek szükség van a fürthöz való hozzáféréssel.

## <a name="prerequisites"></a>Előfeltételek

Ha Ön még nem tette, [egy tartományhoz csatlakoztatott HDInsight-fürt létrehozása](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Adja hozzá az új Azure AD-felhasználók

A gazdagépek megtekintéséhez nyissa meg az Ambari webes Kezelőfelületen. Minden egyes csomópontot az új felügyelet nélküli frissítési beállítások frissülni fog.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a tartományhoz csatlakozó fürthöz társított Azure AD-címtárat.

2. Válassza ki **minden felhasználó** elemet a bal oldali menüben, majd válassza ki **új felhasználó**.

    ![Az összes felhasználó panel](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Töltse ki az új felhasználói űrlapot. Válassza ki a létrehozott fürt-alapú engedélyek hozzárendelése a csoportokhoz. Ebben a példában a neve "HiveUsers", amelyhez új felhasználókat rendelhet csoport létrehozása. A [példa utasításokat](hdinsight-domain-joined-configure.md) a tartományhoz csatlakoztatott fürtöt létrehozni tartalmazza a két csoport hozzáadása `HiveUsers` és `AAD DC Administrators`.

    ![Új felhasználó ablaktábla](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Kattintson a **Létrehozás** gombra.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Felhasználók szinkronizálása az Ambari REST API használatával

Felhasználói csoportok, a fürt létrehozása során megadott-nál több időt szinkronizálva legyenek. Felhasználók szinkronizálása automatikusan óránként egyszer történik. A felhasználók szinkronizálása azonnal, vagy egy csoport, mint a fürt létrehozásakor megadott csoportok szinkronizálása, az Ambari REST API használata.

A következő metódust használja a POST az Ambari REST API-val. További információkért lásd: [kezelése a HDInsight-fürtök az Ambari REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Csatlakozás a fürthöz az ssh-val](hdinsight-hadoop-linux-use-ssh-unix.md). A fürt az Azure portal áttekintés ablaktábláján válassza ki a **Secure Shell (SSH)** gombra.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Másolja a megjelenített `ssh` parancsot, majd illessze be az SSH-ügyfél. Adja meg az ssh felhasználói jelszót, amikor a rendszer kéri.

3. Hitelesítés után írja be a következő parancsot:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    A válasz kell kinéznie:

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

4. A szinkronizálási állapot megtekintéséhez hajtsa végre egy új `curl` parancsot:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    A válasz kell kinéznie:
    
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

5. Ez az eredmény azt mutatja, hogy az állapot **COMPLETE**, egy új felhasználó lett létrehozva, és a felhasználó a tagság hozzá volt rendelve. Ebben a példában a felhasználó hozzá van rendelve a "HiveUsers" szinkronizált LDAP-csoport, mivel a felhasználó hozzáadva a csoportot, az Azure ad-ben.

> [!NOTE]
> Az előző metódus csak szinkronizálja a megadott Azure AD-csoportokat a **hozzáférési felhasználói csoport** tulajdonsága a fürt létrehozásakor a tartomány beállításait. További információkért lásd: [hozzon létre egy HDInsight-fürt](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Ellenőrizze az újonnan hozzáadott Azure AD-felhasználó

Nyissa meg a [az Ambari webes felhasználói felület](hdinsight-hadoop-manage-ambari.md) ellenőrizheti, hogy az új Azure AD-felhasználó lett felvéve. Az Ambari webes Kezelőfelületen eléréséhez megkeresve **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Adja meg a fürt rendszergazdai felhasználónevet és jelszót.

1. Az Ambari irányítópultján válassza ki **kezelése az Ambari** alatt a **rendszergazdai** menü.

    ![Az Ambari kezelése](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Válassza ki **felhasználók** alatt a **felhasználó és csoport felügyelet** az oldal a bal oldali menü csoport.

    ![Felhasználók menüpont](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Az új felhasználó szerepelnie kell a felhasználók a táblán belül. A típusuk értéke `LDAP` helyett `Local`.

    ![Felhasználók lapra](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Jelentkezzen be az Ambari az új felhasználó

Amikor az Ambari bejelentkezik az új felhasználó (vagy bármely más tartomány felhasználó), teljes körű Azure AD felhasználói nevét és a tartományi hitelesítő adatait használják.  Az Ambari felhasználói alias, amely a felhasználó megjelenített neve az Azure ad-ben jeleníti meg. Példa az új felhasználó rendelkezik a felhasználónév `hiveuser3@contoso.com`. Az Ambari megjelenik az új felhasználóhoz `hiveuser3` , de a felhasználó bejelentkezik, az Ambari `hiveuser3@contoso.com`.

## <a name="see-also"></a>Lásd még

* [A tartományhoz csatlakoztatott HDInsight Hive-házirendek konfigurálása](hdinsight-domain-joined-run-hive.md)
* [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](hdinsight-domain-joined-manage.md)
* [A felhasználóknak engedélyezik, Ambari](hdinsight-authorize-users-to-ambari.md)
