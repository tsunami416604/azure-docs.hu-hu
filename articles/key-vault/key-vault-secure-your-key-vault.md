---
title: Biztonságos hozzáférés a kulcstartóhoz - Azure Key Vault | Microsoft dokumentumok
description: Az Azure Key Vault hozzáférési engedélyeit, a kulcsokat és a titkos kulcsokat kezelheti. Ismerteti a key vault hitelesítési és engedélyezési modelljét, és a kulcstartó biztonságossá tétele.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: eac3850cfa0684bd1751cf7b88b4ff8e92667293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284381"
---
# <a name="secure-access-to-a-key-vault"></a>Biztonságos hozzáférés a kulcstartóhoz

Az Azure Key Vault egy felhőalapú szolgáltatás, amely védi a titkosítási kulcsokat és a titkos kulcsokat, például a tanúsítványokat, a kapcsolati karakterláncokat és a jelszavakat. Mivel ezek az adatok bizalmas és üzleti szempontból kritikus fontosságúak, csak az engedélyezett alkalmazások és felhasználók engedélyezésével kell biztonságos hozzáférést biztosítania a kulcstartókhoz. Ez a cikk áttekintést nyújt a Key Vault hozzáférési modell. Ismerteti a hitelesítést és az engedélyezést, és ismerteti, hogyan biztosíthatja a hozzáférést a kulcstartókhoz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Access modell – áttekintés

A kulcstartóhoz való hozzáférés két felületen keresztül történik: a **felügyeleti síkon** és az **adatsíkon**keresztül. A felügyeleti sík, ahol kezelheti a Key Vault magát. A gépen végzett műveletek közé tartozik a kulcstartók létrehozása és törlése, a Key Vault-tulajdonságok beolvasása és a hozzáférési házirendek frissítése. Az adatsík, ahol a kulcstartóban tárolt adatokkal dolgozik. Kulcsokat, titkos kulcsokat és tanúsítványokat adhat hozzá, törölhet és módosíthat.

A kulcstartó eléréséhez mindkét síkban, minden hívó (felhasználók vagy alkalmazások) megfelelő hitelesítéssel és engedélyezéssel kell rendelkeznie. A hitelesítés határozza meg a hívó identitását. Az engedélyezés határozza meg, hogy a hívó milyen műveleteket hajthat végre.

Mindkét sík az Azure Active Directory (Azure AD) hitelesítést használ. Engedélyezéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC) használ, az adatsík pedig key vault hozzáférési szabályzatot.

## <a name="active-directory-authentication"></a>Active Directory-hitelesítés

Amikor létrehoz egy kulcstartót egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlőjéhez. Mindkét sík összes hívójának regisztrálnia kell ebben a bérlőben, és hitelesítenie kell magát a kulcstartó eléréséhez. Az alkalmazások mindkét esetben kétféleképpen férhetnek hozzá a Key Vaulthoz:

- **Felhasználó és alkalmazás-hozzáférés:** Az alkalmazás egy bejelentkezett felhasználó nevében fér hozzá a Key Vaulthoz. Ilyen típusú hozzáférés példák közé tartozik az Azure PowerShell és az Azure Portal. A felhasználói hozzáférés kétféleképpen érhető el. A felhasználók bármely alkalmazásból hozzáférhetnek a Key Vaulthoz, vagy egy adott alkalmazást _(összetett identitást)_ kell használniuk.
- **Csak alkalmazás-hozzáférés**: Az alkalmazás démonszolgáltatásként vagy háttérfeladatként fut. Az alkalmazás identitása hozzáférést kap a key vaulthoz.

Mindkét típusú hozzáférés, az alkalmazás hitelesíti magát az Azure AD. Az alkalmazás az alkalmazás típusától függően bármilyen [támogatott hitelesítési módszert](../active-directory/develop/authentication-scenarios.md) használ. Az alkalmazás beszerez egy jogkivonatot a síkban a hozzáférést biztosító erőforráshoz. Az erőforrás egy végpont a felügyeleti vagy adatsíkban, az Azure-környezet alapján. Az alkalmazás a jogkivonatot használja, és egy REST API-kérelmet küld a Key Vaultnak. További információért tekintse át a [teljes hitelesítési folyamatot.](../active-directory/develop/v2-oauth2-auth-code-flow.md)

