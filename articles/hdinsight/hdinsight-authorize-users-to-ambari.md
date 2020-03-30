---
title: Felhasználók engedélyezése Ambari nézetekhez – Azure HDInsight
description: Az Ambari felhasználói és csoportengedélyeinek kezelése hdinsight-fürtökhöz, amelyeken engedélyezve van az ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435649"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Az Apache Ambari Views használatának engedélyezése felhasználók számára

[A vállalati biztonsági csomag (ESP) által engedélyezett HDInsight-fürtök](./domain-joined/hdinsight-security-overview.md) nagyvállalati szintű képességeket biztosítanak, beleértve az Azure Active Directory-alapú hitelesítést is. [Szinkronizálhatja](hdinsight-sync-aad-users-to-cluster.md) az Azure AD-csoportokhoz hozzáadott új felhasználókat, amelyek hozzáférést kaptak a fürthöz, lehetővé téve az adott felhasználók számára bizonyos műveletek elvégzését. Az [Apache Ambari](https://ambari.apache.org/) felhasználóival, csoportjaival és engedélyekkel való együttműködése támogatja az ESP HDInsight-fürtök és a szabványos HDInsight-fürtök esetében egyaránt.

Az Active Directory felhasználói a tartományi hitelesítő adataikkal jelentkezhetnek be a fürtcsomópontokba. A tartományi hitelesítő adataikkal is hitelesíthetik a fürtinterakciókat más jóváhagyott végpontokkal, például [a Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell és REST API-kkal.

> [!WARNING]  
> Ne módosítsa az Ambari figyelő (hdinsightwatchdog) jelszavát a Linux-alapú HDInsight-fürtön. A jelszó módosítása megszakítja a parancsfájlműveletek használatát vagy a méretezési műveletek et a fürttel.

Ha még nem tette meg, kövesse [az alábbi utasításokat](./domain-joined/apache-domain-joined-configure.md) egy új ESP-fürt kiépítési éhez.

## <a name="access-the-ambari-management-page"></a>Az Ambari felügyeleti lap elérése

Az [Apache Ambari Web felhasználói felületének](hdinsight-hadoop-manage-ambari.md) `https://CLUSTERNAME.azurehdinsight.net` **Ambari felügyeleti lapjának** tallózásához keresse meg a tallózva a tallózhat. Adja meg a fürt gondnokának felhasználónevét és jelszavát, amelyet a fürt létrehozásakor megadott. Ezután az Ambari irányítópulton válassza az **Ambari kezelése parancsot** a **rendszergazdai** menü alatt:

![Apache Ambari irányítópult kezelése](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Felhasználók hozzáadása

### <a name="add-users-through-the-portal"></a>Felhasználók hozzáadása a portálon keresztül

1. A kezelés lapon válassza a **Felhasználók**lehetőséget.

    ![Apache Ambari felügyeleti oldal felhasználói](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Válassza **a + Create Local User**lehetőséget.

1. Adja meg **a felhasználónevet** és **a jelszót**. Válassza a **Mentés**lehetőséget.

### <a name="add-users-through-powershell"></a>Felhasználók hozzáadása a PowerShellen keresztül

Az alábbi változókat `CLUSTERNAME`a `NEWUSER`, `PASSWORD` a lehetőség és a megfelelő értékek lecserélésével szerkeszthetjük.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Felhasználók hozzáadása a Curl-en keresztül

Az alábbi változókat `CLUSTERNAME`a `ADMINPASSWORD` `NEWUSER`, `USERPASSWORD` , és a megfelelő értékek helyett szerkeszthetjük. A szkript célja, hogy végre bash. A Windows parancssorához kisebb módosításokra lenne szükség.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Engedélyek megadása Apache Hive-nézetekhez

Ambari jön-val kilátás példányok [Apache Hive](https://hive.apache.org/) és [Apache TEZ](https://tez.apache.org/), többek között. Egy vagy több Hive-nézetpéldányhoz való hozzáférés engedélyezéséhez lépjen az **Ambari felügyeleti lapra.**

1. A kezelés lapon válassza a **Nézetek** hivatkozást a bal oldali **Nézetek** menüfejléc alatt.

    ![Apache Ambari nézetek nézetek nézet linkek](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. A Nézetek lapon bontsa ki a **HIVE-sort.** Van egy alapértelmezett Hive nézet, amely akkor jön létre, amikor a Hive szolgáltatás hozzá van adva a fürthöz. Szükség szerint további Hive-nézetpéldányokat is létrehozhat. Válasszon egy Hive nézetet:

    ![HDInsight-nézetek – Apache Hive nézet](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Görgetés a Nézet oldal alja felé. Az *Engedélyek szakaszban* két lehetőség közül választhat, hogy a tartományi felhasználók nak engedélyeket adjon a nézethez:

**Engedély megadása ezeknek a felhasználóknak,** ![engedély megadása ezeknek a felhasználóknak](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Engedély megadása ezeknek a csoportoknak** ![Engedély megadása ezeknek a csoportoknak](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Felhasználó hozzáadásához kattintson a **Felhasználó hozzáadása** gombra.

   * Kezdje el beírni a felhasználónevet, és megjelenik a korábban definiált nevek legördülő listája.

     ![Apache Ambari felhasználó automatikus befejezi](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Jelölje ki vagy fejezze be a felhasználónevet, vagy fejezze be a felhasználónevet. Ha ezt a felhasználónevet új felhasználóként szeretné hozzáadni, kattintson az **Új** gombra.

   * A módosítások mentéséhez jelölje be a **kék jelölőnégyzetet**.

     ![Az Apache Ambari felhasználói engedélyeket ad](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Csoport hozzáadásához kattintson a **Csoport hozzáadása** gombra.

   * Kezdje el beírni a csoport nevét. A meglévő csoportnév kiválasztásának vagy új csoport hozzáadásának folyamata megegyezik a felhasználók hozzáadásával.
   * A módosítások mentéséhez jelölje be a **kék jelölőnégyzetet**.

     ![Az Apache Ambari engedélyeket ad](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Ha a felhasználókat közvetlenül a nézethez adja hozzá, akkor hasznos, ha engedélyeket szeretne hozzárendelni egy felhasználóhoz a nézet használatához, de nem szeretné, hogy olyan csoport tagjai legyenek, amely további engedélyekkel rendelkezik. A felügyeleti többletterhelés csökkentése érdekében egyszerűbb lehet engedélyeket rendelni a csoportokhoz.

## <a name="grant-permissions-to-apache-tez-views"></a>Engedélyek megadása Apache TEZ-nézetekhez

Az [Apache TEZ](https://tez.apache.org/) nézetpéldányok lehetővé teszik a felhasználók számára az [Apache Hive-lekérdezések](https://hive.apache.org/) és az [Apache Pig](https://pig.apache.org/) parancsfájlok által benyújtott összes Tez-feladat figyelése és hibakeresése. Van egy alapértelmezett Tez nézetpéldány, amely a fürt kiépítésekor jön létre.

Ha felhasználókat és csoportokat szeretne hozzárendelni egy Tez nézetpéldányhoz, bontsa ki a **TEZ-sort** a Nézetek lapon, ahogy azt korábban leírtuk.

![HDInsight-nézetek – Apache Tez nézet](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Felhasználók vagy csoportok hozzáadásához ismételje meg az előző szakasz 3–5.

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz

A felhasználók és csoportok számára öt biztonsági szerepkör létezik, csökkenő hozzáférési engedélyek sorrendjében:

* Fürtfelügyelő
* Fürt operátor
* Szolgáltatás-rendszergazda
* Szolgáltatás operátora
* Fürt felhasználója

A szerepkörök kezeléséhez lépjen az **Ambari felügyeleti lapra,** majd válassza a **Szerepkörök** hivatkozást a bal *oldali Fürtök* menücsoportban.

![Apache Ambari szerepkörök menü linkek](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Az egyes szerepkörökhöz megadott engedélyek listájának megtekintéséhez kattintson a Szerepkörök lap **Szerepkörök** táblázatfejléce melletti kék kérdőjelre.

![Apache Ambari szerepkörök menühivatkozási engedélyek](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari szerepkörök menühivatkozási engedélyek")

Ezen a lapon két különböző nézet et használhat a felhasználók és csoportok szerepkörei kezelésére: Letiltás és lista.

### <a name="block-view"></a>Blokk nézet

A Blokk nézet az egyes szerepköröket a saját sorában jeleníti meg, és biztosítja a **Szerepkörök hozzárendelése ezeket a felhasználókat,** és **szerepkörök hozzárendelése ezekhez a csoportokhoz** beállításokat a korábban leírtak szerint.

![Apache Ambari szerepkörök blokknézete](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Listanézet

A Lista nézet gyors szerkesztési lehetőségeket biztosít két kategóriában: Felhasználók és csoportok.

* A Lista nézet Felhasználók kategóriája megjeleníti az összes felhasználó listáját, így a legördülő lista minden felhasználójának kijelölhet egy szerepkört.

    ![Apache Ambari szerepkörlista nézete - felhasználók](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* A Lista nézet Csoportok kategóriája megjeleníti az összes csoportot és az egyes csoportokhoz rendelt szerepkört. A példában a csoportok listáját szinkronizálja az Azure AD-csoportok a fürt tartománybeállításainak **Access felhasználói csoport** tulajdonságában megadott. Lásd: [HDInsight-fürt létrehozása ESP-vel.](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)

    ![Apache Ambari szerepkörlista nézete - csoportok](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    A fenti képen a "hiveusers" csoport a *fürt felhasználói* szerepkörrel van rendelve. Ez egy csak olvasható szerepkör, amely lehetővé teszi a csoport felhasználói számára a szolgáltatáskonfigurációk és a fürtmetrikák megtekintését, de nem módosíthatók.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Jelentkezzen be az Ambari-ba, mint csak megtekintést lelátó felhasználó

Az Azure AD tartományi felhasználó "hiveuser1" engedélyeket rendeltünk a Hive- és tez-nézetekhez. Amikor elindítjuk az Ambari web felhasználói felületét, és megadjuk a felhasználó tartományi hitelesítő adatait (Azure AD-felhasználónév e-mail formátumban és jelszóban), a felhasználó átlesz irányítva az Ambari Nézetek lapra. Itt a felhasználó bármelyik elérhető nézetet kiválaszthatja. A felhasználó nem látogathatja meg a webhely bármely más részét, beleértve az irányítópultot, a szolgáltatásokat, a gazdagépeket, a riasztásokat vagy a rendszergazdai oldalakat.

![Apache Ambari felhasználó csak nézetekkel](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Bejelentkezés az Ambari szolgáltatásba fürtfelhasználóként

Az Azure AD tartományi felhasználó "hiveuser2" a *fürt felhasználói* szerepkörhöz rendelt. Ez a szerepkör képes elérni az irányítópultot és az összes menüpontot. A fürtfelhasználó nak kevesebb engedélyezett lehetősége van, mint a rendszergazdának. Például a hiveuser2 megtekintheti az egyes szolgáltatások konfigurációit, de nem szerkesztheti őket.

![Apache Ambari műszerfal megjelenítése](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>További lépések

* [Az Apache Hive-házirendek konfigurálása a HDInsightban az ESP-vel](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md)
* [Az Apache Hive nézet használata az Apache Hadoop segítségével a HDInsightban](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Az Azure AD-felhasználók szinkronizálása a fürttel](hdinsight-sync-aad-users-to-cluster.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](./hdinsight-hadoop-manage-ambari-rest-api.md)
