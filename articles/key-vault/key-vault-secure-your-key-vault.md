---
title: Biztonságos hozzáférés a key vault – Azure Key Vault |} A Microsoft Docs
description: Hozzáférési engedélyek Azure Key Vault, a kulcsok és titkos kulcsok kezeléséhez. A Key Vault, és hogyan védheti meg a kulcstartó hitelesítési és engedélyezési modellt ismerteti.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 3b302c60aefec1c4cd37a7dde82a2f11a9eeed33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862862"
---
# <a name="secure-access-to-a-key-vault"></a>Biztonságos hozzáférés a key vault

Az Azure Key Vault egy felhőszolgáltatás, amely megvédi a titkosítási kulcsokat és titkokat – például a tanúsítványok, kapcsolati karakterláncok és jelszavak. Mivel ezek az adatok bizalmas és kritikus fontosságú üzleti szeretné megvédeni a kulcstartókhoz azáltal, hogy csak az engedélyezett alkalmazások és felhasználók. Ez a cikk áttekintést a Key Vault hozzáférés-modellt. Hitelesítését és engedélyezését ismerteti és bemutatja, hogyan teheti biztonságossá a kulcstartók való hozzáférést.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Hozzáférés modell áttekintése

A key vault hozzáférésének két felületen: a **felügyeleti sík** és a **adatsík**. A felügyeleti sík, amellyel kezelheti a Key Vault magát. Az adatsík-műveletek közé tartozik a létrehozása és törlése a kulcstartók, a Key Vault tulajdonságainak beolvasása és a hozzáférési szabályzatainak frissítése. Az adatsík, ahol dolgozik a key vaultban tárolt adatokat. Hozzáadhat, törlése és módosítása a kulcsok, titkos kódok és tanúsítványok.

Mindkét sík a key vault eléréséhez valamennyi hívót (felhasználók vagy alkalmazások) megfelelő hitelesítésre és engedélyezésre kell rendelkeznie. Hitelesítés a hívó azonosítását hoz létre. Engedélyezési meghatározza, hogy melyik a hívó hajthat végre műveleteket. 

Mindkét sík Azure Active Directory (Azure AD) használja a hitelesítéshez. A hitelesítéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlés (RBAC), és az adatsík a kulcstartó-hozzáférési szabályzat használja.

## <a name="active-directory-authentication"></a>Active Directory-alapú hitelesítés

Ha az Azure-előfizetésében kulcstartót hoz létre, azt automatikusan az előfizetés az Azure AD-bérlő társítva. Mindkét sík az összes hívó kell ezen a bérlőn regisztrálja, és hitelesíteni magukat a key vaultban. Mindkét esetben alkalmazásai is elérhetik a Key Vault két módon:

- **Felhasználó- és alkalmazás-hozzáférési**: Az alkalmazás hozzáfér a Key Vault bejelentkezett felhasználó nevében. Ilyen típusú hozzáférés például az Azure PowerShell és az Azure Portalon. Felhasználói hozzáférés két módon. Felhasználók elérhetik a Key Vault bármilyen alkalmazás vagy egy adott alkalmazást kell használniuk (néven _összetett identitás_).
- **Csak alkalmazás hozzáférése**: Az alkalmazás fut egy démon szolgáltatás vagy a háttérben futó feladat. Az alkalmazásazonosító hozzáférést kap a key vaultban.

Hozzáférés mindkét típusú az alkalmazás hitelesíti az Azure ad-ben. Használja az alkalmazás bármely [támogatott hitelesítési módszer](../active-directory/develop/authentication-scenarios.md) az alkalmazás típusától függően. Az alkalmazás megszerzi az adatsík hozzáférést az erőforráshoz tartozó jogkivonatot. Az erőforrás egy végpontot az a adatok vagy a felügyeleti sík, az Azure-környezet alapján. Az alkalmazás használja a jogkivonatot, és a egy REST API-kérést küld a Key Vault. További információkért tekintse át a [teljes hitelesítési folyamatról](../active-directory/develop/v1-protocols-oauth-code.md).

A modell egy egyetlen, mindkét sík hitelesítési mechanizmust alkalmaz a számos előnye van:

- Szervezetek szabályozhatja a hozzáférést a szervezeten belüli összes kulcstárolójának az központilag.
- Ha egy felhasználó távozik, azonnal elveszíti hozzáférést a szervezeten belüli összes kulcstartó.
- Szervezetek testreszabható hitelesítési beállításai segítségével az Azure ad-ben, mint például a multi-factor authentication szolgáltatás fokozott biztonság engedélyezéséhez.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

