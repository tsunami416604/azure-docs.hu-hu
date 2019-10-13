---
title: Biztonságos hozzáférés a Key vaulthoz – Azure Key Vault | Microsoft Docs
description: Azure Key Vault, kulcsok és titkos kódok hozzáférési engedélyeinek kezelése. Lefedi a Key Vault hitelesítési és engedélyezési modelljét, valamint a kulcstartó biztonságossá tételét.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 04f4a71e6b54100e5a133958845cf732c2286b32
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301064"
---
# <a name="secure-access-to-a-key-vault"></a>Biztonságos hozzáférés a kulcstartóhoz

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. Mivel ezek az adatok érzékenyek és üzleti szempontból kritikus fontosságúak, a kulcstartóhoz való hozzáférést csak a jogosult alkalmazások és felhasználók engedélyezésével kell biztosítani. Ez a cikk a Key Vault hozzáférési modell áttekintését tartalmazza. Ismerteti a hitelesítést és az engedélyezést, valamint ismerteti a kulcstartók hozzáférésének biztonságossá tételét.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Hozzáférési modell áttekintése

A kulcstartóhoz való hozzáférést két interfész szabályozza: a **felügyeleti síkon** és az **adatsíkon**. A felügyeleti síkon a Key Vault saját maga felügyeli. Az ebben a síkban található műveletek közé tartozik a kulcstartók létrehozása és törlése, Key Vault tulajdonságok beolvasása és a hozzáférési házirendek frissítése. Az adatsíkon a Key vaultban tárolt adatmennyiséggel dolgozik. Kulcsok, titkos kódok és tanúsítványok hozzáadására, törlésére és módosítására is lehetőség van.

A kulcstartók bármelyik síkon való eléréséhez minden hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítéssel és engedélyezéssel kell rendelkeznie. A hitelesítés létrehozza a hívó identitását. Az engedélyezés meghatározza, hogy a hívó milyen műveleteket hajthat végre. 

Mindkét síkon Azure Active Directoryt (Azure AD) használ a hitelesítéshez. Az engedélyezéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC) használ, és az adatsík egy Key Vault hozzáférési házirendet használ.

## <a name="active-directory-authentication"></a>Active Directory-alapú hitelesítés

Amikor kulcstartót hoz létre egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlője számára. Mindkét síkon lévő hívónak regisztrálnia kell ebben a bérlőben, és hitelesítenie kell magát a kulcstartó eléréséhez. Mindkét esetben az alkalmazások kétféleképpen férhetnek hozzá Key Vaulthoz:

- **Felhasználói Plus alkalmazás-hozzáférés**: az alkalmazás a bejelentkezett felhasználó nevében fér hozzá Key Vaulthoz. Ilyen típusú hozzáférés például a Azure PowerShell és a Azure Portal. A felhasználói hozzáférés két módon adható meg. A felhasználók bármely alkalmazásból hozzáférhetnek Key Vaulthoz, vagy egy adott alkalmazást (más néven _összetett identitást_) kell használniuk.
- **Csak alkalmazáshoz való hozzáférés**: az alkalmazás démoni szolgáltatásként vagy háttérbeli feladatokként fut. Az alkalmazás identitása hozzáférést kap a kulcstartóhoz.

Mindkét típusú hozzáférés esetén az alkalmazás az Azure AD-vel is hitelesít. Az alkalmazás a [támogatott hitelesítési módszereket](../active-directory/develop/authentication-scenarios.md) használja az alkalmazás típusától függően. Az alkalmazás jogkivonatot ad a síkon lévő erőforráshoz a hozzáférés biztosításához. Az erőforrás az Azure-környezet alapján a felügyelet vagy az adatsík végpontja. Az alkalmazás a jogkivonatot használja, és egy REST API kérelmet küld Key Vaultnak. További információért tekintse át a [teljes hitelesítési folyamatot](../active-directory/develop/v2-oauth2-auth-code-flow.md).

A mindkét síkon történő hitelesítéshez használt egyetlen mechanizmus modellje számos előnnyel jár:

- A szervezetek központilag vezérelhetik a hozzáférést a szervezet összes kulcstartója számára.
- Ha a felhasználó elhagyja, azonnal elveszíti a hozzáférést a szervezet összes kulcstartójának.
- A szervezetek az Azure AD lehetőségeit használva szabhatják testre a hitelesítést, például lehetővé teszik a többtényezős hitelesítés használatát a további biztonság érdekében.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

