---
title: "Hozzon létre egy egyszerű Azure verem |} Microsoft Docs"
description: "Hozzon létre egy új szolgáltatás egyszerű erőforrásokhoz való hozzáférés kezelésére használható a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager ismerteti."
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
ms.date: 02/25/2018
ms.author: mabrigg
ms.openlocfilehash: 64c424ee7045ae20b3fba6433166039580387d76
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
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

Ha az Azure AD használva identity Azure verem telepítése után, szolgáltatásnevekről ugyanúgy, mint az Azure hozhat létre.  Ez a szakasz bemutatja, hogyan hajtsa végre a lépéseket a portálon keresztül.  Ellenőrizze, hogy rendelkezik a [szükséges engedélyek az Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) megkezdése előtt.

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása
Ebben a szakaszban hoz létre egy alkalmazást (egyszerű szolgáltatásnév) az Azure ad-ben, amely jelöli az alkalmazás.

1. Az Azure-fiók használatával jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **App regisztrációk** > **hozzáadása**   
3. Adjon meg egy nevet és egy URL-címet az alkalmazáshoz. Válassza ki vagy **Web app / API** vagy **natív** a létrehozandó alkalmazás típusától. Miután beállította az értékeket, válassza ki a **létrehozása**.

Az alkalmazás hozott létre egy egyszerű szolgáltatást.

### <a name="get-credentials"></a>Hitelesítő adatainak lekérése
Bejelentkezéskor programozott módon, a Azonosítóját használnia az alkalmazás és egy hitelesítési kulcs. Ahhoz, hogy ezeket az értékeket, tegye a következőket:

1. A **App regisztrációk** az Active Directoryban, válassza ki az alkalmazást.

2. Másolás a **Alkalmazásazonosító** és az alkalmazás kódjában tárolja. Az alkalmazások a [mintaalkalmazást](#sample-applications) szakasz tekintse meg ezt az értéket az ügyfél-azonosítót.

     ![ügyfél-azonosító](./media/azure-stack-create-service-principal/image12.png)
3. A hitelesítési kulcs létrehozásához válasszon **kulcsok**.

4. Adja meg a kulcsot, és egy időtartamot a kulcs leírását. Ha elkészült, válassza ki a **mentése**.

A kulcs mentése után a kulcsnak az értéke megjelenik. Másolja ezt az értéket, mert nem sikerült beolvasni a a kulcs később. A kulcs értéke megegyezik az alkalmazáséval aláírásához az alkalmazás azonosítójával adja meg. Tárolja a kulcs értékét, ahol az alkalmazás kérheti le.

![kulcs mentése](./media/azure-stack-create-service-principal/image15.png)


Művelet befejeződése után folytassa a [az alkalmazás-szerepkör hozzárendelése](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Az AD FS egyszerű szolgáltatás létrehozása
Ha telepítette az AD FS Azure verem, PowerShell segítségével hozzon létre egy egyszerű szolgáltatást, rendeljen hozzá egy szerepkört a hozzáféréshez és jelentkezzen be a Powershellből ezzel az identitással.

A parancsfájl futtatása a rendszerjogosultságú végpontról ERCS virtuális géphez.


Követelmények:
- Egy hitelesített meg kell adni.

**Paraméterek**

A következő információkra szükség az automation paraméterek bemenetként:


|Paraméter|Leírás|Példa|
|---------|---------|---------|
|Name (Név)|Az SPN-fiók nevét|MyAPP|
|ClientCertificates|Tanúsítvány objektumokból álló tömb|X509 tanúsítvány|
|ClientRedirectUris<br>(Választható lehetőség)|Alkalmazás átirányítási URI|         |

**Példa**

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   > [!NOTE]
   > Ez a példa létrehoz egy önaláírt tanúsítványt. Ezek a parancsok éles környezetben való futtatásakor használja a Get-tanúsítvány a használni kívánt tanúsítvány tanúsítvány-objektum beolvasása.

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. Az automatizálás befejezése után az egyszerű szolgáltatásnév használatához szükséges részleteit jeleníti meg. 

   Példa:

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```
### <a name="assign-a-role"></a>A szerepkör hozzárendelése
Az egyszerű szolgáltatás létrehozása után kell [hozzárendelése szerepkörhöz](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Jelentkezzen be a PowerShell segítségével
Ha egy szerepkörhöz hozzárendelt, bármikor beléphet Azure veremnek az egyszerű szolgáltatás használata a következő parancsot:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Szolgáltatás egyszerű szerepkör hozzárendelése
Az előfizetés az erőforrások eléréséhez az alkalmazást egy szerepkörhöz kell rendelni. Döntse el, melyik szerepkört jelöli a megfelelő engedélyekkel az alkalmazáshoz. A rendelkezésre álló szerepkörökkel kapcsolatos további tudnivalókért lásd: [RBAC: beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md).

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

[Felhasználók hozzáadása az AD FS szolgáltatáshoz](azure-stack-add-users-adfs.md)
[felhasználói engedélyeinek kezelése](azure-stack-manage-permissions.md)