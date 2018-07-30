---
title: Az Azure Key Vault elérése Windows VM-beli felügyeltszolgáltatás-identitással
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure Key Vaulthoz egy Windows VM-beli felügyeltszolgáltatás-identitással.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 81bab96b91bb71a91ea0b6046b16ef86c8d27061
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248061"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-key-vault"></a>Oktatóanyag: Az Azure Key Vault elérése Windows VM-beli felügyeltszolgáltatás-identitással 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan engedélyezheti a felügyeltszolgáltatás-identitást egy Windows rendszerű virtuális gépen, majd hogyan használhatja az identitást az Azure Key Vault hozzáféréséhez. A rendszerindítóként szolgáló Key Vault segítségével az ügyfélalkalmazás ezután a titkos kódot használhatja a nem az Azure Active Directory (AD) által védett erőforrások eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. 

Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Felügyeltszolgáltatás-identitás engedélyezése Windows rendszerű virtuális gépen 
> * Hozzáférés engedélyezése a VM számára a Key Vaultban tárolt titkos kódokhoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a titkos kód lekérése a Key Vaultból 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Egy Windows rendszerű virtuális gép létrehozása egy új erőforráscsoportban

Ebben az oktatóanyagban egy új Windows VM-et fog létrehozni. A meglévő virtuális gépeken is engedélyezheti a felügyeltszolgáltatás-identitást.

1.  Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. Az itt létrehozott **felhasználónév** és **jelszó** alkotják a virtuális gépre való bejelentkezéshez használt hitelesítő adatokat.
4.  Válassza ki a megfelelő **előfizetést** a virtuális géphez a legördülő menüben.
5.  Ha a virtuális gépet egy új **erőforráscsoportban** szeretné létrehozni, válassza az **Új létrehozása** elemet. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a virtuális gép méretét. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Helyettesítő képszöveg](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>Felügyeltszolgáltatás-identitás engedélyezése a virtuális gépen 

A virtuális gép felügyeltszolgáltatás-identitásával anélkül kérhet le hozzáférési jogkivonatokat az Azure AD-ből, hogy hitelesítő adatokat kellene a kódba illesztenie. A felügyeltszolgáltatás-identitás engedélyezése arra utasítja az Azure-t, hogy hozzon létre egy felügyelt identitást a virtuális géphez. A háttérben a felügyeltszolgáltatás-identitás engedélyezésének két következménye van: regisztrálja a virtuális gépet az Azure Active Directoryban a felügyelt identitása létrehozásához, és konfigurálja az identitást a virtuális gépen.

1.  Válassza ki azt a **virtuális gépet**, amelyen engedélyezni szeretné a felügyeltszolgáltatás-identitást.  
2.  A bal oldali navigációs sávban kattintson a **Konfigurálás** elemre. 
3.  Megjelenik a **felügyeltszolgáltatás-identitás**. A felügyeltszolgáltatás-identitás regisztrálásához és engedélyezéséhez kattintson az **Igen**, a letiltásához a Nem gombra. 
4.  Mindenképp kattintson a **Mentés** gombra a konfiguráció mentéséhez.  

    ![Helyettesítő képszöveg](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Hozzáférés engedélyezése a VM számára a Key Vaultban tárolt titkos kódokhoz 
 
A felügyeltszolgáltatás-identitás segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést.  Az Azure AD-hitelesítést azonban nem minden Azure-szolgáltatás támogatja. A felügyeltszolgáltatás-identitás ilyen szolgáltatásokkal való használatához tárolja el a szolgáltatás hitelesítő adatait az Azure Key Vaultban, és a felügyeltszolgáltatás-identitással férjen hozzá a Key Vaulthoz, hogy le tudja kérni a hitelesítő adatokat. 

Először létre kell hozni egy Key Vaultot, és gondoskodni kell róla, hogy a VM-identitás hozzá tudjon férni.   

1. A bal oldali navigációs sáv tetején válassza az **Erőforrás létrehozása** > **Biztonság + identitás** > **Key Vault** lehetőséget.  
2. Adja meg az új Key Vault **nevét**. 
3. A Key Vaultot ugyanabban az előfizetésben és erőforráscsoportban hozza létre, mint a korábban létrehozott virtuális gépet. 
4. Válassza a **Hozzáférési szabályzatok** lehetőséget, és kattintson az **Új hozzáadása** gombra. 
5. A Konfigurálás sablonból mezőben válassza a **Titkos kódok kezelése** sablont. 
6. Válassza a **Rendszerbiztonsági tag kijelölése** lehetőséget, és a keresőmezőben adja meg a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális gépet az eredménylistából, és kattintson a **Kiválasztás** gombra. 
7. Az új hozzáférési szabályzat hozzáadásának befejezéshez kattintson az **OK**, majd a hozzáférési szabályzat kiválasztásának befejezéséhez ugyanúgy az **OK** gombra. 
8. Kattintson a **Létrehozás** gombra a Key Vault létrehozásának befejezéséhez. 

    ![Helyettesítő képszöveg](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Ezután adjon hozzá egy titkos kódot a Key Vaulthoz, hogy később le tudja kérni a VM-ben futó titkos kódot: 

1. Válassza a **Minden erőforrás** lehetőséget, majd keresse meg és válassza ki a létrehozott Key Vaultot. 
2. Válassza a **Titkos kódok** lehetőséget, és kattintson a **Hozzáadás** gombra. 
3. A **Feltöltési beállítások** mezőben válassza a **Manuális** lehetőséget. 
4. Adja meg a titkos kód nevét és értékét.  Az érték bármi lehet. 
5. Hagyja az aktiválási és a lejárati dátumot üresen, az **Engedélyezve** beállítást pedig az **Igen** értéken. 
6. A titkos kód létrehozásához kattintson a **Létrehozás** parancsra. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd a titkos kód lekérése a Key Vaultból  

Ha nincs telepítve a PowerShell 4.3.1-es vagy újabb verziója, [le kell töltenie és telepítenie kell a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

Először a VM felügyeltszolgáltatás-identitásával szerezzen be egy hozzáférési jogkivonatot a Key Vaulton végzett hitelesítéshez:
 
1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a **Windows VM** létrehozásakor adott meg.  
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.  
4. A PowerShellben hívjon meg egy webes kérést a bérlőn, amellyel lekéri a jogkivonatot a helyi gazdagéphez a virtuális gép adott portján.  

    A PowerShell-kérés:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Ezután nyerje ki a teljes választ, amelyet egy JavaScript Object Notation (JSON) formátumú sztringként tárol a $response objektum.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Végül a Powershell Invoke-WebRequest parancsával kérje le a Key Vaultban korábban létrehozott titkos kódot úgy, hogy a hozzáférési jogkivonatot az engedélyezési fejlécbe foglalja.  Szüksége lesz a Key Vault URL-címére, amely a Key Vault **Áttekintés** lapjának **Alapvető erőforrások** szakaszában található.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A válasz a következőképp fog kinézni: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Miután lekérte a titkos kódot a Key Vaultból, a használatával hitelesítést végezhet olyan szolgáltatásokban, amelyekhez név és jelszó szükséges. 

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan hozhat létre felügyeltszolgáltatás-identitásokat az Azure Key Vault eléréséhez.  További információ az Azure Key Vault szolgáltatásról:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-whatis)
