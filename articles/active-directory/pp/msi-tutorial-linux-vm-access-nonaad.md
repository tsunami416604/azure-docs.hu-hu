---
title: Linux virtuális gép MSI használata az Azure Key Vault elérése
description: Ez az oktatóanyag végigvezeti a folyamat használatával egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) Azure Resource Manager eléréséhez.
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
ms.openlocfilehash: 95a9530c02bbf7b1cd9d137129f96ff4ee016966
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007665"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata az Azure Key Vault eléréséhez 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan lehet engedélyezni a Felügyeltszolgáltatás-identitás (MSI) egy Linux rendszerű virtuális gép esetében, akkor az identitásukat használata az Azure Key Vault eléréséhez. A rendszerindítási szolgáló, a Key Vault lehetővé teszi az ügyfélalkalmazás által az Azure Active Directory (AD) nem védett erőforrások eléréséhez a titkos kulcsot használja. Felügyelt Szolgáltatásidentitások Azure automatikusan kezeli, és lehetővé teszi szolgáltatások, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy a hitelesítő adatok beszúrása a kódot a hitelesítéshez. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Linux rendszerű virtuális gépen az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést biztosítani a Key Vaultban tárolt titkos kulcs 
> * A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon, és a titkos kulcs lekérése a Key Vault használatával 
 
## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása az új erőforráscsoport

Ebben az oktatóanyagban létrehozunk egy új Linux rendszerű virtuális Gépet. A meglévő virtuális gép MSI is engedélyezheti.

1. Kattintson a **erőforrás létrehozása** a az Azure portal bal felső sarkában.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**válassza **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik jelentkezzen be a virtuális Gépre.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza ki a **az összes megtekintése** vagy a lemez típusát támogatott szűrő módosítása. A beállítások oldalon hagyja változatlanul az alapértelmezett beállításokat, és kattintson a **OK**.

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez

A virtuális gép MSI lehetővé teszi hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepítése és a virtuális gép MSI lehetővé teszi.  

1. Válassza ki a **virtuális gép** , hogy szeretné-e az MSI engedélyezéséhez.
2. A bal oldali navigációs sávon kattintson **konfigurációs**.
3. Látja **Felügyeltszolgáltatás-identitás**. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, hogy a rendszer milyen kiterjesztésű **Linux rendszerű virtuális gép**, kattintson a **bővítmények**. Ha az MSI engedélyezve van, a **ManagedIdentityExtensionforLinux** megjelenik a listán.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>A virtuális gép hozzáférést adni a Key Vaultban tárolt titkos kulcs  

MSI használatával a kód kérheti le jogkivonatot az Azure Active Directory-hitelesítést támogató erőforrásokhoz történő hitelesítéshez. Azonban nem minden Azure-szolgáltatások támogatják az Azure AD-hitelesítés. Az MSI használata ezeket a szolgáltatásokat, a szolgáltatás hitelesítő adatok tárolása az Azure Key Vaultban, és MSI használata a Key Vault beolvasni a hitelesítő adatok eléréséhez. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>A virtuális gép identitással hozzáférési jogkivonatot kapjon, és a titkos kulcs lekérése a Key Vault használatával  

A lépések elvégzéséhez szüksége van egy SSH-ügyfél.  Ha Windows használ, használhatja az SSH-ügyfél a a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [használata SSH-kulcsok az Azure-ban a Windows hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása és a nyilvános és titkos ssh-kulcs használata Linux rendszerű virtuális gépekhez az Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. A portálon lépjen a Linux rendszerű virtuális gépre, majd a a **áttekintése**, kattintson a **Connect**. 
2. **Csatlakozás** az SSH-ügyféllel a választott a virtuális géphez. 
3. A terminálablakban a CURL, használatával indítson egy a helyi MSI-végpontot a hozzáférési jogkivonat beszerzése az Azure Key Vault.  
 
    A CURL kérés esetében a hozzáférési jogkivonat nem éri el.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    A válasz tartalmazza a Resource Manager eléréséhez szükséges hozzáférési jogkivonat. 
    
    Válasz:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Ez a jogkivonat segítségével hitelesíti az Azure Key Vaultba.  A következő CURL-kérelmet bemutatja, hogyan titkos Key Vault kulcsainak olvasásához a CURL és a Key Vault REST API használatával.  A Key vault, amely az URL-cím lesz szüksége a **Essentials** szakaszában a **áttekintése** a Key Vault lapján.  Emellett az előző hívás a kapott hozzáférési jogkivonat. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A válasz fog kinézni: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Miután a titkos kulcsot a Key Vault már lekért, használhatja hitelesíthetik magukat egy szolgáltatásban, amely egy nevet és jelszót igényel.


## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.




