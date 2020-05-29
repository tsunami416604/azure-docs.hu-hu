---
title: Azure Key Vault-Azure Key Vault virtuális hálózati szolgáltatásbeli végpontok | Microsoft Docs
description: A Key Vault virtuális hálózati szolgáltatási végpontjának áttekintése
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 9a6fab9b98029eeefdd4649b0949a1a61da8b0c1
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168828"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuális hálózati szolgáltatás végpontjai Azure Key Vault

A Azure Key Vault Virtual Network szolgáltatásbeli végpontok lehetővé teszik, hogy korlátozza a hozzáférést egy adott virtuális hálózathoz. A végpontok azt is lehetővé teszik, hogy korlátozza a hozzáférést az IPv4 (Internet Protocol Version 4)-címtartományok listájához. A Key vaulthoz a forrásokon kívülről csatlakozó felhasználók hozzáférése megtagadva.

Erre a korlátozásra vonatkozóan van egy fontos kivétel. Ha egy felhasználó engedélyezte a megbízható Microsoft-szolgáltatások engedélyezését, a szolgáltatással létesített kapcsolatok a tűzfalon keresztül érhetők el. Ilyen szolgáltatások például az Office 365 Exchange Online, az Office 365 SharePoint Online, az Azure számítás, a Azure Resource Manager és a Azure Backup. Az ilyen felhasználóknak továbbra is érvényes Azure Active Directory jogkivonatot kell tartalmazniuk, és a kért művelet végrehajtásához engedélyekkel kell rendelkezniük (hozzáférési házirendként konfigurálva). További információ: [Virtual Network szolgáltatás-végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Használati forgatókönyvek

A [Key Vault tűzfalak és virtuális hálózatok](network-security.md) konfigurálásával megtagadhatja az adatforgalom elérését az összes hálózatról (beleértve az internetes forgalmat is), alapértelmezés szerint. Hozzáférést biztosíthat bizonyos Azure-beli virtuális hálózatokból és nyilvános internetes IP-címtartományok érkező adatforgalomhoz, így biztonságos hálózati határt hozhat létre az alkalmazásai számára.

> [!NOTE]
> Key Vault tűzfalak és virtuális hálózati szabályok csak Key Vault [adatsíkon](secure-your-key-vault.md#data-plane-access-control) érvényesek. A tűzfalak és a virtuális hálózati szabályok nem érintik a vezérlési sík műveleteit (például a létrehozási, törlési és módosítási műveletek, a hozzáférési házirendek beállítása, a tűzfalak beállítása és a virtuális hálózati szabályok). Key Vault

Íme néhány példa a szolgáltatási végpontok használatára:

* A Key Vault használatával tárolja a titkosítási kulcsokat, az alkalmazási titkokat és a tanúsítványokat, és le szeretné tiltani a Key vaulthoz való hozzáférést a nyilvános internetről.
* Le szeretné zárni a kulcstartóhoz való hozzáférést, hogy csak az alkalmazása vagy a kijelölt gazdagépek rövid listája csatlakozhasson a kulcstartóhoz.
* Rendelkezik egy, az Azure-beli virtuális hálózaton futó alkalmazással, és ez a virtuális hálózat le van zárva az összes bejövő és kimenő forgalom esetében. Az alkalmazásnak továbbra is csatlakoznia kell Key Vaulthoz a titkok vagy tanúsítványok lekéréséhez, vagy titkosítási kulcsokat kell használnia.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault-tűzfalak és virtuális hálózatok konfigurálása

A következő lépések szükségesek a tűzfalak és a virtuális hálózatok konfigurálásához. Ezek a lépések a PowerShell, az Azure CLI vagy a Azure Portal használata esetén érvényesek.

1. Engedélyezze [Key Vault naplózást](logging.md)) a részletes hozzáférési naplók megjelenítéséhez. Ez segít a diagnosztikaben, ha a tűzfalak és a virtuális hálózati szabályok megakadályozzák a hozzáférést a kulcstartóhoz. (Ez a lépés nem kötelező, de kifejezetten ajánlott.)
2. A **Key Vault szolgáltatási végpontjának** engedélyezése célként megadott virtuális hálózatokhoz és alhálózatokhoz.
3. Egy kulcstartóhoz tartozó tűzfalak és virtuális hálózati szabályok beállításával korlátozhatja a kulcstartóhoz való hozzáférést adott virtuális hálózatokból, alhálózatokból és IPv4-címtartományokből.
4. Ha a kulcstartónak bármely megbízható Microsoft-szolgáltatás számára elérhetőnek kell lennie, engedélyezze a **megbízható Azure-szolgáltatások** Key Vaulthoz való kapcsolódásának lehetőségét.

További információ: [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md).

> [!IMPORTANT]
> A tűzfalszabályok érvénybe léptetése után a felhasználók csak akkor hajthatják végre Key Vault [adatsík](secure-your-key-vault.md#data-plane-access-control) -műveleteket, ha a kérésük engedélyezett virtuális hálózatokból vagy IPv4-címtartományok származnak. Ez a Azure Portal Key Vault elérésére is vonatkozik. Bár a felhasználók megkereshetik a kulcstartót a Azure Portalból, előfordulhat, hogy nem tudják listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezési listán. Ez hatással van a más Azure-szolgáltatások Key Vault választóra is. Előfordulhat, hogy a felhasználók megtekinthetik a kulcstárolók listáját, de nem listázják a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.


> [!NOTE]
> Vegye figyelembe a következő konfigurációs korlátozásokat:
> * Legfeljebb 127 virtuális hálózati szabály és 127 IPv4-szabály engedélyezett. 
> * A "/31" vagy "/32" előtagot használó kisméretű címtartományok nem támogatottak. Ehelyett konfigurálja ezeket a tartományokat az egyes IP-címek szabályainak használatával.
> * Az IP-hálózati szabályok csak nyilvános IP-címek esetén engedélyezettek. A magánhálózati hálózatok számára fenntartott IP-címtartományok (az RFC 1918-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok közé tartoznak a következők: **10.**, **172.16-31**és **192,168.** 
> * Jelenleg csak IPv4-címek támogatottak.

## <a name="trusted-services"></a>Megbízható szolgáltatások

Itt látható azoknak a megbízható szolgáltatásoknak a listája, amelyek hozzáférhetnek a kulcstartóhoz, ha engedélyezve van a **megbízható szolgáltatások engedélyezése** beállítás.

|Megbízható szolgáltatás|Támogatott használati forgatókönyvek|
| --- | --- |
|Azure Virtual Machines üzembe helyezési szolgáltatás|[Tanúsítványokat telepíthet az ügyfél által felügyelt Key Vault virtuális gépekre](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Azure Resource Manager sablon központi telepítési szolgáltatása|[Biztonságos értékek továbbítása az üzembe helyezés során](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Application Gateway v2 SKU|[TLS-lezárás Key Vault tanúsítványokkal](/azure/application-gateway/key-vault-certs)|
|Azure Disk Encryption mennyiségi titkosítási szolgáltatás|Hozzáférés engedélyezése a BitLocker-kulcshoz (Windows VM) vagy DM-jelszóhoz (Linux virtuális gép) és a kulcs titkosítási kulcsához a virtuális gépek telepítése során. Ez lehetővé teszi a [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|A megfelelő kulcsok és titkok biztonsági mentésének és helyreállításának engedélyezése az Azure Virtual Machines Backup szolgáltatásban [Azure Backup](../../backup/backup-introduction-to-azure-backup.md)használatával.|
|Exchange Online & SharePoint Online-ban|Hozzáférés engedélyezése az Azure Storage Service Encryptionhoz az [ügyfél kulcsával](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|A bérlői kulcs elérésének engedélyezése [Azure Information Protection számára.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Azure webalkalmazás-tanúsítvány üzembe helyezése Key Vault használatával](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transzparens adattitkosítás a Azure SQL Database és az adattárház bring your own Key támogatásával](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Storage Service encryption az ügyfél által felügyelt kulcsokat Azure Key Vault](../../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|A [Azure Data Lake Storeban lévő adattitkosítás](../../data-lake-store/data-lake-store-encryption.md) ügyfél által felügyelt kulccsal.|
|Azure Databricks|[Gyors, könnyű és együttműködő Apache Spark-alapú elemzési szolgáltatás](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Egyéni tartomány tanúsítványainak központi telepítése Key Vault az MSI használatával](../../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Adattároló hitelesítő adatainak beolvasása a Key Vault Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Key Vault hozzáférésének engedélyezése az ügyfél által felügyelt kulcsok esetén](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Key Vault hozzáférésének engedélyezése az ügyfél által felügyelt kulcsok esetén](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Az ügyfél által felügyelt kulcsok használata Azure Key Vault importálási/exportálási szolgáltatáshoz](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)
|Azure Container Registry|[Beállításjegyzék-titkosítás az ügyfél által felügyelt kulcsokkal](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> Be kell állítania a megfelelő Key Vault hozzáférési szabályzatokat, hogy a megfelelő szolgáltatások hozzáférjenek a Key Vaulthoz.

## <a name="next-steps"></a>Következő lépések

* [A Key Vault biztonságossá tétele](secure-your-key-vault.md))
* [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md)
