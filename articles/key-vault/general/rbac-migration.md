---
title: Áttelepítés Azure szerepköralapú hozzáférés-vezérlésre | Microsoft Docs
description: Megtudhatja, hogyan telepíthet át a tár hozzáférési házirendjeiről az Azure-szerepkörökre.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e06a7a759c712b47f3a725a3c49a660226da6a09
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064148"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>Áttelepítés a tár hozzáférési házirendjéből egy Azure szerepköralapú hozzáférés-vezérlés (előzetes verzió) engedélyezési modelljére

A tár hozzáférési szabályzatának modellje egy Key Vault beépített engedélyezési rendszer, amely hozzáférést biztosít a kulcsokhoz, a titkokhoz és a tanúsítványokhoz. A hozzáférést úgy szabályozhatja, hogy a rendszerbiztonsági tag (felhasználó, csoport, szolgáltatásnév, felügyelt identitás) egyéni engedélyeit rendeli hozzá Key Vault hatókörben. 

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) egy [Azure Resource Managerra](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) épülő engedélyezési rendszer, amely az Azure-erőforrások részletes hozzáférés-kezelését teszi lehetővé. A Key Vault kulcsok, titkok és tanúsítványok hozzáférés-vezérlésének Azure-RBAC jelenleg nyilvános előzetes verzióban érhető el. Az Azure RBAC szerepkör-hozzárendelések létrehozásával szabályozhatja az erőforrásokhoz való hozzáférést, amely három elemből áll: rendszerbiztonsági tag, szerepkör-definíció (az engedélyek előre meghatározott készlete) és a hatókör (erőforrás-csoport vagy egyedi erőforrás). További információ: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview).

Az Azure RBAC való Migrálás előtt fontos megérteni az előnyeit és korlátozásait.

Az Azure RBAC fő előnyei a tár-hozzáférési szabályzatok esetében:
- Egyesített hozzáférés-vezérlési modellt biztosít az Azure-erőforrásokhoz – azonos API az Azure-szolgáltatások között
- Központosított hozzáférés-kezelés rendszergazdák számára – egyetlen nézetben kezelheti az összes Azure-erőforrást
- Integrált [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) az Időalapú hozzáférés-vezérléshez
- Hozzárendelések megtagadása – a rendszerbiztonsági tag kizárásának lehetősége az adott hatókörön belül. További információ: az [Azure deny-hozzárendelések ismertetése](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)

Az Azure RBAC hátrányai:
- Szerepkör-hozzárendelések késése – a szerepkör-hozzárendelés alkalmazása több percet is igénybe vehet. A tár hozzáférési szabályzatait azonnal hozzárendeli a rendszer.
- Korlátozott számú szerepkör-hozzárendelés – 2000 szerepkör-hozzárendelések/előfizetés és 1024 hozzáférési szabályzatok száma Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Hozzáférési szabályzatok az Azure-szerepkörök hozzárendeléséhez

Az Azure RBAC számos Azure beépített szerepkörrel rendelkezik, amelyeket a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz rendelhet hozzá. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni Azure-szerepköröket](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)is.

Key Vault beépített szerepkörök a kulcsok, tanúsítványok és titkok hozzáférésének kezeléséhez:
- Key Vault rendszergazda (előzetes verzió)
- Key Vault olvasó (előzetes verzió)
- Key Vault Certificate Officer (előzetes verzió)
- Key Vault titkosítási tisztviselő (előzetes verzió)
- Key Vault kriptográfiai felhasználó (előzetes verzió)
- Key Vault Secrets Officer (előzetes verzió)
- Key Vault Secrets User (előzetes verzió)