A mindkét sík on-már egyetlen hitelesítési mechanizmusának számos előnye van:

- A szervezetek központilag szabályozhatják a hozzáférést a szervezet összes kulcstartójához.
- Ha egy felhasználó távozik, azonnal elveszíti a hozzáférést a szervezet összes kulcstartójához.
- A szervezetek testre szabhatják a hitelesítést az Azure AD beállításaival, például a többtényezős hitelesítés engedélyezéséhez a nagyobb biztonság érdekében.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

Az alkalmazások végpontokon keresztül érik el a síkokat. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy egy alkalmazás hozzáférést a kulcsok használatához egy key vault, hozzáférést biztosít az adatsík egy Key Vault hozzáférési szabályzat használatával. Ha olvasási hozzáférést szeretne adni a felhasználónak a Key Vault-tulajdonságokhoz és -címkékhez, de nem szeretne hozzáférni az adatokhoz (kulcsokhoz, titkos kulcsokhoz vagy tanúsítványokhoz), az RBAC-mal hozzáférést kell biztosítania a felügyeleti síkhoz.

Az alábbi táblázat a felügyeleti és adatsíkok végpontjait mutatja be.

| Hozzáférési&nbsp;sík | Hozzáférés végpontjai | Műveletek | Hozzáférés-ellenőrzési&nbsp;mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális:**<br> management.azure.com:443<br><br> **Az Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Németország:**<br> management.microsoftazure.de:443 | Kulcstartók létrehozása, olvasása, frissítése és törlése<br><br>Key Vault-hozzáférési házirendek beállítása<br><br>Key Vault-címkék beállítása | Az Azure Erőforrás-kezelő RBAC |
| Adatsík | **Globális:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Az Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Németország:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: visszafejtés, titkosítás,<br> kicsomagolás, becsomagolás, ellenőrzés, aláírás,<br> beszerezni, felsorolni, frissíteni, létrehozni,<br> importálás, törlés, biztonsági mentés, visszaállítás<br><br> Titkok: get, list, set, delete | Key Vault hozzáférési szabályzat |

## <a name="management-plane-and-rbac"></a>Felügyeleti sík és RBAC

