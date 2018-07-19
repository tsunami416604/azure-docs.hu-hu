---
title: Windows VM-beli MSI használata az Azure Resource Manager eléréséhez
description: Az oktatóanyag azt ismerteti, hogyan érhető el az Azure Resource Managerhez egy Windows VM-beli felügyeltszolgáltatás-identitással (MSI).
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 7e2033310a30499cf862fb4d399cb0180ac9b713
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006964"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>A Resource Manager elérése Windows VM-beli felügyeltszolgáltatás-identitással (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan engedélyezheti a felügyeltszolgáltatás-identitást (MSI) egy Windows rendszerű virtuális gépen (VM). Ezután az identitással érheti el az Azure Resource Manager API-t. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * MSI engedélyezése Windows VM-en 
> * Hozzáférés engedélyezése virtuális gép számára az Azure Resource Managerben lévő erőforráscsoporthoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Egy Windows rendszerű virtuális gép létrehozása egy új erőforráscsoportban

Ebben az oktatóanyagban egy új Windows VM-et fog létrehozni.  A meglévő virtuális gépeken is engedélyezheti az MSI-t.

1.  Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. Az itt létrehozott **felhasználónév** és **jelszó** alkotják a virtuális gépre való bejelentkezéshez használt hitelesítő adatokat.
4.  Válassza ki a megfelelő **előfizetést** a virtuális géphez a legördülő menüben.
5.  A virtuális gép létrehozásához használni kívánt új **erőforráscsoport** kiválasztásához válassza az **Új létrehozása** lehetőséget. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a virtuális gép méretét. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások lapon hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Helyettesítő képszöveg](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>MSI engedélyezése a virtuális gépen 

A VM MSI-vel anélkül kérhet le hozzáférési jogkivonatokat az Azure AD-ből, hogy hitelesítő adatokat kellene a kódba illesztenie. A felügyeltszolgáltatás-identitás VM-en való engedélyezése két dolgot tesz: regisztrálja a VM-et az Azure Active Directoryban a felügyelt identitása létrehozásához, és konfigurálja az identitást a VM-en.

1.  Válassza ki azt a **virtuális gépet**, amelyen engedélyezni szeretné az MSI-t.  
2.  A bal oldali navigációs sávban kattintson a **Konfigurálás** elemre. 
3.  Megjelenik a **felügyeltszolgáltatás-identitás**. Az MSI regisztrálásához és engedélyezéséhez kattintson az **Igen**, a letiltásához a Nem gombra. 
4.  Mindenképp kattintson a **Mentés** gombra a konfiguráció mentéséhez.  
    ![Helyettesítő képszöveg](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Hozzáférés biztosítása a VM számára a Resource Managerben lévő erőforráscsoporthoz
Az MSI segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést.  Az Azure Resource Manager támogatja az Azure AD-hitelesítést.  Először hozzáférést kell adnunk a VM identitásának egy erőforráshoz a Resource Managerben, ebben az esetben ahhoz az erőforráscsoporthoz, amelyben a VM található.  

1.  Navigáljon az **Erőforráscsoportok** lapra. 
2.  Válassza ki a **Windows VM** számára létrehozott **erőforráscsoportot**. 
3.  Lépjen a **Hozzáférés-vezérlés (IAM)** részre a bal oldali panelen. 
4.  Ezután a **Hozzáadás** elemre kattintva adjon hozzá egy új szerepkör-hozzárendelést a **Windows VM-hez**.  A **Szerepkör** beállításhoz válassza ki az **Olvasó** értéket. 
5.  A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a **Virtuális gép** értéket. 
6.  Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben. Az **Erőforráscsoport** esetében válassza a **Minden erőforráscsoport** lehetőséget. 
7.  Végül a **Kiválasztás** mezőben válassza ki a Windows VM-et a legördülő menüben, majd kattintson a **Mentés** gombra.

    ![Helyettesítő képszöveg](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával 

Ebben a részben a **PowerShellt** kell használnia.  Ha nincs telepítve, [innen](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1) letöltheti. 

1.  A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre. 
2.  A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3.  Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben. 
4.  A PowerShell Invoke-WebRequest parancsával kezdeményezzen egy kérést a helyi MSI-végpont felé egy hozzáférési jogkivonat lekérésére az Azure Resource Managerhez.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > A „resource” paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    
    Ezután nyerje ki a teljes választ, amelyet egy JavaScript Object Notation (JSON) formátumú sztringként tárol a $response objektum. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Végül hívja meg az Azure Resource Managert a hozzáférési jogkivonattal. Ebben a példában a PowerShell Invoke-WebRequest paranccsal az Azure Resource Managert is meghívjuk, és a hozzáférési jogkivonatot az engedélyezési fejlécbe foglaljuk.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > Az URL megkülönbözteti a kis- és nagybetűket, ezért ellenőrizze, hogy pontosan ugyanúgy írja be a kis- és nagybetűket, mint az erőforráscsoport elnevezésekor, illetve hogy a „resourceGroups” kifejezésben nagy legyen a „G” betű.
        
    A következő parancs adja vissza az erőforráscsoport adatait:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogy hogyan hozhat létre felhasználóhoz hozzárendelt identitást, majd csatolhatja azt egy Azure-beli virtuális géphez az Azure Resource Manager API eléréséhez.  További információ az Azure Resource Managerről:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

