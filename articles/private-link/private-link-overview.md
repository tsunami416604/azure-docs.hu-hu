---
title: Mi az az Azure Private link?
description: További információ az Azure Private linkről.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: overview
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9b7dea0507d4434274d78546b98961b008ea8cd8
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71017891"
---
# <a name="what-is-azure-private-link-preview"></a>Mi az az Azure Private link? (Előzetes verzió)
Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át, ami kiküszöböli a nyilvános internetről való kitettséget. Létrehozhatja saját [privát kapcsolati szolgáltatását](private-link-service-overview.md) is a virtuális hálózaton (VNet), és saját maga is elvégezheti az ügyfelek számára. Az Azure Private link használatával történő telepítési és használati élmény konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Az ismert korlátozásokért lásd: [privát végpont](private-endpoint-overview.md#limitations) és [privát kapcsolat szolgáltatás](private-link-service-overview.md#limitations).


![Privát végpont áttekintése](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Private link a következő előnyöket biztosítja:  
- **Saját hozzáférésű szolgáltatások az Azure platformon**: A virtuális hálózatot a saját Azure-ban futó szolgáltatásokhoz is összekapcsolhatjuk anélkül, hogy nyilvános IP-címet kellene létesíteni a forráson vagy a célhelyen. A szolgáltatók saját virtuális hálózatban tehetik saját szolgáltatásaikat, és a felhasználók a helyi virtuális hálózatban is hozzáférhetnek a szolgáltatásokhoz. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton. 
 
- Helyszíni **és társas hálózatok**: Az Azure-ban futó, ExpressRoute privát és VPN-alagutakból (helyszíni) és a privát végpontokat használó, egymástól független virtuális hálózatokból származó, az Azure-ban futtatott elérési szolgáltatások. A szolgáltatás eléréséhez nincs szükség nyilvános vagy az Internet bejárására. Ez a funkció biztonságos módszert biztosít a számítási feladatok Azure-ba való átirányításához.
 
- **Védelem az adatkiszűréseon**:  Az Azure Private-hivatkozással a VNet lévő privát végpont az ügyfélhez tartozó, a teljes szolgáltatáshoz képest egy adott példányra van leképezve. A privát végpontok használatával a felhasználók csak az adott erőforráshoz csatlakozhatnak, és nem a szolgáltatásban lévő más erőforrásokhoz. Ez az épített mechanizmus védelmet nyújt az adatkiszűrései kockázatokkal szemben. 
 
- **Globális elérhetőség**: Privát Kapcsolódás más régiókban futó szolgáltatásokhoz. Ez azt jelenti, hogy a fogyasztó virtuális hálózata az A régióban lehet, és a B régióban található privát kapcsolaton keresztül kapcsolódhat a szolgáltatásokhoz.  
 
- **Kiterjesztheti saját szolgáltatásait**: Használja ki ugyanazt a tapasztalatot és funkciót, hogy saját szolgáltatását saját maga is felhasználja az Azure-beli felhasználók számára. Ha a szolgáltatást egy standard Load Balancer mögé helyezi, akkor engedélyezheti azt a privát hivatkozáshoz. A fogyasztó ezután közvetlenül kapcsolódhat a szolgáltatáshoz a saját VNet lévő privát végpont használatával. Ezeket a kapcsolatkérelmeket egyszerű jóváhagyási hívási folyamattal kezelheti. Az Azure Private link a különböző Active Directory bérlők által használt felhasználók és szolgáltatások számára is működik. 

## <a name="availability"></a>Rendelkezésre állás 
 A következő táblázat felsorolja a privát kapcsolati szolgáltatásokat, valamint azokat a régiókat, ahol elérhetők. 

|Forgatókönyv  |Támogatott szolgáltatások   |Elérhető régiók | State   |
|---------|---------|---------|---------|
|Privát hivatkozás az ügyfél tulajdonában lévő szolgáltatásokhoz|standard Load Balancer mögötti privát kapcsolati szolgáltatások |USA nyugati középső régiója; WestUS USA déli középső régiója; USA keleti régiója; USA északi régiója  |  Előzetes verzió  |
|Privát hivatkozás az Azure Pásti-szolgáltatásokhoz   | Azure Storage        |  USA keleti régiója, USA nyugati régiója, USA nyugati középső régiója       | Előzetes verzió         |
|  | Azure Data Lake Storage Gen2        |  USA keleti régiója, USA nyugati régiója, USA nyugati középső régiója       | Előzetes verzió         |
|  |  Azure SQL Database         | USA nyugati középső régiója; WestUS USA déli középső régiója; USA keleti régiója; USA északi régiója       |   Előzetes verzió      |
||Azure SQL Data Warehouse| USA nyugati középső régiója; WestUS USA déli középső régiója; USA keleti régiója; USA északi régiója |Előzetes verzió|

A legfrissebb értesítésekért keresse fel az [Azure Virtual Network Updates oldalt](https://azure.microsoft.com/updates/?product=virtual-network). 

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Az Azure Private link integrálva van Azure Monitor, amely lehetővé teszi a naplók archiválását egy Storage-fiókba, az események továbbítását az Event hub-ba, vagy elküldheti őket Azure Monitor naplókba. Azure Monitor a következő információkat érheti el: 
- **Privát végpont**: A magánhálózati végpont által feldolgozott adatértékek (be/ki)
 
- **Magánhálózati kapcsolati szolgáltatás**:
    - A privát kapcsolati szolgáltatás által feldolgozott adatértékek (be/ki)
    - NAT-port elérhetősége  
 
## <a name="pricing"></a>Díjszabás   
A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Gyakori kérdések  
Gyakori kérdések: [Azure Private link – gyakori kérdések](private-link-faq.md).
 
## <a name="limits"></a>Korlátok  
A korlátokat lásd: [Azure Private link Limits](../azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>További lépések
- [Privát végpont létrehozása SQL Database kiszolgálóhoz a portál használatával](create-private-endpoint-portal.md)
- [Magánhálózati végpont létrehozása SQL Database kiszolgálóhoz a PowerShell használatával](create-private-endpoint-powershell.md)
- [Magánhálózati végpont létrehozása SQL Database kiszolgálóhoz a parancssori felület használatával](create-private-endpoint-cli.md)
- [Privát végpont létrehozása a Storage-fiókhoz a portál használatával](create-private-endpoint-storage-portal.md)
- [Saját privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával](create-private-link-service-powershell.md)


 
