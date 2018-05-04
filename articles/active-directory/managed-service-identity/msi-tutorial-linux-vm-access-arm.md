---
title: A Linux virtuális gép felhasználó által hozzárendelt MSI Azure Resource Manager eléréséhez használja
description: Ez az oktatóanyag végigvezeti egy User-Assigned felügyelt szolgáltatás identitásának (MSI) használata a Linux virtuális gép, Azure Resource Manager eléréséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8beb9e4e8abb09cf3e1a0f89baa33431ee74c160
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>A felhasználói identitás használatára a Linux virtuális gép, Azure Resource Manager eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag ismerteti, hogyan hozzon létre egy felhasználó lehet hozzárendelve identitás, rendelje hozzá a Linux virtuális gép (VM) és az Azure Resource Manager API eléréséhez majd használja az identitásukat. Felügyelt szolgáltatás-identitások Azure automatikusan kezeli. Az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatok beágyazása a kódot támogató szolgáltatások lehetővé teszik. 

Felügyelt szolgáltatás-identitások Azure automatikusan kezeli. Az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatok beágyazása a kódot támogató szolgáltatások lehetővé teszik.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy felhasználó lehet hozzárendelve identitás
> * A felhasználó identitása rendelt Linux virtuális gép 
> * A felhasználó identitása hozzáférési jogot egy erőforráscsoportot az Azure Resource Manager 
> * Szereznie egy hozzáférési jogkivonatot használja a felhasználó identitását, és hívja az Azure Resource Manager használatával 

## <a name="prerequisites"></a>Előfeltételek

