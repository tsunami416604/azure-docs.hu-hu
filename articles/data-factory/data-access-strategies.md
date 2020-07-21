---
title: Adathozzáférési stratégiák
description: A Azure Data Factory mostantól támogatja a statikus IP-címtartományok használatát.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 015feac819467cf60bfb2faab27af769fadc3cfa
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86522873"
---
# <a name="data-access-strategies"></a>Adathozzáférési stratégiák

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A szervezet létfontosságú biztonsági célja az adattárak védelme az interneten keresztüli véletlenszerű hozzáféréssel, lehet helyszíni vagy felhőalapú/SaaS-adattár is. 

A Felhőbeli adattár jellemzően az alábbi mechanizmusokkal szabályozza a hozzáférést:
* Privát hivatkozás egy Virtual Networkról privát Endpoint-kompatibilis adatforrásokra
* A kapcsolódást IP-cím alapján korlátozó tűzfalszabályok
* Hitelesítési mechanizmusok, amelyeknek a felhasználóknak igazolniuk kell identitásukat
* Engedélyezési mechanizmusok, amelyek korlátozzák a felhasználókat az adott műveletekre és adatszolgáltatásokra

> [!TIP]
> A [statikus IP-címtartomány bevezetésével](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)mostantól engedélyezheti az adott Azure Integration RUNTIME régió IP-címeinek listázását, így biztosítva, hogy az összes Azure IP-cím ne legyen engedélyezve a Felhőbeli adattárakban. Így korlátozhatja azokat az IP-címeket, amelyek jogosultak az adattárak elérésére.

> [!NOTE] 
> Az IP-címtartományok blokkolva vannak a Azure Integration Runtime számára, és jelenleg csak adatáthelyezésre, folyamatra és külső tevékenységekre használható. A felügyelt Virtual Networkt engedélyező adatfolyamok és Azure Integration Runtime mostantól nem használják ezeket az IP-tartományokat. 

Ennek számos forgatókönyvben kell működnie, és tisztában vagyunk vele, hogy az integrációs modulhoz tartozó egyedi statikus IP-cím kívánatos, de ez nem lehetséges, ha a Azure Integration Runtime jelenleg nem a kiszolgáló nélküli. Ha szükséges, bármikor beállíthatja a saját üzemeltetésű Integration Runtime, és használhatja a statikus IP-címét. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Adathozzáférési stratégiák Azure Data Factory

