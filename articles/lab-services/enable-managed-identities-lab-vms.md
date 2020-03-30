---
title: Felügyelt identitások engedélyezése a laboros virtuális gépeken az Azure DevTest Labsben
description: Ez a cikk bemutatja, hogyan engedélyezheti a tesztkörnyezet tulajdonosa a felhasználó által hozzárendelt felügyelt identitások a tesztkörnyezet virtuális gépein.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692666"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Felhasználó által hozzárendelt felügyelt identitások engedélyezése az Azure DevTest Labs laboralapú virtuális gépein
A labor tulajdonosaként engedélyezheti a felhasználó által hozzárendelt felügyelt identitások a labor virtuális gépek (VM- ek) az Azure DevTest Labs.

A felügyelt identitás hitelesítésére használható minden olyan szolgáltatás, amely támogatja az Azure Active Directory (AD) hitelesítés, beleértve a Key Vault, anélkül, hogy a kód hitelesítő adatok megadása. A felügyelt identitásokról a [Mi az Azure-erőforrások felügyelt identitásai című témakörben](../active-directory/managed-identities-azure-resources/overview.md)talál további információt.

Ezzel a funkcióval a labor-felhasználók megoszthatják az Azure-erőforrásokat, például az Azure SQL Database-t a labor környezetében. Az erőforrás hitelesítését maga az identitás gondoskodik. Konfigurálás után minden meglévő/újonnan létrehozott tesztkörnyezet virtuális gép engedélyezve lesz ezzel az identitással. A laborfelhasználók hozzáférhetnek az erőforrásokhoz, miután bejelentkeztek a gépeikbe.

> [!NOTE]
> Több felhasználó által hozzárendelt felügyelt identitások engedélyezni kell a labor virtuális gépeken.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ha hozzá szeretne adni egy felügyelt felhasználót a tesztkörnyezet virtuális gépeihez, kövesse az alábbi lépéseket:

1. [Felhasználó által hozzárendelt felügyelt identitás létrehozása az előfizetésben](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Keresse meg a **tesztkörnyezet konfigurációja és házirendjei** lapját.
2. Válassza **az Identitás (Előnézet)** lehetőséget a bal oldali menüben.
3. Válassza a **Virtuális gép** lapot.
4. Válassza **a Hozzáadás** lehetőséget, ha egy előre kitöltött legördülő listából szeretne meglévő identitást kijelölni. 

    ![Identitás hozzáadása gomb](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Válasszon ki egy **meglévő, felhasználó által felügyelt identitást** az eldobott listából, és kattintson az **OK gombra.** 

    ![Identitás hozzáadása](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>AZ API használata

1.  Az identitás létrehozása után jegyezze fel az identitás erőforrás-azonosítóját. A következő mintának kell kinéznie: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Futtasson egy PUT HTTPS metódust egy új **ServiceRunner** erőforrás hozzáadásához a laborhoz az alábbi példában látható módon. 

    A Service Runner erőforrás egy proxyerőforrás a felügyelt identitások kezeléséhez és kezeléséhez a DevTest Labs.Service runner resource is a proxy resource to manage and control managed managed managed and control managed managed managed s A szolgáltatás futó neve lehet bármilyen érvényes nevet, de azt javasoljuk, hogy használja a nevét a felügyelt identitás-erőforrás.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>További lépések
A felügyelt identitásokról a [Mi az Azure-erőforrások felügyelt identitásai című](../active-directory/managed-identities-azure-resources/overview.md)témakörben olvashat bővebben.







