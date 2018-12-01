---
title: Az Azure Key Vault biztonságos |} A Microsoft Docs
description: Hozzáférési engedélyek Azure Key Vault, a kulcsok és titkos kulcsok kezeléséhez. A Key Vault, és hogyan védheti meg a kulcstartó hitelesítési és engedélyezési modellt ismerteti.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 23f02f87b75cd41d1a56a388e4526be6d9a2e119
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682732"
---
# <a name="secure-your-key-vault"></a>Kulcstartó védelme
Az Azure Key Vault egy felhőszolgáltatás, amely megvédi a titkosítási kulcsok és titkos kulcsokat (például a tanúsítványok, kapcsolati karakterláncok és jelszavak). Mivel ezek az adatok bizalmas, és az üzletileg kritikus, meg kell biztonságos hozzáférés a a kulcstartók, így csak a hitelesített alkalmazások és felhasználók számára. Ez a cikk áttekintést a Key Vault hozzáférés-modellt. Hitelesítési és engedélyezési ismerteti, és hogyan biztosíthat hozzáférést.

## <a name="overview"></a>Áttekintés
Két külön felületen key vault elérését Ön szabályozza: az *felügyeleti sík* és a *adatsík*. Mindkét sík esetében megfelelő hitelesítés és engedélyezés részére egy kulcstartó eléréséhez kell rendelkeznie egy hívó (felhasználó vagy alkalmazás). Hitelesítés a hívó azonosítását hoz létre, az engedélyezés pedig meghatározza a műveletek a hívó hajthat végre.

Mindkét sík hitelesítést, az Azure Active Directory (Azure AD) használja. A hitelesítéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC), míg az adatsík használja a Key Vault hozzáférési szabályzattal.

## <a name="authenticate-by-using-azure-active-directory"></a>Hitelesítés Azure Active Directory használatával
Ha az Azure-előfizetésében kulcstartót hoz létre, azt automatikusan az előfizetéshez tartozó Azure AD-bérlő társítva. Valamennyi hívót regisztrálni kell az ehhez a bérlőhöz, és a key vault eléréséhez hitelesítenie kell. Ez a követelmény vonatkozik mindkét felügyeleti sík és az adatsík-hozzáférés. Mindkét esetben egy alkalmazás képes elérni a Key Vault két módon:

* **felhasználó + alkalmazás-hozzáférés**: használja olyan alkalmazásokkal, amelyek a Key Vault elérése a bejelentkezett felhasználó nevében. Az Azure PowerShell és az Azure Portalon az ilyen típusú hozzáférés példák. Hozzáférés biztosítása a felhasználók két módja van: 
  - Key Vault elérése bármilyen alkalmazásból.
  - Key Vault elérése csak akkor, amikor egy adott alkalmazáshoz (néven összetett identitás) használnak.

* **csak az alkalmazásra vonatkozó hozzáférési**: használja olyan alkalmazásokkal, amelyek futtató démonszolgáltatásokat, vagy a háttérben futó feladatok. A kulcstartó-hozzáférés az alkalmazásidentitás számára engedélyezett.

Mindkét típusú alkalmazásokat, az alkalmazás hitelesíti az Azure ad-vel egyikének használatával a [támogatott hitelesítési módszerek](../active-directory/develop/authentication-scenarios.md), és -tokenbeolvasás. A használt hitelesítési módszert az alkalmazás típusától függ. Az alkalmazás ezután ezt a tokent használja, és a Key Vault REST API-val kérelmet küld. Felügyeleti sík kérelmek egy Azure Resource Manager végpontjához kerülnek. Az adatsík elérésekor az alkalmazás közvetlenül a Key Vault-végpont a ismerteti. További információkért lásd: a [teljes hitelesítési folyamatról](../active-directory/develop/v1-protocols-oauth-code.md). 

Az erőforrás nevét, amelyhez az alkalmazás kéri a jogkivonat attól függ, melyik adatsík az alkalmazás fér hozzá. Az erőforrásnév vagy felügyeleti sík végpontot, vagy egy adatsík végpontja, attól függően, az Azure-környezetben. További részletekért lásd a táblázat a cikk későbbi részében.

Egy egyetlen hitelesítési mechanizmust alkalmaz, mindkét sík kellene a néhány előnyökkel jár:

