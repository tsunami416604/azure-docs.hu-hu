---
title: Alkalmazás-hozzáférés biztosítása más Azure-erőforrásokhoz
description: Ez a cikk bemutatja, hogyan adhat hozzáférést a felügyelt identitás-alapú Service Fabric-alkalmazás nak az Azure Active Directory-alapú hitelesítést támogató egyéb Azure-erőforrásokhoz.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614793"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Service Fabric-alkalmazás felügyelt identitás-hozzáférés ének megadása az Azure-erőforrásokhoz (előzetes verzió)

Ahhoz, hogy az alkalmazás a felügyelt identitás használatával más erőforrások eléréséhez, engedélyeket kell adni az adott identitás a védett Azure-erőforrás elérése. Engedélyek megadása általában egy felügyeleti művelet a "vezérlősíkon" az Azure szolgáltatás tulajdonában lévő védett erőforrás az Azure Resource Manager, amely kikényszeríti a vonatkozó szerepköralapú hozzáférés-ellenőrzés.

A lépések pontos sorrendje ezután az elérhető Azure-erőforrás típusától, valamint az engedélyek megadásához használt nyelvtől/ügyféltől függ. A cikk többi része az alkalmazáshoz rendelt felhasználó által hozzárendelt identitást feltételez, és számos tipikus példát tartalmaz az Ön kényelme érdekében, de ez semmilyen módon nem kimerítő hivatkozás erre a témakörre; az engedélyek megadásával kapcsolatos naprakész utasításokat a megfelelő Azure-szolgáltatások dokumentációjában találja.  

## <a name="granting-access-to-azure-storage"></a>Hozzáférés megadása az Azure Storage-hoz
Használhatja a Service Fabric-alkalmazás felügyelt identitás (felhasználó rendelt ebben az esetben) az adatok lekérése egy Azure storage blob. Adja meg az identitásnak a szükséges engedélyeket az Azure Portalon a következő lépésekkel:

1. Keresse meg a tárfiókot
2. Kattintson a Hozzáférés-vezérlés (IAM) hivatkozásra a bal oldali panelen.
3. (nem kötelező) Meglévő hozzáférés ellenőrzése: válassza a Rendszer- vagy Felhasználó által hozzárendelt felügyelt identitást a "Keresés" vezérlőben; válassza ki a megfelelő identitást az azt követő eredménylistából
4. Kattintson a + Szerepkör-hozzárendelés hozzáadása a lap tetejére gombra az alkalmazás identitásához új szerepkör-hozzárendelés hozzáadásához.
A Szerepkör csoportban a legördülő menüben válassza a Storage Blob Data Reader lehetőséget.
5. A következő legördülő menü Hozzáférés hozzárendelése csoportban válassza a lehetőséget. `User assigned managed identity`
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az Előfizetés legördülő menüben, majd állítsa az Erőforráscsoport értékét a Minden erőforráscsoport értékre.
7. A Kijelölés csoportban válassza ki a Service Fabric-alkalmazásnak megfelelő felhasználói felületi azonosítót, majd kattintson a Mentés gombra.

A rendszeráltal hozzárendelt Service Fabric felügyelt identitások támogatása nem tartalmazza az Azure Portalon való integrációt; ha az alkalmazás rendszeráltal hozzárendelt identitást használ, először meg kell találnia az alkalmazás identitásának ügyfélazonosítóját, `Azure AD user, group, or service principal` majd meg kell ismételnie a fenti lépéseket, de a Keresés vezérlőben be kell jelölnie a beállítást.

## <a name="granting-access-to-azure-key-vault"></a>Hozzáférés megadása az Azure Key Vaulthoz
Hasonlóképpen a storage eléréséhez, használhatja a Service Fabric-alkalmazás felügyelt identitását egy Azure-kulcstartó eléréséhez. Az Azure Portalon való hozzáférés biztosításának lépései hasonlóak a fent felsoroltakhoz, és itt nem ismétlődnek meg. A különbségeket lásd az alábbi képen.

![Key Vault hozzáférési szabályzat](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

A következő példa bemutatja a hozzáférést a tároló egy sablon központi telepítésén keresztül; adja hozzá az alábbi kódrészlet(eke)t egy másik bejegyzésként a `resources` sablon eleméalatt. A minta bemutatja a hozzáférést biztosít mind a felhasználó által hozzárendelt, mind a rendszer által hozzárendelt identitástípusokhoz – válassza ki a megfelelőt.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
És a rendszer által hozzárendelt felügyelt identitások:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

További részletek: [Vaults - Update Access Policy](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>További lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)