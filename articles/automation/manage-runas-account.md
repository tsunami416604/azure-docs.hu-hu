---
title: Azure Automation futtató fiók kezelése
description: Ez a cikk azt ismerteti, hogyan kezelheti a futtató fiókot a PowerShell vagy a Azure Portal használatával.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: 0d854c8815ff6acd54036e023b35c3b3ac2c56cf
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85444522"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók kezelése

A Azure Automation futtató fiókok az Azure-parancsmagok használatával biztosítanak hitelesítést az Azure-erőforrások kezeléséhez. A futtató fiók létrehozásakor létrehoz egy új egyszerű szolgáltatásnevet a Azure Active Directory (AD) szolgáltatásban, és hozzárendeli a közreműködői szerepkört a felhasználóhoz az előfizetés szintjén.

## <a name="types-of-run-as-accounts"></a>A futtató fiókok típusai

Azure Automation két típusú futtató fiókot használ:

* Azure-beli futtató fiók
* Klasszikus Azure-beli futtató fiók

>[!NOTE]
>A Azure Cloud Solution Provider (CSP) előfizetések csak a Azure Resource Manager modellt támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha CSP-előfizetést használ, a klasszikus Azure-beli futtató fiók nem jön létre, de létrejön az Azure-beli futtató fiók. A CSP-előfizetésekkel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört.

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek**alatt. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Futtató fiók

A futtató fiók a [Resource Manager üzembe helyezési modell](../azure-resource-manager/management/deployment-models.md) erőforrásait kezeli. A következő feladatokat hajtja végre.

* Létrehoz egy önaláírt tanúsítvánnyal ellátott Azure AD-alkalmazást, továbbá létrehoz egy egyszerűszolgáltatás-fiókot az Azure AD-ben lévő alkalmazáshoz, és hozzárendeli a közreműködői szerepkört a jelenlegi előfizetésben lévő fiókhoz. A tanúsítvány beállítását a tulajdonosra vagy bármely más szerepkörre módosíthatja. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
  
* Létrehoz egy nevű Automation-tanúsítványt `AzureRunAsCertificate` a megadott Automation-fiókban. A tanúsítvány objektuma tartalmazza az Azure AD-alkalmazás által használt tanúsítvány titkos kulcsát.
  
* Létrehoz egy nevű Automation-összekötő eszközt `AzureRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az alkalmazás AZONOSÍTÓját, a bérlő AZONOSÍTÓját, az előfizetés AZONOSÍTÓját és a tanúsítvány ujjlenyomatát.

### <a name="azure-classic-run-as-account"></a>Klasszikus Azure-futtatófiók

A klasszikus Azure-beli futtató fiók kezeli a [klasszikus üzembe helyezési modell](../azure-resource-manager/management/deployment-models.md) erőforrásait. Ilyen típusú fiók létrehozásához vagy megújításához az előfizetés egyik társ-rendszergazda tagjának kell lennie.

A klasszikus Azure-beli futtató fiók a következő feladatokat hajtja végre.

  * Létrehoz egy felügyeleti tanúsítványt az előfizetésben.

  * Létrehoz egy nevű Automation-tanúsítványt `AzureClassicRunAsCertificate` a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.

  * Létrehoz egy nevű Automation-összekötő eszközt `AzureClassicRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az előfizetés nevét, az előfizetés AZONOSÍTÓját és a tanúsítvány-eszköz nevét.

>[!NOTE]
>A klasszikus Azure-beli futtató fiók alapértelmezés szerint nem jön létre Automation-fiók létrehozásakor. Ezt a fiókot külön hozza létre a cikk későbbi részében ismertetett lépéseket követve.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Futtató fiók engedélyeinek beszerzése

Ez a szakasz a normál futtató fiókok és a klasszikus futtató fiókok engedélyeit határozza meg.

### <a name="get-permissions-to-configure-run-as-accounts"></a>A futtató fiókok konfigurálásához szükséges engedélyek beolvasása

