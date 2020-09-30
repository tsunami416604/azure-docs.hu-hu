---
title: Az ID Broker (előzetes verzió) használata a hitelesítő adatok kezeléséhez – Azure HDInsight
description: Tudnivalók a HDInsight-ügynökről a tartományhoz csatlakoztatott Apache Hadoop fürtök hitelesítésének egyszerűsítése érdekében.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 8f1e0a6aecc9702552a3dd66acc8dc7eb5bf1d85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529931"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (előzetes verzió)

Ez a cikk bemutatja, hogyan állíthatja be és használhatja a HDInsight ID Broker (HIB) szolgáltatást az Azure HDInsight. Ezzel a funkcióval modern OAuth-hitelesítéssel érheti el az Apache Ambari-t, miközben a Azure Active Directory Domain Services (HRE-DS) örökölt jelszó-kivonatok nélkül is Multi-Factor Authentication (MFA) kényszerítést alkalmaz.

## <a name="overview"></a>Áttekintés

A HIB a következő helyzetekben egyszerűsíti az összetett hitelesítési beállításokat:

* A szervezet támaszkodik az összevonásra, hogy hitelesítse a felhasználókat a felhőalapú erőforrásokhoz való hozzáféréshez. Korábban a HDInsight Enterprise Security Package (ESP) fürtök használatához engedélyeznie kell a jelszó-kivonatok szinkronizálását a helyszíni környezetből a Azure Active Directory (Azure AD) szolgáltatásba. Ez a követelmény bizonyos szervezeteknél nehéz vagy nemkívánatos lehet.

* A szervezete szeretné kikényszeríteni az MFA használatát a webes és HTTP-alapú hozzáféréshez az Apache Ambari és más fürterőforrás-eszközökhöz.

A HIB olyan hitelesítési infrastruktúrát biztosít, amely lehetővé teszi a OAuth (modern) és a Kerberos (örökölt) protokoll közötti áttérést anélkül, hogy a jelszó-kivonatokat a HRE-DS-be kellene szinkronizálnia. Ez az infrastruktúra egy Windows Server rendszerű virtuális gépen (ID Broker-csomóponton) futó összetevőkből áll, valamint a fürt átjárójának csomópontjaival.

Az alábbi ábra a modern OAuth-alapú hitelesítési folyamatot mutatja az összes felhasználóra vonatkozóan, beleértve az összevont felhasználókat is, miután engedélyezte az azonosító-átvitelszervezőt:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Hitelesítési folyamat azonosítója-átvitelszervezővel":::

Ebben a diagramban az ügyfélnek (azaz böngészőnek vagy alkalmazásnak) először be kell állítania az OAuth tokent, majd a tokent egy HTTP-kérelemben kell megadnia az átjárónak. Ha már bejelentkezett más Azure-szolgáltatásokba, például a Azure Portalba, bejelentkezhet az HDInsight-fürtbe egyszeri bejelentkezéses (SSO) felhasználói élményben.

Még mindig sok olyan örökölt alkalmazás lehet, amely csak az alapszintű hitelesítést támogatja (például felhasználónév/jelszó). Ilyen esetekben továbbra is használhatja a HTTP alapszintű hitelesítést a fürt átjáróinak való kapcsolódáshoz. Ebben a telepítőben biztosítania kell, hogy a hálózati kapcsolat az átjáró-csomópontok között az összevonási végpont (ADFS-végpont) felé legyen biztosítva, így biztosítva az átjáró-csomópontok közvetlen vonalát.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Hitelesítési folyamat azonosítója-átvitelszervezővel":::

A következő táblázat segítségével meghatározhatja a szervezete által igényelt legjobb hitelesítési lehetőséget:

|Hitelesítési lehetőségek |HDInsight-konfiguráció | Megfontolandó tényezők |
|---|---|---|
| Teljes OAuth | ESP + HIB | 1. a legtöbb biztonságos beállítás (MFA támogatott) 2.    NINCS szükség pass hash-szinkronizálásra. 3.  Nincs SSH/kinit parancsot/keytab kiterjesztésű hozzáférés a helyszíni fiókokhoz, amelyek nem rendelkeznek jelszó-kivonattal a HRE-DS-ben. 4.   A csak Felhőbeli fiókok továbbra is SSH/kinit parancsot/keytab kiterjesztésű. 5. Web-alapú hozzáférés a Ambari-hez a OAuth 6 használatával.  A OAuth támogatásához a régi alkalmazások (JDBC/ODBC stb.) frissítésére van szükség.|
| OAuth + alapszintű hitelesítés | ESP + HIB | 1. webalapú hozzáférés a Ambari-hez a OAuth 2 használatával. Az örökölt alkalmazások továbbra is az alapszintű hitelesítést használják. 3. Az MFA-t le kell tiltani az alapszintű hitelesítéshez való hozzáféréshez. 4. NINCS szükség pass hash-szinkronizálásra. 5. Nincs SSH/kinit parancsot/keytab kiterjesztésű hozzáférés a helyszíni fiókokhoz, amelyek nem rendelkeznek jelszó-kivonattal a HRE-DS-ben. 6. A csak Felhőbeli fiókok továbbra is SSH/kinit parancsot. |
| Teljesen egyszerű hitelesítés | ESP | 1. a legtöbb hasonló a helyszíni telepítésekhez. 2. Jelszó-kivonat szinkronizálása a HRE – DS-re van szükség. 3. A helyszíni fiókok SSH-/kinit parancsot-vagy keytab kiterjesztésű-t is használhatnak. 4. Az MFA-t le kell tiltani, ha a biztonsági másolati tároló ADLS Gen2 |


