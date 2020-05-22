---
title: Azure-erőforrások felügyelt identitásai
description: Az Azure-erőforrások felügyelt identitásainak áttekintése.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 05/20/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 738a5bd76cc15b9356275707aed0d0a695aa6367
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770924"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Mik azok az Azure-erőforrások felügyelt identitásai?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez.

Az Azure Active Directory (Azure AD) Azure-erőforrások felügyelt identitásai szolgáltatása megoldást kínál erre a problémára. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.

Az Azure-erőforrások felügyelt identitásai szolgáltatás ingyenesen használható az Azure Active Directoryval az Azure-előfizetésekben. Nincs további költség.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="terminology"></a>Terminológia

Az Azure-erőforrások dokumentációs készletének összes felügyelt identitása a következő feltételekkel használható:

- **Ügyfél-azonosító** – az Azure ad által generált egyedi azonosító, amely egy alkalmazáshoz és egyszerű szolgáltatáshoz van kötve a kezdeti kiépítés során.
- **Elsődleges azonosító** – a felügyelt identitáshoz tartozó egyszerű szolgáltatásnév, amely az Azure-erőforrásokhoz való szerepköralapú hozzáférés biztosítására szolgál.
- **Azure instance metadata Service (IMDS)** – egy Rest-végpont elérhető minden, a Azure Resource Manager használatával létrehozott IaaS-virtuális gép számára. A végpont egy jól ismert, nem irányítható IP-címen (169.254.169.254) érhető el, amely csak a virtuális gépről érhető el.

## <a name="how-does-the-managed-identities-for-azure-resources-work"></a>Hogyan működik az Azure-erőforrások felügyelt identitásai?

A felügyelt identitásoknak két típusa létezik:

- A **rendszer által hozzárendelt felügyelt identitás** közvetlenül egy Azure-beli szolgáltatáspéldányon van engedélyezve. Az identitás engedélyezésekor az Azure létrehoz egy identitást a példány számára a példány előfizetése által megbízhatónak tekintett Azure AD-bérlőn. Az identitás létrehozása után a rendszer hozzárendeli a hitelesítő adatokat a példányon. A rendszerhez rendelt identitás életciklusa közvetlenül ahhoz az Azure Service-példányhoz van kötve, amelyhez engedélyezve van. A példány törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
- A **felhasználó által hozzárendelt felügyelt identitás** különálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz. A felhasználó által hozzárendelt identitás életciklusa külön felügyelhető azon Azure-szolgáltatási példányok életciklusa alapján, amelyekhez hozzá van rendelve.

Belsőleg a felügyelt identitások olyan speciális típusú szolgáltatások, amelyek csak az Azure-erőforrásokkal való használatra vannak zárolva. A felügyelt identitás törlése után a rendszer automatikusan eltávolítja a megfelelő egyszerű szolgáltatást.
Emellett, ha a felhasználó által hozzárendelt vagy rendszerhez rendelt identitás létrejött, a felügyelt identitás erőforrás-szolgáltatója (MSRP) belső tanúsítványt bocsát ki az identitásnak. 

A kód a felügyelt identitások használatával hozzáférési jogkivonatokat igényelhet az Azure AD-hitelesítést támogató szolgáltatásokhoz. Az Azure gondoskodik a szolgáltatáspéldány által használt hitelesítő adatok biztosításáról. 

Az alábbi ábrán a felügyelszolgáltatás-identitások az Azure-beli virtuális gépekkel (VM) való működése látható:

![Felügyeltszolgáltatás-identitások és Azure-beli virtuális gépek](media/overview/data-flow.png)

