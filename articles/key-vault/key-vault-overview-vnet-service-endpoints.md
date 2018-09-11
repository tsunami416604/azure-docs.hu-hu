---
ms.assetid: ''
title: Virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault |} A Microsoft Docs
description: A Key Vault számára a virtuális hálózati Szolgáltatásvégpontok áttekintése
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: c2696d5eb22443b565c48ef4f96d6e4a25827606
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295004"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuális hálózati Szolgáltatásvégpontok az Azure Key Vaultban

A virtuális hálózati Szolgáltatásvégpontok a Key vault lehetővé teszi korlátozza a hozzáférést a megadott virtuális hálózathoz, illetve IPv4-címtartományokat (az Internet Protocol version 4) listáját. A key vault kívül tudja a forrásokhoz való kapcsolódás bármilyen hívó sem lesz hozzáférése. Ha az ügyfél rendelkezik kilépteti a "Megbízható Microsoft-szolgáltatások" lehetővé teszik, ilyen például az Office 365 Exchange online-hoz, az Office 365 SharePoint online-hoz, az Azure compute, Azure Resource Manager, az Azure biztonsági mentés stb., ezeket a szolgáltatásokat a kapcsolatok lehetővé teszi a tűzfalon keresztül. Természetesen az ilyen hívóit továbbra is kell megadnia a érvényes AAD-jogkivonatot, és (a hozzáférési házirendek szerint konfigurálva) engedélyekkel rendelkezik a kért művelet végrehajtásához. További technikai részleteket olvashat [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Felhasználási területek

Konfigurálható [Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md) számára megtagadja a hozzáférést a forgalomra, minden hálózatból (beleértve az internetes forgalmat) alapértelmezés szerint. Hozzáférés adható a forgalmat az adott Azure virtuális hálózatok és/vagy a nyilvános interneten IP-cím-tartományait, így az alkalmazások biztonságos hálózati határt hozhat létre.

> [!NOTE]
> A Key Vault-tűzfalak és virtuális hálózati szabályok csak vonatkoznak a key vault [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control). A Key Vault vezérlési síkjával végzett műveletek (például a key vault létrehozása, törlés, műveletek, a hozzáférési szabályzatok beállítása a tűzfalak és virtuális hálózati szabályok beállítása módosítása) nem érinti a tűzfalak és virtuális hálózati szabályok.

Például:
* Ha használ a Key Vaultban tárolni a titkosítási kulcsok, titkos alkalmazáskulcsok, tanúsítványok, és szeretné a kulcstartó hozzáférési megakadályozza a nyilvános interneten.
* A key vaulthoz való hozzáférés zárolását, így csak az alkalmazás vagy egy rövid lista a kijelölt gazdagépek tud csatlakozni a key vault szeretné
* Egy alkalmazást az Azure virtuális hálózat (VNET) futó és a virtuális hálózat zárolva van az összes bejövő és kimenő forgalmat. Az alkalmazás továbbra is kell csatlakoznia vagy beolvasni a titkos kódok és tanúsítványok, vagy a titkosítási kulcsok használata a key vault.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>A Key Vault-tűzfalak és virtuális hálózatok konfigurálása

Az alábbiakban a tűzfalak és virtuális hálózatok konfigurálásához szükséges lépéseket. Ezeket a lépéseket marad, függetlenül attól, milyen interface (PowerShell, CLI, az Azure portal) fogja használni a tűzfal és a virtuális hálózati szabályok azonos.
1. Nem kötelező, de erősen ajánlott: engedélyezése [key vault naplózása](key-vault-logging.md) részletes hozzáférés-naplók megtekintéséhez. Ez segít a diagnosztika során tűzfalak és virtuális hálózati szabályok a key vault való hozzáférés letiltása.
2. A cél virtuális hálózat és alhálózat "key vault szolgáltatásvégpont" engedélyezése
3. Tűzfalak és virtuális hálózati szabályok adott kulcstartóhoz való hozzáférés korlátozása a megadott virtuális hálózat, alhálózat és IPv4-címtartományokat key vault beállítása.
4. A kulcstartó kell lennie minden olyan megbízható Microsoft-szolgáltatások által elérhető, ha azt a lehetőséget, hogy a "Megbízható Azure-szolgáltatások" szeretne csatlakozni a key vault engedélyeznie kell.

Tekintse meg [konfigurálása az Azure Key Vault tűzfalak és virtuális hálózatok](key-vault-network-security.md) részletes útmutatásért.

> [!IMPORTANT]
> Ha tűzfalszabályok érvényben, az összes Key Vault [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) csak akkor hajtható végre műveleteket, ha a hívó ügyfélkérések engedélyezett virtuális hálózat vagy IPV4-címtartományokat. Ez vonatkozik a key vault elérése az Azure Portalról. Amíg a felhasználó böngészőben is a key vault Azure Portalról, előfordulhat, hogy nem tudnak kulcsok/titkos kulcsok és tanúsítványok listája, ha az ügyfélszámítógép nem szerepel az engedélyezési listán. Ez is hatással van a "Key Vault választó" más Azure-szolgáltatások. Lehet, hogy a felhasználók kulcstartók listája, de nem kulcsok, listázását, ha a tűzfal-szabályok megakadályozzák fejlesztőkörnyezetükben.


> [!NOTE]
> * Egy legfeljebb 127 VNET-szabályok és 127 IPv4-szabályok használata engedélyezett. 
> * Kis címtartományok használatával "/ 31" vagy "/ 32" előtag méretei nem támogatottak. Ezek a tartományok egyedi IP-cím szabályok használatával kell konfigurálni.
> * IP-hálózati szabályok csak a nyilvános IP-címek engedélyezettek. Magánhálózatok számára fenntartott (RFC 1918-ban meghatározott) IP-címtartományok az IP-szabályok nem engedélyezettek. Magánhálózatokat közé tartozik a címek kezdődő * 10.* *, * 172.16. **, és a * 192.168. **. 
> * Jelenleg csak az IPv4-cím támogatott.

## <a name="trusted-services"></a>Megbízható szolgáltatások
Ez egy lista a megbízható szolgáltatások, amelyek jogosultak egy kulcstartó eléréséhez, ha az "Allow megbízható szolgáltatások" beállítás engedélyezve van.

|Megbízható szolgáltatás|Felhasználási területek|
| --- | --- |
|Az Azure Virtual Machines üzembe helyezési szolgáltatása|[Tanúsítványok telepítése a virtuális gépek ügyfél által felügyelt Key vaultból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Az Azure Resource Manager sablontelepítési szolgáltatása|[Biztonságos értékek továbbítása üzembe helyezés során](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Az Azure Disk Encryption kötettitkosítási szolgáltatása|BitLocker-kulcs (VM-Windows) vagy a DM jelszót (a Linux rendszerű virtuális gép), a Kulcsalapú titkosítás kulcsa való hozzáférés engedélyezése a virtuális gép üzembe helyezésének engedélyezése során [az Azure Disk Encryption](../security/azure-security-disk-encryption.md)|
|Azure Backup|Lehetővé teszi a biztonsági mentéséhez és visszaállításához kapcsolódó kulcsok és titkos kulcsok Azure VM backup esetében során használatával [Azure Backup](../backup/backup-introduction-to-azure-backup.md)|
|Az Exchange Online és SharePoint Online|Ügyfélkulcs való hozzáférés engedélyezése a szolgáltatás titkosítási [Ügyfélkulcs](https://support.office.com/en-us/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|A bérlői kulcs való hozzáférés engedélyezése [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|App Services|[Key Vault segítségével az Azure webalkalmazás-tanúsítvány telepítése](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Transzparens adattitkosítás Bring Your Own Key-támogatással az Azure SQL Database és a Data warehouse-bA](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Felhasználó által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[Az Azure Data Lake Store az adatok titkosítása az](../data-lake-store/data-lake-store-encryption.md) az ügyfél által felügyelt kulcsot|



> [!NOTE]
> A megfelelő kulcstartó-hozzáférési szabályzatok kell beállítani, hogy a megfelelő szolgáltatásokat a key vault eléréséhez.

## <a name="next-steps"></a>További lépések

* [Kulcstartó védelme](key-vault-secure-your-key-vault.md)
* [Az Azure Key Vault-tűzfalak és virtuális hálózatok konfigurálása](key-vault-network-security.md)