---
title: Azure Service Fabric – más Azure-erőforrásokhoz való hozzáférés biztosítása Service Fabric alkalmazás számára | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan biztosítható a felügyelt identitást támogató Service Fabric alkalmazásokhoz való hozzáférés más Azure-erőforrásokhoz Azure Active Directory-alapú hitelesítés támogatásával.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: b6e1108ffee13f1583d920947404963a69616788
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958420"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Service Fabric alkalmazás felügyelt identitás-hozzáférésének megadása az Azure-erőforrásokhoz

Ahhoz, hogy az alkalmazás a felügyelt identitásával hozzáférhessen más erőforrásokhoz, engedélyeket kell adni ahhoz az identitáshoz a védett Azure-erőforráson. Az engedélyek megadása általában a Azure Resource Manageron keresztül átirányított védett erőforrást birtokló Azure-szolgáltatás vezérlési síkon található felügyeleti művelet, amely az összes vonatkozó szerepköralapú hozzáférés-ellenőrzést kikényszeríti.

A lépések pontos lépései ezután az elérhető Azure-erőforrás típusától, valamint az engedélyek megadásához használt nyelvtől/ügyféltől függenek. A cikk hátralévő része feltételezi, hogy a felhasználó által hozzárendelt identitás az alkalmazáshoz van rendelve, és számos tipikus példát tartalmaz a kényelemre, de ez a témakör semmilyen módon nem használható. az engedélyek megadásáról a megfelelő Azure-szolgáltatások dokumentációjában tájékozódhat.  

## <a name="granting-access-to-azure-storage"></a>Hozzáférés biztosítása az Azure Storage-hoz
Az adatok Azure Storage-blobból való lekéréséhez használhatja a Service Fabric alkalmazás felügyelt identitását (ebben az esetben a felhasználóhoz rendelt). Adja meg a szükséges engedélyeket a Azure Portal a következő lépésekkel:

1. Navigáljon a Storage-fiókhoz
2. Kattintson a hozzáférés-vezérlés (IAM) hivatkozásra a bal oldali panelen.
3. választható Meglévő hozzáférés ellenőrzése: válassza a rendszer vagy a felhasználó által hozzárendelt felügyelt identitás elemet a keresés vezérlőelemben; Válassza ki a megfelelő identitást az eredményül kapott eredmények listájából
4. Kattintson a + szerepkör-hozzárendelés hozzáadása lehetőségre az oldal tetején egy új szerepkör-hozzárendelés hozzáadásához az alkalmazás identitásához.
A szerepkör alatt a legördülő listából válassza a Storage blob-Adatolvasó lehetőséget.
5. A következő legördülő menüben válassza a hozzáférés társítása elemet `User assigned managed identity`.
6. Ezután győződjön meg arról, hogy a megfelelő előfizetés szerepel az előfizetés legördülő listájában, majd állítsa be az erőforráscsoportot az összes erőforráscsoporthoz.
7. A kiválasztás területen válassza ki a Service Fabric alkalmazásnak megfelelő UAI, majd kattintson a Mentés gombra.

A rendszer által hozzárendelt Service Fabric felügyelt identitások támogatása nem tartalmazza a Azure Portal integrációját; Ha az alkalmazás rendszer által hozzárendelt identitást használ, először meg kell keresnie az alkalmazás identitásának ügyfél-azonosítóját, majd újra meg kell ismételnie a fenti lépéseket, `Azure AD user, group, or service principal` de a keresés vezérlőelemben is ki kell választania a lehetőséget.

## <a name="granting-access-to-azure-key-vault"></a>Hozzáférés biztosítása Azure Key Vault
A tárterület eléréséhez hasonlóan kihasználhatja egy Service Fabric alkalmazás felügyelt identitását az Azure Key Vault eléréséhez. A hozzáférés megadásának lépései a Azure Portalban hasonlóak a fent felsoroltak esetében, és itt nem ismétlődik meg. A különbségeket az alábbi képen tekintheti meg.

![Hozzáférési szabályzat Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Az alábbi példa azt szemlélteti, hogyan lehet hozzáférést biztosítani a tárolóhoz egy sablon központi telepítése révén; adja hozzá az alábbi kódrészletet egy másik bejegyzéshez a sablon `resources` eleme alatt.

```json
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

További részletekért lásd: tárolók [– frissítési hozzáférési szabályzat](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>További lépések

* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Felügyelt identitások támogatásának](./concepts-managed-identity.md) áttekintése az Azure Service Fabric

* [Új telepítése](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-fürt felügyelt identitások támogatásával 

* [Felügyelt identitás engedélyezése](./configure-existing-cluster-enable-managed-identity-token-service.md) meglévő Azure Service Fabric-fürtben

* Service Fabric alkalmazás felügyelt identitásának kihasználása [a forráskódból](./how-to-managed-identity-service-fabric-app-code.md)

* Nézze meg az Azure [ad-hitelesítést támogató Azure-szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-msi.md) listáját
