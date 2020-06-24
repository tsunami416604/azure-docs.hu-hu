---
title: Környezetek létrehozása az Azure által felügyelt identitásokkal a DevTest Labs szolgáltatásban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a felügyelt identitásokat az Azure-ban környezetek üzembe helyezéséhez egy Azure DevTest Labsban található laborban.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895310"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Környezetek üzembe helyezése az Azure által felügyelt identitások használatával tesztkörnyezetben 
A tesztkörnyezet tulajdonosaként felügyelt identitást használhat a környezetek tesztkörnyezetben való üzembe helyezéséhez. Ez a funkció olyan esetekben hasznos, amikor a környezet olyan Azure-erőforrásokra hivatkozik, mint a kulcstartók, a megosztott képtárak és a környezet erőforráscsoporthoz tartozó hálózatok. Lehetővé teszi olyan homokozó környezetek létrehozását, amelyek nem korlátozódnak az adott környezet erőforráscsoporthoz.

> [!NOTE]
> Jelenleg egyetlen felhasználó által hozzárendelt identitás támogatott egy laborban. 

## <a name="prerequisites"></a>Előfeltételek
- [Szerepkörök létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ebben a szakaszban a tesztkörnyezet tulajdonosaként a Azure Portal használatával adhat hozzá felhasználót felügyelt identitást a laborhoz. 

1. A labor lapon válassza a **konfiguráció és szabályzatok**lehetőséget. 
1. Válassza az **identitás** lehetőséget a **Beállítások** szakaszban.
1. Felhasználóhoz rendelt identitás hozzáadásához válassza a **Hozzáadás** lehetőséget az eszköztáron. 
1. Válasszon ki egy **identitást** egy előre feltöltött legördülő listából.
1. Válassza az **OK** lehetőséget.

    ![Felhasználó által felügyelt identitás hozzáadása](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. A felhasználó által felügyelt identitás megjelenik a listában. 

    ![Felhasználó által felügyelt identitás a listában](./media/use-managed-identities-environments/identity-in-list.png)

A mentés után a labor ezt az identitást fogja használni az összes tesztkörnyezet környezetének telepítésekor. Az identitás erőforrását az Azure-ban is elérheti, ha kijelöli az identitást a listából. 

A labor tulajdonosának semmilyen különleges teendőt nem kell tennie a környezet üzembe helyezésekor, ha a laborhoz hozzáadott identitás rendelkezik a környezethez hozzáférő külső erőforrásokhoz szükséges engedélyekkel. 

Ha módosítani szeretné a laborhoz rendelt felhasználó által felügyelt identitást, először távolítsa el a laborhoz csatolt identitást, majd adjon hozzá egy másikat a laborhoz. A laborhoz csatolt identitás eltávolításához válassza a **... lehetőséget. (három pont)**, majd kattintson az **Eltávolítás**gombra. 

![Felhasználó által felügyelt identitás a listában](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>API használata

1. Az identitás létrehozása után jegyezze fel az identitás erőforrás-AZONOSÍTÓját. A következő mintához hasonlóan kell kinéznie: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Ha egy PUT HTTPS-metódust hoz létre, az alábbi példához hasonló módon vegyen fel egy új `ServiceRunner` erőforrást a laborba. A Service Runner-erőforrás egy proxy erőforrás a felügyelt identitások kezeléséhez és vezérléséhez a DevTest Labs szolgáltatásban. A szolgáltatás futó neve lehet bármilyen érvényes név, de javasoljuk, hogy használja a felügyelt identitás erőforrásának nevét. 
 
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
 
    Íme egy példa: 

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
 
Miután hozzáadta a felhasználóhoz rendelt identitást a laborhoz, a Azure DevTest Labs szolgáltatás Azure Resource Manager környezetek telepítésekor fogja használni. Ha például szüksége van a Resource Manager-sablonra egy külső megosztott képkatalógus rendszerképének eléréséhez, győződjön meg arról, hogy a laborhoz hozzáadott identitásnak minimálisan szükséges engedélyei vannak a megosztott Képtár erőforrásához. 