* **[Privát hivatkozás](https://docs.microsoft.com/azure/private-link/private-link-overview)** – létrehozhat egy Azure Integration Runtimet Azure Data Factory felügyelt Virtual Networkon belül, és a saját végpontokat fogja használni a támogatott adattárakhoz való biztonságos csatlakozáshoz. A felügyelt Virtual Network és az adatforrások közötti forgalom a Microsoft gerinc-hálózatba kerül, és nem a nyilvános hálózatra van kitéve.
* **[Megbízható szolgáltatás](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** – az Azure Storage (Blob, ADLS Gen2) támogatja a tűzfal-konfigurációt, amely lehetővé teszi a megbízható Azure platform-szolgáltatások kiválasztását a Storage-fiók biztonságos eléréséhez. A megbízható szolgáltatások kikényszerítik a felügyelt identitások hitelesítését, amely biztosítja, hogy más adatok előállítója ne kapcsolódjon ehhez a tárhelyhez, kivéve, ha a felhasználó felügyelt identitását használja. További részleteket ebben a **[blogban](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** talál. Ezért ez rendkívül biztonságos és ajánlott. 
* **Egyedi statikus IP** – létre kell hoznia egy saját üzemeltetésű integrációs modult, amely egy statikus IP-címet kap Data Factory összekötők számára. Ez a mechanizmus biztosítja, hogy letiltsa a hozzáférést az összes többi IP-címről. 
* **[Statikus IP-címtartomány](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** – a Azure Integration Runtime IP-címeivel engedélyezheti a tárolóban való listázást (például S3, Salesforce stb.). Minden bizonnyal korlátozza azokat az IP-címeket, amelyek csatlakozni tudnak az adattárakhoz, de a hitelesítési/engedélyezési szabályokra is támaszkodnak.
* **[Szolgáltatás címkéje](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** – a szolgáltatás címkéje az adott Azure-szolgáltatás (például Azure Data Factory) IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. Ez akkor hasznos, ha a Virtual Network IaaS tárolt adattárakban lévő adatelérést engedélyez.
* **Azure-szolgáltatások engedélyezése** – egyes szolgáltatások lehetővé teszik, hogy az összes Azure-szolgáltatás csatlakozni tudjanak hozzá, ha ezt a beállítást választja. 

A Azure Integration Runtime és a saját üzemeltetésű Integration Runtime adattárakban található, támogatott hálózati biztonsági mechanizmusokkal kapcsolatos további információkért lásd: két táblázat.  
* **Azure Integration Runtime**

    | Adattárak                  | Az adattárakban támogatott hálózati biztonsági mechanizmus | Privát kapcsolat     | Megbízható szolgáltatás     | Statikus IP-címtartomány | Szolgáltatáscímkék | Azure-szolgáltatások engedélyezése |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Azure Pásti-adattárak       | Azure Cosmos DB                                     | Igen              | -                   | Igen             | -            | Igen                  |
    |                              | Azure Data Explorer                                 | -                | -                   | Igen*            | Igen*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Igen             | -            | Igen                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | Igen             | -            | Igen                  |
    |                              | Azure File Storage                                  | Igen              | -                   | Igen             | -            | .                    |
    |                              | Azure Storage (blob, ADLS Gen2)                     | Igen              | Igen (csak MSI-hitelesítés) | Igen             | -            | .                    |
    |                              | Azure SQL DB, SQL DW (szinapszis Analytics), SQL ml  | Igen (csak az Azure SQL DB/DW)        | -                   | Igen             | -            | Igen                  |
    |                              | Azure Key Vault (a Titkok/a kapcsolatok karakterláncának beolvasásához) | igen      | Igen                 | Igen             | -            | -                    |
    | Egyéb Pásti/SaaS-adattárak | AWS S3, SalesForce, Google Cloud Storage stb.    | -                | -                   | Igen             | -            | -                    |
    | Azure-laaS                   | SQL Server, Oracle stb.                          | -                | -                   | Igen             | Igen          | -                    |
    | Helyszíni laaS              | SQL Server, Oracle stb.                          | -                | -                   | Igen             | -            | -                    |
    
    **Csak akkor alkalmazható, ha az Azure Adatkezelő virtuális hálózati befecskendezéses, és az IP-címtartomány a NSG/Firewall-on is alkalmazható.* 

* **Saját üzemeltetésű Integration Runtime (vnet/helyszíni)**
    
    | Adattárak                  | Az adattárakban támogatott hálózati biztonsági mechanizmus         | Statikus IP-cím | Megbízható szolgáltatások  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure Pásti-adattárak       | Azure Cosmos DB                                               | Igen       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Igen       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Igen       | -                   |
    |                                | Azure File Storage                                            | Igen       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Igen       | Igen (csak MSI-hitelesítés) |
    |                                | Azure SQL DB, SQL DW (szinapszis Analytics), SQL ml          | Igen       | -                   |
    |                                | Azure Key Vault (a Titkok/a kapcsolatok karakterláncának beolvasásához) | Igen       | Igen                 |
    | Egyéb Pásti/SaaS-adattárak | AWS S3, SalesForce, Google Cloud Storage stb.              | Igen       | -                   |
    | Azure-laaS                     | SQL Server, Oracle stb.                                  | Igen       | -                   |
    | Helyszíni laaS              | SQL Server, Oracle stb.                                  | Igen       | -                   |    

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a következő kapcsolódó cikkeket:
* [Támogatott adattárak](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Azure Key Vault "megbízható szolgáltatások"](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Azure Storage – megbízható Microsoft-szolgáltatások](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Felügyelt identitás Data Factoryhoz](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