További információ a meglévő beépített szerepkörökről: [Azure beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

A tár-hozzáférési szabályzatok az egyénileg kiválasztott engedélyekkel vagy előre definiált engedélyezési sablonokkal rendelhetők hozzá.

Hozzáférési szabályzatok előre definiált engedélyezési sablonjai:
- Kulcs, titok, Tanúsítványkezelő
- Kulcs & Secret Management
- Secret & Tanúsítványkezelő
- Kulcskezelő
- Titkos kód kezelése
- Tanúsítványkezelés
- SQL Server Connector
- Azure Data Lake Storage vagy Azure Storage
- Azure Backup
- Exchange Online-ügyfél kulcsa
- SharePoint Online-ügyfél kulcsa
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Hozzáférési szabályzatok sablonjai az Azure-szerepkörök hozzárendeléséhez
| Hozzáférési házirend sablonja | Üzemeltetés | Azure-szerepkör |
| --- | --- | --- |
| Kulcs, titok, Tanúsítványkezelő | Kulcsok: minden művelet <br>Tanúsítványok: minden művelet<br>Titkok: minden művelet | Key Vault rendszergazda (előzetes verzió) |
| Kulcs & Secret Management | Kulcsok: minden művelet <br>Titkok: minden művelet| Key Vault titkosítási tisztviselő (előzetes verzió)<br> Key Vault Secrets Officer (előzetes verzió)|
| Secret & Tanúsítványkezelő | Tanúsítványok: minden művelet <br>Titkok: minden művelet| Key Vault Certificates Officer (előzetes verzió)<br> Key Vault Secrets Officer (előzetes verzió)|
| Kulcskezelő | Kulcsok: minden művelet| Key Vault titkosítási tisztviselő (előzetes verzió)|
| Titkos kód kezelése | Titkok: minden művelet| Key Vault Secrets Officer (előzetes verzió)|
| Tanúsítványkezelés | Tanúsítványok: minden művelet | Key Vault Certificates Officer (előzetes verzió)|
| SQL Server Connector | Kulcsok: Get, List, wrap Key, dewrap Key | Titkosítási szolgáltatás titkosításának Key Vault (előzetes verzió)|
| Azure Data Lake Storage vagy Azure Storage | Kulcsok: beolvasás, Listázás, kicsomagolási kulcs | N/A<br> Egyéni szerepkör szükséges|
| Azure Backup | Kulcsok: beolvasás, Listázás, biztonsági mentés<br> Tanúsítvány: lekérés, Listázás, biztonsági mentés | N/A<br> Egyéni szerepkör szükséges|
| Exchange Online-ügyfél kulcsa | Kulcsok: Get, List, wrap Key, dewrap Key | Titkosítási szolgáltatás titkosításának Key Vault (előzetes verzió)|
| Exchange Online-ügyfél kulcsa | Kulcsok: Get, List, wrap Key, dewrap Key | Titkosítási szolgáltatás titkosításának Key Vault (előzetes verzió)|
| Azure Information BYOK | Kulcsok: beolvasás, visszafejtés, aláírás | N/A<br>Egyéni szerepkör szükséges|


## <a name="assignment-scopes-mapping"></a>Hozzárendelési hatókörök leképezése  

Az Azure RBAC for Key Vault lehetővé teszi a szerepkörök hozzárendelését a következő hatókörökben:
- Felügyeleti csoport
- Előfizetés
- Erőforráscsoport
- Erőforrás Key Vault
- Egyéni kulcs, titkos kód és tanúsítvány

A tár hozzáférési szabályzatának engedélyezési modellje csak Key Vault erőforrás szintjén van korlátozva a szabályzatok hozzárendeléséhez, ami 

Általánosságban elmondható, hogy az alkalmazás és a Key Vault szintjén a hozzáférés kezelése egy kulcstartóval történik. A más hatókörökhöz való hozzáférés kezelése egyszerűbbé teheti a hozzáférés-kezelést.

- * * Infrastruktúra, biztonsági rendszergazdák és kezelők: a Key vaultok csoportjának kezelése a felügyeleti csoportban, az előfizetés vagy az erőforráscsoport szintjén a tár-hozzáférési házirendekkel minden kulcstartó számára szükséges a szabályzatok karbantartása. Az Azure RBAC lehetővé teszi egy szerepkör-hozzárendelés létrehozását a felügyeleti csoport, az előfizetés vagy az erőforráscsoport számára. Ez a hozzárendelés minden olyan új kulcstárolóra érvényes lesz, amely ugyanabban a hatókörben lett létrehozva. Ebben az esetben javasoljuk, hogy az igény szerinti hozzáférés biztosításához használja az Privileged Identity Management-t.
 
- * * Alkalmazások: vannak olyan helyzetek, amikor az alkalmazásnak meg kell osztania a titkot más alkalmazással. A tár hozzáférési szabályzatait külön Key Vault használatával kellett létrehozni, hogy elkerülje a hozzáférést az összes titokhoz. Az Azure RBAC egyetlen kulcstartó használatával teszi lehetővé az egyéni titkos kódok hatókörének hozzárendelését.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>A tár hozzáférési szabályzata az Azure RBAC áttelepítési lépéseihez
Az Azure RBAC és a tár hozzáférési szabályzatának engedélyezési modellje számos különbséget mutat. A kiesések elkerülése érdekében az alábbi lépések ajánlottak.
 
1. **Szerepkörök azonosítása és hozzárendelése**: a fenti leképezési táblázat alapján azonosítsa a beépített szerepköröket, és szükség esetén hozzon létre egyéni szerepköröket. Szerepkörök hozzárendelése hatókörökhöz a hatókörök hozzárendelési útmutatója alapján. A szerepkörök Key vaulthoz való hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés biztosítása Key Vault Azure szerepköralapú hozzáférés-vezérléssel (előzetes verzió)](rbac-guide.md)
1. **Szerepkörök hozzárendelésének ellenőrzése**: az Azure RBAC szerepkör-hozzárendelései több percet is igénybe vehetnek. A szerepkör-hozzárendelések vizsgálatával kapcsolatos útmutatóért lásd: [szerepkörök hozzárendeléseinek listázása a hatókörben](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)
1. **Figyelés és riasztás konfigurálása a Key vaultban**: fontos, hogy engedélyezze a naplózást és a beállítás riasztást a hozzáférés-megtagadási kivételekhez. További információ: [figyelés és riasztás a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/alert)
1. **Azure szerepköralapú hozzáférés-vezérlési engedély modell beállítása Key Vault: az**Azure RBAC engedélyezési modell engedélyezése érvényteleníti az összes meglévő hozzáférési szabályzatot. Ha hiba történt, az engedélyezési modell visszaváltható az összes meglévő, érintetlen maradó hozzáférési házirenddel.

> [!NOTE]
> Ha az Azure RBAC engedélyezési modellje engedélyezve van, az összes olyan parancsfájl sikertelen lesz, amely a hozzáférési szabályzatok frissítését kísérli meg. Fontos, hogy frissítse ezeket a parancsfájlokat az Azure RBAC használatához.

## <a name="troubleshooting"></a>Hibaelhárítás
-  A szerepkör-hozzárendelés néhány perc elteltével nem működik – előfordulhatnak olyan helyzetek, amikor a szerepkör-hozzárendelések hosszabb időt vehetnek igénybe. Fontos, hogy az ilyen esetekben az újrapróbálkozási logikát írja a kódban.
- A szerepkör-hozzárendelések eltűntek, ha Key Vault törölve lettek (Soft-delete) A helyreállítás után újra létre kell hozni az összes szerepkör-hozzárendelést.    

## <a name="learn-more"></a>Tudjon meg többet

- [Az Azure RBAC áttekintése](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Egyéni szerepkörök oktatóanyaga](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
