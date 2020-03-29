---
title: 'Azure Databricks: Gyakori kérdések és súgó'
description: Válaszok az Azure Databricks-el kapcsolatos gyakori kérdésekre és hibaelhárítási információk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671566"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Gyakori kérdések az Azure Databricks-ről

Ez a cikk felsorolja az Azure Databricks-szel kapcsolatos legfontosabb kérdéseket. A Databricks használata során is felsorol néhány gyakori problémát. További információ: [Mi az Azure Databricks.](what-is-azure-databricks.md) 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Használhatom az Azure Key Vault ot az Azure Databricksben használandó kulcsok/titkos kulcsok tárolására?
Igen. Az Azure Key Vault segítségével kulcsokat/titkos kulcsokat tárolhat az Azure Databricks használatával. További információ: [Azure Key Vault által támogatott hatókörök.](/azure/databricks/security/secrets/secret-scopes)


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Használhatom az Azure virtuális hálózatokat a Databricks segítségével?
Igen. Az Azure Databricks egy Azure Virtual Network (VNET) használatával. További információ: [Azure Databricks telepítése az Azure virtuális hálózatban.](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Hogyan érhetem el az Azure Data Lake Storage-t egy jegyzetfüzetből? 

Kövesse az alábbi lépéseket:
1. Az Azure Active Directoryban (Azure AD), üzembe létesítsen egy egyszerű szolgáltatás, és rögzítse a kulcs.
1. Rendelje hozzá a szükséges engedélyeket a data lake storage szolgáltatásnévhez.
1. A Data Lake Storage-ban egy fájl eléréséhez használja a szolgáltatásnév hitelesítő adatait a Jegyzetfüzetben.

További információ: [Azure Data Lake Storage használata az Azure Databricks használatával című témakörben.](/azure/databricks/data/data-sources/azure/azure-datalake)

## <a name="fix-common-problems"></a>Gyakori problémák megoldása

Íme néhány probléma, amellyel a Databricks-szel találkozhat.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probléma: Ez az előfizetés nincs regisztrálva a "Microsoft.Databricks" névtér használatához.

#### <a name="error-message"></a>Hibaüzenet

"Ez az előfizetés nincs regisztrálva a "Microsoft.Databricks" névtér használatához. Tekintse https://aka.ms/rps-not-found meg, hogyan regisztrálhatja az előfizetéseket. (Kód: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Megoldás

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Válassza **az Előfizetések**lehetőséget, válassza a használt előfizetést, majd **az Erőforrás-szolgáltatók**lehetőséget. 
1. Az erőforrás-szolgáltatók listájában a **Microsoft.Databricks**ellen válassza a **Regisztráció**lehetőséget. Az erőforrás-szolgáltató regisztrálásához rendelkeznie kell az előfizetés közreműködői vagy tulajdonosi szerepkörével.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probléma: A(z) {email} fiók nem rendelkezik tulajdonosi vagy közreműködői szerepkörrel a Databricks munkaterületi erőforráson az Azure Portalon

#### <a name="error-message"></a>Hibaüzenet

"A(z) {email} fiók nem rendelkezik tulajdonosi vagy közreműködői szerepkörrel a Databricks munkaterületi erőforráson az Azure Portalon. Ez a hiba akkor is előfordulhat, ha a bérlő vendégfelhasználója. Kérje meg a rendszergazdát, hogy közvetlenül a Databricks-munkaterületen adjon hozzáférést vagy adjon hozzá felhasználóként." 

#### <a name="solution"></a>Megoldás

Az alábbiakban néhány megoldást talál erre a kérdésre:

* A bérlő inicializálásához be kell jelentkeznie a bérlő rendszeres felhasználójaként, nem pedig vendégfelhasználóként. A Databricks munkaterület-erőforrásközreműködői szerepkörrel is rendelkeznie kell. A databricks-munkaterületen belül az Azure Portalon a **hozzáférés-vezérlés (IAM)** lapon adhat felhasználói hozzáférést.

* Ez a hiba akkor is előfordulhat, ha az e-mail tartományneve az Azure AD-ben több könyvtárhoz van rendelve. A probléma kerülő megoldásához hozzon létre egy új felhasználót a címtárban, amely tartalmazza az előfizetést a Databricks munkaterülettel.

    a. Az Azure Portalon nyissa meg az Azure AD.In the Azure Portal, go to Azure AD. Válassza a **Felhasználók és csoportok** > **Felhasználó hozzáadása lehetőséget.**

    b. E-mail helyett `@<tenant_name>.onmicrosoft.com` e-mailben küldött felhasználó hozzáadása. `@<your_domain>` Ezt a lehetőséget az **Egyéni tartományok**ban találja, az Azure AD az Azure Portalon.
    
    c. Adja meg az új felhasználónak a Databricks munkaterületi erőforrás **közreműködői** szerepkörét.
    
    d. Jelentkezzen be az Azure Portalon az új felhasználóval, és keresse meg a Databricks munkaterületet.
    
    e. Indítsa el a Databricks munkaterületet, mint ez a felhasználó.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probléma: A(z) {email} fiók nincs regisztrálva a Databricks-ben 

#### <a name="solution"></a>Megoldás

Ha nem ő hozta létre a munkaterületet, és felhasználóként lett hozzáadva, forduljon a munkaterületet létrehozó személyhez. Az adott személy az Azure Databricks felügyeleti konzol használatával adja hozzá. További információt a Felhasználók hozzáadása és kezelése témakörben [talál.](/azure/databricks/administration-guide/users-groups/users) Ha létrehozta a munkaterületet, és továbbra is ezt a hibát kapja, próbálja meg újra a **Munkaterület inicializálása** lehetőséget az Azure Portalon.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Probléma: A felhőszolgáltató indítási hibája a fürt beállítása közben (PublicIPCountLimitReached)

#### <a name="error-message"></a>Hibaüzenet

"Felhőszolgáltató indítási hibája: Felhőszolgáltatói hiba történt a fürt beállítása során. További információkért tekintse meg a Databricks útmutatót. Azure-hibakód: PublicIPCountLimitReached. Azure-hibaüzenet: Ebben a régióban nem lehet 10-nél több nyilvános IP-címet létrehozni ehhez az előfizetéshez."

#### <a name="background"></a>Háttér

Databricks fürtök használata egy nyilvános IP-címet csomópontonként (beleértve az illesztőprogram-csomópont). Az Azure-előfizetések [régiónként nyilvános IP-címkorlátokkal](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) rendelkeznek. Így a fürt létrehozása és a felskálázási műveletek sikertelen lehet, ha azok hatására az adott előfizetéshez rendelt nyilvános IP-címek száma az adott régióban meghaladja a korlátot. Ez a korlát magában foglalja a nem Databricks-használathoz lefoglalt nyilvános IP-címeket is, például az egyéni felhasználó által definiált virtuális gépeket.

A fürtök általában csak akkor használnak nyilvános IP-címeket, ha aktívak. A `PublicIPCountLimitReached` hibák azonban rövid ideig továbbra is előfordulhatnak, még akkor is, ha más fürtök et is leállítanak. Ennek az az oka, hogy a Databricks ideiglenesen gyorsítótárazza az Azure-erőforrásokat, amikor egy fürt leáll. Az erőforrás-gyorsítótárazás tervezés alatt történik, mivel számos gyakori esetben jelentősen csökkenti a fürt indítási és automatikus skálázásának késését.

#### <a name="solution"></a>Megoldás

Ha az előfizetés már elérte a nyilvános IP-cím korlátot egy adott régióban, akkor az alábbiak közül az egyiket vagy a másikat kell tennie.

- Hozzon létre új fürtöket egy másik Databricks-munkaterületen. A másik munkaterületnek olyan régióban kell lennie, ahol még nem érte el az előfizetés nyilvános IP-címkorlátját.
- [A nyilvános IP-címkorlát növelésére irányuló kérés.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) Válassza a **Kvóta lehetőséget** **a problématípusaként**és **a Hálózat: ARM** típust. **Quota Type** A **Részletek részben**nyilvános IP-címkvóta-növelést kérhet. Ha például a korlát jelenleg 60, és 100 csomópontos fürtöt szeretne létrehozni, kérje a korlát 160-ra való növelését.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Probléma: A fürt beállítása közben a felhőszolgáltató indításának egy második típusa (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Hibaüzenet

"Felhőszolgáltató indítási hibája: Felhőszolgáltatói hiba történt a fürt beállítása során. További információkért tekintse meg a Databricks útmutatót.
Azure-hibakód: MissingSubscriptionRegistration Azure hibaüzenet: Az előfizetés nincs regisztrálva a "Microsoft.Compute" névtér használatához. Tekintse https://aka.ms/rps-not-found meg, hogyan regisztrálhatja az előfizetéseket."

#### <a name="solution"></a>Megoldás

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Válassza **az Előfizetések**lehetőséget, válassza a használt előfizetést, majd **az Erőforrás-szolgáltatók**lehetőséget. 
1. Az erőforrás-szolgáltatók listájában a **Microsoft.Compute**ellen válassza a **Regisztráció**lehetőséget. Az erőforrás-szolgáltató regisztrálásához rendelkeznie kell az előfizetés közreműködői vagy tulajdonosi szerepkörével.

További részletes utasításokat az [Erőforrás-szolgáltatók és -típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakörben talál.

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Probléma: Az Azure Databricks engedélyeket igényel a szervezet erőforrásainak eléréséhez, amelyeket csak egy rendszergazda adhat meg.

#### <a name="background"></a>Háttér

Az Azure Databricks integrálva van az Azure Active Directoryval. Az Azure Databricks -en belüli engedélyeket (például jegyzetfüzeteken vagy fürtökön) állíthatja be az Azure AD-ből származó felhasználók megadásával. Ahhoz, hogy az Azure Databricks az Azure AD-ből származó felhasználók nevét listázhassa, olvasási engedélyszükséges az adatokhoz, és ehhez hozzájárulás szükséges. Ha a hozzájárulás még nem érhető el, megjelenik a hiba.

#### <a name="solution"></a>Megoldás

Jelentkezzen be globális rendszergazdaként az Azure Portalra. Az Azure Active Directory esetén lépjen a **Felhasználói beállítások** lapra, és győződjön meg arról, hogy a felhasználók beleegyezhetnek abba, hogy a **vállalati adatokhoz a nevükben hozzáférő alkalmazások** Igen **(Igen)** legyen.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Első lépések az Azure Databricks-szel](quickstart-create-databricks-workspace-portal.md)
- [Mi az az Azure Databricks?](what-is-azure-databricks.md)