- Ha a felügyelt Szolgáltatásidentitás ismerik biztosan tekintse meg a [áttekintése](overview.md) szakasz. **Ne feledje el áttekinteni a [rendszer és a felhasználó közötti különbségek hozzárendelt identitások](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.
- Ebben az oktatóanyagban a szükséges erőforrások létrehozása és a szerepkör felügyeleti lépések végrehajtásához a fiókot kell a "Tulajdonos" engedélyeket a megfelelő hatókörben (saját előfizetés vagy az erőforrás csoport). Ha a szerepkör-hozzárendelés segítségre van szüksége, tekintse meg [Use Role-Based hozzáférés-vezérlés kezelése az Azure-előfizetés erőforrásokhoz való hozzáférés](/azure/role-based-access-control/role-assignments-portal).

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

A jelen oktatóanyag esetében először létrehozhat egy új Linux virtuális Gépet. Úgy is dönthet, hogy egy meglévő virtuális gép használja.

1. Kattintson a **hozzon létre egy erőforrást** az Azure portál bal felső sarkában.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

    ![Linux virtuális gép létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép meg a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Adja meg a virtuális gép számára. További méretek megtekintéséhez válasszon **összes** , vagy módosítsa a lemez típusát támogatott szűrő. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="create-a-user-assigned-identity"></a>Hozzon létre egy felhasználó lehet hozzárendelve identitás

1. A parancssori felület konzol (helyett egy Azure-felhő rendszerhéj munkamenet) használata, ha először jelentkezik be az Azure-bA. Használjon olyan fiókot, amelybe szeretné létrehozni az új felhasználó identitása Azure-előfizetéssel társított:

    ```azurecli
    az login
    ```

2. Felhasználó által hozzárendelt identitás létre [az identitás létrehozása](/cli/azure/identity#az_identity_create). A `-g` paraméter határozza meg az erőforráscsoportot, ahol az MSI-fájl jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékekkel:
    
    > [!IMPORTANT]
    > A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    A válasz a felhasználó identitása létrehozott, az alábbi példához hasonló részleteit tartalmazza. Megjegyzés: a `id` érték a felhasználó identitását, a következő lépésben lesz használható:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>A Linux virtuális gép identitásának rendelt felhasználó hozzárendelése

Egy felhasználó lehet hozzárendelve identitás több Azure-erőforrások az ügyfelek által használható. A következő parancsok segítségével a felhasználó identitásának rendelhet egy virtuális. Használja a `Id` tulajdonságot adott vissza az előző lépésben a `-IdentityID` paraméter.

A felhasználó által hozzárendelt MSI hozzárendelése a Linux virtuális gép [az vm hozzárendelése-identitás](/cli/azure/vm#az_vm_assign_identity). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. Használja a `id` tulajdonságot adott vissza az előző lépésben a `--identities` paraméter értékét.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>A felhasználó identitása hozzáférési jogot egy erőforráscsoportot az Azure Resource Manager 

Felügyelt szolgáltatás identitásának (MSI) biztosít identitásokat tartalmaz, amelyek a kód segítségével való hitelesítésre szolgáló erőforrás API-k, támogatás az Azure AD-hitelesítés hozzáférési jogkivonatok igényelhetnek. Ebben az oktatóanyagban a kódot az Azure Resource Manager API-val érik el.  

A kódot az API eléréséhez, az identitás hozzáférést egy erőforráshoz az Azure Resource Manager szeretné. Ebben az esetben az erőforráscsoportot, amelyben a virtuális gép található. Frissítse az értéket a `<SUBSCRIPTION ID>` és `<RESOURCE GROUP>` a környezetnek megfelelően. Továbbá cserélje le `<MSI PRINCIPALID>` rendelkező a `principalId` tulajdonság által visszaadott a `az identity create` parancsot [hozzon létre egy felhasználó által hozzárendelt MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

A válasz tartalmazza a létrehozott, az alábbi példához hasonló szerepkör-hozzárendelés részletei:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és hívja az erőforrás-kezelő használatával 

Az oktatóanyag a hátralévő azt fog működni a korábban létrehozott virtuális gépről.

Lépések elvégzéséhez szüksége van egy SSH-ügyfél. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/about). 

1. Jelentkezzen be a Azure [portal](https://portal.azure.com).
2. A portálon lépjen a **virtuális gépek** és nyissa meg a Linux virtuális gép és a a **áttekintése**, kattintson a **Connect**. Másolja a karakterláncot, amellyel a virtuális Géphez csatlakozik.
3. Csatlakoztassa a virtuális Gépet az SSH-ügyfél az Ön által választott. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. A Terminálszolgáltatások ablakban CURL, használatával indítson egy lekérdezést a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont megszerezni egy hozzáférési jogkivonatot az Azure Resource Manager.  

   A CURL kérelem olyan hozzáférési jogkivonatot szerezni az alábbi példában látható. Ügyeljen arra, hogy a csere `<CLIENT ID>` rendelkező a `clientId` tulajdonság által visszaadott a `az identity create` parancsot [nem hoz létre felhasználó által hozzárendelt](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Értékét a `resource` paraméter az Azure AD által várt pontosan egyeznie kell. Az erőforrás-kezelő erőforrás-azonosító használata esetén meg kell adni a záró perjelet URI-n. 
    
    A válasz tartalmazza az Azure Resource Manager eléréséhez szükséges jogkivonat. 
    
    Válasz példa:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. A hozzáférési jogkivonat Azure Resource Manager eléréséhez használja, és az erőforráscsoportot, amelyhez Ön korábban hozzáférést a felhasználó lehet hozzárendelve identitás tulajdonságainak olvasása. Ügyeljen arra, hogy a csere `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` a korábban megadott értékeket, és `<ACCESS TOKEN>` a jogkivonatok az előző lépés eredményeképpen visszakapott.

    > [!NOTE]
    > Az URL-címe: kis-és nagybetűket, ezért ügyeljen arra, hogy a használt korábban az erőforráscsoportot és a nagybetűk "G" nevű pontos nagybetűket `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    A válasz az adott erőforráscsoport információkat tartalmaz, a következőhöz hasonló: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>További lépések

- Felügyelt Szolgáltatásidentitás áttekintését lásd: [áttekintése](overview.md).

