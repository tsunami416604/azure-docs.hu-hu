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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Azure Databricks előzetes verziója: Gyakori kérdések és Súgó

Ez a cikk az első lehetséges, hogy lekérdezések kapcsolódnak az Azure Databricks sorolja fel. Előfordulhat, hogy futtatja az Azure Databricks használata során jelentkező gyakori problémákat is felsorolja. Az Azure Databricks további információkért lásd: [Mi az Azure Databricks?](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Gyakori kérdések

Ez a rész felsorolja az Azure Databricks kapcsolatos gyakori kérdésekre.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>Használható a helyi titkosítási kulcsok az saját? 
A jelenlegi kiadásban a saját Azure Keyvault a kulcsok használata nem támogatott. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Használható az Azure Databricks Azure Vnetekhez?
Új virtuális Hálózatot az Azure Databricks kiépítés részeként jön létre. Ebben a kiadásban részeként a saját Azure virtuális hálózat nem használható.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Hogyan érhetem el az Azure Data Lake Store a jegyzetfüzet? 

1. Az Azure Active Directoryban kiépíteni egy egyszerű szolgáltatást, és jegyezze fel annak kulcsát.
2. A szükséges engedélyek hozzárendelése a a szolgáltatás egyszerű Azure Data Lake Store-ban.
3. Próbál hozzáférni egy fájlhoz az Azure Data Lake Store, a szolgáltatás egyszerű hitelesítő adatokat használja a notebook.

További információkért lásd: [használata Data Lake Store az Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz ismerteti az Azure Databricks kapcsolatos gyakori hibák elhárítása.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probléma: Ez az előfizetés nincs regisztrálva a "Microsoft.Databricks" névtér használatára

**Hibaüzenet**

Ez az előfizetés nincs regisztrálva a "Microsoft.Databricks" névtér használatára. Lásd: hogyan regisztrálhat előfizetések https://aka.ms/rps-not-found. (Kód: MissingSubscriptionRegistration)

**Megoldás**

1. Ugrás a [Azure-portálon](https://portal.azure.com).
2. Kattintson a **előfizetések**, az előfizetés, és kattintson **erőforrás-szolgáltató**. 
3. Az erőforrás-szolgáltatók listáját elleni **Microsoft.Databricks**, kattintson a **regisztrálása**. Az erőforrás-szolgáltató regisztrálása előfizetésnek közreműködő vagy a tulajdonos szerepkörrel kell rendelkeznie.


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probléma: Az a fiók {e-mail} az Azure-portálon Databricks munkaterület erőforráson nem rendelkezik tulajdonosi vagy közreműködői szerepkör.

**Hibaüzenet**

A fiók {e-mail} nem rendelkezik tulajdonosi vagy közreműködői szerepkört az Azure-portálon Databricks munkaterület erőforráson. Ez a hiba akkor is előfordulhat, ha a bérlő vendég felhasználók. Kérje meg a rendszergazdát, hogy eléréséhez, vagy adja hozzá azt A felhasználó közvetlenül a Databricks munkaterületen. 

**Megoldás**

Az alábbiakban láthatók a probléma néhány:

* A bérlő inicializálni kell bejelentkeznie tenant, nem a Vendég felhasználó felhasználói. Az erőforrás-Databricks munkaterület közreműködői szerepkört is rendelkeznie kell. Az egy felhasználó hozzáférést biztosíthat a **hozzáférés-vezérlés (IAM)** lapon belül a Azure Databricks munkaterület az Azure portálon.

* Ez a hiba akkor is előfordulhat, ha e-mailek tartománynevére több aktív könyvtárban van rendelve. A probléma megoldása érdekében hozzon létre egy új felhasználói az Active Directory, az előfizetés a Databricks munkaterülettel tartalmazó.

    a. Az Azure-portálon lépjen az Azure Active Directoryhoz, kattintson a **felhasználók és csoportok**, kattintson a **hozzáadni egy felhasználót**.

    b. A felhasználó hozzáadása egy `@<tenant_name>.onmicrosoft.com` ahelyett, hogy e-mail @< adott_tartomány > e-mailt. Megtalálhatja az < bérlő_neve >. onmicrosoft.com az Active Directory-társított a **egyéni tartományok** az Azure Active Directory, az Azure portálon.
    
    c. Adja meg az új felhasználó **közreműködő** szerepkör a Databricks munkaterület erőforráson.
    
    d. Jelentkezzen be az Azure-portálon az új felhasználóval, és a Databricks munkaterületen található.
    
    e. A Databricks munkaterületen nyissa meg a felhasználó nevében.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probléma: A fiók {e-mail} nincs regisztrálva a Databricks 

**Megoldás**

Ha nem hozott létre a munkaterületet, és a munkaterület felhasználóit hozzá, lépjen kapcsolatba a venni az Azure Databricks felügyeleti konzolját használja a munkaterületen létrehozott személy. Útmutatásért lásd: [hozzáadása és a felhasználók kezelése](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Ha a munkaterületet hozott létre, és továbbra is a következő hibaüzenet jelenik meg, próbálja meg újra gombra kattintva "Inicializálása munkaterület" Azure-portálról.

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Probléma: Felhő szolgáltató indítása sikertelen (PublicIPCountLimitReached): A felhőbeli szolgáltató hiba történt a fürt beállítása

**Hibaüzenet**

Felhő szolgáltató indítási hiba: A felhőbeli szolgáltató hiba történt a fürt beállítása. A Databricks útmutatójában olvashat. Az Azure hibakód: PublicIPCountLimitReached. Azure-hibaüzenet: nem hozható létre több mint 60 nyilvános IP-címek ehhez az előfizetéshez ebben a régióban.

**Megoldás**

Azure Databricks fürtök csomópontonként egy nyilvános IP-cím használatára. Ha az előfizetés már használatban van minden a nyilvános IP-címeket, akkor [a kvóta növeléséhez kérelem](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Válasszon **kvóta** , a **problématípust**, **hálózati: ARM** , a **Kvótatípus**, és a nyilvános IP-cím kvóta növelését a  **Részletek**. Például ha a korlát jelenleg 60 és akarja hozzon létre egy 100 csomópontot tartalmazó fürtben, hogy 160 a korlát növelését.

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Probléma: Felhő szolgáltató indítása sikertelen (MissingSubscriptionRegistration): A felhőbeli szolgáltató hiba történt a fürt beállítása

**Hibaüzenet**

Felhő szolgáltató indítási hiba: A felhőbeli szolgáltató hiba történt a fürt beállítása. A Databricks útmutatójában olvashat.
Az Azure hibakód: MissingSubscriptionRegistration Azure-hibaüzenet: az előfizetés nincs regisztrálva "Microsoft.Compute" névtér használatára. Hogyan regisztrálhat előfizetések https://aka.ms/rps-not-found lásd:

**Megoldás**

1. Ugrás a [Azure-portálon](https://portal.azure.com).
2. Kattintson a **előfizetések**, az előfizetés, és kattintson **erőforrás-szolgáltató**. 
3. Az erőforrás-szolgáltatók listáját elleni **Microsoft.Compute**, kattintson a **regisztrálása**. Az erőforrás-szolgáltató regisztrálása előfizetésnek közreműködő vagy a tulajdonos szerepkörrel kell rendelkeznie.

Lásd: [erőforrás-szolgáltatók és típusok](../azure-resource-manager/resource-manager-supported-services.md) részletesebb leírását.

## <a name="next-steps"></a>Következő lépések
A 2-es verzióját tartalmazó data factory létrehozása lépésenkénti útmutatójáért tekintse meg az alábbi oktatóanyagok:

- [Gyors üzembe helyezés: Az Azure Databricks első lépései](quickstart-create-databricks-workspace-portal.md)
- [Mi az Azure Databricks?](what-is-azure-databricks.md)

