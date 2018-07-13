---
title: Windows virtuális gép MSI használata az Azure Resource Manager eléréséhez
description: Ez az oktatóanyag végigvezeti a folyamatot, egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használatával az Azure Resource Manager eléréséhez.
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a7960ab4aee80c7d15ea0f031790dd089424565d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610257"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata a Resource Manager eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a Felügyeltszolgáltatás-identitás (MSI) egy Windows virtuális gép (VM). Ezután használhatja az identitáshoz az Azure Resource Manager API eléréséhez. Felügyelt Szolgáltatásidentitások Azure automatikusan kezeli, és lehetővé teszi, anélkül, hogy a hitelesítő adatokat beszúrni a kódot az Azure AD-hitelesítést támogató szolgáltatások hitelesítéséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egy Windows virtuális gép az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést biztosítani az erőforráscsoport az Azure Resource Manager 
> * A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon és meghívására az Azure Resource Manager

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban létrehozunk egy új Windows virtuális Gépet.  A meglévő virtuális gép MSI is engedélyezheti.

1.  Kattintson a **erőforrás létrehozása** a az Azure portal bal felső sarkában.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gépet.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** , amelyben a virtuális gép létrehozásához, válassza ki a **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások oldalon hagyja változatlanul az alapértelmezett beállításokat, majd kattintson **OK**.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez 

Virtuális gép MSI lehetővé teszi, hogy a hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. MSI engedélyezéséhez jelzi az Azure a virtuális gép létrehozása egy felügyelt identitás. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepíti, és lehetővé teszi, hogy az Azure Resource Manager MSI.

1.  Válassza ki a **virtuális gép** , hogy szeretné-e az MSI engedélyezéséhez.  
2.  A bal oldali navigációs sávon kattintson **konfigurációs**. 
3.  Látja **Felügyeltszolgáltatás-identitás**. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem. 
4.  Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  
    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, és a virtuális gépen ellenőrizze, hogy mely bővítmények, kattintson a **bővítmények**. Ha az MSI engedélyezve van, majd **ManagedIdentityExtensionforWindows** megjelenik a listában.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>A virtuális gép hozzáférést biztosítani az erőforráscsoport a Resource Managerben
MSI használatával a kód kérheti le jogkivonatot az Azure AD-hitelesítést támogató erőforrásokhoz történő hitelesítéshez.  Az Azure Resource Manager támogatja az Azure AD-hitelesítés.  Először létre kell hozzáférést a virtuális gép identitását egy erőforrást a Resource Managerben, ebben az esetben az erőforráscsoport, amelyben a virtuális gép szerepel.  

1.  Lépjen a lapra vonatkozó **erőforráscsoportok**. 
2.  Válassza ki az adott **erőforráscsoport** létrehozott a **Windows virtuális gép**. 
3.  Lépjen a **hozzáférés-vezérlés (IAM)** a bal oldali panelen. 
4.  Ezután **Hozzáadás** az új szerepkör-hozzárendelést a **Windows virtuális gép**.  Válasszon **szerepkör** , **olvasó**. 
5.  A következő legördülő **rendelhet hozzáféréseket** az erőforrás **virtuális gép**. 
6.  Következő lépésként győződjön meg arról, a megfelelő előfizetés szerepel-e a **előfizetés** legördülő listából. És a **erőforráscsoport**válassza **összes erőforráscsoport**. 
7.  Végül a **kiválasztása** válassza ki a legördülő listából, majd kattintson a Windows virtuális gép **mentése**.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon és meghívására az Azure Resource Manager 

Használni kell **PowerShell** ezen részében.  Ha nincs telepítve, töltse le [Itt](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  A portálon lépjen a **virtuális gépek** , és nyissa meg a Windows virtuális gép és a a **áttekintése**, kattintson a **Connect**. 
2.  Adja meg a **felhasználónév** és **jelszó** számára, amelyhez hozzáadta a Windows virtuális gép létrehozásakor. 
3.  Most, hogy létrehozott egy **távoli asztali kapcsolat** nyissa meg a virtuális géppel **PowerShell** a távoli munkamenet. 
4.  A helyi MSI-végpontot a hozzáférési jogkivonat beszerzése az Azure Resource Manager Powershell Invoke-WebRequest használja, végezze el egy kérelmet.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Az "erőforrás" paraméter értékét az Azure AD által várt pontos egyezésűnek kell lennie. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adnia az URI a záró perjellel.
    
    Ezután bontsa ki a teljes választ, amely egy JavaScript Object Notation (JSON) formátumú karakterláncot a $response objektumban van tárolva. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután bontsa ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Végezetül hívja meg az Azure Resource Manager hozzáférési token használatával. Ebben a példában is használunk PowerShell Invoke-WebRequest a hívást, az Azure Resource Manager és a hozzáférési jogkivonat felvétel az engedélyezési fejléc.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > Az URL-címe: kis-és nagybetűket, ezért győződjön meg arról, ha az azonos megkülönböztetése korábban használja, ha az erőforráscsoport és a "resourceGroups." nagy "G" nevű
        
    A következő parancsot az erőforráscsoport részleteit adja vissza:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.