Alkalmazások a síkok végpontokon keresztül férhetnek hozzá. A két sík hozzáférés-vezérléssel egymástól függetlenül működik. Egy alkalmazás hozzáférést kulcsok használata a key vaultban, adatsík-hozzáférés a kulcstartó-hozzáférési szabályzat segítségével megadása. Egy felhasználó olvasási hozzáférés a Key Vault tulajdonságait és címkéit, de az adatokat (kulcsok, titkos kódok vagy tanúsítványok) való hozzáférés megadását az RBAC felügyeletisík-hozzáférés biztosítása.

Az alábbi táblázat a végpontok a felügyeleti és az adatok síkot jelenti.

| Access&nbsp;plane | Hozzáférés végpontjai | Műveletek | Hozzáférés&nbsp;ellenőrzési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globálisan:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Létrehozása, olvasása, frissítése és törlése a kulcstartók<br><br>Kulcstartó hozzáférési házirendjeinek beállítása<br><br>A Key Vault-címkék beállítása | Azure Resource Manager RBAC |
| Adatsík | **Globálisan:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: visszafejtés, titkosítás,<br> kicsomagolása, Becsomagolás, győződjön meg arról, aláírásához,<br> első, list, frissíteni, létrehozni,<br> importálás, törlés, biztonsági mentési és visszaállítási<br><br> Titkos kulcsok: beolvasása, listázás, beállítása és törlése | Key Vault hozzáférési szabályzattal |

## <a name="management-plane-and-rbac"></a>Felügyeleti sík és az RBAC