Futtató fiók létrehozásához vagy frissítéséhez konkrét jogosultságokkal és engedélyekkel kell rendelkeznie. Azure Active Directory és egy előfizetéshez tartozó egyik alkalmazás rendszergazdája elvégezheti az összes feladatot. Az alábbi táblázat a feladatok elkülönítését mutatja be, a megfelelő parancsmagot és engedélyeket, valamint a szükséges engedélyek listáját:

|Tevékenység|Parancsmag  |Minimális engedélyek  |Az engedélyek beállítása|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[Új – AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Alkalmazás fejlesztői szerepköre<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja > |
|Adjon hozzá egy hitelesítő adatot az alkalmazáshoz.|[Új – AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|Azure AD-szolgáltatásnév létrehozása és beszerzése|[Új – AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|A RBAC szerepkör kiosztása vagy beolvasása a megadott rendszerbiztonsági tag számára|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Felhasználói hozzáférés a rendszergazdához vagy a tulajdonoshoz, vagy a következő engedélyekkel rendelkezik:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Home >-előfizetések > \<subscription name\> -Access Control (iam)|
|Automation-tanúsítvány létrehozása vagy eltávolítása|[Új – AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Közreműködő az erőforráscsoporthoz         |Automation-fiók erőforráscsoport|
|Automation-kapcsolatok létrehozása vagy eltávolítása|[Új – AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Közreműködő az erőforráscsoporthoz |Automation-fiók erőforráscsoport|

<sup>1</sup> az Azure ad-bérlőben nem rendszergazda felhasználók [regisztrálhatnak ad-alkalmazásokat](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , ha az Azure ad-bérlő **felhasználóinak az alkalmazások regisztrálása** lehetőség a felhasználói beállítások lapon az **Igen**értékre van állítva. Ha az alkalmazás regisztrációs beállítása **nem**, a műveletet végrehajtó felhasználónak a táblázatban megadott módon kell szerepelnie.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt hozzáadja az előfizetés globális rendszergazdai szerepköréhez, vendégként lesz hozzáadva. Ebben az esetben `You do not have permissions to create…` figyelmeztetést kap az **Automation-fiók hozzáadása** oldalon.

Ha az előfizetés Active Directory példányának tagja a globális rendszergazdai szerepkör hozzárendelésekor, akkor `You do not have permissions to create…` az **Automation-fiók hozzáadása** oldalon is megjelenik egy figyelmeztetés. Ebben az esetben kérheti az előfizetés Active Directory példányának eltávolítását, majd újból felveszi a kérést, hogy teljes jogú felhasználó legyen a Active Directoryban.

Annak ellenőrzése, hogy a hibaüzenetet előállító helyzet kijavítása megtörtént-e:

1. A Azure Portal Azure Active Directory ablaktábláján válassza a **felhasználók és csoportok**lehetőséget.
2. Válassza **a minden felhasználó**lehetőséget.
3. Válassza ki a nevét, majd válassza a **profil**lehetőséget. 
4. Győződjön meg arról, hogy a felhasználó profiljában a **felhasználó típusa** attribútum értéke nincs beállítva **vendégként**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Engedélyek beszerzése klasszikus futtató fiókok konfigurálásához

Klasszikus futtató fiókok konfigurálásához vagy megújításához az előfizetés szintjén kell lennie a társ-rendszergazda szerepkörnek. A klasszikus előfizetési engedélyekkel kapcsolatos további tudnivalókért tekintse meg a [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md#add-a-co-administrator)című témakört.

## <a name="create-a-run-as-account-in-azure-portal"></a>Futtató fiók létrehozása a Azure Portalban

A következő lépésekkel frissítheti Azure Automation-fiókját a Azure Portalban. Egyenként hozza létre a futtató és a klasszikus futtató fiókokat. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.

2. Keresse meg és válassza ki az **Automation-fiókokat**.

3. Az Automation-fiókok lapon válassza ki az Automation-fiókját a listából.

4. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.

5. Attól függően, hogy melyik fiókra van szüksége, válassza az **Azure-alapú futtató fiók** vagy a **Klasszikus Azure-alapú futtató fiók** lehetőséget. 

6. Az érdeklődési fióktól függően használja az Azure-beli **futtató fiók hozzáadása** vagy a klasszikus Azure-beli **futtató fiók hozzáadása** panelt. Az áttekintő információk áttekintése után kattintson a **Létrehozás**gombra.

7. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Megjelenik egy szalagcím is, amely azt jelzi, hogy a fiók létrehozása folyamatban van. A folyamat eltarthat néhány percig.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Futtató fiók vagy klasszikus futtató fiók törlése

Ez a szakasz azt ismerteti, hogyan lehet törölni egy futtató vagy klasszikus futtató fiókot. A művelet során a rendszer megőrzi az Automation-fiókot. A fiók törlése után újból létrehozhatja azt a Azure Portalban.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.

3. A Futtató fiókok tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván. 

4. A kiválasztott fiók Tulajdonságok paneljén kattintson a **Törlés**elemre.

   ![Futtató fiók törlése](media/manage-runas-account/automation-account-delete-runas.png)

5. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

6. A törlés után újra létrehozhatja a fiókot a Futtató fiókok tulajdonságlapon az **Azure-alapú futtató fiók** lehetőség kiválasztásával.

   ![Automation futtató fiók újbóli létrehozása](media/manage-runas-account/automation-account-create-runas.png)

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

Az Azure-beli erőforrásokhoz való automatizálás célzásának szabályozásához futtathatja a [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) szkriptet. Ez a parancsfájl módosítja a meglévő futtató fiók egyszerű szolgáltatását egy egyéni szerepkör-definíció létrehozásához és használatához. A szerepkör a [Key Vault](https://docs.microsoft.com/azure/key-vault/)kivételével minden erőforráshoz rendelkezik engedéllyel.

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

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

A futtató fiókok által használt szerepkör-definíciót több előfizetés vagy Automation-fiók esetében is meghatározhatja. Ezt a PowerShell-galéria [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) parancsfájljának használatával teheti meg.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vaulthoz

Engedélyezheti Azure Automation annak ellenőrzését, hogy a Key Vault és a futtató fiók egyszerű szolgáltatása egyéni szerepkör-definíciót használ-e. A következőket kell tennie:

* Engedélyek megadása Key Vault számára.
* Adja meg a hozzáférési házirendet.

A PowerShell-galéria [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) parancsfájllal megadhatja a futtató fiók engedélyeit Key Vault. A Key Vault engedélyeinek beállításával kapcsolatos további információkért lásd: az [alkalmazások hozzáférésének biztosítása a Key vaulthoz](../key-vault/general/group-permissions-for-apps.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>A futtató fiókok konfigurációs problémáinak elhárítása

Előfordulhat, hogy a futtató vagy klasszikus futtató fiókhoz szükséges konfigurációs elemeket törölték vagy helytelenül hozták létre a kezdeti beállítás során. A helytelen konfiguráció lehetséges példányai a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A futtató fiók el lett távolítva a közreműködő szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az ilyen hibás konfigurációs példányok esetében az Automation-fiók észleli a módosításokat, és *hiányos* állapotot jelenít meg a fiók futtató fiókok tulajdonságok paneljén.

![Hiányos futtatófiók-konfigurációs állapot](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Ha kiválasztja a futtató fiókot, a fiók tulajdonságai ablaktábla a következő hibaüzenetet jeleníti meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

A futtató fiókkal kapcsolatos hasonló problémákat gyorsan elháríthatja a fiók törlésével és ismételt létrehozásával.

## <a name="next-steps"></a>További lépések

* [Alkalmazás-objektumok és egyszerű szolgáltatások objektumai](../active-directory/develop/app-objects-and-service-principals.md).
* [Az Azure Cloud Services tanúsítványok áttekintése](../cloud-services/cloud-services-certs-create.md).