## <a name="enable-hdinsight-id-broker"></a>HDInsight-azonosító-átvitelszervező engedélyezése

A következő lépésekkel hozhat létre egy ESP-fürtöt, amelyen engedélyezve van az azonosító-átvitelszervező:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az ESP-fürt alapvető létrehozási lépéseinek követése. További információ: HDInsight- [fürt létrehozása ESP-vel](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Válassza a **HDInsight-azonosító-átvitelszervező engedélyezése**lehetőséget.

Az ID Broker szolgáltatás egy további virtuális gépet ad hozzá a fürthöz. Ez a virtuális gép az ID Broker csomópontja, és a hitelesítést támogató kiszolgálói összetevőket tartalmaz. Az ID Broker csomópont tartományhoz van csatlakoztatva az Azure AD DS tartományhoz.

![Az ID Broker engedélyezésének lehetősége](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata
Ha új szerepkört ad hozzá a `idbrokernode` következő attribútumokkal a sablon számítási profiljához, akkor a fürt az azonosító-átvitelszervező csomóponttal lesz létrehozva:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Eszköz-integráció

A HDIsngith-eszközök natív módon támogatják a OAuth. Javasoljuk, hogy ezeket az eszközöket modern OAuth-alapú hozzáféréshez használja a fürtökhöz. A HDInsight [IntelliJ beépülő modul](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) olyan Java-alapú alkalmazásokhoz is használható, mint a Scala. A VS Code-hoz készült [Spark & kaptár eszközei](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) PySpark és kaptári feladatokhoz is használhatók. A Batch-és az interaktív feladatokat is támogatják.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-hozzáférés jelszó-kivonat nélkül az Azure-ban AD DS

|SSH-beállítások |Megfontolandó tényezők |
|---|---|
| Helyi VM-fiók (pl. sshuser) | 1. a fiókot a fürt létrehozási idején adták meg. 2.  Ehhez a fiókhoz nincs Kerberos-authication |
| Csak Felhőbeli fiók (például alice@contoso.onmicrosoft.com ) | 1. a jelszó kivonata a HRE-DS 2 címen érhető el. A Kerberos-hitelesítés SSH Kerberos használatával lehetséges |
| Helyszíni fiók (például alice@contoso.com ) | 1. az SSH Kerberos-hitelesítés csak akkor lehetséges, ha a jelszó kivonata elérhető a HRE-DS szolgáltatásban, máskülönben ez a felhasználó nem tud SSH-t a fürthöz csatlakozni |

Ha az SSH-t egy tartományhoz csatlakoztatott virtuális gépre vagy a parancs futtatására szeretné futtatni, `kinit` meg kell adnia egy jelszót. Az SSH Kerberos-hitelesítéshez szükséges, hogy a kivonat elérhető legyen a HRE-DS-ben. Ha csak rendszergazdai forgatókönyvekhez kíván SSH-t használni, létrehozhat egy csak felhőalapú fiókot, és az SSH-t a fürthöz is használhatja. Más helyszíni felhasználók továbbra is használhatják a Ambari vagy a HDInsight eszközt vagy a HTTP alapszintű hitelesítést anélkül, hogy az HRE-DS-ben elérhető jelszó-kivonatot kellene használni.

Ha a szervezet nem szinkronizálja a jelszó-kivonatokat a HRE-DS-be, ajánlott eljárásként hozzon létre egy csak Felhőbeli felhasználót az Azure AD-ben, és rendelje hozzá a fürtöt rendszergazdaként a fürt létrehozásakor, és használja azt adminisztrációs célokra, amely magában foglalja az SSH-n keresztüli rendszergazdai hozzáférés beszerzését.

A hitelesítési problémák elhárításához tekintse meg ezt az [útmutatót](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>A OAuth-t használó ügyfelek a HDInsight-átjáróhoz való kapcsolódáshoz a HIB használatával

A HIB beállításban az átjáróhoz csatlakozó egyéni alkalmazások és ügyfelek is frissíthetők a szükséges OAuth-token beszerzéséhez. A jelen [dokumentumban](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) ismertetett lépéseket követve szerezze be a jogkivonatot a következő információkkal:

*   OAuth erőforrás URI-ja: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Engedély: (név: cluster. ReadWrite, azonosító: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Az OAuth token beszerzése után ezt a HTTP-kérelem engedélyezési fejlécében használhatja a fürt átjárójának (például https:// <clustername> -int.azurehdinsight.net). Például az Apache Livy API-hoz hasonló minta curl-parancs a következőképpen nézhet ki:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>További lépések

* [HDInsight-fürt konfigurálása Enterprise Security Package használatával Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
* [Fürtteljesítmény monitorozása](../hdinsight-key-scenarios-to-monitor.md)
