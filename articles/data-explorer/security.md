---
title: Biztonságos Azure Data Explorer-fürtök az Azure-ban
description: Ismerje meg, hogyan biztosíthat fürtöket az Azure Data Explorerben.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373350"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Biztonságos Azure Data Explorer-fürtök az Azure-ban

Ez a cikk bemutatja az Azure Data Explorer biztonságának használatát, amely segítséget nyújt az adatok és az erőforrások felhőbeli védelméhez és a vállalkozás biztonsági igényeinek kielégítéséhez. Fontos, hogy a fürtök biztonságos. A fürtök biztonságossá tétele magában foglal egy vagy több Azure-funkciót, amelyek magukban foglalják a biztonságos hozzáférést és a tárolást. Ez a cikk a fürt biztonságának megőrzéséhez nyújt tájékoztatást.

## <a name="managed-identities-for-azure-resources"></a>Azure-erőforrások felügyelt identitásai

A felhőalapú alkalmazások létrehozásakor gyakori kihívás a felhőszolgáltatásokhitelesítéshez használt kód hitelesítő adatok kezelése. A hitelesítő adatok biztonságának megőrzése fontos feladat. A hitelesítő adatokat nem szabad fejlesztői munkaállomásokon tárolni, és nem kell ellenőrizni a forrásvezérlőbe. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez.

Az Azure Active Directory (Azure AD) felügyelt identitások az Azure-erőforrások funkció megoldja ezt a problémát. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének. A szolgáltatásról további információt az [Azure-erőforrások felügyelt identitások](/azure/active-directory/managed-identities-azure-resources/overview) – áttekintés i. lap című témakörben talál.

## <a name="data-encryption"></a>Adattitkosítás

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Az Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segít megvédeni és megvédeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalásoknak. Kötettitkosítást biztosít a fürt virtuális gépeinek operációs rendszeréhez és adatlemezeihez. Az Azure Disk Encryption is integrálható [az Azure Key Vault,](/azure/key-vault/)amely lehetővé teszi számunkra, hogy ellenőrizzék és kezeljék a lemez titkosítási kulcsok és titkos kulcsok, és győződjön meg arról, hogy a virtuális gép lemezeken lévő összes adat titkosítva van. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok az Azure Key Vault segítségével

Alapértelmezés szerint az adatok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához megadhat ügyfél által felügyelt kulcsokat az adattitkosításhoz. Az adatok titkosítását a tárolási szinten kezelheti a saját kulcsaival. Az ügyfél által felügyelt kulcs a gyökértitkosítási kulcshoz való hozzáférés védelmére és szabályozására szolgál, amely az összes adat titkosítására és visszafejtésére szolgál. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlők létrehozásához, elforgatásához, letiltásához és visszavonásához. Az adatok védelmére használt titkosítási kulcsokat is naplózhatja.

Az Azure Key Vault használatával tárolhatja az ügyfél által felügyelt kulcsokat. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-t a kulcsok létrehozásához. Az Azure Data Explorer-fürt és az Azure Key Vault kell ugyanabban a régióban, de lehet nek iktatott különböző előfizetések. Az Azure Key Vaultról a [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview) Az ügyfelek által felügyelt kulcsok részletes magyarázatát az [Azure Key Vault ügyfél által felügyelt kulcsok című témakörben tetszetős ekben.](/azure/storage/common/storage-service-encryption) Ügyfél által kezelt kulcsok konfigurálása az Azure Data Explorer-fürtben a [C#](/azure/data-explorer/customer-managed-keys-csharp) vagy az [Azure Resource Manager sablon](/azure/data-explorer/customer-managed-keys-resource-manager) használatával

> [!Note]
> Az ügyfél által felügyelt kulcsok az Azure Active Directory (Azure AD) egyik szolgáltatásának felügyelt identitásaira támaszkodnak. Az ügyfél által felügyelt kulcsok konfigurálásához az Azure Portalon konfigurálnia kell egy **SystemAssigned** felügyelt identitást a fürthöz az [Azure Data Explorer-fürt felügyelt identitásainak konfigurálása](/azure/data-explorer/managed-identities)című részben.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása az Azure Key Vaultban

Az ügyfél által felügyelt kulcsok fürtön való engedélyezéséhez használja az Azure Key Vault a kulcsok tárolására. Engedélyeznie kell mind a **soft delete,** mind a **Ne ürítse** ki a kulcstartót. A key vault kell lennie ugyanabban az előfizetésben, mint a fürt. Az Azure Data Explorer felügyelt identitások használatával hitelesíti magát a kulcstárolóban a titkosítási és visszafejtési műveletekhez. A felügyelt identitások nem támogatják a címtárközi forgatókönyveket.

#### <a name="rotate-customer-managed-keys"></a>Ügyfél által kezelt kulcsok elforgatása

Az azure-key vaultban az ügyfél által felügyelt kulcsot a megfelelőségi szabályzatok szerint forgathatja. A kulcs elforgatása után frissítenie kell a fürtöt az új kulcs URI-jának használatához. A kulcs elforgatása nem indítja el a fürtben lévő adatok újratitkosítását. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShell vagy az Azure CLI használatát. További információ: [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) vagy [Azure Key Vault CLI.](/cli/azure/keyvault) A hozzáférés visszavonása blokkolja a fürt tárolási szintjén lévő összes adathoz való hozzáférést, mivel a titkosítási kulcsot ezért az Azure Data Explorer nem éri el.

> [!Note]
> Amikor az Azure Data Explorer azonosítja, hogy az ügyfél által kezelt kulcshoz való hozzáférés visszavonásra kerül, automatikusan felfüggeszti a fürtöt a gyorsítótárazott adatok törléséhez. A kulcshoz való hozzáférés visszaadása után a fürtöt manuálisan kell folytatni.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Szerepköralapú [hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)használatával elkülönítheti a feladatokat a csapaton belül, és csak a szükséges hozzáférést adja meg a fürtfelhasználóknak. Ahelyett, hogy mindenkinek korlátlan engedélyeket adna a fürthöz, csak bizonyos műveleteket engedélyezhet. Az Azure CLI vagy az [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)használatával konfigurálhatja [az adatbázisok hozzáférés-vezérlését](/azure/data-explorer/manage-database-permissions) az [Azure Portalon.](/azure/role-based-access-control/role-assignments-portal) [Azure CLI](/azure/role-based-access-control/role-assignments-cli)

## <a name="next-steps"></a>További lépések

* [Biztonságossá a fürt az Azure Data Explorer – Portal](manage-cluster-security.md) titkosítás engedélyezése inaktív módon.
* [Felügyelt identitások konfigurálása az Azure Data Explorer-fürthöz](managed-identities.md)
* [Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)
* [Ügyfél által kezelt kulcsok konfigurálása C használatával #](customer-managed-keys-csharp.md)

