---
title: Mi az Azure-erőforrásokhoz készült felügyeltszolgáltatás-identitás?
description: Az Azure-erőforrásokhoz készült felügyeltszolgáltatás-identitás (MSI) áttekintése.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 3d6df04df8ceac1f868e64f0e8fbc7eb0fa317e3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38547973"
---
#  <a name="what-is-managed-service-identity-for-azure-resources"></a>Mi az Azure-erőforrásokhoz készült felügyeltszolgáltatás-identitás?

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A felhőalapú alkalmazások készítésének általános kihívását jelenti azon hitelesítő adatok a kódban történő kezelése, amelyekkel az alkalmazás hitelesíti magát a felhőalapú szolgáltatásokban. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben ezek soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. A felügyeltszolgáltatás-identitás segít leegyszerűsíteni ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

A felügyeltszolgáltatás-identitások ingyenesen használhatók az Azure Active Directoryval, amely alapértelmezés szerint az Azure-előfizetések részét képezi. A felügyeltszolgáltatás-identitások használata nem jár többletköltséggel.

## <a name="how-does-it-work"></a>Hogyan működik?

Két típusú felügyeltszolgáltatás-identitás létezik: **rendszerhez hozzárendelt** és **felhasználóhoz hozzárendelt**.

- A **rendszerhez hozzárendelt identitás** közvetlenül egy Azure-beli szolgáltatáspéldányon van engedélyezve. Az engedélyezésekor az Azure létrehoz egy identitást a szolgáltatáspéldány számára a szolgáltatáspéldány előfizetése által megbízhatónak tekintett Azure AD-bérlőn. Az identitás létrehozása után a rendszer hozzárendeli a hitelesítő adatokat a szolgáltatáspéldányon. A rendszerhez hozzárendelt identitás életciklusa közvetlenül kötődik ahhoz az Azure-beli szolgáltatáspéldányhoz, amelyen engedélyezve van. A szolgáltatáspéldány törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
- A **felhasználóhoz hozzárendelt identitás** különálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz. A felhasználóhoz hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányoktól, amelyekhez hozzá lettek rendelve.

Ennek eredményeképpen a kód használhat egy rendszerhez vagy egy felhasználóhoz hozzárendelt identitást is az Azure AD-hitelesítést támogató szolgáltatások hozzáférési jogkivonatainak igényléséhez. Mindeközben az Azure gondoskodik a szolgáltatáspéldány által használt hitelesítő adatok alkalmazásáról.

Az alábbiakban egy példát láthat a rendszerhez hozzárendelt identitások az Azure-beli virtuális gépeken történő használatáról:

![Példa virtuális gép által felügyelt identitásra](overview/msi-vm-vmextension-imds-example.png)

1. Az Azure Resource Managerbe érkezik egy kérés a rendszerhez hozzárendelt identitás egy virtuális gépen történő engedélyezésére.
2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a virtuális gép identitásának jelölésére. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.
3. Az Azure Resource Manager konfigurálja az identitást a virtuális gépen:
    - Frissíti az Azure Instance Metadata szolgáltatás identitásvégpontját a szolgáltatásnév ügyfél-azonosítójával és tanúsítványával.
    - Létrehozza a VM-bővítményt, és hozzáadja a szolgáltatásnév ügyfél-azonosítóját és tanúsítványát. (hamarosan elavul)