A felügyeleti síkban az RBAC(szerepköralapú hozzáférés-vezérlés) használatával engedélyezheti a hívó által végrehajtható műveleteket. Az RBAC-modellben minden Azure-előfizetés rendelkezik egy Azure AD-példánysal. Hozzáférést biztosít a felhasználóknak, csoportoknak és alkalmazásoknak ebből a címtárból. Az Access az Azure Resource Manager telepítési modelljét használó Azure Resource Manager-telepítési modellt használó erőforrások kezeléséhez érhető el. A hozzáférés engedélyezéséhez használja az [Azure Portalt,](https://portal.azure.com/)az [Azure CLI-t](../cli-install-nodejs.md), az [Azure PowerShellt](/powershell/azureps-cmdlets-docs)vagy az [Azure Resource Manager REST API-kat.](https://msdn.microsoft.com/library/azure/dn906885.aspx)

Hozzon létre egy kulcstartót egy erőforráscsoportban, és az Azure AD használatával kezelheti a hozzáférést. A felhasználók vagy csoportok lehetővé teszik a kulcstartók kezelését egy erőforráscsoportban. A hozzáférést egy adott hatókör szintjén adja meg a megfelelő RBAC-szerepkörök hozzárendelésével. Ahhoz, hogy hozzáférést adjon egy felhasználónak a kulcstartók kezeléséhez, egy előre definiált szerepkört `key vault Contributor` rendel a felhasználóhoz egy adott hatókörben. RBAC szerepkörhöz a következő hatókörszintek rendelhetők hozzá:

- **Előfizetés:** Az előfizetés szintjén hozzárendelt RBAC-szerepkör az adott előfizetésen belüli összes erőforráscsoportra és erőforrásra vonatkozik.
- **Erőforráscsoport**: Az erőforráscsoport szintjén hozzárendelt RBAC-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott erőforrás**: Egy adott erőforráshoz rendelt RBAC-szerepkör vonatkozik az adott erőforrásra. Ebben az esetben az erőforrás egy adott key vault.

Számos előre definiált szerepkör létezik. Ha egy előre definiált szerepkör nem felel meg az igényeinek, megadhatja a saját szerepkörét. További információ: [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Ha egy `Contributor` felhasználó rendelkezik engedélyekkel egy key vault felügyeleti síkhoz, a felhasználó hozzáférést biztosíthat magának az adatsíkhoz egy Key Vault hozzáférési házirend beállításával. Szorosan szabályozhatja, `Contributor` hogy kinek van szerepkör-hozzáférése a kulcstartókhoz. Győződjön meg arról, hogy csak arra jogosult személyek férhetnek hozzá és kezelhetik a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Adatsík- és hozzáférési házirendek

Az adatsík-hozzáférést a key vault-hozzáférés házirendjeinek beállításával adja meg. Ezeknek a hozzáférési házirendeknek a beállításához `Contributor` egy felhasználónak, csoportnak vagy alkalmazásnak rendelkeznie kell a kulcstartó felügyeleti síkjának engedélyekkel.

Felhasználói, csoport- vagy alkalmazás-hozzáférést biztosít a kulcs- vagy titkos kulcsok vagy titkos kulcsok egy kulcstartóban végrehajtott műveletek végrehajtásához. A Key Vault legfeljebb 1024 hozzáférési házirend-bejegyzést támogat egy key vaulthoz. Ha több felhasználónak szeretne hozzáférést biztosítani az adatsíkhoz, hozzon létre egy Azure AD biztonsági csoportot, és adjon hozzá felhasználókat a csoporthoz.

<a id="key-vault-access-policies"></a>A Key Vault hozzáférési szabályzatok külön-külön adnak engedélyeket a kulcsoknak, titkos kulcsoknak és tanúsítványoknak. A felhasználók csak a kulcsokhoz férhetnek hozzá, a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférési engedélyei a tároló szintjén vannak. A Key Vault hozzáférési szabályzatai nem támogatják a részletes, objektumszintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. A kulcstartó hozzáférési szabályzatainak beállításához használja az [Azure Portalt,](https://portal.azure.com/)az [Azure CLI-t](../cli-install-nodejs.md), az [Azure PowerShellt](/powershell/azureps-cmdlets-docs)vagy a [Key Vault Felügyeleti REST API-kat.](https://msdn.microsoft.com/library/azure/mt620024.aspx)

> [!IMPORTANT]
> A Key Vault hozzáférési szabályzatai a tároló szintjén érvényesek. Ha egy felhasználó engedélyt kap kulcsok létrehozására és törlésére, ezeket a műveleteket a kulcstartó összes kulcsán végrehajthatja.
>

Az Azure Key Vault [virtuális hálózati szolgáltatásvégpontjainak](key-vault-overview-vnet-service-endpoints.md)használatával korlátozhatja az adatsík-hozzáférést. [Tűzfalak at és virtuális hálózati szabályokat](key-vault-network-security.md) állíthat be egy további biztonsági réteghez.

## <a name="example"></a>Példa

Ebben a példában egy olyan alkalmazást fejlesztünk, amely egy tanúsítványt használ a TLS/SSL-hez, az Azure Storage-t az adatok tárolására, és egy RSA 2048 bites kulcsot az aláírási műveletekhez. Alkalmazásunk egy Azure virtuális gépen (VM) (vagy egy virtuális gép méretezési csoportban) fut. Használhatjuk a key vault az alkalmazás titkos kulcsainak tárolására. Az alkalmazás által az Azure AD-vel való hitelesítéshez használt rendszerindítási tanúsítvány tárolhatja.

Hozzá kell férnünk a következő tárolt kulcsokhoz és titkokhoz:
- **TLS/SSL tanúsítvány:** TLS/SSL-hez használatos.
- **Tárolási kulcs:** A tárfiók eléréséhez használható.
- **RSA 2048 bites kulcs:** aláírási műveletekhez használható.
- **Rendszerindítási tanúsítvány:** Az Azure AD-vel való hitelesítéshez használható. A hozzáférés megadása után letudjuk kérni a tárolókulcsot, és használhatjuk az RSA-kulcsot az aláíráshoz.

Meg kell határoznunk a következő szerepköröket, hogy meghatározzuk, ki kezelheti, telepítheti és naplózhatja az alkalmazásunkat:
- **Biztonsági csoport**: informatikai személyzet a CSO (chief Security Officer) vagy hasonló közreműködők irodájából. A biztonsági csapat felelős a titkok megfelelő megőrzéséért. A titkos kulcsok közé tartozhatnak a TLS/SSL-tanúsítványok, az rsa-kulcsok az aláíráshoz, a kapcsolati karakterláncok és a tárfiók kulcsai.
- **Fejlesztők és operátorok**: Az alkalmazás fejlesztői és az Azure-ban üzembe helyező munkatársak. A csapat tagjai nem tagjai a biztonsági személyzetnek. Nem férhetnek hozzá olyan bizalmas adatokhoz, mint a TLS/SSL-tanúsítványok és az RSA-kulcsok. Csak az általuk telepített alkalmazásnak kell hozzáférnie a bizalmas adatokhoz.
- **Auditorok**: Ez a szerep olyan közreműködőknek szól, akik nem tagjai a fejlesztésnek vagy az informatikai személyzet általános személyzetének. A biztonsági szabványoknak való megfelelés biztosítása érdekében áttekintik a tanúsítványok, kulcsok és titkos kulcsok használatát és karbantartását.

Van egy másik szerepkör, amely kívül esik az alkalmazás hatókörén: az előfizetés (vagy erőforráscsoport) rendszergazda. Az előfizetés rendszergazdája kezdeti hozzáférési engedélyeket állít be a biztonsági csapat számára. Hozzáférést biztosítanak a biztonsági csapatnak egy olyan erőforráscsoport használatával, amely rendelkezik az alkalmazás által igényelt erőforrásokkal.

A következő műveleteket kell engedélyeznünk a szerepünkhöz:

**Biztonsági csapat**
- Hozzon létre kulcstartókat.
- A Key Vault naplózásának bekapcsolása.
- Kulcsok és titkok hozzáadása.
- Hozzon létre biztonsági másolatokat a kulcsokról a vészhelyreállításhoz.
- Állítsa be a Key Vault hozzáférési szabályzatokat, hogy engedélyeket adjon a felhasználóknak és az alkalmazásoknak adott műveletekhez.
- Roll a kulcsokat és titkokat rendszeresen.

**Fejlesztők és üzemeltetők**
- A rendszerindító-tároló és a TLS/SSL-tanúsítványok (ujjlenyomatok), a tárolókulcs (titkos URI) és az RSA-kulcs (kulcs URI) hivatkozásainak beszereznie a rendszerindító és a TLS/SSL-tanúsítványok (ujjlenyomatok), a tárolókulcs (titkos URI) és az RSA-kulcs (kulcs URI) aláírására vonatkozó hivatkozásokat.
- Az alkalmazás fejlesztése és üzembe helyezése a kulcsok és a titkos kulcsok programozott eléréséhez.

**Ellenőrök**
- Tekintse át a Key Vault-naplókat a kulcsok és titkos kulcsok megfelelő használatának és az adatbiztonsági szabványoknak való megfelelés megerősítéséhez.

Az alábbi táblázat összefoglalja a szerepköreink és alkalmazásunk hozzáférési engedélyeit.

| Szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei |
| --- | --- | --- |
| Biztonsági csapat | Kulcstartó közreműködője | Kulcsok: biztonsági mentése, létrehozása, törlése, beolvasása, importálása, listázása, visszaállítása<br>Titkok: minden művelet |
| Fejlesztők&nbsp;és üzemeltetők | Key Vault üzembe helyezési engedélye<br><br> **Megjegyzés:** Ez az engedély lehetővé teszi az üzembe helyezett virtuális gépek számára a titkos kulcsok lekérése egy key vaultból. | None |
| Ellenőrök | None | Kulcsok: listája<br>Titkos kulcsok: listája<br><br> **Megjegyzés:** Ez az engedély lehetővé teszi az auditorok számára, hogy ellenőrizzék a naplókban nem kibocsátott kulcsok és kulcsok attribútumait (címkék, aktiválási dátumok, lejárati dátumok). |
| Alkalmazás | None | Kulcsok: aláírása<br>Titkos kulcsok: beolvasása |

A három csapatszerepkörnek hozzá kell férnie más erőforrásokhoz a Key Vault-engedélyekkel együtt. Virtuális gépek (vagy az Azure App Service webalkalmazások szolgáltatásának) üzembe helyezéséhez a fejlesztőknek és az operátoroknak hozzá kell `Contributor` férnie az ilyen erőforrástípusokhoz. Az auditorok olvasási hozzáférést kell biztosítania a Storage-fiókhoz, ahol a Key Vault-naplók tárolódnak.

A tanúsítványok, hozzáférési kulcsok és titkos kulcsok programozott telepítéséről az alábbi forrásokban talál további információt:
- Megtudhatja, hogyan [helyezheti üzembe a tanúsítványokat a virtuális gépeken egy ügyfél által felügyelt key vaultból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogbejegyzés).
- Töltse le az [Azure Key Vault-ügyfélmintákat.](https://www.microsoft.com/download/details.aspx?id=45343) Ez a tartalom bemutatja, hogyan használhatja a bootstrap tanúsítványt az Azure AD-ben egy key vault eléréséhez.

A hozzáférési engedélyek nagy részét az Azure Portal használatával adhatja meg. Részletes engedélyek megadásához használhatja az Azure PowerShell vagy az Azure CLI.

A PowerShell-kódrészletek ebben a szakaszban a következő feltételezésekkel vannak felépítve:
- Az Azure AD-rendszergazda biztonsági csoportokat hozott létre a három szerepkör öklére: Contoso Security Team, Contoso App DevOps és Contoso App Auditorok. A rendszergazda felhasználókat adott hozzá a megfelelő csoportokhoz.
- Minden erőforrás a **ContosoAppRG** erőforráscsoportban található.
- A Key Vault-naplók a **contosologstorage** storage-fiókban tárolódnak.
- A **ContosoKeyVault** key vault és a **contosologstorage storage-fiók** ugyanazon az Azure-helyen található.

Az előfizetés rendszergazdája `key vault Contributor` `User Access Administrator` hozzárendeli a szerepköröket a biztonsági csapathoz. Ezek a szerepkörök lehetővé teszik, hogy a biztonsági csapat más erőforrásokhoz és kulcstárolókhoz való hozzáférést kezelje, amelyek mindegyike a **ContosoAppRG** erőforráscsoportban található.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A biztonsági csoport létrehoz egy key vaultot, és beállítja a naplózási és hozzáférési engedélyeket. A Key Vault hozzáférési szabályzati engedélyeiről az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok – ismertet című témakörben találja.](about-keys-secrets-and-certificates.md)

```powershell
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

A definiált egyéni szerepkörök csak ahhoz az előfizetéshez rendelhető, ahol a **ContosoAppRG** erőforráscsoport jön létre. Ha más előfizetések más projektjeihez egyéni szerepkört szeretne használni, adjon hozzá más előfizetéseket a szerepkör hatóköréhez.

A DevOps-személyzet, a key vault `deploy/action` engedély egyéni szerepkör-hozzárendelés hatóköre az erőforráscsoport. Csak a **ContosoAppRG** erőforráscsoportban létrehozott virtuális gépek férhetnek hozzá a titkos kulcsokhoz (TLS/SSL és bootstrap tanúsítványok). A DevOps-tagok által más erőforráscsoportokban létrehozott virtuális gépek nem férhetnek hozzá ezekhez a titkos kulcsokhoz, még akkor sem, ha a virtuális gép rendelkezik a titkos URI-k.

A példa egy egyszerű forgatókönyvet ír le. A valós életből kiinduló forgatókönyvek összetettebbek is lehetnek. Az engedélyeket az igényeinek megfelelően módosíthatja a kulcstartóhoz. Feltételeztük, hogy a biztonsági csapat biztosítja a kulcs- és titkos hivatkozásokat (URI-k és ujjlenyomatok), amelyeket a DevOps-személyzet használ az alkalmazásaikban. A fejlesztőknek és az operátoroknak nincs szükségük adatsík-hozzáférésre. Arra összpontosítottunk, hogyan biztosítsuk a kulcstartóját. Hasonló figyelmet kell [fordítania a virtuális gépek,](https://azure.microsoft.com/services/virtual-machines/security/) [a tárfiókok](../storage/blobs/security-recommendations.md)és más Azure-erőforrások biztonságossá tétele kor.

> [!NOTE]
> Ez a példa bemutatja, hogyan key vault-hozzáférés van lezárva az éles környezetben. A fejlesztőknek rendelkezniük kell saját előfizetéssel vagy erőforráscsoporttal, amely teljes körű engedélyekkel rendelkezik a tárolók, a virtuális gépek és a tárfiók kezeléséhez, ahol az alkalmazást fejlesztik.

Javasoljuk, hogy a [Key Vault tűzfalainak és virtuális hálózatainak konfigurálásával](key-vault-network-security.md)további biztonságos hozzáférést biztosítson a kulcstartóhoz.

## <a name="resources"></a>Források

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)

* [Az Erőforrás-kezelő telepítésének és a klasszikus telepítésnek a megismerése](../azure-resource-manager/management/deployment-models.md)

* [Az RBAC kezelése az Azure PowerShell segítségével](../role-based-access-control/role-assignments-powershell.md)

* [Az RBAC kezelése a REST API-val](../role-based-access-control/role-assignments-rest.md)

* [RBAC a Microsoft Azure-hoz](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Ez a 2015-ös Microsoft Ignite konferenciavideó az Azure hozzáférés-kezelési és jelentéskészítési lehetőségeit ismerteti. Azt is vizsgálja, gyakorlati az Azure-előfizetések azure-előfizetések azure-előfizetések azure AD használatával való hozzáférés biztosításához ajánlott eljárásokat.

* [Webalkalmazásokhoz való hozzáférés engedélyezése az OAuth 2.0 és az Azure AD használatával](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Key Vault management REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A REST API-k a key vault programozott kezeléséhez, beleértve a Key Vault hozzáférési házirend beállítása hivatkozás.

* [KEY Vault REST API-k](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Kulcshozzáférés-vezérlés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Titkoskulcs-hozzáférés vezérlése](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Állítsa be](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) és [távolítsa el](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) a Key Vault hozzáférési szabályzat ot a PowerShell használatával.

## <a name="next-steps"></a>További lépések

Konfigurálja [a Key Vault tűzfalait és a virtuális hálózatokat.](key-vault-network-security.md)

A rendszergazda ima kezdési oktatóanyaga: [Mi az Azure Key Vault?](key-vault-overview.md)

A Key Vault használati naplózásáról az [Azure Key Vault naplózása című](key-vault-logging.md)témakörben talál további információt.

A kulcsok és titkos kulcsok Azure Key Vault használatával kapcsolatos további tudnivalókért olvassa el [a Kulcsok és titkos kulcsok – például a kulcsok és a titkos kulcsok című témakört.](https://msdn.microsoft.com/library/azure/dn903623.aspx)

Ha kérdése van a Key Vault, látogasson el a [fórumokon](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
