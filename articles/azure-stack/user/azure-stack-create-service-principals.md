---
title: Hozzon létre egy egyszerű Azure verem |} Microsoft Docs
description: Hozzon létre egy új szolgáltatás egyszerű erőforrásokhoz való hozzáférés kezelésére használható a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager ismerteti.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 6b53e46253edc063b1b7ae94bf2fac2781a0613a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="provide-applications-access-to-azure-stack"></a>Alkalmazások elérést biztosíthat az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ha egy alkalmazás központi telepítése, illetve az Azure Resource Manageren keresztül erőforrások konfigurálása az Azure-verem hozzáférést kér, hozzon létre egy egyszerű szolgáltatás, amely az alkalmazás hitelesítő adatokat.  Majd delegálhatja, hogy a szolgáltatás egyszerű csak a szükséges engedélyekkel.  

Például előfordulhat, hogy a konfigurációs felügyeleti eszközt, amely Azure Resource Manager leltárt készíteni az Azure-erőforrások.  Ebben a forgatókönyvben egy szolgáltatásnevet létrehozni, az olvasó szerepkört biztosítása, hogy a szolgáltatás egyszerű, és korlátozza a konfigurációs felügyeleti eszköz csak olvasási hozzáféréssel. 

Szolgáltatásnevekről előnyösebb fut az alkalmazás a saját hitelesítő adatok, mert:

* Engedélyeket rendelhet az egyszerű szolgáltatásnév, amelyek eltérnek a saját fiók engedélyeit. Ezek az engedélyek általában korlátozódik, hogy mit az alkalmazás kell tennie.
* Nem kell módosítani az alkalmazás hitelesítő adatokat, ha az Ön feladatkörei módosítása.
* Tanúsítvány segítségével automatizálhatja a hitelesítést egy felügyelet nélküli parancsfájl végrehajtása közben.  

## <a name="getting-started"></a>Első lépések