4. Most, hogy a virtuális gép rendelkezik identitással, a szolgáltatásnév-adatok használatával biztosítunk hozzáférést a virtuális gépnek az Azure-erőforrásokhoz. Például ha a kódnak meg kell hívnia az Azure Resource Managert, a virtuális gép szolgáltatásnevét hozzárendelnénk a megfelelő szerepkörhöz a szerepköralapú hozzáférés-vezérlés (RBAC) használatával az Azure AD-ben. Ha a kódnak meg kell hívnia a Key Vaultot, hozzáférést adnánk a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.
5. A virtuális gépen futó kód két végpontról kérhet le jogkivonatot, amelyek csak a virtuális gépen belülről érhetők el:

    - Az Azure Instance Metadata szolgáltatás (IMDS) identitásvégpontjáról: http://169.254.169.254/metadata/identity/oauth2/token (ajánlott)
        - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Például ha azt szeretné, hogy a kód az Azure Resource Managerben végezze el a hitelesítést, használja a resource=https://management.azure.com/ értéket.
        - Az API version (API-verzió) paraméter adja meg az IMDS-verziót – használja az api-version=2018-02-01, vagy egy nagyobb értéket.
    - A VM-bővítmény végpontjáról: http://localhost:50342/oauth2/token (hamarosan elavul)
        - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Például ha azt szeretné, hogy a kód az Azure Resource Managerben végezze el a hitelesítést, használja a resource=https://management.azure.com/ értéket.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel az 5. lépésben leírtak szerint, a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

Ugyanennek az ábrának az alapján az alábbiakban egy példát láthat a felhasználóhoz hozzárendelt identitások Azure-beli virtuális gépeken történő használatáról.

1. Az Azure Resource Managerbe érkezik egy kérés egy felhasználóhoz hozzárendelt identitás létrehozására.
2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a felhasználóhoz hozzárendelt identitás jelölésére. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.
3. Az Azure Resource Managerbe érkezik egy kérés a felhasználóhoz hozzárendelt identitás egy virtuális gépen történő konfigurálására:
    - Frissíti az Azure Instance Metadata szolgáltatás identitásvégpontját a felhasználóhoz hozzárendelt identitás szolgáltatásnevének ügyfél-azonosítójával és tanúsítványával.
    - Létrehozza a VM-bővítményt, és hozzáadja a felhasználóhoz hozzárendelt identitás szolgáltatásnevének ügyfél-azonosítóját és tanúsítványát (hamarosan elavul).
4. Most, hogy a felhasználóhoz hozzárendelt identitás létrejött, a szolgáltatásnevének adataival biztosítunk hozzáférést számára az Azure-erőforrásokhoz. Például ha a kódnak meg kell hívnia az Azure Resource Managert, a felhasználóhoz hozzárendelt identitás szolgáltatásnevét hozzárendelnénk a megfelelő szerepkörhöz a szerepköralapú hozzáférés-vezérlés (RBAC) használatával az Azure AD-ben. Ha a kódnak meg kell hívnia a Key Vaultot, hozzáférést adnánk a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban. Megjegyzés: Ez a lépés a 3. lépés előtt is végrehajtható.
5. A virtuális gépen futó kód két végpontról kérhet le jogkivonatot, amelyek csak a virtuális gépen belülről érhetők el:

    - Az Azure Instance Metadata szolgáltatás (IMDS) identitásvégpontjáról: http://169.254.169.254/metadata/identity/oauth2/token (ajánlott)
        - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Például ha azt szeretné, hogy a kód az Azure Resource Managerben végezze el a hitelesítést, használja a resource=https://management.azure.com/ értéket.
        - A Client ID (ügyfél-azonosító) paraméter határozza meg azt az identitást, amely számára a hozzáférési jogkivonat kérvényezve lett. Ez akkor szükséges, ha egyazon virtuális gépen több felhasználóhoz hozzárendelt identitás is létezik.
        - Az API version (API-verzió) paraméter adja meg az IMDS-verziót – használja az api-version=2018-02-01, vagy egy nagyobb értéket.

    - A VM-bővítmény végpontjáról: http://localhost:50342/oauth2/token (hamarosan elavul)
        - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Például ha azt szeretné, hogy a kód az Azure Resource Managerben végezze el a hitelesítést, használja a resource=https://management.azure.com/ értéket.
        - A Client ID (ügyfél-azonosító) paraméter határozza meg azt az identitást, amely számára a hozzáférési jogkivonat kérvényezve lett. Ez akkor szükséges, ha egyazon virtuális gépen több felhasználóhoz hozzárendelt identitás is létezik.
