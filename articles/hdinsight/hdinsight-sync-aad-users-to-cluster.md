---
title: Azure Active Directory-felhasználók Azure HDInsight - fürthöz szinkronizálását |} Microsoft Docs
description: Azure Active Directory hitelesített felhasználók fürthöz szinkronizálását.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: f2deaaa31a4d0e8a91d048b538e9251a8eb9e1b7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409281"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-felhasználók egy HDInsight-fürthöz szinkronizálását

[A HDInsight-fürtök tartományhoz](hdinsight-domain-joined-introduction.md) is Azure Active Directory (Azure AD) felhasználók erős hitelesítés használatára, valamint használja *szerepköralapú hozzáférés-vezérlés* (RBAC) házirendek. Felhasználók és csoportok hozzáadása az Azure ad Szolgáltatásba, szinkronizálhatja a felhasználók, akik a fürt eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

Ha Ön még nem tette meg, [hozzon létre egy tartományhoz csatlakozó HDInsight-fürt](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Adja hozzá az új Azure AD-felhasználók

A gazdagépek megtekintéséhez nyissa meg az Ambari webes felhasználói felületén. Minden csomópont frissíti az új beállítások a felügyelet nélküli frissítése.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a tartományhoz csatlakoztatott fürthöz tartozó Azure AD-címtárban.

2. Válassza ki **minden felhasználó** a bal oldali menüből, majd válassza ki **új felhasználó**.

    ![Összes felhasználók ablaktábla](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Töltse ki az új felhasználói űrlapot. Válassza ki a fürt-alapú engedélyek csoportja. Ebben a példában, amelyhez új felhasználókat rendelhet "HiveUsers" nevű csoport létrehozása. A [példa utasításokat](hdinsight-domain-joined-configure.md) a tartományhoz csatlakoztatott fürt létrehozásának felvételét két csoportok `HiveUsers` és `AAD DC Administrators`.

    ![Új felhasználó ablaktábla](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Kattintson a **Létrehozás** gombra.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Felhasználók szinkronizálása az Ambari REST API használatával

A fürt létrehozása során meghatározott felhasználói csoportok szinkronizálva legyenek a, időpont. Felhasználók szinkronizálása körülbelül óránként automatikusan megtörténik. A felhasználók szinkronizálása azonnal, vagy a fürt létrehozása során megadott csoport eltérő szinkronizálni, használja az Ambari REST API-t.

A következő metódus POST az Ambari REST API-t használ. További információkért lásd: [kezelése HDInsight-fürtök az Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Csatlakozzon a fürthöz az SSH](hdinsight-hadoop-linux-use-ssh-unix.md). Az Áttekintés ablaktábláján, a fürt az Azure portálon, válassza ki a **Secure Shell (SSH)** gombra.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Másolja a megjelenített `ssh` parancsot, majd illessze be az SSH-ügyfél. Adja meg az ssh felhasználó jelszót.

3. Hitelesítés után adja meg a következő parancsot:

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

4. A szinkronizálási állapot megtekintéséhez végrehajtani egy új `curl` parancsot a `href` az előző parancs által visszaadott érték:

    ```bash
    curl -u admin:<YOUR PASSWORD> http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1
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

5. Ez az eredmény azt mutatja, hogy az állapot **COMPLETE**, egy új felhasználó lett létrehozva, és a felhasználó a tagság kapott. Ebben a példában a felhasználó hozzá van rendelve a "HiveUsers" szinkronizált LDAP-csoport, mert a felhasználó ezt a csoportot az Azure ad-ben lett felvéve.

> [!NOTE]
> A metódus csak a megadott Azure AD-csoport szinkronizálja a **hozzáférés felhasználói csoport** tulajdonság a fürt létrehozása során a tartomány beállításait. További információkért lásd: [HDInsight-fürtök létrehozása](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Ellenőrizze az újonnan hozzáadott Azure AD-felhasználó

Nyissa meg a [Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md) annak ellenőrzésére, hogy az új Azure AD-felhasználó hozzá lett adva. Az Ambari webes felhasználói felületén hozzáférési tallózással **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Adja meg a fürt rendszergazdai jogosultságú felhasználónevet és jelszót.

1. Válassza ki az Ambari irányítópult **kezelése az Ambari** alatt a **admin** menü.

    ![Ambari kezelése](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Válassza ki **felhasználók** alatt a **felhasználót + csoportkezelés** a lap bal oldali menü csoport.

    ![Felhasználók menüpont](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Az új felhasználó szerepelnie kell a felhasználók táblán belül. A típus értéke `LDAP` helyett `Local`.

    ![A felhasználók lapon](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Jelentkezzen be az Ambari az új felhasználó

Amikor az Ambari bejelentkezik az új felhasználó (vagy bármely más tartományi felhasználó), a teljes Azure AD felhasználói nevét és a tartományi hitelesítő adatok használata.  Ambari jeleníti meg egy felhasználónevet, amely a felhasználó megjelenítendő nevét az Azure ad-ben. Az új példa felhasználó rendelkezik-e a felhasználó nevét `hiveuser3@contoso.com`. Az Ambari, mint a megjelenik az új felhasználó `hiveuser3` , de a felhasználó bejelentkezik, Ambari `hiveuser3@contoso.com`.

## <a name="see-also"></a>Lásd még

* [A tartományhoz csatlakoztatott HDInsight Hive-szabályzatok konfigurálása](hdinsight-domain-joined-run-hive.md)
* [Tartományhoz csatlakozó HDInsight-fürtök kezelése](hdinsight-domain-joined-manage.md)
* [Ambari felhasználók engedélyezése](hdinsight-authorize-users-to-ambari.md)
