---
title: Azure Adatkezelő-fürtök védelme az Azure-ban
description: Ismerje meg, hogyan védheti meg a fürtöket az Azure Adatkezelőban.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5f3bceb8398f9837f6f8eaa390def41456daf08d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271589"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Azure Adatkezelő-fürtök védelme az Azure-ban

Ez a cikk az Azure Adatkezelő biztonságának bevezetését ismerteti, amely segít a felhőben tárolt adatainak és erőforrásainak védelmében, és megfelel a vállalat biztonsági igényeinek. Fontos, hogy a fürtök biztonságban maradjanak. A fürtök biztonságossá tétele egy vagy több olyan Azure-szolgáltatást foglal magában, amely biztonságos hozzáférést és tárterületet tartalmaz. Ez a cikk a fürt biztonságának megőrzéséhez nyújt segítséget.

## <a name="managed-identities-for-azure-resources"></a>Azure-erőforrások felügyelt identitásai

A felhőalapú alkalmazások kiépítésekor a hitelesítő adatok kezelése a Felhőbeli szolgáltatásokban való hitelesítés során gyakori kihívás. A hitelesítő adatok biztonságának megőrzése fontos feladat. A hitelesítő adatokat nem lehet a fejlesztői munkaállomásokban tárolni, vagy be kell jelölni a verziókövetésba. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez.

A probléma megoldásához a Azure Active Directory (Azure AD) felügyelt identitások az Azure-erőforrások szolgáltatásban. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének. További információ erről a szolgáltatásról: [felügyelt identitások az Azure-erőforrásokhoz](/azure/active-directory/managed-identities-azure-resources/overview) – Áttekintés lap.

## <a name="data-encryption"></a>Adattitkosítás

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Mennyiségi titkosítást biztosít a fürt virtuális gépei operációsrendszer-és adatlemezei számára. A Azure Disk Encryption a [Azure Key Vault](/azure/key-vault/)is integrálható, ami lehetővé teszi a lemezes titkosítási kulcsok és titkos kódok vezérlését és kezelését, valamint gondoskodik arról, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok Azure Key Vault

Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat, amelyeket az adattitkosításhoz használhat. A tárolási szinten kezelheti az adatai titkosítását a saját kulcsaival. Az ügyfél által felügyelt kulcs használatával biztosítható a legfelső szintű titkosítási kulcshoz való hozzáférés, amely az összes információ titkosítására és visszafejtésére szolgál. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

Az ügyfél által felügyelt kulcsok tárolásához használja a Azure Key Vault. Létrehozhat saját kulcsokat, és tárolhatja őket egy kulcstartóban, vagy használhat egy Azure Key Vault API-t kulcsok létrehozásához. Az Azure Adatkezelő-fürtnek és a Azure Key Vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview) Az ügyfél által felügyelt kulcsok részletes ismertetését lásd: [ügyfél által felügyelt kulcsok Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok Azure Portal konfigurálásához konfigurálnia kell egy **SystemAssigned** által felügyelt identitást a fürtön.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása Azure Key Vault

Az ügyfél által felügyelt kulcsok fürtön való engedélyezéséhez használjon egy Azure Key Vault a kulcsok tárolásához. Engedélyeznie kell a **Soft delete** és a No **Purge** tulajdonságot a Key vaulton. A Key vaultnak a fürttel megegyező előfizetésben kell lennie. Az Azure Adatkezelő felügyelt identitásokat használ az Azure-erőforrások számára a titkosítási és visszafejtési műveletek Key vaultba való hitelesítéséhez. A felügyelt identitások nem támogatják a könyvtárak közötti forgatókönyveket.

#### <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor a fürtöt úgy kell frissíteni, hogy az új kulcs URI-JÁT használja. A kulcs elforgatása nem aktiválja újra a fürtben lévő adattitkosítást. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) vagy [Azure Key Vault parancssori](/cli/azure/keyvault)felület. A hozzáférési blokkok visszavonása a fürt tárolási szintjén lévő összes adattal elérhetővé válik, mivel a titkosítási kulcs ezért nem érhető el az Azure Adatkezelő.

> [!Note]
> Amikor az Azure Adatkezelő megállapítja, hogy az ügyfél által felügyelt kulcshoz való hozzáférés visszavonásra kerül, automatikusan felfüggeszti a fürtöt a gyorsítótárazott adat törléséhez. A kulcshoz való hozzáférés után a fürtöt manuálisan kell újraindítani.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A [szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)használatával elkülönítheti a feladatait a csapaton belül, és csak a szükséges hozzáférést biztosíthat a fürt felhasználói számára. Ahelyett, hogy mindenki számára nem korlátozott engedélyeket adna a fürthöz, csak bizonyos műveleteket engedélyezhet. A [Azure Portalban](/azure/role-based-access-control/role-assignments-portal)lévő [adatbázisok hozzáférés-vezérlését](/azure/data-explorer/manage-database-permissions) az [Azure CLI](/azure/role-based-access-control/role-assignments-cli)vagy a [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)használatával konfigurálhatja.

## <a name="next-steps"></a>Következő lépések

* [Felügyelt identitások konfigurálása az Azure Adatkezelő-fürthöz](managed-identities.md)
* [A fürt biztonságossá tétele az Azure adatkezelő-Portalon](manage-cluster-security.md) a titkosítás nyugalmi állapotban való engedélyezésével.
* [Az ügyfél által felügyelt kulcsok konfigurálása a Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)
* [Ügyfél által felügyelt kulcsok konfigurálása a használatávalC#](customer-managed-keys-csharp.md)