|  Tulajdonság    | Rendszer által hozzárendelt felügyelt identitás | Felhasználó által hozzárendelt felügyelt identitás |
|------|----------------------------------|--------------------------------|
| Létrehozás |  Azure-erőforrás részeként létrehozva (például egy Azure-beli virtuális gép vagy Azure App Service) | Önálló Azure-erőforrásként létrehozva |
| Életciklus | Megosztott életciklus az Azure-erőforrással, amelyet a felügyelt identitás létrehoz. <br/> A fölérendelt erőforrás törlésekor a felügyelt identitás is törlődik. | Független életciklus. <br/> Explicit módon törölni kell. |
| Megosztás az Azure-erőforrások között | Nem osztható meg. <br/> Csak egyetlen Azure-erőforráshoz társítható. | Megosztható <br/> Ugyanaz a felhasználó által hozzárendelt felügyelt identitás több Azure-erőforráshoz is társítható. |
| Gyakori használati helyzetek | Egyetlen Azure-erőforráson belül található munkaterhelések <br/> Olyan munkaterhelések, amelyekhez független identitásokra van szükség. <br/> Például egyetlen virtuális gépen futó alkalmazás | Több erőforráson futó munkaterhelések, amelyek egyetlen identitást is megoszthatnak. <br/> Olyan munkaterhelések, amelyeknek a létesítési folyamat részeként meg kell adni a biztonságos erőforráshoz való előzetes engedélyezést. <br/> Olyan munkaterhelések, amelyekben az erőforrások gyakran újrahasznosíthatók, de az engedélyek konzisztensek maradnak. <br/> Például egy olyan munkaterhelés, amelyben több virtuális gépnek is hozzá kell férnie ugyanahhoz az erőforráshoz |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Hogyan működnek együtt a rendszer által hozzárendelt felügyelt identitások az Azure-beli virtuális gépekkel?

1. Az Azure Resource Managerbe érkezik egy kérés a rendszer által hozzárendelt felügyelt identitás virtuális gépen történő engedélyezésére.

2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a virtuális gép identitása számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

3. Azure Resource Manager úgy konfigurálja az identitást a virtuális gépen, hogy az Azure Instance Metadata Service Identity végpontot az egyszerű szolgáltatás ügyfél-azonosítójával és tanúsítvánnyal frissíti.

4. Miután a virtuális gép már rendelkezik identitással, a szolgáltatásnév-adatok használatával biztosítson hozzáférést a virtuális gépnek az Azure-erőforrásokhoz. Az Azure Resource Manager meghívásához szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelje hozzá a megfelelő szerepkört a virtuális gép szolgáltatásnevéhez az Azure AD-ben. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

5. A virtuális gépen futó kód az Azure-példány metaadatainak szolgáltatásának végpontján igényelhet jogkivonatot, amely csak a virtuális gépről érhető el:`http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - Az API version (API-verzió) paraméter adja meg az IMDS-verziót – használja az api-version=2018-02-01, vagy egy nagyobb értéket.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.

7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Hogyan működnek együtt a felhasználó által hozzárendelt felügyelt identitások az Azure-beli virtuális gépekkel?

1. Az Azure Resource Managerbe érkezik egy kérés egy felhasználó által hozzárendelt felügyelt identitás létrehozására.

2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a felhasználó által hozzárendelt felügyelt identitás számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

3. A Azure Resource Manager kérést kap a felhasználó által hozzárendelt felügyelt identitás konfigurálására egy virtuális gépen, és frissíti az Azure Instance Metadata Service Identity végpontot a felhasználó által hozzárendelt felügyelt identitási szolgáltatás egyszerű ügyfelének azonosítójával és tanúsítványával.

4. Miután a felhasználó által hozzárendelt felügyelt identitás létrejött, a szolgáltatásnév-adatok használatával biztosítson hozzáférést az identitásnak az Azure-erőforrásokhoz. Az Azure Resource Manager meghívásához szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelje hozzá a megfelelő szerepkört a felhasználó által hozzárendelt identitás szolgáltatásnevéhez az Azure AD-ben. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

   > [!Note]
   > Ezt a lépést a 3. lépés előtt is végrehajthatja.

5. A virtuális gépen futó kód az Azure Instance Metadata Service Identity végponttól kérhet jogkivonatot, amely csak a virtuális gépről érhető el:`http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - A client ID (ügyfél-azonosító) paraméter határozza meg azt az identitást, amely számára a hozzáférési jogkivonat kérvényezve lett. Ez az érték akkor szükséges egyértelműsítés céljából, ha egyazon virtuális gépen több felhasználó által hozzárendelt identitás is létezik.
    - Az API version (API verziója) paraméter az Azure Instance Metadata szolgáltatás verzióját adja meg. Használja a(z) `api-version=2018-02-01` vagy újabb verziót.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