Attól függően, hogy hogyan telepített Azure verem akkor először hozzon létre egy szolgáltatás egyszerű.  Ez a dokumentum végigvezeti az egyszerű szolgáltatás létrehozása [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) és [Active Directory összevonási Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Ha létrehozta az egyszerű szolgáltatás, az AD FS és az Azure Active Directory közös ismertetett lépések használt [engedélyek delegálása](azure-stack-create-service-principals.md#assign-role-to-service-principal) a szerepkörhöz.     

## <a name="create-service-principal-for-azure-ad"></a>Egyszerű szolgáltatásnév létrehozása az Azure AD

Ha az Azure AD használva identity Azure verem telepítése után, szolgáltatásnevekről ugyanúgy, mint az Azure hozhat létre.  Ez a szakasz bemutatja, hogyan hajtsa végre a lépéseket a portálon keresztül.  Ellenőrizze, hogy rendelkezik a [szükséges engedélyek az Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) megkezdése előtt.

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása
Ebben a szakaszban az Azure ad-ben, amely képviseli az alkalmazást egy alkalmazás (egyszerű szolgáltatásnév) létrehozása.

1. Az Azure-fiók használatával jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **App regisztrációk** > **hozzáadása**   
3. Adja meg az alkalmazás nevét és URL-címét. Válassza ki vagy **Web app / API** vagy **natív** a létrehozandó alkalmazás típusától. Miután beállította az értékeket, válassza ki a **létrehozása**.

Az alkalmazás hozott létre egy egyszerű szolgáltatást.

### <a name="get-credentials"></a>Hitelesítő adatainak lekérése
Bejelentkezéskor programozott módon, a Azonosítóját használnia az alkalmazás és egy hitelesítési kulcs. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. A **App regisztrációk** az Active Directoryban, válassza ki az alkalmazást.

2. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. Az alkalmazások a [mintaalkalmazást](#sample-applications) szakasz tekintse meg ezt az értéket az ügyfél-azonosító.

     ![ügyfél-azonosító](./media/azure-stack-create-service-principal/image12.png)
3. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

4. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, mivel később nem lesz lehetősége lekérni a kulcsot. A kulcs értéke megegyezik az alkalmazáséval aláírásához az alkalmazás azonosítójával adja meg. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

![mentett kulcs](./media/azure-stack-create-service-principal/image15.png)


Művelet befejeződése után folytassa a [az alkalmazás-szerepkör hozzárendelése](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Az AD FS egyszerű szolgáltatás létrehozása
Ha telepítette az AD FS Azure verem, PowerShell segítségével hozzon létre egy egyszerű szolgáltatást, rendeljen hozzá egy szerepkört a hozzáféréshez és jelentkezzen be a Powershellből ezzel az identitással.

### <a name="before-you-begin"></a>Előkészületek

[Töltse le a helyi számítógépen az Azure veremnek megfelelő működéséhez szükséges eszközt.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Az Identity PowerShell modul importálása
Miután letöltötte az eszközök, keresse meg a letöltött mappát, és az Identity PowerShell modul importálása a következő paranccsal:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Amikor importálja a modult, előfordulhat, hogy hibaüzenet, amely szerint a "AzureStack.Connect.psm1 kabinetfájl nincs digitálisan aláírva. A parancsfájl nem futtatja a rendszer". A probléma megoldásához, beállíthatja a végrehajtási házirend engedélyezi a parancsprogram futtatása a következő paranccsal egy rendszergazda jogú PowerShell-munkamenetben:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>A szolgáltatásnév létrehozása
Létrehozhat egy egyszerű szolgáltatás hajtja végre a következő parancsot, meggyőződött arról, hogy frissíteni a *DisplayName* paraméter:
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>A szerepkör hozzárendelése
Az egyszerű szolgáltatás létrehozása után kell [hozzárendelése szerepkörhöz](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Jelentkezzen be a PowerShell segítségével
Ha egy szerepkörhöz hozzárendelt, bármikor beléphet Azure veremnek az egyszerű szolgáltatás használata a következő parancsot:

```powershell
Connect-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Szolgáltatás egyszerű szerepkör hozzárendelése
Az előfizetés az erőforrások eléréséhez az alkalmazást egy szerepkörhöz kell rendelni. Döntse el, melyik szerepkört jelöli a megfelelő engedélyekkel az alkalmazáshoz. A rendelkezésre álló szerepkörökkel kapcsolatos további tudnivalókért lásd: [RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md).

A hatókör szintjén található az előfizetés, erőforráscsoportból vagy erőforrás állíthatja be. Engedélyek hatókör alacsonyabb szintre származnak. Például egy alkalmazást az olvasó szerepkört erőforráscsoport hozzáadása azt jelenti, hogy ez az erőforráscsoport és a benne található erőforrásokat tudja olvasni.

1. A verem Azure portálon lépjen a szintre hatókör szeretne hozzárendelni az alkalmazást. Például egy szerepkört az előfizetés hatókörből rendeléséhez jelölje **előfizetések**. Ehelyett kiválaszthatja egy erőforráscsoport vagy az erőforrás.

2. Válassza az adott előfizetés (erőforráscsoport vagy erőforrás) az alkalmazást.

     ![Válassza ki az előfizetést a hozzárendeléshez](./media/azure-stack-create-service-principal/image16.png)

3. Válassza ki **hozzáférés-vezérlés (IAM)**.

     ![Jelölje be a hozzáférés](./media/azure-stack-create-service-principal/image17.png)

4. Válassza a **Hozzáadás** lehetőséget.

5. Jelölje ki az alkalmazáshoz hozzárendelni kívánt szerepkört.

6. Keresse meg az alkalmazást, és válassza ki azt.

7. Válassza ki **OK** a szerepkör hozzárendelése befejezéséhez. Megjelenik a listában, hogy a hatókör adott szerepkörhöz rendelt felhasználók az alkalmazást.

Most, hogy létrehozni egy egyszerű szolgáltatást és szerepkört rendeltek, használatának megkezdése Ez az alkalmazáson belül Azure verem erőforrások eléréséhez.  

## <a name="next-steps"></a>További lépések

[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)
