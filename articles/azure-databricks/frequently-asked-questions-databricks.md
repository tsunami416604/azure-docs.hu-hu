---
title: 'Az Azure Databricks: Gyakori kérdések és Súgó'
description: Válaszok a gyakori kérdéseket és az Azure Databricks kapcsolatos hibaelhárítási információkat.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 3bcc511ec6ad8a246c2b1b3a33eb59043a45830e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138361"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Az Azure Databricks kapcsolatos gyakori kérdések

Ez a cikk felsorolja a, előfordulhat, hogy rendelkezik az Azure databricks kapcsolatos leggyakoribb kérdésekre. Databricks használatakor előfordulhat néhány gyakori problémát is felsorolja. További információkért lásd: [Mi az Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Azure Key Vault használatával tárolja a kulcsok vagy titkos kódokkal használható az Azure Databricks?
Igen. Az Azure Key Vault használatával tárolja az Azure Databricks használja kulcsokkal vagy titkos kódokkal. További információkért lásd: [Azure Key Vault biztonsági hatókörök](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#akv-ss).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Használható az Azure virtuális hálózatok a Databricks?
Igen. Az Azure Databricks az Azure Virtual Network (VNET) is használhatja. További információkért lásd: [üzembe helyezése az Azure Databricks az Azure virtuális hálózaton](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Hogyan érhetem el az Azure Data Lake Store a jegyzetfüzet? 

Kövesse az alábbi lépéseket:
1. Az Azure Active Directoryban (Azure AD) egy egyszerű szolgáltatás kiépítése, és jegyezze fel a kulcsot.
1. A szükséges engedélyek hozzárendelése a Data Lake Store az egyszerű szolgáltatás.
1. Egy fájlt a Data Lake Store eléréséhez használja az egyszerű szolgáltatás hitelesítő adatai jegyzetfüzetben.

További információkért lásd: [használata Data Lake Store az Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Gyakori problémák megoldása

Az alábbiakban néhány, a Databricks esetleg felmerülő problémák.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probléma: Az előfizetés nincs regisztrálva a "Microsoft.Databricks" névtér használatára

#### <a name="error-message"></a>Hibaüzenet

"Ez az előfizetés nincs regisztrálva a"Microsoft.Databricks"névtér használatára. Lásd: https://aka.ms/rps-not-found előfizetések esetében. (Code: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Megoldás

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
1. Válassza ki **előfizetések**, az előfizetést használ, majd **erőforrás-szolgáltatók**. 
1. Erőforrás-szolgáltatók listáján elleni **Microsoft.Databricks**válassza **regisztrálása**. Az előfizetésben az erőforrás-szolgáltató regisztrálása a közreműködői vagy tulajdonosi szerepkörrel kell rendelkeznie.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probléma: {Email} fiókja nem rendelkezik a tulajdonosi vagy közreműködői szerepkörhöz a Databricks-munkaterület erőforrás az Azure Portalon

#### <a name="error-message"></a>Hibaüzenet

"A fiók {email} nem rendelkezik tulajdonosi vagy közreműködői szerepkörhöz az Azure Portalon a Databricks munkaterületen erőforráson. Ez a hiba akkor is előfordulhat, ha Ön a bérlő vendégfelhasználó. Kérje meg a rendszergazdát, hogy eléréséhez, vagy vegye fel a Databricks-munkaterülethez közvetlenül a felhasználó." 

#### <a name="solution"></a>Megoldás

Az alábbiakban néhány probléma megoldása:

* A bérlő inicializálása, akkor be kell jelentkeznie a bérlő nem meg vendégként normál felhasználóként. A Databricks-munkaterület erőforráson is egy közreműködő szerepkörrel kell rendelkeznie. Az egy felhasználó hozzáférést biztosíthat a **hozzáférés-vezérlés (IAM)** lapján a Databricks-munkaterület az Azure Portalon.

* Ezt a hibát is okozhat az e-mailben is tartománynév hozzá van rendelve több címtár Azure AD-ben. A probléma megkerüléséhez a könyvtárban, amely tartalmazza az előfizetést a Databricks-munkaterület az új felhasználó létrehozása.

    a. Az Azure Portalon nyissa meg Azure ad-ben. Válassza ki **felhasználók és csoportok** > **felhasználó hozzáadása**.

    b. A felhasználó hozzáadása egy `@<tenant_name>.onmicrosoft.com` helyett e-mail `@<your_domain>` e-mailt. Ezt a beállítást annak **egyéni tartományok**, az Azure Portalon az Azure AD-csoportban.
    
    c. Adja meg az új felhasználóhoz a **közreműködői** szerepkör a Databricks-munkaterület erőforráson.
    
    d. Jelentkezzen be az Azure portal az új felhasználó, és keresse meg a Databricks-munkaterülethez.
    
    e. Nyissa meg a Databricks-munkaterülethez, a felhasználó.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probléma: A fiók {email} nincs regisztrálva a Databricksben 

#### <a name="solution"></a>Megoldás

Ha nem hozott létre a munkaterületet, és Ön hozzá szeretné adni a felhasználó, lépjen kapcsolatba a munkaterületet létrehozó személy. Vegye fel Önt az Azure Databricks felügyeleti konzol az adott személy rendelkezik. Útmutatásért lásd: [felhasználók kezelése és felvétele](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Ha a munkaterületet hozott létre, és továbbra is megjelenik a hibaüzenet, próbálkozzon **munkaterület inicializálása** újra az Azure Portalról.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Probléma: A felhő szolgáltatóhiba indítsa el a fürt (PublicIPCountLimitReached) beállítása közben

#### <a name="error-message"></a>Hibaüzenet

"A felhő-szolgáltató indítása sikertelen: A felhőbeli szolgáltató hiba történt a fürt beállítása. További információkért tekintse meg a Databricks útmutató. Az Azure-hibakód: PublicIPCountLimitReached. Azure-hibaüzenet: nem hozható létre több mint 60 nyilvános IP-címeket, ehhez az előfizetéshez ebben a régióban. "

#### <a name="solution"></a>Megoldás

Databricks-fürtök csomópontonként egy nyilvános IP-címet használja. Ha az előfizetés már használatban van az összes hozzá tartozó nyilvános IP-címek, meg kell [kérelmet a kvóta növeléséhez](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Válassza ki **kvóta** , a **Problématípus**, és **Hálózatkezelés: ARM** , a **Kvótatípus**. A **részletek**, nyilvános IP-címet a kvóta növelésére. Például ha a korlát jelenleg 60, és szeretne létrehozni egy 100 csomópontos fürtre, korlátozás megnövelésére 160 való.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Probléma: A második típusú indítási szolgáltatóhiba felhő beállításakor a fürt (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Hibaüzenet

"A felhő-szolgáltató indítása sikertelen: A felhőbeli szolgáltató hiba történt a fürt beállítása. További információkért tekintse meg a Databricks útmutató.
Az Azure-hibakód: MissingSubscriptionRegistration Azure-hibaüzenet: az előfizetés nincs regisztrálva a névtér "Microsoft.Compute". Lásd: https://aka.ms/rps-not-found előfizetések számára. "

#### <a name="solution"></a>Megoldás

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
1. Válassza ki **előfizetések**, az előfizetést használ, majd **erőforrás-szolgáltatók**. 
1. Erőforrás-szolgáltatók listáján elleni **Microsoft.Compute**válassza **regisztrálása**. Az előfizetésben az erőforrás-szolgáltató regisztrálása a közreműködői vagy tulajdonosi szerepkörrel kell rendelkeznie.

További részletes utasítások: [erőforrás-szolgáltatókat és típusaikat](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Probléma: Az Azure Databricks engedélyre van szüksége, amelyek csak egy rendszergazda is biztosítanak a szervezet erőforrásaihoz.

#### <a name="background"></a>Háttér

Az Azure Databricks integrálva van az Azure Active Directoryban. Beállíthatja az Azure Databricks engedélyek (például a jegyzetfüzetek és a fürtök) megadásával a felhasználók Azure AD-ből. A tudják listázza a nevüket, a felhasználók az Azure AD-ből az Azure Databricks megtalálhasson olvasási engedélyt ezt az információt és engedélyt adjanak meg kell adni. Ha a jóváhagyás már nem érhető el, a következő hibát látja.

#### <a name="solution"></a>Megoldás

Jelentkezzen be az Azure Portalra globális rendszergazdaként. Az Azure Active Directory, nyissa meg a **felhasználói beállítások** lapra, és győződjön meg arról, hogy **felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való** értékre van állítva **Igen**.

## <a name="next-steps"></a>További lépések

- [Gyors útmutató: Azure Databricks használatának első lépései](quickstart-create-databricks-workspace-portal.md)
- [Mi az Azure Databricks?](what-is-azure-databricks.md)

