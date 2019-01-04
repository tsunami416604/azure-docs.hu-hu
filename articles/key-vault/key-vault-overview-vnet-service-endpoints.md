---
ms.assetid: ''
title: Virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault – Azure Key Vault |} A Microsoft Docs
description: A Key vault virtuális hálózati Szolgáltatásvégpontok áttekintése
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 01/02/2019
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 1d53fc6cef022f627bb1cd1f832ebf65698207a9
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002423"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault

A virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault lehetővé teszi, hogy korlátozza a hozzáférést egy adott virtuális hálózatban. A végpontok is lehetővé teszik az IPv4-címtartományokat (az internet protocol version 4) listáját való hozzáférés korlátozásához. A key vault kívül tudja a forrásokhoz való csatlakozás bármely felhasználó hozzáférése megtagadva.

Ez a korlátozás egyetlen fontos kivétel van. A felhasználó rendelkezik kilépteti a megbízható Microsoft-szolgáltatások, ezeket a szolgáltatásokat érkező kapcsolatokat is lehetővé teszik a tűzfalon keresztül. Például ilyen szolgáltatás például az Office 365 Exchange online-hoz, az Office 365 SharePoint online-hoz, az Azure számítási, Azure Resource Manager és az Azure Backup. Ezek a felhasználók továbbra is kell megadnia a egy érvényes Azure Active Directory-jogkivonatot, és kell (a hozzáférési házirendek szerint konfigurálva) engedélyekkel rendelkezik a kért művelet végrehajtásához. További információkért lásd: [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Felhasználási területek

Konfigurálható [Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md) számára megtagadja a hozzáférést a forgalomra, minden hálózatból (beleértve az internetes forgalmat) alapértelmezés szerint. Biztosíthat hozzáférést az adott Azure virtuális hálózatok és a nyilvános internetről forgalmat IP-cím-tartományait, így az alkalmazások biztonságos hálózati határt hozhat létre.

> [!NOTE]
> A Key Vault-tűzfalak és virtuális hálózati szabályok csak az a alkalmazni a [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key vault. A Key Vault vezérlési síkjával végzett műveletek (például létrehozása, törlése és módosítása operations, hozzáférési szabályzatok beállítása, a beállítást tűzfalak és virtuális hálózati szabályok) nem érinti a tűzfalak és virtuális hálózati szabályok.

Íme néhány példa a Szolgáltatásvégpontok használatára:

* A Key Vault segítségével tárolja a titkosítási kulcsok, titkos alkalmazáskulcsok és tanúsítványok, és szeretné a kulcstartó hozzáférési tiltsa le a nyilvános interneten.
* Szeretné, hogy csak az alkalmazás, vagy egy rövid lista a kijelölt gazdagépek, a key vault képes csatlakozni a key vaulthoz való hozzáférés zárolását.
* Az Azure virtuális hálózaton futó alkalmazás rendelkezik, és ez a virtuális hálózat zárolva van az összes bejövő és kimenő forgalmat. Az alkalmazása továbbra is szeretne csatlakozni a Key Vault titkos kódok és tanúsítványok beolvasása, vagy a titkosítási kulcsok használata szükséges.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>A Key Vault-tűzfalak és virtuális hálózatok konfigurálása

Az alábbiakban a tűzfalak és virtuális hálózatok konfigurálásához szükséges lépéseket. Ezeket a lépéseket a PowerShell, az Azure CLI vagy az Azure Portalon használja-e alkalmazni.

1. Engedélyezése [Key Vault naplózásának](key-vault-logging.md) részletes hozzáférés-naplók megtekintéséhez. Ez segít diagnosztikát, amikor a tűzfalak és virtuális hálózati szabályok a key vault való hozzáférés letiltása. (Ez a lépés nem, nem kötelező, de erősen ajánlott.)
2. Engedélyezése **szolgáltatásvégpontokat kulcstartó** a cél virtuális hálózatok és alhálózatok.
3. Tűzfalak és virtuális hálózati szabályok adott kulcstartóhoz való hozzáférés korlátozása adott virtuális hálózatok, alhálózatok és IPv4-címtartományokat a key vault beállítása.
4. Ha ezt a kulcstartót kell lennie minden olyan megbízható Microsoft-szolgáltatások által elérhető, engedélyezze a lehetőséget, hogy **megbízható Azure-szolgáltatások** szeretne csatlakozni a Key Vaultban.

További információkért lásd: [konfigurálása az Azure Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md).

> [!IMPORTANT]
> Tűzfalszabályok vannak érvényben, miután a felhasználók csak hajthat végre a Key Vault [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) műveleteket, ha az ügyfélkérések az engedélyezett virtuális hálózatok vagy IPv4-címtartományokat. Ez vonatkozik a Key Vault elérése az Azure Portalról. Bár az Azure Portalon, egy kulcstárolóba is böngésző felhasználók számára, akkor előfordulhat, hogy nem tudják kulcsok listázása, titkos kódok és tanúsítványok, ha az ügyfélszámítógép nem szerepel az engedélyezési listán. Ez is hatással van a Key Vault-választó más Azure-szolgáltatások. Lehet, hogy a felhasználók tudni kulcstartók listája látható, de nem kulcsok, listázását, ha a tűzfal-szabályok megakadályozzák fejlesztőkörnyezetükben.


> [!NOTE]
> Vegye figyelembe a következő konfigurációs korlátozások vonatkoznak:
> * Legfeljebb 127 virtuális hálózati szabályok és 127 IPv4-szabályok használata engedélyezett. 
> * Kis-címtartományok, használja a "/ 31" vagy "/ 32" előtag méretei nem támogatottak. Konfigurálja a tartományok egyedi IP-cím szabályok használatával.
> * IP-hálózati szabályok csak a nyilvános IP-címek engedélyezettek. Magánhálózatok számára fenntartott (RFC 1918-ban meghatározott) IP-címtartományok az IP-szabályok nem engedélyezettek. Magánhálózatokat közé tartozik a címek kezdődő **10.**, **172.16.**, és **192.168.**. 
> * Jelenleg csak az IPv4-cím támogatott.

## <a name="trusted-services"></a>Megbízható szolgáltatások

A megbízható szolgáltatások, amelyek jogosultak egy kulcstartó eléréséhez, ha a következők a **megbízható szolgáltatások** beállítás engedélyezve van.

|Megbízható szolgáltatás|Felhasználási területek|
| --- | --- |
|Az Azure Virtual Machines üzembe helyezési szolgáltatása|[Tanúsítványok telepítése a virtuális gépek ügyfél által felügyelt Key vaultból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Az Azure Resource Manager sablontelepítési szolgáltatása|[Biztonságos értékek továbbítása üzembe helyezés során](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Az Azure Disk Encryption kötettitkosítási szolgáltatása|BitLocker-kulcs (VM-Windows) vagy a DM jelszót (a Linux rendszerű virtuális gép), a Kulcsalapú titkosítás kulcsa, való hozzáférés engedélyezése a virtuális gép üzembe helyezése során. Ez lehetővé teszi a [az Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Lehetővé teszi a biztonsági mentéséhez és visszaállításához kapcsolódó kulcsok és titkos kulcsok Azure Virtual Machines biztonsági mentés során, a [Azure Backup](../backup/backup-introduction-to-azure-backup.md).|
|Az Exchange Online és SharePoint Online|Ügyfélkulcs való hozzáférés engedélyezése az Azure Storage Service Encryption az [Ügyfélkulcs](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|A bérlői kulcs való hozzáférés engedélyezése [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Üzembe helyezése az Azure webalkalmazás-tanúsítvány Key Vault segítségével](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Azure SQL Database|[Transzparens adattitkosítás Bring Your Own Key-támogatással az Azure SQL Database és a Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Felhasználó által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Az Azure Data Lake Store az adatok titkosítása az](../data-lake-store/data-lake-store-encryption.md) ügyfél által felügyelt kulccsal.|



> [!NOTE]
> Be kell állítania a Key Vault vonatkozó hozzáférési szabályzatokat, hogy a Key Vault eléréséhez megfelelő szolgáltatásokat.

## <a name="next-steps"></a>További lépések

* [Kulcstartó védelme](key-vault-secure-your-key-vault.md)
* [Az Azure Key Vault-tűzfalak és virtuális hálózatok konfigurálása](key-vault-network-security.md)