Az alkalmazások végpontokon keresztül férnek hozzá a síkokhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy egy alkalmazás hozzáférést biztosítson a Key vaultban lévő kulcsok használatához, Key Vault hozzáférési szabályzattal kell megadnia az adatsíkok elérését. Ahhoz, hogy a felhasználó olvasási hozzáférést biztosítson Key Vault tulajdonságokhoz és címkékhez, de nem fér hozzá az adatokhoz (kulcsok, titkok vagy tanúsítványok), a felügyeleti sík hozzáférést biztosít a RBAC.

A következő táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférés @ no__t – 0plane | Hozzáférés végpontjai | Műveletek | Hozzáférés a @ no__t-0control mechanizmushoz |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globálisan:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Kulcstartók létrehozása, olvasása, frissítése és törlése<br><br>Key Vault hozzáférési szabályzatok beállítása<br><br>Key Vault címkék beállítása | Azure Resource Manager RBAC |
| Adatsík | **Globálisan:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: visszafejtés, titkosítás,<br> kicsomagolás, becsomagolás, ellenőrzés, aláírás,<br> beolvasás, Listázás, frissítés, létrehozás,<br> importálás, törlés, biztonsági mentés, visszaállítás<br><br> Titkok: beolvasás, Listázás, beállítás, törlés | Hozzáférési szabályzat Key Vault |

## <a name="management-plane-and-rbac"></a>Felügyeleti sík és RBAC

