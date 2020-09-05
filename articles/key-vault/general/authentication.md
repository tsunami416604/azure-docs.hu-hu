---
title: Hitelesítés Azure Key Vault
description: Útmutató a Azure Key Vault való hitelesítéshez
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: d247e657e93afd0c43ecee1154c542398304d8dd
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89481375"
---
# <a name="authenticate-to-azure-key-vault"></a>Hitelesítés Azure Key Vault

Azure Key Vault lehetővé teszi a titkok tárolását és a terjesztésük irányítását egy központi, biztonságos Felhőbeli tárházban, amely szükségtelenné teszi a hitelesítő adatok tárolását az alkalmazásokban. Az alkalmazásoknak csak a Key Vault futtatásával kell hitelesíteniük a titkok eléréséhez.

## <a name="app-identity-and-security-principals"></a>Alkalmazás-identitás és rendszerbiztonsági tag

A Key Vault-alapú hitelesítés az [Azure Active Directory (Azure ad)](/azure/active-directory/fundamentals/active-directory-whatis)szolgáltatással együtt működik, amely az adott **rendszerbiztonsági tag**identitásának hitelesítéséhez felelős.

A rendszerbiztonsági tag egy olyan objektum, amely az Azure-erőforrásokhoz hozzáférést kérő felhasználót, csoportot, szolgáltatást vagy alkalmazást jelöl. Az Azure egy egyedi **objektumazonosítót** rendel minden rendszerbiztonsági tag számára.

* A **felhasználói** rendszerbiztonsági tag azonosítja azt a személyt, aki Azure Active Directory profillal rendelkezik.

* A **csoport** rendszerbiztonsági tagja a Azure Active Directoryban létrehozott felhasználók készletét azonosítja. A csoportba tartozó összes szerepkör vagy engedély a csoport összes felhasználója számára elérhető.

* Az **egyszerű szolgáltatásnév** olyan rendszerbiztonsági tag, amely egy alkalmazás vagy szolgáltatás identitását jelenti, azaz egy kódrészletet, amely nem felhasználó vagy csoport. Egy egyszerű szolgáltatásnév AZONOSÍTÓjának neve **ügyfél-azonosítóként** működik, és a neve, mint a felhasználóneve. Az egyszerű szolgáltatás **ügyfél-titka** úgy viselkedik, mint a jelszava.

Alkalmazások esetében kétféleképpen szerezhet be egyszerű szolgáltatást:

* Ajánlott: engedélyezze a rendszerhez rendelt **felügyelt identitást** az alkalmazáshoz.

    A felügyelt identitással az Azure belsőleg kezeli az alkalmazás egyszerű szolgáltatását, és automatikusan hitelesíti az alkalmazást más Azure-szolgáltatásokkal. A felügyelt identitás számos szolgáltatáshoz központilag telepített alkalmazásokhoz érhető el.

    További információ: a [felügyelt identitás áttekintése](/azure/active-directory/managed-identities-azure-resources/overview). Tekintse meg a [felügyelt identitást támogató Azure-szolgáltatásokat](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)is, amelyek az adott szolgáltatások felügyelt identitásának engedélyezését írják le (például App Service, Azure Functions, Virtual Machines stb.).

* Ha nem tudja használni a felügyelt identitást, az alkalmazást az Azure AD-Bérlővel **regisztrálja** , a gyors útmutató [: alkalmazás regisztrálása az Azure Identity platformon](/azure/active-directory/develop/quickstart-register-app)című témakörben leírtak szerint. A regisztráció egy második alkalmazásobjektum-objektumot is létrehoz, amely az alkalmazást az összes bérlőn azonosítja.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Rendszerbiztonsági tag engedélyezése Key Vaulthoz való hozzáféréshez

Key Vault két külön engedélyezési szinttel működik:

