---
title: 'Azure Databricks: gyakori kérdések és Súgó'
description: Választ kaphat a Azure Databricks kapcsolatos gyakori kérdésekre és hibaelhárítási információkra.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671566"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Gyakori kérdések a Azure Databricks

Ez a cikk a Azure Databrickshoz kapcsolódó leggyakoribb kérdéseket sorolja fel. Emellett felsorolja a Databricks használata során esetlegesen előforduló gyakori problémákat is. További információ: [What is Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Használhatom a Azure Key Vault az Azure Databricksban használandó kulcsok/titkos kódok tárolására?
Igen. A Azure Key Vault használatával a kulcsok/titkos kódok tárolhatók a Azure Databricksval való használathoz. További információ: [Azure Key Vault támogatott hatókörök](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Használhatom az Azure-beli virtuális hálózatokat a Databricks?
Igen. Azure Databricks használatával Azure Virtual Network (VNET) is használható. További információ: [Azure Databricks üzembe helyezése az Azure-Virtual Networkban](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Hogyan a Notebookról való hozzáférési Azure Data Lake Storage? 

Kövesse az alábbi lépéseket:
1. A Azure Active Directory (Azure AD) szolgáltatásban hozzon létre egy egyszerű szolgáltatásnevet, és jegyezze fel a kulcsát.
1. Rendelje hozzá a szükséges engedélyeket az egyszerű szolgáltatáshoz Data Lake Storageban.
1. Data Lake Storage található fájl eléréséhez használja az egyszerű szolgáltatás hitelesítő adatait a jegyzetfüzetben.

További információ: [Azure Data Lake Storage használata a Azure Databricks használatával](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Gyakori problémák elhárítása

Itt talál néhány problémát, amely felmerülhet a Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probléma: ez az előfizetés nincs regisztrálva a (z) Microsoft. Databricks névtér használatára

#### <a name="error-message"></a>Hibaüzenet

"Ez az előfizetés nincs regisztrálva a Microsoft. Databricks névtér használatára. Lásd https://aka.ms/rps-not-found : előfizetések regisztrálása. (Kód: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Megoldás

1. Lépjen a [Azure Portal](https://portal.azure.com).
1. Válassza ki az **előfizetéseket**, a használt előfizetést, majd az **erőforrás-szolgáltatókat**. 
1. Az erőforrás-szolgáltatók listájában, a **Microsoft. Databricks**, válassza a **regisztráció**lehetőséget. Az erőforrás-szolgáltató regisztrálásához az előfizetés közreműködői vagy tulajdonosi szerepkörének kell lennie.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probléma: a (z) {e-mail} fiókja nem rendelkezik tulajdonosi vagy közreműködői szerepkörrel a Databricks munkaterület-erőforráshoz a Azure Portal

#### <a name="error-message"></a>Hibaüzenet

"A fiók {e-mail} nem rendelkezik tulajdonosi vagy közreműködői szerepkörrel a Databricks munkaterület-erőforráshoz a Azure Portal. Ez a hiba akkor is előfordulhat, ha Ön vendég felhasználó a bérlőben. Kérje meg a rendszergazdát, hogy engedélyezze a hozzáférést, vagy közvetlenül a Databricks munkaterületen vegye fel Önt felhasználóként. " 

#### <a name="solution"></a>Megoldás

A probléma néhány megoldása a következő:

* A bérlő inicializálásához a bérlő normál felhasználójának kell bejelentkeznie, nem pedig vendég felhasználóként. Emellett közreműködői szerepkörrel kell rendelkeznie a Databricks-munkaterület erőforráson. A Databricks munkaterület **hozzáférés-vezérlés (iam)** lapján adhat meg felhasználói hozzáférést a Azure Portal.

* Ez a hiba akkor is előfordulhat, ha az e-mail tartományneve több címtárhoz van rendelve az Azure AD-ben. A probléma megkerüléséhez hozzon létre egy új felhasználót abban a könyvtárban, amely a Databricks-munkaterülettel rendelkező előfizetést tartalmazza.

    a. A Azure Portal nyissa meg az Azure AD-t. **Felhasználók és csoportok** > kiválasztása**felhasználó hozzáadása**.

    b. E- `@<tenant_name>.onmicrosoft.com` mail-cím helyett `@<your_domain>` adjon hozzá egy e-mailt a felhasználóhoz. Ezt a lehetőséget az Azure Portal Azure AD-ban található **Egyéni tartományokban**találja.
    
    c. Adja meg ezt az új felhasználót a **közreműködői** szerepkörnek a Databricks-munkaterület erőforráson.
    
    d. Jelentkezzen be a Azure Portalba az új felhasználóval, és keresse meg a Databricks-munkaterületet.
    
    e. Indítsa el a Databricks-munkaterületet a felhasználóként.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probléma: a (z) {e-mail} fiók nincs regisztrálva a Databricks-ben 

#### <a name="solution"></a>Megoldás

Ha nem hozta létre a munkaterületet, és felhasználóként adja hozzá, forduljon a munkaterületet létrehozó személyhez. Adja hozzá a személyt a Azure Databricks felügyeleti konzoljának használatával. Útmutatásért lásd: [felhasználók hozzáadása és kezelése](/azure/databricks/administration-guide/users-groups/users). Ha létrehozta a munkaterületet, és továbbra is ezt a hibaüzenetet kapja, próbálja meg újra kiválasztani a **munkaterületet** a Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Probléma: a felhőalapú szolgáltató indítási hibája a fürt beállításakor (PublicIPCountLimitReached)

#### <a name="error-message"></a>Hibaüzenet

"A Cloud Provider elindítása sikertelen: A fürt beállítása során hiba történt A Felhőbeli szolgáltatónál. További információ: Databricks útmutató. Azure-hibakód: PublicIPCountLimitReached. Azure-hibaüzenet: ebben a régióban nem hozható létre több mint 10 nyilvános IP-cím ehhez az előfizetéshez. "

#### <a name="background"></a>Háttér

A Databricks-fürtök minden csomóponton egy nyilvános IP-címet használnak (az illesztőprogram-csomópontot is beleértve). Az Azure-előfizetések régiónként egy [nyilvános IP-cím korláttal](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) rendelkeznek. Ezért előfordulhat, hogy a fürt létrehozása és a vertikális Felskálázási műveletek sikertelenek lesznek, ha az adott régióban az adott előfizetéshez lefoglalt nyilvános IP-címek száma meghaladja a korlátot. Ez a korlát magában foglalja a nem Databricks használathoz lefoglalt nyilvános IP-címeket is, például az egyéni felhasználó által definiált virtuális gépeket.

Általában a fürtök csak a nyilvános IP-címeket használják aktív állapotban. Azonban előfordulhat `PublicIPCountLimitReached` , hogy a hibák rövid ideig továbbra is előfordulhatnak, még a többi fürt leállítása után is. Ennek az az oka, hogy a Databricks átmenetileg gyorsítótárazza az Azure-erőforrásokat a fürt leállításakor. Az erőforrás-gyorsítótárazást úgy tervezték, hogy jelentősen csökkenti a fürt indítási és automatikus skálázási késését számos gyakori helyzetben.

#### <a name="solution"></a>Megoldás

Ha az előfizetése már elérte a nyilvános IP-cím korlátját egy adott régióban, akkor a következők egyikét kell tennie.

- Hozzon létre új fürtöket egy másik Databricks-munkaterületen. A másik munkaterületnek olyan régióban kell lennie, amelyben nem érte el az előfizetés nyilvános IP-címének korlátját.
- [A nyilvános IP-cím korlátjának növelésére irányuló kérés](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Válassza a **kvóta** lehetőséget a **probléma típusaként**, és a **hálózatkezelés: ARM** **értéket a kvóta típusaként**. A **részletek**területen kérjen egy nyilvános IP-cím kvótájának növekedését. Ha például a korlátja jelenleg 60, és egy 100 csomópontos fürtöt szeretne létrehozni, igényeljen határérték-növekedést 160-ra.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Probléma: a Cloud Provider indításának második típusa a fürt beállításakor (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Hibaüzenet

"A Cloud Provider elindítása sikertelen: A fürt beállítása során hiba történt A Felhőbeli szolgáltatónál. További információ: Databricks útmutató.
Azure-hibakód: MissingSubscriptionRegistration Azure-hibaüzenet: az előfizetés nincs regisztrálva a (z) Microsoft. számítás névtér használatára. Lásd https://aka.ms/rps-not-found : előfizetések regisztrálása.

#### <a name="solution"></a>Megoldás

1. Lépjen a [Azure Portal](https://portal.azure.com).
1. Válassza ki az **előfizetéseket**, a használt előfizetést, majd az **erőforrás-szolgáltatókat**. 
1. Az erőforrás-szolgáltatók listájában a **Microsoft. számítás**lapon válassza a **regisztráció**lehetőséget. Az erőforrás-szolgáltató regisztrálásához az előfizetés közreműködői vagy tulajdonosi szerepkörének kell lennie.

Részletesebb útmutatásért lásd: [erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Probléma: Azure Databricks engedélyekre van szükség a szervezet erőforrásaihoz való hozzáféréshez, csak a rendszergazda adhat meg.

#### <a name="background"></a>Háttér

Azure Databricks integrálva van Azure Active Directorysal. Az Azure AD-beli felhasználók megadásával engedélyeket állíthat be Azure Databricks belül (például jegyzetfüzeteken vagy fürtökön). Ahhoz, hogy Azure Databricks az Azure AD-beli felhasználók nevét lehessen listázni, olvasási engedéllyel kell rendelkeznie az adott információhoz, és meg kell adni a beleegyezését. Ha a beleegyezikés még nem érhető el, a következő hibaüzenet jelenik meg:.

#### <a name="solution"></a>Megoldás

Jelentkezzen be globális rendszergazdaként a Azure Portalba. Azure Active Directory esetében lépjen a **felhasználói beállítások** lapra, és győződjön meg arról, hogy a **felhasználók jóváhagyják az alkalmazások számára a vállalati adatokhoz való hozzáférést** az **Igen**értékre.

## <a name="next-steps"></a>További lépések

- [Gyors útmutató: az Azure Databricks használatának első lépései](quickstart-create-databricks-workspace-portal.md)
- [Mi az az Azure Databricks?](what-is-azure-databricks.md)
