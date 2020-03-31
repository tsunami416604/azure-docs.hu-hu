---
title: Azure felügyelt identitások használatával környezeteket hozhat létre a DevTest Labs-ben | Microsoft dokumentumok
description: Megtudhatja, hogy miként használhatja a felügyelt identitásokat az Azure-ban a környezetek üzembe helyezéséhez az Azure DevTest Labs tesztkörnyezetében.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931157"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Az Azure által felügyelt identitások használatával környezeteket helyezhet üzembe egy tesztkörnyezetben 
Labortulajdonosként egy felügyelt identitás használatával telepítheti a környezeteket egy tesztkörnyezetben. Ez a funkció olyan esetekben hasznos, ahol a környezet tartalmaz, vagy az Azure-erőforrásokra, például a kulcstartók, a megosztott képgalériák és a környezet erőforráscsoportján kívüli hálózatokra mutató hivatkozásokat tartalmaz. Lehetővé teszi olyan sandbox-környezetek létrehozását, amelyek nem korlátozódnak az adott környezet erőforráscsoportjára.

> [!NOTE]
> Jelenleg egy felhasználó által hozzárendelt identitás laboronként támogatott. 

## <a name="prerequisites"></a>Előfeltételek
- [Szerepkör létrehozása, listázása, törlése vagy hozzárendelése egy felhasználó által hozzárendelt felügyelt identitáshoz az Azure Portalhasználatával.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ebben a szakaszban, mint a labor tulajdonosa, az Azure Portal használatával adja hozzá a felhasználó által felügyelt identitást a laborban. 

1. A tesztkörnyezet lapon válassza a **Konfiguráció és házirendek**lehetőséget. 
1. Válassza **az Identitás** lehetőséget a **Beállítások** szakaszban.
1. Felhasználó hozrendelt identitásának hozzáadásához kattintson a **Hozzáadás** gombra az eszköztáron. 
1. Válasszon **identitást** egy előre kitöltött legördülő listából.
1. Válassza **az OK gombot.**

    ![Felhasználó által felügyelt identitás hozzáadása](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. A hozzáadott felhasználó által felügyelt identitás látható a listában. 

    ![Felhasználó által felügyelt identitás a listában](./media/use-managed-identities-environments/identity-in-list.png)

Mentés után a labor fogja használni ezt az identitást, miközben az összes tesztkörnyezetben. Az identitás-erőforrás azure-ban is hozzáférhet az identitás kiválasztásával a listából. 

A labor tulajdonosának nem kell semmi különlegeset tennie egy környezet üzembe helyezése közben, amíg a laborhoz hozzáadott identitás rendelkezik a környezet által a környezet eléréséhez szükséges külső erőforrásokhoz szükséges hozzáféréssel. 

A tesztkörnyezethez rendelt felhasználó által felügyelt identitás módosításához először távolítsa el a laborhoz csatolt identitást, majd adjon hozzá egy másikat a laborhoz. A laborhoz csatolt identitás eltávolításához válassza **a ... (három ponttal) gombra,** majd kattintson **az Eltávolítás gombra.** 

![Felhasználó által felügyelt identitás a listában](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>AZ API használata

1. Az identitás létrehozása után jegyezze fel az identitás erőforrás-azonosítóját. A következő mintának kell kinéznie: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. A következő példához hasonló `ServiceRunner` új erőforrás hozzáadásához hajtson végre egy PUT Https metódust. A Service Runner erőforrás egy proxyerőforrás a felügyelt identitások kezeléséhez és kezeléséhez a DevTest Labs.Service runner resource is a proxy resource to manage and control managed managed managed and control managed managed managed s A szolgáltatás futó neve lehet bármilyen érvényes nevet, de azt javasoljuk, hogy használja a felügyelt identitás-erőforrás nevét. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Például: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Miután a felhasználó által hozzárendelt identitás tanod a laborba, az Azure DevTest Labs szolgáltatás fogja használni az Azure Resource Manager-környezetek üzembe helyezésekor. Ha például szüksége van az Erőforrás-kezelő sablonra egy külső megosztott képtár képének eléréséhez, győződjön meg arról, hogy a laborba hozzáadott identitás rendelkezik a megosztott képtár erőforrás minimálisan szükséges engedélyekkel. 