* Szervezetek központilag vezérelhetik a szervezeten belüli összes kulcstartó.
* Ha egy felhasználó távozik, ő azonnal elveszíti a hozzáférést a szervezeten belüli összes kulcstartó.
* Szervezetek testre szabhatja a beállításokat az Azure ad-ben (például multi-factor authentication engedélyezésével a fokozott biztonság) hitelesítést.

## <a name="the-management-plane-and-the-data-plane"></a>A felügyeleti sík és az adatsík
A felügyeleti sík használatával kezelheti a Key Vault magát. Ez magában foglalja a műveleteket, például attribútumok kezelése és az adatsík az adathozzáférési házirendek beállítása. Az adatsík használatával hozzáadása, törlése, módosítása és a kulcsok, titkos kódok és tanúsítványok a Key Vault használata.

A következő táblázatban a különböző végpontok keresztül érik el a felügyeleti sík és az adatsík felületei. A táblázat második oszlopa ismerteti ezeket a végpontokat különböző Azure-környezetekben a DNS-neveit. A harmadik oszlop az egyes hozzáférési síkokról elvégezhető műveleteket ismerteti. Minden hozzáférési sík is rendelkezik a saját hozzáférés-vezérlési mechanizmus. Felügyeleti sík hozzáférés-vezérlés az Azure Resource Manager szerepköralapú hozzáférés-vezérlés (RBAC) használatával van beállítva. Adatsík-hozzáférés vezérlése a Key Vault hozzáférési szabályzattal van beállítva.

| Hozzáférési sík | Hozzáférés végpontjai | Műveletek | Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík |**Globálisan:**<br> management.azure.com:443<br><br> **Az Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |A Key Vault létrehozása/olvasása/frissítése/törlése <br> A kulcstartó hozzáférési házirendjeinek beállítása<br>A Key Vault címkék beállítása |Az Azure Resource Manager-RBAC |
| Adatsík |**Globálisan:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Az Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 |Kulcsok: visszafejtés, titkosítása, UnwrapKey, WrapKey, győződjön meg arról, bejelentkezési, Get, lista, frissítés, létrehozás, importálás, törlése, biztonsági mentés, visszaállítás<br><br> Titkos kulcsok: Beolvasás, Listázás, Beállítás, Törlés |Key Vault hozzáférési szabályzattal |

Felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik. Például ha szeretne hozzáférést biztosítani egy alkalmazás-kulcsok használata a key vaultban, akkor csak kell adatsík-hozzáférés biztosítása. Hozzáférést biztosít a Key Vault hozzáférési házirendek. Ezzel szemben olyan felhasználó, aki el kell olvasnia a Key Vault tulajdonságok és címkék, de nem érheti el adatait (kulcsok, titkos kódok vagy tanúsítványokat), csak a felügyeleti sík hozzáférés kell. Hozzáférést biztosít az RBAC a felhasználónak olvasási hozzáférés hozzárendelésével.

## <a name="management-plane-access-control"></a>Felügyeleti sík hozzáférés-vezérlése
A felügyeleti sík olyan műveleteket tartalmaz, amelyek hatással vannak a key vaultban, például:

- Létrehozás, vagy törölje a key vault.
- Bevezetés a tárolók listáját az előfizetéshez.
- A Key Vault tulajdonságait (például a Termékváltozat és címkék) beolvasása.
- A beállítás a Key Vault hozzáférési házirendeket vezérlő felhasználói és hozzáférési kulcsok és titkos kulcsok.