## <a name="credential-rotation"></a>Hitelesítő adatok elforgatása
A hitelesítő adatok rotációját az Azure-erőforrást üzemeltető erőforrás-szolgáltató vezérli. A hitelesítő adatok alapértelmezett rotációja 46 naponta történik. Az erőforrás-szolgáltatónak új hitelesítő adatokat kell meghívnia, így az erőforrás-szolgáltató 46 napnál hosszabb ideig is várhat.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hogyan használhatom az Azure-erőforrások felügyelt identitásait?

A különféle Azure-erőforrások felügyelt identitással való elérésének megismeréséért tekintse át az alábbi oktatóanyagokat.

> [!NOTE]
> Tekintse át a [felügyelt identitások megvalósítása a Microsoft Azure erőforrásokhoz](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) című témakört, ahol további információt talál a felügyelt identitásokról, beleértve a számos támogatott forgatókönyv részletes videós bemutatóit.

Útmutató a felügyelt identitások használatához Windows rendszerű virtuális gépeken:

* [Az Azure Data Lake Store elérése](tutorial-windows-vm-access-datalake.md)
* [Az Azure Resource Manager elérése](tutorial-windows-vm-access-arm.md)
* [Az Azure SQL elérése](tutorial-windows-vm-access-sql.md)
* [Az Azure Storage elérése hozzáférési kulcs használatával](tutorial-vm-windows-access-storage.md)
* [Az Azure Storage elérése közös hozzáférésű jogosultságkódok használatával](tutorial-windows-vm-access-storage-sas.md)
* [Nem Azure AD-erőforrások elérése az Azure Key Vaulttal](tutorial-windows-vm-access-nonaad.md)

Útmutató a felügyelt identitások használatához Linux rendszerű virtuális gépeken:

* [Hozzáférés az Azure Container Registryhez](../../container-registry/container-registry-authentication-managed-identity.md)
* [Az Azure Data Lake Store elérése](tutorial-linux-vm-access-datalake.md)
* [Az Azure Resource Manager elérése](tutorial-linux-vm-access-arm.md)
* [Az Azure Storage elérése hozzáférési kulcs használatával](tutorial-linux-vm-access-storage.md)
* [Az Azure Storage elérése közös hozzáférésű jogosultságkódok használatával](tutorial-linux-vm-access-storage-sas.md)
* [Nem Azure AD-erőforrások elérése az Azure Key Vaulttal](tutorial-linux-vm-access-nonaad.md)

Útmutató a felügyelt identitások használatához egyéb Azure-szolgáltatásokban:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry Tasks](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/authenticate-managed-identity.md)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Data Factory](../../data-factory/data-factory-service-identity.md)


## <a name="what-azure-services-support-the-feature"></a>Mely Azure-szolgáltatások támogatják a szolgáltatást?<a name="which-azure-services-support-managed-identity"></a>

Az Azure-erőforrások felügyelt identitásai használatával hitelesítést végezhet az Azure AD-hitelesítést támogató szolgáltatásokban. Az Azure-erőforrások felügyelt identitásai szolgáltatást támogató Azure-szolgáltatások listájáért lásd [az Azure-erőforrások felügyelt identitásait támogató szolgáltatásokkal](services-support-msi.md) foglalkozó részt.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az Azure-erőforrások felügyelt identitásai szolgáltatással a következő rövid útmutatók segítségével:

* [Hozzáférés a Resource Managerhez egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-windows-vm-access-arm.md)
* [Hozzáférés a Resource Managerhez egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-linux-vm-access-arm.md)
