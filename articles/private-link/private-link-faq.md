---
title: Azure Private-hivatkozás – gyakori kérdések (GYIK)
description: További információ az Azure Private linkről.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 53cb9b91d62c65cefb33451c716e677599306e9c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229330"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private-hivatkozás – gyakori kérdések (GYIK)

## <a name="private-link"></a>Privát kapcsolat

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Mi az Azure Private Endpoint és az Azure Private link Service?

- **[Azure Private-végpont](private-endpoint-overview.md)** : az Azure Private Endpoint egy olyan hálózati adapter, amely az Azure Private-kapcsolaton keresztüli szolgáltatáshoz kapcsolódóan és biztonságosan csatlakozik az Ön számára. A privát végpontok használatával kapcsolódhat egy olyan Azure Pásti szolgáltatáshoz, amely támogatja a privát vagy a saját privát kapcsolati szolgáltatását.
- **[Azure Private link Service](private-link-service-overview.md)** : az Azure Private link Service szolgáltató által létrehozott szolgáltatás. Jelenleg egy magánhálózati kapcsolati szolgáltatás csatlakoztatható egy standard Load Balancer előtérbeli IP-konfigurációjához. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hogyan történik a forgalom küldése a privát hivatkozás használatakor?
A forgalmat a Microsoft gerincen keresztül küldjük el. Nem halad át az interneten.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Mi a különbség a szolgáltatási végpontok és a privát végpontok között?
- Privát végpontok használata esetén a hálózati hozzáférés egy adott szolgáltatás mögötti, részletes szegmentálást biztosító erőforrásokhoz van biztosítva, a forgalom azonban a helyi végpontok használata nélkül is elérheti a szolgáltatási erőforrást a helyszínen.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Mi a kapcsolat a Private link Service és a privát végpont között?
A privát végpont több privát kapcsolati erőforrás-típushoz biztosít hozzáférést, beleértve az Azure Pásti-szolgáltatásokat és a saját privát kapcsolati szolgáltatását. Ez egy-a-többhöz kapcsolat. Egy privát kapcsolati szolgáltatás több privát végpontról is fogadhat kapcsolatokat. Másfelől az egyik privát végpont csak egy privát kapcsolati szolgáltatáshoz tud csatlakozni.    

## <a name="private-endpoint"></a>Privát végpont 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Létrehozhatok több privát végpontot is ugyanabban a VNet? Csatlakozhatnak különböző szolgáltatásokhoz? 
Igen. Több privát végpont is szerepelhet ugyanabban a VNet vagy alhálózatban. Más szolgáltatásokhoz is csatlakozhatnak.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Szükség van dedikált alhálózatra a privát végpontokhoz? 
Nem. Nincs szükség dedikált alhálózatra a privát végpontokhoz. A privát végponti IP-címet bármely alhálózatból kiválaszthatja abban a VNet, ahol a szolgáltatás telepítve van.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>A privát végpont Azure Active Directory bérlők között csatlakozhat a privát kapcsolati szolgáltatáshoz? 
Igen. A privát végpontok a Private link Serviceshez vagy az Azure Pástihez is csatlakozhatnak az AD-bérlők között.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Csatlakozhatnak a privát végpontok az Azure Pásti-erőforrásokhoz az Azure-régiók között?
Igen. A privát végpontok Azure Pásti-erőforrásokhoz is csatlakozhatnak az Azure-régiók között.

## <a name="private-link-service"></a>Privát kapcsolati szolgáltatás
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Mik a Private link Service létrehozásához szükséges előfeltételek? 
A szolgáltatás hátterének virtuális hálózatban kell lennie, és egy standard Load Balancer mögött kell lennie.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hogyan méretezhetim a privát link Service-t? 
A privát kapcsolati szolgáltatást többféleképpen is méretezheti: 
- Háttérbeli virtuális gépek hozzáadása a standard Load Balancer mögötti készlethez 
- Adjon hozzá egy IP-címet a privát kapcsolati szolgáltatáshoz. Privát kapcsolati szolgáltatásként legfeljebb 8 IP-címet engedélyezünk.  
- Adja hozzá a standard Load Balancerhoz az új Private link Service-t. A Load Balancer legfeljebb nyolc magánhálózati kapcsolati szolgáltatást engedélyez.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>A szolgáltatáshoz több privát végpont is csatlakoztatható?
Igen. Egy privát kapcsolati szolgáltatás több privát végpontról is fogadhat kapcsolatokat. Egy privát végpont azonban csak egy privát kapcsolati szolgáltatáshoz tud csatlakozni.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hogyan szabályozható a privát kapcsolati szolgáltatás expozíciója?
Az expozíciót a magánjellegű kapcsolat szolgáltatás láthatósági konfigurációjának használatával szabályozhatja. A láthatóság három beállítást támogat:

- A nem csak a **RBAC-hozzáféréssel** rendelkező előfizetések megkereshetik a szolgáltatást. 
- A szolgáltatás megkeresheti a **korlátozott** , csak az engedélyezett és a RBAC hozzáféréssel rendelkező előfizetéseket. 
- **Minden** – mindenki megtalálja a szolgáltatást. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Létrehozhatok alapszintű Load Balancerekkel rendelkező privát hivatkozás szolgáltatást? 
Nem. A Private link Service egy alapszintű Load Balanceron nem támogatott.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Dedikált alhálózat szükséges a Private link Service-hez? 
Nem. A Private link Service nem igényel dedikált alhálózatot. Bármelyik alhálózatot kiválaszthatja a VNet, ahol a szolgáltatás telepítve van.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Az Azure privát hivatkozását használó szolgáltató vagyok. Meg kell győződni arról, hogy minden ügyfélnek egyedi IP-címmel kell rendelkeznie, és ne legyen átfedésben az IP-tárhelytel? 
Nem. Az Azure Private link ezt a funkciót biztosítja Önnek. Ezért nincs szükség arra, hogy nem átfedésben lévő címtartomány álljon rendelkezésre az ügyfél címterület használatával. 

##  <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](private-link-overview.md)
