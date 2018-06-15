---
title: A Linux virtuális gép MSI Azure Key Vault eléréséhez használja
description: Ez az oktatóanyag végigvezeti az Azure Resource Manager eléréséhez használt egy Linux virtuális gép felügyelt szolgáltatás Identity (MSI).
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
ms.openlocfilehash: e42ed14b9c7c89972021a5422ebdfe415a70830d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29384701"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Az Azure Key Vault eléréséhez használja egy Linux virtuális gép felügyelt szolgáltatás Identity (MSI) 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag bemutatja, hogyan engedélyezése felügyelt szolgáltatás identitásának (MSI) a Linux virtuális gép, majd az Azure Key Vault eléréséhez használja az identitásukat. A rendszerindítási szolgál, Key Vault lehetővé teszi az ügyfélalkalmazás által Azure Active Directory (AD) nem védett erőforrások eléréséhez a titkos kulcsot használja. Felügyelt szolgáltatás-identitások Azure automatikusan kezeli, és lehetővé teszik, hogy az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat beszúrni a kódot támogató szolgáltatások hitelesítést. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Linux virtuális gépek MSI engedélyezése 
> * A virtuális gép hozzáférést biztosíthat a kulcstároló tárolt titkos kulcs 
> * Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és a titkos kulcs lekérése a Key Vault használatával 
 
## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Ebben az oktatóanyagban létrehozhatunk egy új Linux virtuális Gépet. A meglévő virtuális MSI is engedélyezheti.

1. Kattintson a **hozzon létre egy erőforrást** az Azure portál bal felső sarkában.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép meg a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Adja meg a virtuális gép számára. További méretek megtekintéséhez válasszon **összes** , vagy módosítsa a lemez típusát támogatott szűrő. A beállítások lapon hagyja az alapértelmezett beállításokat, és kattintson a **OK**.

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése

A virtuális gép MSI hozzáférési jogkivonatok beolvasása az Azure AD meg szeretne adni a kód hitelesítő adatokat igénylő nélkül teszi lehetővé. A színfalak MSI engedélyezése két dolgot eredményez: az MSI-Virtuálisgép-bővítmény a virtuális Gépet telepít, és MSI lehetővé teszi a virtuális gép számára.  

1. Válassza ki a **virtuális gép** , hogy szeretné-e engedélyezze MSI-t.
2. A bal oldali navigációs sávon kattintson **konfigurációs**.
3. Látni **Szolgáltatásidentitás felügyelt**. Regisztrálja, és engedélyezze a MSI-t, jelölje be **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha a rendszer mely bővítmények megtekintéséhez **Linux virtuális gép**, kattintson a **bővítmények**. Ha MSI engedélyezve van, a **ManagedIdentityExtensionforLinux** megtalálható a listán.

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>A virtuális gép hozzáférést biztosítson a kulcstároló tárolt titkos kulcs  

MSI-fájl használata a kódban való hitelesítésre szolgáló erőforrásokat, amelyek támogatják az Azure Active Directory-hitelesítés hozzáférési jogkivonatok kérheti le. Azonban nem minden Azure-szolgáltatásokat támogatja az Azure AD-alapú hitelesítés. MSI-fájl használata a szolgáltatások, a szolgáltatás hitelesítő adatok tárolása az Azure Key Vault, és MSI beolvasni a hitelesítő adatok a kulcstároló elérésére használhat. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és a titkos kulcs lekérése a Key Vault használatával  

Lépések elvégzéséhez szüksége van egy SSH-ügyfél.  Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. A portálon lépjen a Linux virtuális gépre, majd a a **áttekintése**, kattintson a **Connect**. 
2. **Csatlakozás** a virtuális géphez a az SSH-ügyfél az Ön által választott. 
3. A Terminálszolgáltatások ablakban használata CURL, indítson egy lekérdezést a helyi MSI-végpont Azure Key vault megszerezni az olyan hozzáférési jogkivonatot.  
 
    A CURL a hozzáférési token kérelme nem éri el.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    A válasz az erőforrás-kezelő eléréséhez szükséges jogkivonat tartalmazza. 
    
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
    
    Ez a jogkivonat segítségével az Azure Key Vaultba hitelesítéséhez.  A következő CURL-kérés megjeleníti a titkos kulcs beolvasása a Key Vault CURL és a Key Vault REST API használatával.  A kulcstároló, amely az URL-CÍMÉT kell a **Essentials** szakasza a **áttekintése** a Key Vault oldalán.  A hozzáférési jogkivonat szerezte be, ha a korábbi hívás is szüksége lesz. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A válasz fog kinézni: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Ha a titkos kulcsot a Key Vault korábban lekért, használhatja, hogy egy szolgáltatás, amely egy nevet és jelszót igényel hitelesítést.


## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.