6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel az 5. lépésben leírtak szerint, a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.
     
## <a name="try-managed-service-identity"></a>A felügyeltszolgáltatás-identitások kipróbálása

A felügyeltszolgáltatás-identitásokkal (MSI) foglalkozó különféle oktatóanyagokból megismerheti a különféle Azure-erőforrások elérését tárgyaló, végpontok közötti forgatókönyveket:
<br><br>
| Felügyeltidentitás-kompatibilis erőforrásból | Az alábbiak végrehajtásának módját ismerheti meg |
| ------- | -------- |
| Azure-beli virtuális gép (Windows) | [Az Azure Data Lake Store elérése Windows VM-beli felügyeltszolgáltatás-identitással](tutorial-windows-vm-access-datalake.md) |
|                    | [Az Azure Resource Manager elérése Windows VM-beli felügyeltszolgáltatás-identitással](tutorial-windows-vm-access-arm.md) |
|                    | [Az Azure SQL elérése Windows VM-beli felügyeltszolgáltatás-identitással](tutorial-windows-vm-access-sql.md) |
|                    | [Az Azure Storage elérése hozzáférési kulccsal Windows VM-beli felügyeltszolgáltatás-identitással](tutorial-windows-vm-access-storage.md) |
|                    | [Az Azure Storage elérése SAS-azonosítással Windows VM-beli felügyeltszolgáltatás-identitással](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Nem Azure AD-erőforrások elérése Windows VM-beli felügyeltszolgáltatás-identitással és az Azure Key Vaulttal](tutorial-windows-vm-access-nonaad.md) |
| Azure-beli virtuális gép (Linux)   | [Az Azure Data Lake Store elérése Linux VM-beli felügyeltszolgáltatás-identitással](tutorial-linux-vm-access-datalake.md) |
|                    | [Az Azure Resource Manager elérése Linux VM-beli felügyeltszolgáltatás-identitással](tutorial-linux-vm-access-arm.md) |
|                    | [Az Azure Storage elérése hozzáférési kulccsal Linux VM-beli felügyeltszolgáltatás-identitással](tutorial-linux-vm-access-storage.md) |
|                    | [Az Azure Storage elérése SAS-azonosítással Linux VM-beli felügyeltszolgáltatás-identitással](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Nem Azure AD-erőforrások elérése Linux VM-beli felügyeltszolgáltatás-identitással és az Azure Key Vaulttal](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Felügyeltszolgáltatás-identitás használata az Azure App Service vagy az Azure Functions szolgáltatással](/azure/app-service/app-service-managed-service-identity) |
| Azure Functions    | [Felügyeltszolgáltatás-identitás használata az Azure App Service vagy az Azure Functions szolgáltatással](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Felügyeltszolgáltatás-identitás használata az Azure Service Bus szolgáltatással](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Felügyeltszolgáltatás-identitás használata az Azure Event Hubs szolgáltatással](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Mely Azure-szolgáltatások támogatják a felügyeltszolgáltatás-identitások használatát?

A felügyelt identitások használatával hitelesítést végezhet az Azure AD-hitelesítést támogató szolgáltatásokban. A felügyeltszolgáltatás-identitásokat támogató szolgáltatások listájáért lásd a következő cikket:
- [A Managed Service Identityt (MSI) támogató szolgáltatások](services-support-msi.md)

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az Azure felügyeltszolgáltatás-identitásokkal a következő rövid útmutatók segítségével:

* [A Resource Manager elérése Windows VM-beli felügyeltszolgáltatás-identitással – Windows VM](tutorial-windows-vm-access-arm.md)
* [Az Azure Resource Manager elérése Linux VM-beli felügyeltszolgáltatás-identitással – Linux VM](tutorial-linux-vm-access-arm.md)