A felügyeleti síkon a RBAC (szerepköralapú Access Control) segítségével engedélyezheti a hívó által végrehajtható műveleteket. A RBAC-modellben minden Azure-előfizetés egy Azure AD-példánnyal rendelkezik. Hozzáférést biztosít a felhasználóknak, csoportoknak és alkalmazásoknak ebben a címtárban. A hozzáférés a Azure Resource Manager üzemi modellt használó Azure-előfizetés erőforrásainak kezeléséhez van megadva. A hozzáférés megadásához használja a [Azure Portal](https://portal.azure.com/), az [Azure CLI](../cli-install-nodejs.md), a [Azure PowerShell](/powershell/azureps-cmdlets-docs)vagy a [Azure Resource Manager REST API-kat](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Hozzon létre egy kulcstartót egy erőforráscsoporthoz, és kezelje a hozzáférést az Azure AD használatával. A felhasználók vagy csoportok számára engedélyezheti az erőforráscsoport kulcstárolóinak kezelését. A hozzáférést adott hatóköri szinten kell megadni a megfelelő RBAC-szerepkörök hozzárendelésével. Ahhoz, hogy hozzáférést biztosítson egy felhasználónak a kulcstartók kezeléséhez, egy előre meghatározott `key vault Contributor` szerepkört kell hozzárendelni a felhasználóhoz egy adott hatókörben. A következő hatókörökhöz rendelhet hozzá RBAC-szerepköröket:

- **Előfizetés**: az előfizetés szintjén hozzárendelt RBAC-szerepkör az adott előfizetésen belüli összes erőforráscsoport és erőforrás esetében érvényes.
- **Erőforráscsoport**: az erőforráscsoport szintjén HOZZÁRENDELt RBAC-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott**erőforrás: egy adott erőforráshoz hozzárendelt RBAC-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkör létezik. Ha egy előre meghatározott szerepkör nem felel meg az igényeinek, megadhatja saját szerepkörét. További információt a [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)című témakörben talál.

> [!IMPORTANT]
> Ha a felhasználó `Contributor` engedélyekkel rendelkezik a kulcstartó felügyeleti síkja számára, akkor a felhasználó egy Key Vault hozzáférési szabályzat beállításával saját maga is megadhatja az adatsíkon való hozzáférést. Szigorúan szabályozhatja, hogy ki `Contributor` szerepkör-hozzáférést biztosít a kulcstartóhoz. Győződjön meg arról, hogy csak a jogosult személyek férhetnek hozzá és kezelhetik a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Adatsík és hozzáférési szabályzatok

A Key vaulthoz Key Vault hozzáférési szabályzatok beállításával biztosíthatja az adatsíkok elérését. A hozzáférési szabályzatok beállításához a felhasználónak, csoportnak vagy alkalmazásnak `Contributor` engedéllyel kell rendelkeznie az adott kulcstartó felügyeleti síkja számára.

Egy kulcstartóban felhasználói, csoport-vagy alkalmazás-hozzáférési jogosultságot adhat adott műveletek végrehajtásához a kulcsok és titkok számára. A Key Vault a Key vaulthoz legfeljebb 1 024 hozzáférési szabályzatot támogat. Ha több felhasználó számára kíván hozzáférést biztosítani az adatsíkon, hozzon létre egy Azure AD biztonsági csoportot, és vegyen fel felhasználókat a csoportba.

<a id="key-vault-access-policies"></a>Key Vault hozzáférési házirendek külön engedélyeket biztosítanak a kulcsokhoz, a titkokhoz és a tanúsítványokhoz. A felhasználók csak a kulcsokhoz és a titkokhoz való hozzáférést adhatnak meg. A kulcsok, titkos kódok és tanúsítványok hozzáférési engedélyei a tároló szintjén találhatók. Key Vault hozzáférési házirendek nem támogatják a szemcsés, az objektum szintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. A Key Vault hozzáférési házirendjeinek beállításához használja a [Azure Portal](https://portal.azure.com/), az [Azure CLI](../cli-install-nodejs.md), a [Azure POWERSHELL](/powershell/azureps-cmdlets-docs)vagy a [Key Vault felügyeleti REST API-kat](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Key Vault hozzáférési szabályzatok a tár szintjén érvényesek. Ha a felhasználó engedélyt kap a kulcsok létrehozására és törlésére, akkor ezeket a műveleteket a kulcstartó összes kulcsán végrehajthatja.
>

Az adatsíkok hozzáférését a [Azure Key Vault virtuális hálózati szolgáltatásbeli végpontjának](key-vault-overview-vnet-service-endpoints.md)használatával korlátozhatja. A [tűzfalat és a virtuális hálózati szabályokat](key-vault-network-security.md) egy további biztonsági réteghez is konfigurálhatja.

## <a name="example"></a>Példa

Ebben a példában egy olyan alkalmazást fejlesztünk, amely egy tanúsítványt használ az SSL-hez, az Azure Storage-hoz az adatok tárolására, valamint egy RSA 2 048 bites kulcsot az aláírási műveletekhez. Az alkalmazás egy Azure-beli virtuális gépen (VM) (vagy egy virtuálisgép-méretezési csoporton) fut. Az alkalmazás titkos kulcsainak tárolására kulcstartót használhatunk. Az alkalmazás által az Azure AD-vel való hitelesítéshez használt rendszerindító tanúsítványt tároljuk.

Hozzáférésre van szükségünk a következő tárolt kulcsokhoz és titkos kulcsokhoz:
- **SSL-tanúsítvány**: az SSL-hez használatos.
- **Tárolási kulcs**: a Storage-fiók eléréséhez használatos.
- **RSA 2 048 bites kulcs**: aláírási műveletekhez használatos.
- Rendszerindítási **tanúsítvány**: az Azure ad-vel való hitelesítéshez használatos. A hozzáférés megadása után beolvashatja a Storage-kulcsot, és használhatja az RSA-kulcsot az aláíráshoz.

Meg kell határoznia a következő szerepköröket annak meghatározásához, hogy ki kezelheti, telepítheti és naplózhatja az alkalmazást:
- **Biztonsági csapat**: a KSH (a biztonsági tisztviselő) vagy hasonló közreműködők irodájában dolgozó személyzet. A biztonsági csapat feladata a titkok megfelelő védelme. A titkok tartalmazhatnak SSL-tanúsítványokat, RSA-kulcsokat az aláíráshoz, a kapcsolati karakterláncokhoz és a Storage-fiók kulcsaihoz.
- **Fejlesztők és operátorok**: az alkalmazást fejlesztő és az Azure-ban üzembe helyezett munkatársak. A csapat tagjai nem tartoznak a biztonsági személyzetbe. Nem férhetnek hozzá a bizalmas adatokhoz, például az SSL-tanúsítványokhoz és az RSA-kulcsokhoz. Csak az általuk telepített alkalmazások férhetnek hozzá a bizalmas adatokhoz.
- **Könyvvizsgálók**: Ez a szerepkör olyan közreműködők számára szól, akik nem tagjai a fejlesztési vagy általános informatikai munkatársainak. A biztonsági szabványoknak való megfelelés biztosítása érdekében áttekintik a tanúsítványok, kulcsok és titkok használatát és karbantartását. 

Van egy másik szerepkör, amely az alkalmazás hatókörén kívül esik: az előfizetés (vagy erőforráscsoport) rendszergazdája. Az előfizetés-rendszergazda beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapat számára. Hozzáférést biztosítanak a biztonsági csapathoz egy olyan erőforráscsoport használatával, amely az alkalmazás által igényelt erőforrásokkal rendelkezik.

A következő műveleteket kell engedélyeznie a szerepkörökhöz:

**Biztonsági csapat**
- Key vaultok létrehozása.
- Key Vault naplózás bekapcsolása.
- Kulcsok és titkos kódok hozzáadása.
- Biztonsági másolatokat készíthet a kulcsokról a vész-helyreállításhoz.
- Key Vault hozzáférési szabályzatok beállításával engedélyezheti a felhasználók és az alkalmazások számára az adott műveletekhez szükséges engedélyeket.
- Rendszeresen a kulcsokat és a titkokat.

**Fejlesztők és operátorok**
- A rendszerindítási és SSL-tanúsítványok (ujjlenyomatai megfelelnek-EK), a tárolási kulcs (titkos URI), valamint az RSA-kulcs (kulcs URI) az aláíráshoz való hivatkozásainak beolvasása.
- Az alkalmazás fejlesztése és üzembe helyezése a kulcsok és a titkos kódok programozott módon való eléréséhez.

**Ellenőrök**
- Tekintse át a Key Vault naplókat a kulcsok és titkok megfelelő használatának megerősítéséhez, valamint az adatbiztonsági szabványoknak való megfelelés ellenőrzéséhez.

A következő táblázat összefoglalja a szerepkörök és alkalmazások hozzáférési engedélyeit. 

| Szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei |
| --- | --- | --- |
| Biztonsági csapat | Key Vault közreműködő | Kulcsok: biztonsági mentése, létrehozása, törlése, beolvasása, importálása, listázása, visszaállítása<br>Titkok: minden művelet |
| Fejlesztők és @ no__t – 0operators | Key Vault üzembe helyezési engedély<br><br> **Megjegyzés**: ez az engedély lehetővé teszi, hogy a telepített virtuális gépek a kulcstartóból beolvassák a titkos kulcsokat. | None |
| Ellenőrök | None | Kulcsok: listája<br>Titkos kulcsok: listája<br><br> **Megjegyzés**: ez az engedély lehetővé teszi, hogy a könyvvizsgálók megvizsgálják a naplókban nem kibocsátott kulcsok és titkos kódok attribútumait (címkéket, aktiválási dátumokat, lejárati dátumokat). |
| Jelentkezés | None | Kulcsok: aláírása<br>Titkos kulcsok: beolvasása |

A három csoport szerepköreinek más erőforrásokhoz való hozzáférésre van szükségük Key Vault engedélyekkel együtt. A virtuális gépek (vagy a Azure App Service Web Apps funkciójának üzembe helyezéséhez) a fejlesztőknek és a kezelőknek `Contributor` hozzáféréssel kell rendelkezniük ezekhez az erőforrásokhoz. A könyvvizsgálóknak olvasási hozzáféréssel kell rendelkezniük ahhoz a Storage-fiókhoz, ahol a Key Vault-naplókat tárolják.

A tanúsítványok, a hozzáférési kulcsok és a titkos kódok programozott módon történő üzembe helyezésével kapcsolatos további információkért tekintse meg a következő forrásokat:
- Megtudhatja, hogyan [helyezhet üzembe tanúsítványokat a virtuális gépeken egy ügyfél által felügyelt kulcstartóból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogbejegyzés).
- Töltse le az [Azure Key Vault-ügyfél mintáit](https://www.microsoft.com/download/details.aspx?id=45343). Ez a tartalom azt szemlélteti, hogyan használható a rendszerindítási tanúsítvány az Azure AD-ben való hitelesítéshez a kulcstartó eléréséhez.

A hozzáférési engedélyek többségét a Azure Portal használatával adhatja meg. A részletes engedélyek megadásához Azure PowerShell vagy az Azure CLI-t használhatja.

Az ebben a szakaszban található PowerShell-kódrészletek a következő feltételezésekkel vannak összeépítve:
- Az Azure AD-rendszergazda biztonsági csoportokat hozott létre a következő három szerepkör képviseletére: contoso Security Team, contoso app DevOps és contoso app Auditors. A rendszergazda hozzáadta a felhasználókat a saját csoportjaihoz.
- Az összes erőforrás a **ContosoAppRG** erőforráscsoport területén található.
- A Key Vault naplók a **contosologstorage** Storage-fiókban tárolódnak. 
- A **ContosoKeyVault** Key Vault és a **contosologstorage** Storage-fiók ugyanazon az Azure-helyen található.

Az előfizetés rendszergazdája a `key vault Contributor` és a `User Access Administrator` szerepkört a biztonsági csapathoz rendeli. Ezek a szerepkörök lehetővé teszik a biztonsági csapatnak, hogy kezelje a más erőforrásokhoz és kulcstartóhoz való hozzáférést, mindkettőt a **ContosoAppRG** erőforráscsoporthoz.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A biztonsági csapat létrehoz egy kulcstartót, és beállítja a naplózási és hozzáférési engedélyeket. Key Vault hozzáférési házirend engedélyeivel kapcsolatos további információkért lásd: [tudnivalók Azure Key Vault kulcsokról, titkokról és tanúsítványokról](about-keys-secrets-and-certificates.md).

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

A definiált egyéni szerepkörök csak ahhoz az előfizetéshez rendelhetők hozzá, amelyben a **ContosoAppRG** -erőforráscsoport létrejött. Ha egyéni szerepkört szeretne használni más előfizetésekben lévő más projektekhez, vegyen fel más előfizetéseket a szerepkör hatóköréhez.

A DevOps-munkatársak esetében a Key vaulthoz tartozó egyéni szerepkör-hozzárendelés @no__t – 0 jogosultság az erőforráscsoport hatóköre. Csak a **ContosoAppRG** -erőforráscsoport számára létrehozott virtuális gépek férhetnek hozzá a titkokhoz (SSL és rendszerindítási tanúsítványok). A DevOps-tag által más erőforráscsoportok által létrehozott virtuális gépek nem férhetnek hozzá ezekhez a titkokhoz, még akkor is, ha a virtuális gép rendelkezik a titkos URI-k használatával.

Példánkban egy egyszerű forgatókönyvet ismertetünk. A valós életbeli forgatókönyvek összetettebbek lehetnek. Az engedélyeket a Key Vault igényei szerint módosíthatja. Feltételezzük, hogy a biztonsági csapat biztosítja a kulcs-és titkos referenciákat (URI-k és ujjlenyomatai megfelelnek), amelyeket az DevOps munkatársai használnak az alkalmazásaikban. A fejlesztőknek és az operátoroknak nincs szükségük adatsíkon való hozzáférésre. A Key Vault biztonságossá tételére koncentrálunk. [A virtuális gépek, a](https://azure.microsoft.com/services/virtual-machines/security/) [Storage-fiókok](../storage/common/storage-security-guide.md)és az egyéb Azure-erőforrások biztonságossá tételéhez hasonló szempontokat kell figyelembe venni.

> [!NOTE]
> Ez a példa azt mutatja be, hogy a Key Vault hozzáférés hogyan legyen zárolva az éles környezetben. A fejlesztőknek saját előfizetéssel vagy erőforráscsoporthoz kell rendelkezniük, amely teljes körű engedélyekkel rendelkezik a tárolók, virtuális gépek és az alkalmazás fejlesztéséhez szükséges Storage-fiók kezeléséhez.

Javasoljuk, hogy [Key Vault tűzfalak és virtuális hálózatok konfigurálásával](key-vault-network-security.md)további biztonságos hozzáférést állítson be a kulcstartóhoz.

## <a name="resources"></a>Segédanyagok és eszközök

* [Azure AD-RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)

* [A Resource Manager és a klasszikus üzembe helyezés ismertetése](../azure-resource-manager/resource-manager-deployment-model.md) 

* [RBAC kezelése Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [RBAC kezelése a REST API](../role-based-access-control/role-assignments-rest.md)

* [Microsoft Azure RBAC](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Ez a 2015-es Microsoft Ignite konferencia videó az Azure-ban elérhető hozzáférés-kezelési és jelentéskészítési képességeket ismerteti. Emellett az Azure-előfizetések Azure AD-vel való hozzáférésének biztosítására vonatkozó ajánlott eljárásokat is vizsgálja.

* [Hozzáférés engedélyezése webalkalmazásokhoz a OAuth 2,0 és az Azure AD használatával](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Key Vault felügyeleti REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A REST API-k referenciája a kulcstartó programozott kezeléséhez, beleértve a Key Vault hozzáférési házirend beállítását.

* [Key Vault REST API-k](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Kulcshozzáférés-vezérlés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Titkoskulcs-hozzáférés vezérlése](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* Key Vault hozzáférési szabályzat [beállítása](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) és [eltávolítása](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) a PowerShell használatával.
  
## <a name="next-steps"></a>Következő lépések

[Key Vault tűzfalak és virtuális hálózatok](key-vault-network-security.md)konfigurálása.

Az első lépéseket ismertető oktatóanyagért tekintse meg a [Mi az Azure Key Vault?](key-vault-overview.md)című témakört.

A kulcstartó használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

A kulcsok és a titkos kódok Azure Key Vault használatával történő használatáról további információt a [kulcsok és titkok](https://msdn.microsoft.com/library/azure/dn903623.aspx)című témakörben talál.

Ha kérdése van a Key Vaultval kapcsolatban, látogasson el a [fórumokra](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
