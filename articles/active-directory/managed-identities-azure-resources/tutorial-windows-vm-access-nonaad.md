---
title: Oktatóanyag`:` felügyelt identitás használata a Azure Key Vault eléréséhez – Windows-Azure AD
description: Az oktatóanyag azt ismerteti, hogyan férhet hozzá az Azure Key Vaulthoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd9f85e3bfd11ee655ce581c60a5b65e13f4497b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75971892"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Oktatóanyag: Hozzáférés az Azure Key Vaulthoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá az Azure Key Vaulthoz egy Windows rendszerű virtuális gép (VM) rendszer által hozzárendelt identitásával. A rendszerindítóként szolgáló Key Vault segítségével az ügyfélalkalmazás ezután a titkos kódot használhatja a nem az Azure Active Directory (AD) által védett erőforrások eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. 

Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Hozzáférés engedélyezése a VM számára a Key Vaultban tárolt titkos kódokhoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a titkos kód lekérése a Key Vaultból 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Engedélyezés

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Hozzáférés biztosítása  
 
Ez a szakasz bemutatja, hogyan biztosítható a virtuális gép hozzáférése egy Key Vault tárolt titkos kulcshoz. Az Azure-erőforrások felügyelt identitásainak segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést.  Azonban nem minden Azure-szolgáltatás támogatja az Azure AD-hitelesítést. Ha az Azure-erőforrások felügyelt identitásait szeretné használni ezekkel a szolgáltatásokkal, tárolja Azure Key Vault a szolgáltatás hitelesítő adatait, és a virtuális gép felügyelt identitásával férhet hozzá az Key Vaulthoz a hitelesítő adatok lekéréséhez. 

Először létre kell hozni egy Key Vaultot, és gondoskodni kell róla, hogy a VM rendszer által hozzárendelt felügyelt identitása hozzá tudjon férni.   

1. A bal oldali navigációs sáv tetején válassza az **Erőforrás létrehozása** > **Biztonság + identitás** > **Key Vault** lehetőséget.  
2. Adja meg az új Key Vault **nevét**. 
3. A Key Vaultot ugyanabban az előfizetésben és erőforráscsoportban hozza létre, mint a korábban létrehozott virtuális gépet. 
4. Válassza a **Hozzáférési szabályzatok** lehetőséget, és kattintson az **Új hozzáadása** gombra. 
5. A Konfigurálás sablonból mezőben válassza a **Titkos kódok kezelése** sablont. 
6. Válassza a **Rendszerbiztonsági tag kijelölése** lehetőséget, és a keresőmezőben adja meg a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális gépet az eredmények listájában, és kattintson a **kiválasztás**elemre. 
7. Az új hozzáférési szabályzat hozzáadásának befejezéshez kattintson az **OK**, majd a hozzáférési szabályzat kiválasztásának befejezéséhez ugyanúgy az **OK** gombra. 
8. Kattintson a **Létrehozás** gombra a Key Vault létrehozásának befejezéséhez. 

    ![Helyettesítő képszöveg](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Ezután adjon hozzá egy titkos kódot a Key Vaulthoz, hogy később le tudja kérni a VM-ben futó titkos kódot: 

1. Válassza a **Minden erőforrás** lehetőséget, majd keresse meg és válassza ki a létrehozott Key Vaultot. 
2. Válassza a **Titkos kódok** lehetőséget, és kattintson a **Hozzáadás** gombra. 
3. A **Feltöltési beállítások** mezőben válassza a **Manuális** lehetőséget. 
4. Adja meg a titkos kód nevét és értékét.  Az érték bármi lehet, amit szeretne. 
5. Hagyja az aktiválási és a lejárati dátumot üresen, az **Engedélyezve** beállítást pedig az **Igen** értéken. 
6. A titkos kód létrehozásához kattintson a **Létrehozás** parancsra. 
 
## <a name="access-data"></a>Adatok elérése  

Ez a szakasz bemutatja, hogyan kérhet hozzáférési tokent a virtuális gép identitásával, és hogyan kérheti le a titkos kulcsot a Key Vault. Ha nincs telepítve a PowerShell 4.3.1-es vagy újabb verziója, [le kell töltenie és telepítenie kell a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

Először a VM rendszer által hozzárendelt felügyelt identitásával szerezzen be egy hozzáférési jogkivonatot a Key Vaulton végzett hitelesítéshez:
 
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
    
    Végül a Powershell Invoke-WebRequest parancsával kérje le a Key Vaultban korábban létrehozott titkos kódot úgy, hogy a hozzáférési jogkivonatot az engedélyezési fejlécbe foglalja.  Szüksége lesz a Key Vault URL-címére, amely a Key Vault **Áttekintés** lapjának **Essentials (alapvető** erőforrások) szakaszában található.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A válasz a következőképp fog kinézni: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Miután lekérte a titkos kódot a Key Vaultból, a használatával hitelesítést végezhet olyan szolgáltatásokban, amelyekhez név és jelszó szükséges. 


## <a name="disable"></a>Letiltás

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan lehet hozzáférni az Azure Key Vaulthoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással.  További információ az Azure Key Vault szolgáltatásról:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-overview)
