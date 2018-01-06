---
title: "Egy Azure virtuális gép felügyelt Szolgáltatásidentitás használatát a bejelentkezéshez"
description: "Lépésenkénti útmutatásért és példákért használatával jelentkezzen be a parancsfájl-ügyfél egy Azure virtuális gép MSI egyszerű szolgáltatásnév és az erőforrás eléréséhez."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Jelentkezzen be egy virtuális Gépet, felhasználó által hozzárendelt felügyelt szolgáltatás identitásának (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]Ez a cikk példákat CLI parancsfájl jelentkezzen be egy felhasználó által hozzárendelt MSI egyszerű szolgáltatást, és útmutatást használatával hibakezelés például a fontos kérdésekben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Ahhoz, hogy az Azure CLI példák ebben a cikkben, ügyeljen arra, hogy telepítse a legújabb verzióját [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Ebben a cikkben minden mintaparancsfájl azt feltételezi, hogy a parancssori ügyfelet futtató egy MSI-kompatibilis virtuális gépen. A virtuális gép "Csatlakozás" szolgáltatást használja az Azure-portálon távolról csatlakozni a virtuális Gépet. A virtuális gép MSI engedélyezésével kapcsolatos részletekért lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md), vagy a variant cikkekben (a PowerShell, Portal, sablon vagy egy Azure SDK használatával). 
> - Során be- és erőforrás-hozzáférés megelőzése érdekében, az MSI meg kell adni legalább "Olvasó" elérni a megfelelő hatókörben (a virtuális gép vagy magasabb) a virtuális gép Azure Resource Manager műveletek engedélyezéséhez. Lásd: [egy felügyelt szolgáltatás identitásának (MSI) hozzáférés hozzárendelése az Azure parancssori felület használatával erőforrás](msi-howto-assign-access-cli.md) részleteiről.

## <a name="overview"></a>Áttekintés

Egy olyan MSI Csomaghoz biztosít egy [egyszerű](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), amely [MSI engedélyezése után létrehozott](msi-overview.md#how-does-it-work) a virtuális Gépen. A szolgáltatás egyszerű Azure-erőforrások való hozzáférése, és egy identitás parancsfájl/parancssor-line ügyfelek használják a bejelentkezési és erőforrás-hozzáféréshez. Hagyományosan a saját identitással védett erőforrások eléréséhez a parancsfájl ügyfél kell:  

   - regisztrált és átadni kívánt hozzájárult e bizalmas vagy webes ügyfél alkalmazásként Azure AD-val
   - Jelentkezzen be a szolgáltatás egyszerű, (amelyek ágyazva a parancsfájlba) az alkalmazás hitelesítő adatokkal a

Az MSI-fájl a parancsfájl-ügyfél már nem kell módszerekkel, mint azt jelentkezhetnek be az MSI egyszerű szolgáltatás alatt. 

## <a name="azure-cli"></a>Azure CLI

Az alábbi parancsfájl bemutatja, hogyan:

1. Jelentkezzen be a felhasználó által hozzárendelt MSI egyszerű szolgáltatásnév az Azure AD.  
2. Azure Resource Manager hívja, és a virtuális gépek az Azure-régiót helyét. Parancssori felület jogkivonat beszerzése vagy használata automatikusan kezelése az Ön gondoskodik. Ügyeljen arra, hogy a virtuális gép nevét `<VM NAME>`, és a felhasználó által hozzárendelt MSI erőforrás-azonosító a `<MSI ID>`. Az MSI erőforrás-azonosító eredmény abban az esetben a `id` tulajdonság egy felhasználó által hozzárendelt MSI-fájl létrehozása során (lásd: [egy felhasználó által hozzárendelt felügyelt szolgáltatás identitásának (MSI) konfigurálja a virtuális gépek Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md) példákat a `az identity create` parancs ).

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Példa válaszok:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítók az Azure-szolgáltatások

Lásd: [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](msi-overview.md#azure-services-that-support-azure-ad-authentication) erőforrásokat, amelyek támogatják az Azure AD, és az MSI lettek tesztelve, és a megfelelő erőforrás-azonosítók listáját.

## <a name="error-handling-guidance"></a>Hiba kezelési útmutató 

A következő válaszok azt jelezheti, hogy az MSI-fájl nincs megfelelően konfigurálva:

- Parancssori felület: *MSI: nem sikerült beolvasni a "http://localhost:50342/oauth2/token" hiba történt a származó jogkivonat "HTTPConnectionPool (host"localhost", port = = 50342)* 

Ha egyik ezeket a hibákat, térjen vissza az Azure virtuális gép a [Azure-portálon](https://portal.azure.com) és:

- Lépjen a **konfigurációs** lapon, és ellenőrizze a "Felügyelt szolgáltatásidentitás" értéke "Yes".
- Lépjen a **bővítmények** lapon, és ellenőrizze az MSI-bővítmény sikeresen telepítve.

Vagy nem megfelelő, ha szeretne újra hozzárendelnie az MSI-csomagot az erőforrás újra, vagy a központi telepítési hiba elhárítása. Lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége.

## <a name="next-steps"></a>További lépések

- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.








