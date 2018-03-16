---
title: "Az Azure Databricks: Gyakori kérdések és Súgó |} Microsoft Docs"
description: "Gyakori kérdések és a hibaelhárítási információk a Azure Databricks választ kaphat."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: nitinme
ms.openlocfilehash: 5da6ffc346cc0e7f0f83bf4a4c33600b668a17ca
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Azure Databricks kapcsolatos gyakori kérdések

Ez a cikk a leggyakoribb lekérdezések, előfordulhat, hogy rendelkezik kapcsolatos Azure Databricks sorolja fel. Is olyan gyakori problémákat, lehetséges, hogy Databricks használatakor sorolja fel. További információkért lásd: [Mi az Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>Használható a helyi titkosítási kulcsok az saját? 
A jelenlegi kiadásban az Azure Key Vault a saját kulcs használata nem támogatott. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Használható az Azure virtuális hálózatok Databricks?
Egy új virtuális hálózat létrejötte Databricks kiépítés részeként. Ebben a kiadásban a saját Azure virtuális hálózat nem használható.

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Hogyan érhetem el az Azure Data Lake Store a jegyzetfüzet? 

Kövesse az alábbi lépéseket:
1. Az Azure Active Directory (Azure AD) egy egyszerű szolgáltatást telepíteni, és jegyezze fel annak kulcsát.
2. A szükséges engedélyek hozzárendelése a szolgáltatás egyszerű Data Lake Store-ban.
3. Hozzáférni egy fájlhoz a Data Lake Store, használja a Notebook a szolgáltatás egyszerű hitelesítő adatait.

További információkért lásd: [használata Data Lake Store az Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Gyakori problémák megoldása

Az alábbiakban néhány Databricks az esetleg felmerülő problémákat.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probléma: Ez az előfizetés nincs regisztrálva a "Microsoft.Databricks" névtér használatára

#### <a name="error-message"></a>Hibaüzenet

"Ez az előfizetés nincs regisztrálva a következő névtérben:"Microsoft.Databricks"használja. Lásd: https://aka.ms/rps-not-found hogyan regisztrálhat előfizetések. (Code: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Megoldás

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Válassza ki **előfizetések**, használ, az előfizetés, majd **erőforrás-szolgáltató**. 
3. Az erőforrás-szolgáltatók listáját elleni **Microsoft.Databricks**, jelölje be **regisztrálása**. Az erőforrás-szolgáltató regisztrálása előfizetésnek közreműködő vagy a tulajdonos szerepkörrel kell rendelkeznie.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probléma: {E-mail} fiókja nem rendelkezik a tulajdonos vagy közreműködő szerepkört az Azure-portálon Databricks munkaterület erőforráson

#### <a name="error-message"></a>Hibaüzenet

"A fiók {e-mail} nem rendelkezik tulajdonosi vagy közreműködői szerepkört az Azure-portálon Databricks munkaterület erőforráson. Ez a hiba akkor is előfordulhat, ha a bérlő vendég felhasználók. Kérje meg a rendszergazdát eléréséhez, vagy adja hozzá azt a felhasználó közvetlenül a Databricks munkaterületen." 

#### <a name="solution"></a>Megoldás

A megoldás a probléma néhány a következők:

* A bérlő inicializálása, be kell jelentkeznie tenant, nem pedig a Vendég felhasználó felhasználói. A közreműködői szerepkör a Databricks munkaterület erőforrás is kell rendelkeznie. Az egy felhasználó hozzáférést biztosíthat a **hozzáférés-vezérlés (IAM)** lapon belül a Databricks munkaterület az Azure portálon.

* Ezt a hibát is okozhat az e-mailek tartománynév van rendelve több címtárral az Azure ad-ben. A probléma megoldása érdekében új felhasználó létrehozása a könyvtárban, amely tartalmazza az előfizetés a Databricks munkaterülettel.

    a. Az Azure-portálon lépjen az Azure ad Szolgáltatásba. Válassza ki **felhasználók és csoportok** > **hozzáadni egy felhasználót**.

    b. A felhasználó hozzáadása egy `@<tenant_name>.onmicrosoft.com` ahelyett, hogy e-mail `@<your_domain>` e-mailt. Ez a beállítás található **egyéni tartományok**, az Azure AD-t az Azure-portálon.
    
    c. Adja meg ennek a felhasználónak a **közreműködő** szerepkör a Databricks munkaterület erőforráson.
    
    d. Jelentkezzen be az Azure-portálon az új felhasználóval, és a Databricks munkaterületen található.
    
    e. A Databricks munkaterületen nyissa meg a felhasználó nevében.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probléma: A fiók {e-mail} nincs regisztrálva a Databricks 

#### <a name="solution"></a>Megoldás

Ha nem hozott létre a munkaterületen, és hogy hozzá szeretné adni a felhasználó, lépjen kapcsolatba a személy, aki létrehozta a munkaterületen. Kell hozzáadnia az Azure Databricks felügyeleti konzol használatával személy. Útmutatásért lásd: [hozzáadása és a felhasználók kezelése](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Ha a munkaterületet hozott létre, és továbbra is a következő hibaüzenet jelenik meg, próbálkozzon **inicializálása munkaterület** újra az Azure portálról.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Probléma: Felhő szolgáltató indítási hiba lépett fel a fürt (PublicIPCountLimitReached) beállítása

#### <a name="error-message"></a>Hibaüzenet

"A felhő szolgáltató indítási hiba: A felhőbeli szolgáltató hiba történt a fürt beállítása. További információ a Databricks útmutatójában talál. Az Azure hibakód: PublicIPCountLimitReached. Azure-hibaüzenet: nem hozható létre több mint 60 nyilvános IP-címek ehhez az előfizetéshez ebben a régióban. "

#### <a name="solution"></a>Megoldás

Databricks fürtök csomópontonként egy nyilvános IP-cím használatára. Ha az előfizetés már használatban van minden a nyilvános IP-címeket, akkor [a kvóta növeléséhez kérelem](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Válasszon **kvóta** , a **típusú**, és **hálózati: ARM** , a **Kvótatípus**. A **részletek**, a nyilvános IP-cím kvótájának növelését. Például ha korlát jelenleg 60, és szeretne létrehozni egy 100-csomópontot tartalmazó fürtben, kérése a 160 a korlát növelését.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Probléma: A második típusú felhő szolgáltató indítási hiba lépett fel a fürt (MissingSubscriptionRegistration) beállítása

#### <a name="error-message"></a>Hibaüzenet

"A felhő szolgáltató indítási hiba: A felhőbeli szolgáltató hiba történt a fürt beállítása. További információ a Databricks útmutatójában talál.
Az Azure hibakód: MissingSubscriptionRegistration Azure-hibaüzenet: az előfizetés nincs regisztrálva "Microsoft.Compute" névtér használatára. Lásd: https://aka.ms/rps-not-found hogyan regisztrálhat előfizetések. "

#### <a name="solution"></a>Megoldás

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Válassza ki **előfizetések**, használ, az előfizetés, majd **erőforrás-szolgáltató**. 
3. Az erőforrás-szolgáltatók listáját elleni **Microsoft.Compute**, jelölje be **regisztrálása**. Az erőforrás-szolgáltató regisztrálása előfizetésnek közreműködő vagy a tulajdonos szerepkörrel kell rendelkeznie.

Részletes utasítások, lásd: [erőforrás-szolgáltatók és típusok](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Probléma: Az Azure Databricks kell engedélyekkel csak a rendszergazda biztosíthat egy szervezeti erőforrások eléréséhez.

#### <a name="background"></a>Háttér

Az Azure Databricks integrálva van az Azure ad-val. Ez lehetővé teszi engedélyeinek beállítása Azure Databricks belül (például notebookok vagy fürtöket) az Azure AD felhasználók megadásával. Az Azure Databricks tudni az Azure ad-felhasználó nevét a listában akkor ezt az információt olvasási engedélyre van szüksége. Ehhez a hozzájárulásukat adják. A hozzájárulási már nem érhető el, ha a hibát látja.

#### <a name="solution"></a>Megoldás

Jelentkezzen be egy globális rendszergazda az Azure portálon. Azure Active Directoryban, látogasson el a **felhasználói beállítások** lapra, és győződjön meg arról, hogy **felhasználók is hozzájárul a nevében a vállalati adatokhoz hozzáférő alkalmazásokat** értéke **Igen**.

## <a name="next-steps"></a>További lépések

- [Gyors üzembe helyezés: Az Azure Databricks első lépései](quickstart-create-databricks-workspace-portal.md)
- [Mi az Azure Databricks?](what-is-azure-databricks.md)

