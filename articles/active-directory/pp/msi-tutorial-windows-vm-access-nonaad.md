---
title: Windows virtuális gép MSI használata az Azure Key Vault elérése
description: Ez az oktatóanyag végigvezeti a folyamatot, egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használatával az Azure Key Vault eléréséhez.
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
ms.openlocfilehash: b3d334edd770ac381a7e0ae6aaa1a9db8c91b961
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002939"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata az Azure Key Vault eléréséhez 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan lehet engedélyezni a Felügyeltszolgáltatás-identitás (MSI) egy Windows virtuális gép esetében, akkor az Azure Key Vault eléréséhez használja az identitásukat. A rendszerindítási szolgáló, a Key Vault lehetővé teszi az ügyfélalkalmazás által az Azure Active Directory (AD) nem védett erőforrások eléréséhez a titkos kulcsot használja. Felügyelt Szolgáltatásidentitások Azure automatikusan kezeli, és lehetővé teszi szolgáltatások, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy a hitelesítő adatok beszúrása a kódot a hitelesítéshez. 

Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * A Windows virtuális gépek Felügyeltszolgáltatás-identitás engedélyezése 
> * A virtuális gép hozzáférést biztosítani a Key Vaultban tárolt titkos kulcs 
> * A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon, és a titkos kulcs lekérése a Key Vault használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban létrehozunk egy új Windows virtuális Gépet. A meglévő virtuális gép MSI is engedélyezheti.

1.  Kattintson a **erőforrás létrehozása** a az Azure portal bal felső sarkában.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gépet.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez 

A virtuális gép MSI lehetővé teszi hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. MSI engedélyezéséhez jelzi az Azure a virtuális gép létrehozása egy felügyelt identitás. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepíti, és lehetővé teszi, hogy az Azure Resource Manager MSI.

1.  Válassza ki a **virtuális gép** , hogy szeretné-e az MSI engedélyezéséhez.  
2.  A bal oldali navigációs sávon kattintson **konfigurációs**. 
3.  Látja **Felügyeltszolgáltatás-identitás**. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem. 
4.  Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, és a virtuális gépen ellenőrizze, hogy mely bővítmények, kattintson a **bővítmények**. Ha az MSI engedélyezve van, majd **ManagedIdentityExtensionforWindows** megjelenik a listában.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>A virtuális gép hozzáférést adni a Key Vaultban tárolt titkos kulcs 
 
MSI használatával a kód kérheti le jogkivonatot az Azure AD-hitelesítést támogató erőforrásokhoz történő hitelesítéshez.  Azonban nem minden Azure-szolgáltatások támogatják az Azure AD-hitelesítés. Az MSI használata ezeket a szolgáltatásokat, a szolgáltatás hitelesítő adatok tárolása az Azure Key Vaultban, és MSI használata a Key Vault beolvasni a hitelesítő adatok eléréséhez. 

Először létre kell hozzon létre egy Key Vaultot és a virtuális gépnél identitásos hozzáférést biztosítani a Key Vault.   

1. Válassza a bal oldali navigációs sáv tetején **+ új** majd **biztonság + identitás** majd **Key Vault**.  
2. Adjon meg egy **neve** az új Key vault. 
3. Keresse meg a Key Vault azonos előfizetésben és erőforráscsoportban tartozik, mint a korábban létrehozott virtuális Gépet. 
4. Válassza ki **hozzáférési házirendek** kattintson **új hozzáadása**. 
5. A konfigurálás sablonból, jelölje ki **titkos felügyeleti**. 
6. Válasszon **rendszerbiztonsági tag kijelölése**, és a keresőmezőbe írja be a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális Gépet a találatok listájában, és kattintson a **kiválasztása**. 
7. Kattintson a **OK** való befejezése az új hozzáférési szabályzat hozzáadása és **OK** hozzáférési szabályzat választását befejezéséhez. 
8. Kattintson a **létrehozás** a Key Vault létrehozásának befejezéséhez. 

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Ezután adjon hozzá egy titkos kulcsot a Key vaulthoz, hogy később kérheti le a titkos kulcsot, a virtuális gépen futó kód használatával: 

1. Válassza ki **összes erőforrás**, és keresse meg és válassza ki a létrehozott Key Vault. 
2. Válassza ki **titkok**, és kattintson a **Hozzáadás**. 
3. Válassza ki **manuális**, a **feltöltési beállítások**. 
4. Adja meg a titkos kód nevét és értékét.  Az érték lehet bármit. 
5. Hagyja meg az aktiválási dátumot és a lejárati dátum törölje, és hagyja **engedélyezve** , **Igen**. 
6. Kattintson a **létrehozás** a titkos kulcs létrehozása. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon, és a titkos kulcs lekérése a Key Vault használatával  

Ha nincs PowerShell 4.3.1 vagy nagyobb telepített, kell [töltse le és telepítse a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

A virtuális gép MSI először hitelesíteni a Key Vault hozzáférési jogkivonatot kapjon használjuk:
 
1. A portálon lépjen a **virtuális gépek** , és nyissa meg a Windows virtuális gép és a a **áttekintése**, kattintson a **Connect**.
2. Adja meg a **felhasználónév** és **jelszó** számára, amely hozzáadott létrehozásakor a **Windows virtuális gép**.  
3. Most, hogy létrehozott egy **távoli asztali kapcsolat** a virtuális géphez, nyissa meg a Powershellt a távoli munkamenet.  
4. A PowerShell hívja meg a webes kérelmet a bérlő úgy szerezheti be a jogkivonatot a helyi gazdagép a meghatározott a virtuális gép számára.  

    A PowerShell-kérelem:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Ezután bontsa ki a teljes válasz egy JavaScript Object Notation (JSON) formátumú karakterláncot a $response objektumban tárolja.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Ezután bontsa ki a hozzáférési jogkivonatot a válaszból.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Végül PowerShell-paranccsal az Invoke-WebRequest beolvasni a korábban létrehozott ad át a hozzáférési jogkivonatot az engedélyezési fejléc a Key Vault titkos kulcsot.  A Key vault, amely az URL-cím lesz szüksége a **Essentials** szakaszában a **áttekintése** a Key Vault lapján.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A válasz fog kinézni: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Miután a titkos kulcsot a Key Vault már lekért, használhatja hitelesíthetik magukat egy szolgáltatásban, amely egy nevet és jelszót igényel. 

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