A felügyeleti sík hozzáférés-vezérlése RBAC használatával történik.  

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Minden Azure-előfizetés rendelkezik egy Azure AD-példányt. Hozzáférést biztosít a felhasználóknak, csoportoknak és alkalmazásoknak a címtárból az Azure-előfizetés az Azure Resource Manager-alapú üzemi modellt használó erőforrásainak kezeléséhez. Az ilyen típusú hozzáférés-vezérlés RBAC nevezzük. A hozzáférés az [Azure Portal](https://portal.azure.com/), az [Azure Parancssorifelület-eszközök](../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs) vagy az [Azure Resource Manager REST API-k](https://msdn.microsoft.com/library/azure/dn906885.aspx) használatával kezelhető.

Hozzon létre egy kulcstartót egy erőforráscsoportban, és a felügyeleti sík hozzáférés vezérlése az Azure AD használatával. Adhat például felhasználóknak vagy egy csoport egy erőforráscsoport kulcstartóit kezeljék lehetővé teszi.

Felhasználók, csoportok és alkalmazások egy adott hatókörnél hozzáférést biztosíthat a megfelelő RBAC-szerepkörök hozzárendelésével. Például hozzáférést biztosítani egy felhasználónak kulcstartóit kezeljék, hozzárendelhet egy előre meghatározott Key Vault-közreműködő szerepkört ehhez a felhasználóhoz egy adott hatókörnél. A hatókör ebben az esetben lenne egy előfizetés, erőforráscsoport vagy egy adott kulcstartó. Az előfizetési szinten hozzárendelt szerepkör az összes erőforráscsoportra és erőforrások adott előfizetésen belül is vonatkozik. Az erőforráscsoport szintjén hozzárendelt szerepkör az erőforráscsoport valamennyi erőforrására vonatkozik. Egy adott erőforráshoz rendelt szerepkör adott erőforrásra vonatkozik. Számos előre definiált szerepkörök állnak rendelkezésre (lásd: [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)). Ha egy előre meghatározott szerepkör nem fér el az igényeinek, megadhatja saját szerepkört.

> [!IMPORTANT]
> Vegye figyelembe, hogy ha egy felhasználó közreműködői engedélyekkel a kulcstartó felügyeleti síkjához is biztosítson engedélyezhet hozzáférést az adatsík kulcstartó-hozzáférési házirend beállításával. Ezért meg kell mértékben vezérelhetik kinek van hozzáférése a kulcstartók közreműködő. Győződjön meg arról, hogy csak az arra jogosult személyek eléréséhez, és a kulcstartók, kulcsok, titkos kódok és tanúsítványok kezelése.
> 
> 

## <a name="data-plane-access-control"></a>Adatsík-hozzáférés vezérlése
A Key Vault adatok síkjával végzett műveletek tárolt objektumokat, például a kulcsok, titkos kódok és tanúsítványok érvényesek. Kulcsműveletek például létrehozása, importálása, frissítése, listázása, biztonsági mentése és állítsa vissza a kulcsokat. Titkosítási műveletek kereszt, győződjön meg arról, titkosítása, visszafejtése, wrap, kicsomagolása, címkék beállítása és más kulcsok attribútumainak beállítása. Hasonlóképpen, titkos műveletek közé tartozik, get, set, list, törölje.

Adatsík-hozzáférés a kulcstartó hozzáférési házirendjeinek beállításán keresztül biztosít. Egy felhasználó, csoport vagy alkalmazás kulcstartó tudni, hogy a kulcstartó hozzáférési házirendjeinek beállítása a felügyeleti sík közreműködői engedélyekkel kell rendelkeznie. Meg lehet adni egy felhasználó, csoport vagy alkalmazás-hozzáférés a kulcstartó kulcsaihoz vagy titkos kulcsaihoz kapcsolódó, meghatározott műveletek végrehajtásához. A Key Vault támogatja a key vault legfeljebb 1024 hozzáférésiszabályzat-bejegyzést. Adatsík-hozzáférés biztosítása a több felhasználó, hozzon létre egy Azure AD biztonsági csoportot, és felhasználók hozzáadása a csoporthoz.

### <a name="key-vault-access-policies"></a>A Key Vault hozzáférési szabályzatok
A Key Vault hozzáférési szabályzatok külön engedélyezheti a kulcsok, titkos kódok és tanúsítványok. Adhat például a felhasználói hozzáférés kizárólag a kulcsokhoz és a titkos kulcsokhoz nem. Engedélyeket az eléréséhez, kulcsok, titkos kódok és tanúsítványok olyan tárolószinten engedélyezett. Key Vault hozzáférési szabályzattal részletes, az Objektumszintű engedélyeket, például egy adott kulcs, a titkos kulcsot vagy a tanúsítvány nem támogatja. Használhatja a [az Azure portal](https://portal.azure.com/), a [Azure CLI-eszközeit](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), vagy a [Key Vault felügyeleti REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx) hozzáférési szabályzatok beállítása a key vault.

> [!IMPORTANT]
> A Key Vault hozzáférési házirendek tárolószinten érvényesek. Például, ha egy felhasználó engedélyt kap kulcsok létrehozására és törlésére, az adott kulcstartó összes kulcsa esetén végrehajthatja ezeket a műveleteket.

A hozzáférési szabályzatokkal mellett is korlátozhatja adatsík-hozzáférés használatával [virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Konfigurálja [tűzfalak és virtuális hálózati szabályok](key-vault-network-security.md) számára egy további biztonsági réteget.

## <a name="example"></a>Példa
Tegyük fel, adatokat és az aláírási műveletekhez RSA 2048 bites kulcs tárolására szolgáló Azure Storage, az SSL-tanúsítványt használó alkalmazás fejlesztésén. Tegyük fel, ez az alkalmazás fut az Azure virtuális gépeken (vagy egy virtuálisgép-méretezési csoportot). A key vault használata az összes titkos Alkalmazáskulcs tárolására, és az Azure AD-hitelesítést az alkalmazás által használt azon rendszerindítási tanúsítvány tárolására.

Itt található egy összefoglaló fájltípusok közül a kulcsok és titkos kódok tárolása:

* **SSL-tanúsítvány**: SSL-hez használt.
* **Tárkulcs**: a tárfiók eléréséhez használt.
* **RSA 2048 bites kulcsot**: aláírási műveletekhez használatos.
* **Rendszerindítási tanúsítvány**: az Azure AD-hitelesítésre használható. Miután engedélyezett hozzáférést a rendszer beolvassa a tárfiók kulcsát, és használja az RSA-kulcsot az aláíráshoz.

Most nézzük meg, kik felügyelete, telepítése és naplózzák ezt az alkalmazást. Ebben a példában három szerepkört használunk.

* **Biztonsági csapat**: általában az informatikai munkatársak a CSO (biztonsági vezető), vagy azzal egyenértékű az irodából. Ez a csapat felelős a megfelelő személyek, a titkos kulcsok. Ilyenek például az SSL-tanúsítványok, a kapcsolati karakterláncokhoz, aláíráshoz használt RSA-kulcsok és a tárfiók kulcsait.
* **A fejlesztők/operátorok**: azok számára, akik fejlesztette az alkalmazást, és ezután telepítheti az Azure-ban. Általában ezek még nem része a biztonsági csapatnak, és ezért azok nem fér hozzá a bizalmas adatok, például SSL-tanúsítványok és az RSA-kulcsok. Csak az általuk üzembe helyezett alkalmazásnak hozzá kell férniük azokat az objektumokat.
* **Ellenőrök**: általában külön csoport, akik, fejlesztők és általános informatikai személyzettől elkülönítve. Felelősségét, hogy tekintse át a használatának és karbantartásának tanúsítványok, kulcsok és titkos kulcsok biztonsági szabványoknak való megfelelőség érdekében. 

Egy szerepkör, amely itt nem foglalkozik az alkalmazás, de Fontos megemlíteni van. A szerepkör az előfizetés (vagy erőforráscsoportot) rendszergazda. Az előfizetés-rendszergazda beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapatnak. Az előfizetés-rendszergazda hozzáférést biztosít a biztonsági csapat, amely tartalmazza az alkalmazás által igényelt erőforrásokra erőforráscsoporttal.

Most nézzük meg, milyen műveletet hajtanak végre az egyes szerepkörök a jelen alkalmazás összefüggésében.

* **Biztonsági csapat**
  * Kulcstartó létrehozása.
  * Kapcsolja be a Key Vault naplózása.
  * Hozzáadja a kulcsok vagy titkos kódokkal.
  * Vész-helyreállítási kulcsok biztonsági másolatának hoz létre.
  * Beállítja a Key Vault hozzáférési szabályzattal engedélyeket a felhasználóknak és alkalmazásoknak meghatározott műveletek elvégzéséhez.
  * Rendszeres időközönként összesíti a kulcsok vagy titkos kódokkal.
* **Fejlesztők/operátorok**
  * A biztonsági csapat rendszerindítási és SSL-tanúsítványok (ujjlenyomatok), tárkulcs (titkos URI) és aláírási kulcs (kulcs URI) referenciák beszerzése.
  * Fejlesztés és hozzáférő kulcsok és titkos kulcsok programozott módon üzembe helyezése.
* **Ellenőrök**
  * Helyes kulcs vagy titkos kódja használatának és adatok biztonsági szabványoknak való megfelelőség megerősítéséhez tekintse át a használati naplók.

Most nézzük meg, milyen hozzáférési engedélyekre szükség minden egyes szerepkör és az alkalmazás hozzárendelt feladataik elvégzéséhez. 

| Felhasználói szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei |
| --- | --- | --- |
| Biztonsági csapat |Key Vault-közreműködő |Kulcsok: biztonsági mentése, létrehozása, törlése, beolvasása, importálása, listázása, visszaállítása <br> Titkos kulcsok: összes |
| A fejlesztők/operátorok |A Key Vault-üzembehelyezési engedély, úgy, hogy az általuk üzembe helyezett virtuális gépek lehet beolvasni a titkos kulcsokat a kulcstartóból. |None |
| Ellenőrök |None |Kulcsok: listája<br>Titkos kulcsok: listája |
| Alkalmazás |None |Kulcsok: aláírása<br>Titkos kulcsok: beolvasása |

> [!NOTE]
> Ellenőrök lista engedély szükséges a a kulcsok és titkos kulcsok és titkos kódok, amelyek nem kerülnek naplózásra a naplók attribútumainak kivizsgálhassák. Ezek az attribútumok címkék, aktiválási és lejárati dátumok közé tartozik.
> 
> 

Key Vault-engedélyek mellett mindhárom szerepkörnek is kell más erőforrásokhoz való hozzáférést. Például, hogy a virtuális gépek (vagy az Azure App Service Web Apps funkcióját) üzembe helyezése, fejlesztők és üzemeltetők is közreműködői hozzáférés szükséges adott erőforrástípusokhoz. Ellenőrök kell olvasási hozzáférést a tárfiókhoz a Key Vault naplóinak tárolására.

A lépéseknek az ismertetése, ez a cikk a biztonságossá tétele a key vaulthoz való hozzáférés, mivel azt a témával kapcsolatos fogalmak csak ismertetéséhez. Tanúsítványok telepítését, és programozott módon hozzáférő kulcsok és titkos kulcsok kapcsolatos részleteket máshol terjed ki. Például:

- Virtuális gépek a Key vaultban tárolt tanúsítványok telepítését tárgyalja [üzembe helyezése tanúsítványok virtuális gépekhez ügyfél által felügyelt Key vaultból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogbejegyzés).
- A [Azure Key Vault ügyfél minták letöltése](https://www.microsoft.com/download/details.aspx?id=45343) azt ábrázolja, hogyan használja a rendszerindítási tanúsítvány hitelesítéséhez az Azure AD részére egy kulcstartó eléréséhez.

A legtöbb a hozzáférési engedélyeket adhat az Azure portal használatával. Részletes engedélyezés, szüksége lehet az Azure PowerShell vagy a parancssori felület használatával a kívánt eredmények eléréséhez. 

A következő PowerShell-kódtöredékek esetén a következőket vesszük alapul:

* Az Azure AD-rendszergazda hozott létre biztonsági csoportokat, amelyek a (a Contoso biztonsági csapat, Contoso alkalmazás fejlesztői és és a Contoso alkalmazás ellenőrei) három szerepkört képviselik. A rendszergazda felhasználókat is hozzáadott, a csoportokat, amelyekhez tartoznak.
* **ContosoAppRG**: az az erőforráscsoport, amelyben minden erőforrás megtalálható. **contosologstorage**: a naplók tárolási helye. 
* A key vault **ContosoKeyVault**, és a Key Vault-naplók használt tárfiók **contosologstorage**, Azure-beli azonos helyen kell lennie.

Először az előfizetés-rendszergazda rendeli `key vault Contributor` és `User Access Administrator` szerepköröket a biztonsági csapatnak. Ezek a szerepkörök lehetővé teszik a biztonsági csapat más erőforrásokhoz való hozzáférés kezelésére, és a ContosoAppRG erőforráscsoport kulcstartóit kezeljék.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A következő parancsfájl bemutatja, hogyan a biztonsági csapat hozzon létre egy kulcstartót, és állítsa be a naplózást és a hozzáférési engedélyeket. A Key Vault hozzáférési házirend engedélyekkel kapcsolatos részletekért lásd: [kapcsolatos Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok](about-keys-secrets-and-certificates.md).

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

A megadott egyéni szerepkör csak az előfizetéshez rendelhető hozzá, a `ContosoAppRG` erőforráscsoportot kell létrehozni. Ha ugyanazon egyéni szerepköröket más előfizetések más projektekhez használható, a hatókör rendelkezhet több előfizetést is hozzáadtak.

Az erőforráscsoport a fejlesztők és üzemeltetők számára a "üzembe helyezés/művelet" engedélyezéséhez az egyéni szerepkör-hozzárendelés hatókörét. Ez lehetővé teszi, hogy csak virtuális gépek az erőforráscsoportban létrehozott `ContosoAppRG` hozzáférése legyen a titkos kulcsok (az SSL-tanúsítványt és a rendszerindítási tanúsítványt). A fejlesztők által másik erőforráscsoportban létrehozott virtuális gépeket, és operátorok csapat egyik tagja többé nem fér hozzá a titkos adatait, még akkor is, ha rendelkezik a titkos URI-k.

Ez a példa bemutatja egy egyszerű forgatókönyvet. Lehet, hogy a valós életbeli forgatókönyvek összetettebb, és engedélyek a kulcstartóba, szükség szerint módosíthatja. Ebben a példában feltételezzük, a biztonsági csapat nyújt a kulcs- és titkoskulcs-referenciákat (URI-k és ujjlenyomatok), amely a fejlesztők és üzemeltetők kell hivatkoznia biztosítani az alkalmazásokban. A fejlesztők és üzemeltetők nem igényelnek semmilyen adatsík-hozzáférést. Ebben a példában legfőképpen a kulcstartó biztonságossá tenni. Hasonló figyelmet biztonságossá tételéhez adjon [a virtuális gépek](https://azure.microsoft.com/services/virtual-machines/security/), [tárfiókok](../storage/common/storage-security-guide.md), és más Azure-erőforrások.

> [!NOTE]
> Ez a példa bemutatja, hogyan lesz zárolva a Key Vault hozzáférés, éles környezetben. A fejlesztők számára a saját előfizetésekhez vagy erőforráscsoportokhoz amelyek teljes körű engedélyekkel a tárolóra, a virtuális gépek és a storage-fiók kezeléséhez, a fejlesztés, ahol az alkalmazás kell rendelkeznie.

Határozottan javasoljuk, hogy a biztonságos hozzáférés a kulcstartó szerint további [konfigurálása a Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md).

## <a name="resources"></a>További források
* [Az Azure Active Directory szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)
  
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel](../role-based-access-control/role-assignments-powershell.md)
  
* [A REST API-val szerepköralapú hozzáférés-vezérlés kezelése](../role-based-access-control/role-assignments-rest.md)
  
* [Szerepköralapú hozzáférés-vezérlés az Ignite-tól a Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  A 2015 Microsoft Ignite konferencia videó ismerteti, hozzáférés-kezelési és jelentéskészítési lehetőségeket az Azure-ban. Azt is bemutatja, ajánlott eljárások az Azure-előfizetésekhez való hozzáférés biztosítása az Azure AD használatával.
* [OAuth 2.0 és az Azure AD használatával webes alkalmazásokhoz való hozzáférés engedélyezése](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Key Vault-felügyeleti REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ez a dokumentum referenciaként szolgál a REST API-kat a kulcstartó programozott módon való kezelése,, a Key Vault hozzáférési házirend beállításával együtt.
* [A Key Vault REST API-k](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Kulcshozzáférés-vezérlés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Titkoskulcs-hozzáférés vezérlése](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Állítsa be](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) és [eltávolítása](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) Key Vault hozzáférési szabályzattal PowerShell-lel
  
## <a name="next-steps"></a>További lépések
[Key Vault-tűzfalak és virtuális hálózatok konfigurálása](key-vault-network-security.md)

A rendszergazdáknak szóló bevezető oktatóanyagért tekintse meg a [Bevezetés az Azure Key Vault használatába](key-vault-get-started.md) című cikket.

A kulcstartó használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

Az Azure Key Vault kulcsok és titkos kulcsok használatával kapcsolatos további információkért lásd: [kulcsok és titkok](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Ha a Key Vaulttal kapcsolatban kérdése van, keresse fel a [fórumok](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

