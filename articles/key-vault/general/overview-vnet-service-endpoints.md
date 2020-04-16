---
title: Virtuális hálózati szolgáltatás végpontjai az Azure Key Vaulthoz – Azure Key Vault | Microsoft dokumentumok
description: A Key Vault virtuális hálózati szolgáltatásvégpontjainak áttekintése
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 2a68a50a5d15b9f38407c19494a39a14abfa0a5a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432072"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuális hálózati szolgáltatás végpontjai az Azure Key Vaulthoz

Az Azure Key Vault virtuális hálózati szolgáltatásvégpontjai lehetővé teszik a hozzáférés korlátozását egy adott virtuális hálózathoz. A végpontok azt is lehetővé teszik, hogy korlátozza a hozzáférést az IPv4 (internet protokoll 4-es verzió) címtartományok listájához. A kulcstartóhoz ezeken a forrásokon kívülről csatlakozó felhasználók számára a rendszer megtagadja a hozzáférést.

Van egy fontos kivétel ez alól a korlátozás alól. Ha egy felhasználó engedélyezte a megbízható Microsoft-szolgáltatások engedélyezését, az ilyen szolgáltatásokból származó kapcsolatok átjutnak a tűzfalon. Ezek a szolgáltatások közé tartozik például az Office 365 Exchange Online, az Office 365 SharePoint Online, az Azure compute, az Azure Resource Manager és az Azure Backup. Ezeka felhasználók továbbra is be kell mutatniuk egy érvényes Azure Active Directory-jogkivonatot, és rendelkezniük kell engedélyekkel (hozzáférési szabályzatként konfigurálva) a kért művelet végrehajtásához. További információ: [Virtual network service endpoints](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Használati forgatókönyvek

Beállíthatja, hogy [a Key Vault tűzfalak és a virtuális hálózatok](network-security.md) alapértelmezés szerint megtagadják a hozzáférést az összes hálózatról (beleértve az internetes forgalmat is). Hozzáférést biztosíthat az adott Azure virtuális hálózatokból és nyilvános internetes IP-címtartományokból származó forgalomhoz, lehetővé téve, hogy biztonságos hálózati határt hozzon létre az alkalmazások számára.

> [!NOTE]
> A Key Vault tűzfalai és a virtuális hálózati szabályok csak a Key Vault [adatsíkjára](secure-your-key-vault.md#data-plane-access-control) vonatkoznak. A Key Vault vezérlősíkműveleteit (például a műveletek létrehozása, törlése és módosítása, a hozzáférési házirendek beállítása, a tűzfalak és a virtuális hálózati szabályok) nem érintik a tűzfalak és a virtuális hálózati szabályok.

Íme néhány példa a szolgáltatásvégpontok használatára:

* A Key Vault segítségével tárolja a titkosítási kulcsokat, alkalmazástitkokat és tanúsítványokat, és le szeretné tiltani a hozzáférést a kulcstartóhoz a nyilvános internetről.
* Azt szeretné, hogy a kulcstartóhoz való hozzáférés zárolása, hogy csak az alkalmazás, vagy egy rövid listát a kijelölt gazdagépek, csatlakozhat a key vault.
* Van egy alkalmazás fut az Azure virtuális hálózatban, és ez a virtuális hálózat zárolva van az összes bejövő és kimenő forgalom. Az alkalmazásnak továbbra is csatlakoznia kell a Key Vaulthoz titkos kulcsok vagy tanúsítványok lekéréséhez, vagy kriptográfiai kulcsok használatához.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault-tűzfalak és virtuális hálózatok konfigurálása

A tűzfalak és a virtuális hálózatok konfigurálásához szükséges lépések az alábbiakban találhatók. Ezek a lépések a PowerShell, az Azure CLI vagy az Azure Portal használatával.

1. A [Key Vault naplózásának](logging.md)engedélyezése) a részletes hozzáférési naplók megtekintéséhez. Ez segít a diagnosztika, amikor a tűzfalak és a virtuális hálózati szabályok megakadályozzák a hozzáférést a key vault. (Ez a lépés nem kötelező, de erősen ajánlott.)
2. A célvirtuális hálózatok és alhálózatok **szolgáltatásvégpontjainak** engedélyezése a kulcstárolóhoz.
3. Állítson be tűzfalakat és virtuális hálózati szabályokat a kulcstartóhoz, hogy korlátozza a hozzáférést az adott kulcstartóhoz adott virtuális hálózatokról, alhálózatokról és IPv4-címtartományokból.
4. Ha ezt a kulcstartót bármely megbízható Microsoft-szolgáltatás nak elérhetővé kell tennie, engedélyezze, hogy a **Trusted Azure Services** csatlakozzon a Key Vaulthoz.

További információ: [Az Azure Key Vault tűzfalainak és virtuális hálózatainak konfigurálása.](network-security.md)

> [!IMPORTANT]
> A tűzfalszabályok érvénybe lépése után a felhasználók csak akkor hajthatnak végre Key [Vault-adatsík-műveleteket,](secure-your-key-vault.md#data-plane-access-control) ha a kéréseik engedélyezett virtuális hálózatokból vagy IPv4-címtartományokból származnak. Ez az Azure Portalról való key vault elérésére is vonatkozik. Bár a felhasználók az Azure Portalról is megkereshetik a kulcstartót, előfordulhat, hogy nem tudják felsorolni a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélgépük nem szerepel az engedélyezett listában. Ez hatással van a Key Vault-választó más Azure-szolgáltatások által. Előfordulhat, hogy a felhasználók láthatják a kulcstartók listáját, de a kulcsokat nem, ha a tűzfalszabályok megakadályozzák az ügyfélgépüket.


> [!NOTE]
> Ne feledje, hogy a következő konfigurációs korlátozások:
> * Legfeljebb 127 virtuális hálózati szabály és 127 IPv4-szabály engedélyezett. 
> * A "/31" vagy "/32" előtagméretet használó kis címtartományok nem támogatottak. Ehelyett konfigurálja ezeket a tartományokat egyedi IP-címszabályok használatával.
> * Az IP-hálózati szabályok csak nyilvános IP-címek esetén engedélyezettek. A magánhálózatok számára fenntartott (az 1918-as rfc-ben meghatározott) IP-címtartományok nem engedélyezettek az IP-szabályokban. A magánhálózatok **10.** **172.16-31** **192.168.** 
> * Jelenleg csak az IPv4-címek támogatottak.

## <a name="trusted-services"></a>Megbízható szolgáltatások

Az alábbiakban azokat a megbízható szolgáltatásokat listázhatja, amelyek hozzáférhetnek a kulcstartóhoz, ha a **Megbízható szolgáltatások engedélyezése** beállítás engedélyezve van.

|Megbízható szolgáltatás|Támogatott használati forgatókönyvek|
| --- | --- |
|Az Azure virtuális gépek telepítési szolgáltatása|[Tanúsítványok üzembe helyezése virtuális gépekre az ügyfél által felügyelt Key Vaultból.](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Az Azure Resource Manager sablontelepítési szolgáltatása|[Biztonságos értékek et ad át az üzembe helyezés során.](../../azure-resource-manager/templates/key-vault-parameter.md)|
|Az Azure Disk Encryption kötettitkosítási szolgáltatása|A bitlockerkulcshoz (Windows VM) vagy a DM-jelszóhoz (Linux virtuális gép) és a kulcstitkosítási kulcshoz való hozzáférés engedélyezése a virtuális gép üzembe helyezése során. Ez lehetővé teszi [az Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Az Azure Backup használatával engedélyezheti a megfelelő kulcsok és titkos kulcsok biztonsági mentését és visszaállítását az [Azure](../../backup/backup-introduction-to-azure-backup.md)Virtuális gépek biztonsági mentése során.|
|Exchange Online & SharePoint Online|Hozzáférés engedélyezése az ügyfélkulcshoz az Azure Storage Service Encryption [számára ügyfélkulccsal.](/microsoft-365/compliance/customer-key-overview)|
|Azure Information Protection|Az Azure Information Protection bérlői kulcshoz való hozzáférés [engedélyezése.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Telepítse az Azure Web App tanúsítványát a Key Vaulton keresztül.](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)|
|Azure SQL Database|[Átlátszó adattitkosítás saját kulcstámogatással az Azure SQL Database és data warehouse számára.](../../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Storage Service Encryption az ügyfél által felügyelt kulcsok használatával az Azure Key Vaultban.](../../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[Adatok titkosítása az Azure Data Lake Store-ban](../../data-lake-store/data-lake-store-encryption.md) egy ügyfél által felügyelt kulccsal.|
|Azure Databricks|[Gyors, egyszerű és együttműködő Apache Spark-alapú elemzési szolgáltatás](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Tanúsítványok telepítése egyéni tartományhoz a Key Vaultból az MSI használatával](../../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Adattároló hitelesítő adatainak beolvasása a Key Vaultban a Data Factory-ból](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Hozzáférés engedélyezése a kulcstartóhoz az ügyfél által felügyelt kulcsok esetén](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Hozzáférés engedélyezése a kulcstartóhoz az ügyfél által felügyelt kulcsok esetén](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Ügyfél által felügyelt kulcsok használata az Azure Key Vaultban importálási/exportálási szolgáltatáshoz](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)

> [!NOTE]
> Be kell állítania a megfelelő Key Vault hozzáférési szabályzatokat, hogy a megfelelő szolgáltatások hozzáférjenek a Key Vaulthoz.

## <a name="next-steps"></a>További lépések

* [A kulcstartó biztonsága)](secure-your-key-vault.md)
* [Az Azure Key Vault tűzfalainak és virtuális hálózatainak konfigurálása](network-security.md)
