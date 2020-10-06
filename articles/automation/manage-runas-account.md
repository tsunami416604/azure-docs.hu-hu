---
title: Azure Automation futtató fiók kezelése
description: Ez a cikk azt ismerteti, hogyan kezelheti a futtató fiókot a PowerShell vagy a Azure Portal használatával.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 3357cb40ff476a3cc0bce259930068aeccf2c10c
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767382"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók kezelése

A Azure Automation futtató fiókok hitelesítést biztosítanak a Azure Resource Manager vagy a klasszikus Azure üzemi modell erőforrásainak automatizálási runbookok és egyéb Automation-funkciók használatával történő kezeléséhez. Ez a cikk útmutatást nyújt egy futtató vagy klasszikus futtató fiók kezeléséhez.

Ha többet szeretne megtudni Azure Automation fiók hitelesítéséről és a folyamat-automatizálási forgatókönyvekkel kapcsolatos útmutatásról, tekintse meg az [Automation-fiók hitelesítésének áttekintése](automation-security-overview.md)című cikket.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Futtató fiók engedélyei

Ez a szakasz a normál futtató fiókok és a klasszikus futtató fiókok engedélyeit határozza meg.

Futtató fiók létrehozásához vagy frissítéséhez konkrét jogosultságokkal és engedélyekkel kell rendelkeznie. Azure Active Directory és egy előfizetéshez tartozó egyik alkalmazás rendszergazdája elvégezheti az összes feladatot. Az alábbi táblázat a feladatok elkülönítését mutatja be, a megfelelő parancsmagot és engedélyeket, valamint a szükséges engedélyek listáját:

