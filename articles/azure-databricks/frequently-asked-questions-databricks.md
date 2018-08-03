---
title: 'Az Azure Databricks: Gyakori kérdések és Súgó |} A Microsoft Docs'
description: Válaszok a gyakori kérdéseket és az Azure Databricks kapcsolatos hibaelhárítási információkat.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c3ba235c60480c38a21ee3264c54b4a4dcdea340
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434601"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Az Azure Databricks kapcsolatos gyakori kérdések

Ez a cikk a leggyakoribb lekérdezések, előfordulhat, hogy rendelkezik az Azure databricks kapcsolódó sorolja fel. Databricks használatakor előfordulhat néhány gyakori problémát is felsorolja. További információkért lásd: [Mi az Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>Használható a helyi titkosítási kulcsok saját? 
A jelenlegi kiadásban a saját kulcsok Azure Key vault használata nem támogatott. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Használható az Azure virtuális hálózatok a Databricks?
Databricks üzembe helyezésének részeként jön létre egy új virtuális hálózatot. Ebben a kiadásban a saját Azure virtuális hálózat nem használható.

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

Az Azure Databricks integrálva van az Azure ad-ben. Ez lehetővé teszi, hogy az Azure databricksben engedélyek beállítása (mint például a jegyzetfüzetek és a fürtök) megadásával a felhasználók Azure AD-ből. Az Azure Databricksnek tudják listázza a nevüket, a felhasználók az Azure AD-ből szükséges adatok olvasási engedéllyel. Ehhez a jóváhagyást. Ha a jóváhagyás már nem érhető el, a következő hibát látja.

#### <a name="solution"></a>Megoldás

Jelentkezzen be az Azure Portalra globális rendszergazdaként. Az Azure Active Directory, nyissa meg a **felhasználói beállítások** lapra, és győződjön meg arról, hogy **felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való** értékre van állítva **Igen**.

## <a name="next-steps"></a>További lépések

- [Gyors útmutató: Azure Databricks használatának első lépései](quickstart-create-databricks-workspace-portal.md)
- [Mi az Azure Databricks?](what-is-azure-databricks.md)