- A **hozzáférési házirendek** határozzák meg, hogy egy felhasználó, csoport vagy egyszerű szolgáltatás jogosult-e a titkok, kulcsok és tanúsítványok elérésére egy meglévő Key Vault erőforráson *belül* (más néven "adatsík" művelet). A hozzáférési házirendek általában a felhasználók, csoportok és alkalmazások számára adhatók meg.

    Hozzáférési szabályzatok hozzárendeléséhez tekintse meg a következő cikkeket:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- A **szerepkör-engedélyek** azt szabályozzák, hogy egy felhasználó, csoport vagy egyszerű szolgáltatásnév jogosult-e a Key Vault erőforrás létrehozására, törlésére és egyéb kezelésére (más néven "felügyeleti sík" műveletre). Ezeket a szerepköröket leggyakrabban csak a rendszergazdák kapják meg.
 
    A szerepkörök hozzárendeléséhez és kezeléséhez tekintse meg a következő cikkeket:

    - [Azure Portal](/azure/role-based-access-control/role-assignments-portal)
    - [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    Key Vault jelenleg támogatja a [közreműködő](/azure/role-based-access-control/built-in-roles#key-vault-contributor) szerepkört, amely lehetővé teszi a felügyeleti műveleteket Key Vault erőforrásokon. Számos más szerepkör jelenleg előzetes verzióban érhető el. Egyéni szerepköröket is létrehozhat az [Azure Custom roles](/azure/role-based-access-control/custom-roles)című témakörben leírtak szerint.

    A szerepkörökkel kapcsolatos általános információkért lásd: [Mi az az Azure szerepköralapú Access Control (RBAC)?](/azure/role-based-access-control/overview).


> [!IMPORTANT]
> A legnagyobb biztonság érdekében mindig kövesse a legalacsonyabb szintű jogosultságokat, és csak a legpontosabban szükséges hozzáférési házirendeket és szerepköröket adja meg. 
    
## <a name="configure-the-key-vault-firewall"></a>A Key Vault tűzfal konfigurálása

Alapértelmezés szerint a Key Vault nyilvános IP-címeken keresztül engedélyezi az erőforrásokhoz való hozzáférést. A nagyobb biztonság érdekében az adott IP-tartományokra, szolgáltatási végpontokra, virtuális hálózatokra vagy magánhálózati végpontokra is korlátozhatja a hozzáférést.

További információ: [hozzáférés Azure Key Vault tűzfal mögött](/azure/key-vault/general/access-behind-firewall).


## <a name="the-key-vault-authentication-flow"></a>A Key Vault hitelesítési folyamat

1. Egyszerű szolgáltatás kérése az Azure AD-vel való hitelesítéshez, például:
    * A felhasználó felhasználónévvel és jelszóval jelentkezik be a Azure Portalba.
    * Egy alkalmazás meghívja az Azure REST API, amely egy ügyfél-azonosítót és egy titkos kulcsot vagy egy ügyféltanúsítványt mutat be.
    * Egy Azure-erőforrás, például egy felügyelt identitású virtuális gép kapcsolatba lép az [azure instance metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) Rest-végponttal egy hozzáférési jogkivonat beszerzéséhez.

1. Ha az Azure AD-val való hitelesítés sikeres, az egyszerű szolgáltatásnév OAuth-tokent kap.

1. Az egyszerű szolgáltatásnév a Key Vault végpontján (URI) keresztül kezdeményezi a Key Vault REST API.

1. Key Vault a tűzfal a következő feltételeket ellenőrzi. Ha bármelyik feltétel teljesül, a hívás engedélyezett. Ellenkező esetben a rendszer letiltja a hívást, és egy tiltott választ ad vissza.

    * A tűzfal le van tiltva, és a Key Vault nyilvános végpontja elérhető a nyilvános internetről.
    * A hívó egy [Key Vault megbízható szolgáltatás](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services), amely lehetővé teszi a tűzfal megkerülését.
    * A hívó a tűzfalon IP-cím, virtuális hálózat vagy szolgáltatási végpont alapján jelenik meg.
    * A hívó elérheti Key Vault egy konfigurált magánhálózati kapcsolaton keresztül.    

1. Ha a tűzfal engedélyezi a hívást, Key Vault meghívja az Azure AD-t, hogy ellenőrizze az egyszerű szolgáltatásnév hozzáférési jogkivonatát.

1. Key Vault ellenőrzi, hogy az egyszerű szolgáltatásnév rendelkezik-e a kért művelethez szükséges hozzáférési házirenddel. Ha nem, Key Vault egy tiltott választ ad vissza.

1. Key Vault végrehajtja a kért műveletet, és visszaadja az eredményt.

A következő ábra egy olyan alkalmazás folyamatát szemlélteti, amely egy Key Vault "titkos kulcs beszerzése" API-t hív meg:

![A Azure Key Vault hitelesítési folyamat](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Kódpéldák

Az alábbi táblázat különböző cikkekre mutat, amelyek bemutatják, hogyan használható az alkalmazás kódjában Key Vault az Azure SDK-kódtárak használatával az adott nyelvhez. Az egyéb felületek, például az Azure CLI és a Azure Portal is kényelmi funkciókat biztosítanak.

| Key Vault titkok | Kulcsok Key Vault | Tanúsítványok Key Vault |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [.NET (SDK v4)](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [.NET (SDK v3)](/azure/key-vault/secrets/quick-create-net-v3) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Azure Portal](/azure/key-vault/secrets/quick-create-portal) | [Azure Portal](/azure/key-vault/keys/quick-create-portal) | [Azure Portal](/azure/key-vault/certificates/quick-create-portal) |
| [Azure CLI](/azure/key-vault/secrets/quick-create-cli) | [Azure CLI](/azure/key-vault/keys/quick-create-cli) | [Azure CLI](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [ARM-sablon](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>További lépések

- [Key Vault hozzáférési szabályzat hibaelhárítása](troubleshooting-access-issues.md)
- [Key Vault REST API hibakódok](rest-error-codes.md)
- [Key Vault fejlesztői útmutató](developers-guide.md)
- [Mi az Azure szerepköralapú Access Control (RBAC)?](/azure/role-based-access-control/overview)