|Feladat|Parancsmag  |Minimális engedélyek  |Az engedélyek beállítása|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[Új – AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Alkalmazás fejlesztői szerepköre<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja > |
|Adjon hozzá egy hitelesítő adatot az alkalmazáshoz.|[Új – AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|Azure AD-szolgáltatásnév létrehozása és beszerzése|[Új – AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|Az Azure-szerepkör kiosztása vagy beszerzése a megadott rendszerbiztonsági tag számára|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Felhasználói hozzáférés a rendszergazdához vagy a tulajdonoshoz, vagy a következő engedélyekkel rendelkezik:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Home >-előfizetések > \<subscription name\> -Access Control (iam)|
|Automation-tanúsítvány létrehozása vagy eltávolítása|[Új – AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Közreműködő az erőforráscsoporthoz         |Automation-fiók erőforráscsoport|
|Automation-kapcsolatok létrehozása vagy eltávolítása|[Új – AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Közreműködő az erőforráscsoporthoz |Automation-fiók erőforráscsoport|

<sup>1</sup> az Azure ad-bérlőben nem rendszergazda felhasználók [regisztrálhatnak ad-alkalmazásokat](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , ha az Azure ad-bérlő **felhasználóinak az alkalmazások regisztrálása** lehetőség a felhasználói beállítások lapon az **Igen**értékre van állítva. Ha az alkalmazás regisztrációs beállítása **nem**, a műveletet végrehajtó felhasználónak a táblázatban megadott módon kell szerepelnie.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt hozzáadja az előfizetés globális rendszergazdai szerepköréhez, vendégként lesz hozzáadva. Ebben az esetben `You do not have permissions to create…` figyelmeztetést kap az **Automation-fiók hozzáadása** oldalon.

Ha az előfizetés Active Directory-példányának tagja, ahol a globális rendszergazdai szerepkör hozzá van rendelve, akkor `You do not have permissions to create…` az **Automation-fiók hozzáadása** oldalon is megjelenik egy figyelmeztetés. Ebben az esetben kérheti az előfizetés Active Directory példányának eltávolítását, majd újból felveszi a kérést, hogy teljes jogú felhasználó legyen a Active Directoryban.

Annak ellenőrzése, hogy a hibaüzenetet előállító helyzet kijavítása megtörtént-e:

1. A Azure Portal Azure Active Directory ablaktábláján válassza a **felhasználók és csoportok**lehetőséget.
2. Válassza **a minden felhasználó**lehetőséget.
3. Válassza ki a nevét, majd válassza a **profil**lehetőséget.
4. Győződjön meg arról, hogy a felhasználó profiljában a **felhasználó típusa** attribútum értéke nincs beállítva **vendégként**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>A klasszikus futtató fiókok létrehozásához és kezeléséhez szükséges engedélyek

Klasszikus futtató fiókok konfigurálásához vagy megújításához az előfizetés szintjén kell lennie a társ-rendszergazda szerepkörnek. A klasszikus előfizetési engedélyekkel kapcsolatos további tudnivalókért tekintse meg a [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md#add-a-co-administrator)című témakört.

## <a name="create-a-run-as-account-in-azure-portal"></a>Futtató fiók létrehozása a Azure Portalban

A következő lépésekkel frissítheti Azure Automation-fiókját a Azure Portalban. Egyenként hozza létre a futtató és a klasszikus futtató fiókokat. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.

2. Keresse meg és válassza ki az **Automation-fiókokat**.

3. Az Automation-fiókok lapon válassza ki az Automation-fiókját a listából.

4. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a **Fiókbeállítások** szakaszban.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Válassza a futtató fiók lehetőséget.":::

5. Attól függően, hogy milyen fiókra van szüksége, használja a **+ Azure-beli futtató fiók** vagy a **klasszikus Azure** -beli futtató fiók panelt. Az áttekintő információk áttekintése után kattintson a **Létrehozás**gombra.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Válassza a futtató fiók lehetőséget.":::

6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Megjelenik egy szalagcím is, amely azt jelzi, hogy a fiók létrehozása folyamatban van. A folyamat eltarthat néhány percig.

## <a name="create-a-run-as-account-using-powershell"></a>Futtató fiók létrehozása a PowerShell használatával

A következő lista a futtató fióknak a PowerShellben egy megadott parancsfájllal történő létrehozásához szükséges követelményeket ismerteti. Ezek a követelmények mindkét típusú futtató fiókra érvényesek.

* Windows 10 vagy Windows Server 2016, Azure Resource Manager modul 3.4.1-es és újabb verzióival. A PowerShell-parancsfájl nem támogatja a Windows korábbi verzióit.
* Azure PowerShell PowerShell-6.2.4 vagy újabb. További információ: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps).
* Egy Automation-fiók, amelyre a és a paraméterek értékeként hivatkozunk `AutomationAccountName` `ApplicationDisplayName` .
* A [futtató fiókok konfigurálásához szükséges engedélyekkel](#permissions)egyenértékű engedélyek.

A következő lépések végrehajtásával kérheti le a, a és a értékeit a `AutomationAccountName` `SubscriptionId` PowerShell- `ResourceGroupName` parancsfájlhoz szükséges paraméterekkel.

1. A Azure Portal válassza az **Automation-fiókok**elemet.

1. Az Automation-fiókok lapon válassza ki az Automation-fiókját.

1. A Fiókbeállítások szakaszban válassza a **Tulajdonságok**lehetőséget.

1. Jegyezze fel a **Name**, az **előfizetés-azonosító**és az **erőforráscsoport** értékét a **Tulajdonságok** lapon.

   ![Automation-fiók tulajdonságai lap](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-parancsfájl futtató fiók létrehozásához

A PowerShell-parancsfájl több konfigurációt is támogat.

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Egy futtató fiók és egy klasszikus futtató fiók létrehozása a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

1. Töltse le és mentse a parancsfájlt egy helyi mappába a következő parancs használatával.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Indítsa el a PowerShellt emelt szintű felhasználói jogosultságokkal, és navigáljon a parancsfájlt tartalmazó mappához.

3. Futtassa a következő parancsok egyikét egy futtató és/vagy klasszikus futtató fiók létrehozásához a követelmények alapján.

    * Futtató fiók létrehozása önaláírt tanúsítvány használatával.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Ha a klasszikus futtató fiókot vállalati nyilvános tanúsítvánnyal (. cer fájllal) hozta létre, használja ezt a tanúsítványt. A parancsfájl létrehozza és menti a számítógép ideiglenes fájlok mappájába a `%USERPROFILE%\AppData\Local\Temp` PowerShell-munkamenet végrehajtásához használt felhasználói profil alatt. Lásd: [felügyeleti API-tanúsítvány feltöltése a Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával a Azure Government-felhőben

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. A parancsfájl végrehajtása után a rendszer kéri, hogy végezzen hitelesítést az Azure-ban. Jelentkezzen be egy olyan fiókkal, amely tagja az előfizetés-rendszergazdák szerepkörnek. Ha klasszikus futtató fiókot hoz létre, akkor a fióknak az előfizetés közös rendszergazdájának kell lennie.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Futtató fiók vagy klasszikus futtató fiók törlése

Ez a szakasz azt ismerteti, hogyan lehet törölni egy futtató vagy klasszikus futtató fiókot. A művelet során a rendszer megőrzi az Automation-fiókot. A futtató fiók törlését követően újra létrehozhatja azt a Azure Portal vagy a megadott PowerShell-parancsfájl használatával.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.

3. A Futtató fiókok tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván.

4. A kiválasztott fiók Tulajdonságok paneljén kattintson a **Törlés**elemre.

   ![Futtató fiók törlése](media/manage-runas-account/automation-account-delete-runas.png)

5. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Önaláírt tanúsítvány megújítása

A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy évig lejár. A futtató fiók lejárata előtt egy bizonyos ponton meg kell újítania a tanúsítványt. Bármikor megújíthatja, mielőtt lejár.

Az önaláírt tanúsítvány megújításakor a rendszer megőrzi a jelenlegi érvényes tanúsítványt, hogy biztosítsa, hogy a várólistára helyezett vagy aktívan futó runbookok és a futtató fiókkal végzett hitelesítés ne legyen negatív hatással. A tanúsítvány a lejárati dátumáig érvényes marad.

>[!NOTE]
>Ha úgy véli, hogy a futtató fiók biztonsága sérül, törölheti és újból létrehozhatja az önaláírt tanúsítványt.

>[!NOTE]
>Ha úgy állította be a futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kiadott tanúsítványt használja, és az önaláírt tanúsítvány megújítására vonatkozó beállítást használja, a vállalati tanúsítvány egy önaláírt tanúsítvány helyett szerepel.

Az önaláírt tanúsítvány megújításához kövesse az alábbi lépéseket.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

1. Válassza a fiók beállításai szakaszban a **futtató fiókok** lehetőséget.

    ![Az Automation-fiók tulajdonságpanelje](media/manage-runas-account/automation-account-properties-pane.png)

1. A futtató fiókok tulajdonságai lapon válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelynek a tanúsítványát meg szeretné újítani.

1. A kiválasztott fiók Tulajdonságok paneljén kattintson a **tanúsítvány megújítása**elemre.

    ![Futtató fiók tanúsítványának megújítása](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="limit-run-as-account-permissions"></a>Futtató fiók engedélyeinek korlátozása

Az Azure-beli erőforrásokhoz való automatizálás célzásának szabályozásához futtathatja a [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) szkriptet. Ez a parancsfájl módosítja a meglévő futtató fiók egyszerű szolgáltatását egy egyéni szerepkör-definíció létrehozásához és használatához. A szerepkör a [Key Vault](../key-vault/index.yml)kivételével minden erőforráshoz rendelkezik engedéllyel.

>[!IMPORTANT]
>A **Update-AutomationRunAsAccountRoleAssignments.ps1** -parancsfájl futtatása után a Runbookok a futtató fiókokkal való Key Vault a továbbiakban nem működik. A szkript futtatása előtt tekintse át a runbookok a fiókjában, hogy meghívja a Azure Key Vault. A Key Vault Azure Automation runbookok való elérésének engedélyezéséhez hozzá kell [adnia a futtató fiókot Key Vault engedélyeihez](#add-permissions-to-key-vault).

Ha korlátoznia kell a szolgáltatást, a Futtatás mint szolgáltatás egyszerű funkciója további erőforrástípusok hozzáadásával is kiegészíthető az `NotActions` Egyéni szerepkör-definíció eleméhez. Az alábbi példa korlátozza a hozzáférését a következőhöz: `Microsoft.Compute/*` . Ha ezt az erőforrástípust hozzáadja `NotActions` a szerepkör-definícióhoz, a szerepkör nem fog tudni hozzáférni a számítási erőforrásokhoz. További információ a szerepkör-definíciókkal kapcsolatban: [Az Azure-erőforrások szerepkör-definícióinak megismerése](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Megadhatja, hogy a futtató fiók által használt egyszerű szolgáltatásnév a közreműködő szerepkör definíciójában vagy egy egyéniben legyen.

1. Nyissa meg az Automation-fiókját, és válassza a fiók beállításai szakaszban a **futtató fiókok** elemet.
2. Válassza az Azure-beli **futtató fiók**lehetőséget.
3. Válassza ki a **szerepkört** a használatban lévő szerepkör-definíció megkereséséhez.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Válassza a futtató fiók lehetőséget." lightbox="media/manage-runas-account/verify-role-expanded.png":::

A futtató fiókok által használt szerepkör-definíciót több előfizetés vagy Automation-fiók esetében is meghatározhatja. Ezt a PowerShell-galéria [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) parancsfájljának használatával teheti meg.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vaulthoz

Engedélyezheti Azure Automation annak ellenőrzését, hogy a Key Vault és a futtató fiók egyszerű szolgáltatása egyéni szerepkör-definíciót használ-e. A következőket kell tennie:

* Engedélyek megadása Key Vault számára.
* Adja meg a hozzáférési házirendet.

A PowerShell-galéria [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) parancsfájllal megadhatja a futtató fiók engedélyeit Key Vault. A Key Vault engedélyeinek beállításával kapcsolatos további részletekért tekintse meg [a Key Vault hozzáférési szabályzatának hozzárendelését](/azure/key-vault/general/assign-access-policy-powershell) ismertető témakört.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>A futtató fiókok konfigurációs problémáinak elhárítása

Előfordulhat, hogy a futtató vagy klasszikus futtató fiókhoz szükséges konfigurációs elemeket törölték vagy helytelenül hozták létre a kezdeti beállítás során. A helytelen konfiguráció lehetséges példányai a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A futtató fiók el lett távolítva a közreműködő szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az ilyen hibás konfigurációs példányok esetében az Automation-fiók észleli a módosításokat, és *hiányos* állapotot jelenít meg a fiók futtató fiókok tulajdonságok paneljén.

![Hiányos futtatófiók-konfigurációs állapot](media/manage-runas-account/automation-account-runas-config-incomplete.png)

Ha kiválasztja a futtató fiókot, a fiók tulajdonságai ablaktábla a következő hibaüzenetet jeleníti meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Ezeket a futtató fiókok hibáit gyorsan megoldhatja a futtató fiók törlésével és újbóli létrehozásával.

## <a name="next-steps"></a>Következő lépések

* [Alkalmazás-objektumok és egyszerű szolgáltatások objektumai](../active-directory/develop/app-objects-and-service-principals.md).
* [Az Azure Cloud Services tanúsítványok áttekintése](../cloud-services/cloud-services-certs-create.md).
