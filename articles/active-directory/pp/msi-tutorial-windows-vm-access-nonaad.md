---
title: Az Azure Key Vault eléréséhez használja a Windows virtuális gép MSI
description: Ez az oktatóanyag bemutatja, hogyan a folyamatot, amely egy Windows virtuális gép felügyelt szolgáltatás identitás (MSI) használata az Azure Key Vault eléréséhez.
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
ms.openlocfilehash: 151a0a1fee72c7bd7adcda8e23ebb06d96e822ae
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29383783"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Az Azure Key Vault eléréséhez használja a Windows virtuális gép felügyelt szolgáltatás identitásának (MSI) 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag bemutatja, hogyan engedélyezése felügyelt szolgáltatás identitásának (MSI) egy Windows rendszerű virtuális gép számára, majd az Azure Key Vault eléréséhez használja az identitásukat. A rendszerindítási szolgál, Key Vault lehetővé teszi az ügyfélalkalmazás által Azure Active Directory (AD) nem védett erőforrások eléréséhez a titkos kulcsot használja. Felügyelt szolgáltatás-identitások Azure automatikusan kezeli, és lehetővé teszik, hogy az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat beszúrni a kódot támogató szolgáltatások hitelesítést. 

Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Windows rendszerű virtuális gép identitásának felügyelt szolgáltatás engedélyezése 
> * A virtuális gép hozzáférést biztosíthat a kulcstároló tárolt titkos kulcs 
> * Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és a titkos kulcs lekérése a Key Vault használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban azt hozzon létre egy új Windows virtuális Gépet. A meglévő virtuális MSI is engedélyezheti.

1.  Kattintson a **hozzon létre egy erőforrást** az Azure portál bal felső sarkában.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gép.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép meg a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gépet **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése 

A virtuális gép MSI hozzáférési jogkivonatok beolvasása az Azure AD meg szeretne adni a kód hitelesítő adatokat igénylő nélkül teszi lehetővé. Egy felügyelt identitást a virtuális gép létrehozása Azure MSI engedélyezése jelzi. A színfalak MSI engedélyezése két dolgot eredményez: az MSI-Virtuálisgép-bővítmény a virtuális Gépet telepít, és lehetővé teszi, hogy az Azure Resource Manager MSI.

1.  Válassza ki a **virtuális gép** , hogy szeretné-e engedélyezze MSI-t.  
2.  A bal oldali navigációs sávon kattintson **konfigurációs**. 
3.  Látni **Szolgáltatásidentitás felügyelt**. Regisztrálja, és engedélyezze a MSI-t, jelölje be **Igen**, ha szeretné letiltani, válassza a nem. 
4.  Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, és a virtuális gép mely bővítmények ellenőrizze, kattintson a **bővítmények**. Ha MSI engedélyezve van, majd **ManagedIdentityExtensionforWindows** listájában jelenik meg.

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>A virtuális gép hozzáférést biztosítson a kulcstároló tárolt titkos kulcs 
 
MSI-fájl használata a kódot a hozzáférési jogkivonatok erőforrásokat, amelyek támogatják az Azure AD-alapú hitelesítés felé történő hitelesítésre kérheti le.  Azonban nem minden Azure-szolgáltatásokat támogatja az Azure AD-alapú hitelesítés. MSI-fájl használata a szolgáltatások, a szolgáltatás hitelesítő adatok tárolása az Azure Key Vault, és MSI beolvasni a hitelesítő adatok a kulcstároló elérésére használhat. 

Először igazolnia kell a kulcstároló létrehozásához, és a virtuális gép identitásának hozzáférést biztosítson a Key Vault.   

1. Válassza ki a bal oldali navigációs sáv felső **+ új** majd **biztonság + identitás szakaszában** majd **Key Vault**.  
2. Adjon meg egy **neve** az új Key vault. 
3. Keresse meg a Key Vault azonos előfizetésbe és erőforráscsoportba tartozik, mint a korábban létrehozott virtuális Gépet. 
4. Válassza ki **hozzáférési házirendek** kattintson **új hozzáadása**. 
5. A konfigurálás a sablon alapján, válassza ki **titkos felügyeleti**. 
6. Válasszon **egyszerű válasszon**, és a keresési mezőbe írja be a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális gép az eredménylista **válasszon**. 
7. Kattintson a **OK** a befejezési hozzáadása az új házirend, és **OK** hozzáférési házirend kijelölés befejezéséhez. 
8. Kattintson a **létrehozása** a Key Vault létrehozásának befejezéséhez. 

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Ezt követően adja hozzá titkos kulcs a Key Vault, hogy később kérheti le a titkos kulcsot, a virtuális gépen futó kód használatával: 

1. Válassza ki **összes erőforrás**, és keresse meg és jelölje ki a létrehozott kulcstároló. 
2. Válassza ki **titkok**, és kattintson a **Hozzáadás**. 
3. Válassza ki **manuális**, a **feltöltési beállításokat**. 
4. Adja meg egy nevet és értéket a titkos kulcsot.  Az érték lehet bármilyen. 
5. Hagyja meg az aktiválási dátuma és a lejárati dátum törölje a jelet, és hagyja **engedélyezve** , **Igen**. 
6. Kattintson a **létrehozása** a titkos kulcs létrehozásához. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és a titkos kulcs lekérése a Key Vault használatával  

Ha nincs PowerShell 4.3.1 vagy újabb verziójának telepítve, kell [töltse le és telepítse a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

A virtuális gép MSI először szereznie egy hozzáférési jogkivonatot a Key Vault hitelesítésére használjuk:
 
1. A portálon lépjen a **virtuális gépek** és nyissa meg a Windows rendszerű virtuális gép és a a **áttekintése**, kattintson a **Connect**.
2. Adja meg a **felhasználónév** és **jelszó** esetében, amelyek hozzáadott létrehozásakor a **Windows virtuális gép**.  
3. Most, hogy létrehozott egy **távoli asztali kapcsolat** a virtuális géppel, nyissa meg a Powershellt a távoli munkamenet.  
4. A PowerShellben hívja meg a webes kérelem a tenant lekérdezni a jogkivonatot a helyi állomás meghatározott portot a virtuális gép.  

    A PowerShell-kérelem:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Ezután bontsa ki a teljes válasz, amely tárolja a $response objektum JavaScript Object Notation (JSON) formátumú karakterláncot.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Ezután bontsa ki a hozzáférési jogkivonat a választ.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Végezetül paranccsal PowerShell Invoke-WebRequest beolvasni a titkos kulcsot a kulcstároló, a hozzáférési jogkivonat hitelesítési fejlécéhez benyújtása korábban létrehozott.  A kulcstároló, amely az URL-CÍMÉT kell a **Essentials** szakasza a **áttekintése** a Key Vault oldalán.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A válasz fog kinézni: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Ha a titkos kulcsot a Key Vault korábban lekért, használhatja, hogy egy szolgáltatás, amely egy nevet és jelszót igényel hitelesítést. 

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