A felügyeleti sík RBAC (szerepkör alapján hozzáférés-vezérlés) használhatja a hívó hajthat végre műveletek engedélyezéséhez. Az RBAC-modellben minden egyes Azure-előfizetés van az Azure AD példányát. Hozzáférést biztosít a felhasználóknak, csoportoknak és alkalmazásoknak a címtárból. Hozzáférés az Azure-előfizetés az Azure Resource Manager-alapú üzemi modellt használó erőforrásainak kezeléséhez. Szeretne hozzáférést biztosítani, használja a [az Azure portal](https://portal.azure.com/), a [Azure CLI-vel](../cli-install-nodejs.md), [Azure PowerShell-lel](/powershell/azureps-cmdlets-docs), vagy a [Azure Resource Manager REST API-k](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Key vault létrehozása egy erőforráscsoportban, és hozzáférés kezelése az Azure AD használatával. Biztosítson a felhasználók vagy csoportok lehetővé teszi az erőforráscsoport kulcstartóit kezeljék. Hozzáférést biztosít az egy adott hatókör szintjén rendelhet a megfelelő RBAC-szerepkörökhöz. Hozzáférést biztosítani egy felhasználónak kulcstartóit kezeljék, hozzárendeli egy előre meghatározott `key vault Contributor` szerepkört a felhasználóhoz egy adott hatókörnél. A következő hatókörök szintek hozzárendelheti az RBAC-szerepkör:

- **Előfizetés**: Az RBAC-szerepkör az előfizetés szintjén hozzárendelt összes erőforráscsoportra és erőforrások adott előfizetésen belül vonatkozik.
- **Erőforráscsoport**: Az erőforráscsoport szintjén hozzárendelt egy RBAC szerepkör az erőforráscsoport valamennyi erőforrására vonatkozik.
- **Adott erőforrás**: Az RBAC-szerepkör a meghatározott erőforráshoz rendelt adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkörök tartoznak. Ha egy előre meghatározott szerepkör nem fér el az igényeinek, megadhatja saját szerepkört. További információkért lásd: [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Ha egy felhasználó `Contributor` a kulcstartó felügyeleti síkjához engedélyeket, a felhasználó lehet hozzáférést magukat az adatsík a kulcstartó hozzáférési házirend beállításával. Meg kell mértékben vezérelhetik kinek van `Contributor` a kulcstartók eléréséhez. Győződjön meg arról, hogy csak az arra jogosult személyek eléréséhez, és a kulcstartók, kulcsok, titkos kódok és tanúsítványok kezelése.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Az adatsík és hozzáférési szabályzatokról

Adatsík-hozzáférés kulcstartó a kulcstartó hozzáférési házirendjeinek beállításán keresztül biztosít. A hozzáférési házirendek beállításának, egy felhasználó, csoport vagy alkalmazás kell `Contributor` engedélyeket adott kulcstartó felügyeleti síkjának.

Azt adja meg a felhasználó, csoport vagy alkalmazás-hozzáférés a kulcstartó kulcsaihoz vagy titkos kulcsaihoz kapcsolódó, meghatározott műveletek végrehajtásához. A Key Vault támogatja a key vault legfeljebb 1024 hozzáférésiszabályzat-bejegyzést. Adatsík-hozzáférés biztosítása a több felhasználó, hozzon létre egy Azure AD biztonsági csoportot, és felhasználók hozzáadása a csoporthoz.

<a id="key-vault-access-policies"></a> A Key Vault hozzáférési házirendekkel külön-külön kulcsok, titkos kódok és tanúsítványok engedélyezheti. Felhasználói hozzáférés kizárólag a kulcsokhoz és titkos kódok, nem adhat meg. Kulcsok, titkos kódok és tanúsítványok hozzáférési engedélyei vannak tárolószinten engedélyezett. Kulcstartó-hozzáférési szabályzatok nem támogatottak egy adott kulcs, a titkos kulcsot vagy a tanúsítvány részletes, az Objektumszintű engedélyeket. Kulcstartó hozzáférési házirendjeinek beállításához használja a [az Azure portal](https://portal.azure.com/), a [Azure CLI-vel](../cli-install-nodejs.md), [Azure PowerShell-lel](/powershell/azureps-cmdlets-docs), vagy a [Key Vault felügyeleti REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> A Key Vault hozzáférési házirendek tárolószinten érvényesek. Amikor egy felhasználó kulcsok létrehozására és törlésére engedélyt kap, minden kulcs ezeket a műveleteket, hogy a key vault végrehajtható.
>

Adatsík-hozzáférés használatával korlátozhatja [virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Konfigurálható [tűzfalak és virtuális hálózati szabályok](key-vault-network-security.md) számára egy további biztonsági réteget.

## <a name="example"></a>Példa

Ebben a példában egy alkalmazás az SSL-hez, adatok és a egy RSA 2048 bites kulcs aláírási műveletekhez az Azure Storage-tanúsítványt használó fejlesztést. Az alkalmazás fut egy Azure virtuális gép (VM) (vagy egy virtuálisgép-méretezési csoportot). Key vault használatával az alkalmazás titkainak tárolásához. A rendszerindítási tanúsítványt, amelyet az alkalmazás az Azure AD-hitelesítést is tároljuk.

A következő tárolt kulcsok és titkos kulcsok hozzáférésre van szükségünk:
- **SSL-tanúsítvány**: Használja az SSL-hez.
- **Tárkulcs**: A tárfiók eléréséhez használt.
- **RSA 2048 bites kulcs**: Aláírási műveletekhez használatos.
- **Rendszerindítási tanúsítvány**: Az Azure AD-hitelesítésre használható. Hozzáférés megadása után lehet beolvasni a tárfiók kulcsát és használja az RSA-kulcsot az aláíráshoz.

Kell megadnia, aki kezelését, telepítését és az alkalmazás naplózása a következő szerepkörök definiálása:
- **Biztonsági csapat**: INFORMATIKAI részleg a CSO (biztonsági vezető) vagy hasonló közreműködők az irodából. A biztonsági csapat felelős a megfelelő személyek, a titkos kulcsok. A titkos kulcsok SSL-tanúsítványok, aláíráshoz, a kapcsolati karakterláncok és a tárfiókkulcsok RSA-kulcsok is tartalmazhat.
- **A fejlesztők és üzemeltetők**: Az alkalmazottak, akik az alkalmazások fejlesztése és üzembe helyezni az Azure-ban. Ennek a csapatnak a tagjai a biztonsági személyzet részét képezik. Nem férhet hozzá, bizalmas adatokat – például SSL-tanúsítványok és az RSA-kulcsok. Csak az általuk üzembe helyezett alkalmazás bizalmas adatokhoz való hozzáféréssel kell rendelkeznie.
- **Ellenőrök**: Ez a szerepkör a közreműködői számára, akik nem tagjai a fejlesztési vagy általános informatikai személyzettől van. Tekintse át a használatának és karbantartásának tanúsítványok, kulcsok és titkos kulcsok biztonsági szabványoknak való megfelelőség érdekében. 

Az alkalmazás hatókörén kívül egy másik szerepkör van: az előfizetés (vagy erőforráscsoportot) rendszergazda. Az előfizetés-rendszergazda állítja be a biztonsági csapat kezdeti hozzáférési engedélyeket. Hozzáférést a biztonsági csapat egy erőforráscsoportot, amely rendelkezik az alkalmazás által igényelt erőforrásokra használatával.

Engedélyezze a szerepköröket a következő műveleteket kell:

**Biztonsági csapat**
- Kulcstartók létrehozása.
- Kapcsolja be a Key Vault naplózása.
- Adja hozzá a kulcsok és titkos kulcsok.
- Vész-helyreállítási kulcsok biztonsági másolatokat készíthet.
- Engedélyek megadása a felhasználók és alkalmazások adott műveletek esetében a Key Vault hozzáférési házirendjeinek beállítása.
- Visszaállítás a kulcsok és titkos kulcsok rendszeres időközönként.

**A fejlesztők és üzemeltetők**
- A biztonsági csapat, a rendszerindítási és SSL-tanúsítványok (ujjlenyomatok), tárkulcs (titkos URI) és RSA-kulcs (kulcs URI) vonatkozó referenciák beszerzése az aláíráshoz.
- Fejlesztés és a kulcsok és titkos kulcsok programozott módon hozzáférni az alkalmazás üzembe helyezése.

**Ellenőrök**
- Tekintse át a Key Vault-naplók, ellenőrizze a kulcsok és titkos kulcsok és megfelelőségi adatokat a biztonsági szabványoknak megfelelő használatát.

A következő táblázat összefoglalja a szerepkörök és az alkalmazás hozzáférési engedélyeit. 

| Szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei |
| --- | --- | --- |
| Biztonsági csapat | Key Vault-közreműködő | Kulcsok: biztonsági mentése, létrehozása, törlése, beolvasása, importálása, listázása, visszaállítása<br>Titkos kódok: minden művelet |
| A fejlesztők és&nbsp;operátorok | A Key Vault-üzembehelyezési engedély<br><br> **Megjegyzés**: Ez az engedély lehetővé teszi, hogy üzembe helyezett virtuális gépek beolvasni a titkos kulcsok a key vault. | None |
| Ellenőrök | None | Kulcsok: listája<br>Titkos kulcsok: listája<br><br> **Megjegyzés**: Ez az engedély lehetővé teszi, hogy auditorok attribútumok (címkék, aktiválási dátumot, lejárati dátumát) a kulcsok és titkos kódok nem kerülnek naplózásra a naplók vizsgálata. |
| Alkalmazás | None | Kulcsok: aláírása<br>Titkos kulcsok: beolvasása |

A csapat három szerepkört kell a Key Vault-engedélyek és egyéb erőforrásokhoz való hozzáférést. Virtuális gépek (vagy az Azure App Service Web Apps funkcióját) üzembe fejlesztők és üzemeltetők kell `Contributor` hozzáférést adott erőforrástípusokhoz. Ellenőrök kell olvasási hozzáférést a tárfiókhoz a Key Vault naplóinak tárolására.

Tanúsítványok üzembe helyezésével kapcsolatos további információkért hozzáférési kulcsokat és titkos kulcsok programozott módon, az alábbi forrásokban talál:
- Ismerje meg, hogyan [tanúsítványok telepítése a virtuális gépek az ügyfél által felügyelt key vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogbejegyzés).
- Töltse le a [ügyfél-minták az Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Ez a tartalom szemlélteti, hogyan kell a rendszerindítási tanúsítványt használják a hitelesítéshez az Azure AD részére egy kulcstartó eléréséhez.

A legtöbb a hozzáférési engedélyeket adhat az Azure portal használatával. Részletes engedélyezés, használhatja az Azure PowerShell vagy az Azure CLI használatával.

Ebben a szakaszban szereplő PowerShell-kódrészletek beépített a következő előfeltételek:
- Az Azure AD-rendszergazda hozott létre biztonsági csoportokat, a három szerepkört képviselik: Contoso biztonsági csapat, Contoso alkalmazás fejlesztői és és a Contoso alkalmazás ellenőrei. A rendszergazda felhasználó hozzáadta a megfelelő csoporthoz.
- Az összes erőforrások találhatók a **ContosoAppRG** erőforráscsoportot.
- A Key Vault-naplók vannak tárolva a **contosologstorage** storage-fiókot. 
- A **ContosoKeyVault** kulcstartó és a **contosologstorage** tárfiók ugyanazon Azure-beli helyen vannak.

Az előfizetés-rendszergazda rendeli hozzá a `key vault Contributor` és `User Access Administrator` szerepköröket a biztonsági csapatnak. Ezek a szerepkörök lehetővé teszik a biztonsági csapat más kulcstartók, és erőforrásaihoz való hozzáférés kezelésére, amelyek a a **ContosoAppRG** erőforráscsoportot.

```PowerShell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A biztonsági csapat kulcstartót hoz létre, és beállítja a naplózás és a hozzáférési engedélyek. A Key Vault hozzáférési házirend engedélyekkel kapcsolatos részletekért lásd: [kapcsolatos Azure Key Vault-kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-and-certificates.md).

```PowerShell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

A megadott egyéni szerepkör a csak ahhoz az előfizetéshez rendelhető hozzá, a **ContosoAppRG** erőforráscsoportot kell létrehozni. A szerepkör hatóköre más előfizetések hozzáadása egy egyéni szerepkört más előfizetések más projektekhez használatához.

A fejlesztési és üzemeltetési csapatának, a key vault egyéni szerepkör-hozzárendelését a `deploy/action` engedély hatókörét az erőforráscsoportot. Csak virtuális gépeket létrehozni a **ContosoAppRG** erőforráscsoport férhetnek hozzá a titkos kulcsokat (SSL és rendszer-indításkori tanúsítványokat). Egyéb erőforráscsoportok DevOps tagja által létrehozott virtuális gépek nem fér hozzá a titkos adatokat, még akkor is, ha a virtuális gép rendelkezik a titkos URI-k.

Példánkban egy egyszerű forgatókönyvet ismertet. Valós forgatókönyvek összetettebb lehet. A kulcstartóengedélyek igényei alapján a kulcstartó. Azt feltételezzük, hogy a biztonsági csapat nyújt a kulcs- és titkoskulcs-referenciákat (URI-k és ujjlenyomatok), a fejlesztési és üzemeltetési csapatának biztosítani az alkalmazásokban által használt. A fejlesztők és üzemeltetők nem igényelnek semmilyen adatsík-hozzáférést. A kulcstartó biztonságossá összpontosítottunk. Hasonló fontolóra védelmének kialakításakor [a virtuális gépek](https://azure.microsoft.com/services/virtual-machines/security/), [tárfiókok](../storage/common/storage-security-guide.md), és más Azure-erőforrások.

> [!NOTE]
> Ez a példa bemutatja, hogyan Key Vault-hozzáférés zárolása le éles környezetben. A fejlesztők rendelkeznie kell a saját vagy erőforráscsoportonként csoport teljes körű engedélyekkel a tárolóra, virtuális gépek és a storage-fiók kezeléséhez, a fejlesztés, ahol az alkalmazás.

Azt javasoljuk, hogy beállíthat egy további, biztonságos hozzáférés a kulcstartó által [konfigurálása a Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md).

## <a name="resources"></a>További források

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)

* [Resource Manager-alapú és klasszikus üzembe helyezés ismertetése](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Az Azure PowerShell-lel az RBAC kezelése](../role-based-access-control/role-assignments-powershell.md) 

* [A REST API az RBAC kezelése](../role-based-access-control/role-assignments-rest.md)

* [RBAC használata a Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    A 2015 Microsoft Ignite konferencia videó ismerteti, hozzáférés-kezelési és jelentéskészítési lehetőségeket az Azure-ban. Azt is bemutatja, ajánlott eljárások az Azure-előfizetésekhez való hozzáférés biztosítása az Azure AD használatával.

* [OAuth 2.0 és az Azure AD használatával webes alkalmazásokhoz való hozzáférés engedélyezése](../active-directory/develop/v1-protocols-oauth-code.md)

* [Key Vault-felügyeleti REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A REST API-k kezelése a key referenciája programozott módon, tár kulcstartó-hozzáférési házirend beállításával együtt.

* [A Key Vault REST API-k](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Kulcshozzáférés-vezérlés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Titkoskulcs-hozzáférés vezérlése](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Állítsa be](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) és [eltávolítása](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) Key Vault hozzáférési szabályzattal PowerShell használatával.
  
## <a name="next-steps"></a>További lépések

Konfigurálása [Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md).

A rendszergazda-bevezető oktatóanyagért lásd: [Mi az Azure Key Vault?](key-vault-overview.md).

A kulcstartó használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

Az Azure Key Vault kulcsok és titkos kulcsok használatával kapcsolatos további információkért lásd: [kulcsok és titkok](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Ha a Key Vaulttal kapcsolatban kérdése van, keresse fel a [fórumok](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
