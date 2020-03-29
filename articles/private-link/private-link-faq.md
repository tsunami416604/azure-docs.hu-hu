---
title: Az Azure Private Link gyakori kérdései (GYIK)
description: További információ az Azure Private Linkről.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349941"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Az Azure Private Link gyakori kérdései (GYIK)

## <a name="private-link"></a>Privát kapcsolat

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Mi az Azure Private Endpoint és az Azure Private Link service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Az Azure Private Endpoint egy olyan hálózati felület, amely privát és biztonságos kapcsolatot biztosít az Azure Private Link által működtetett szolgáltatással. Privát végpontok használatával csatlakozhat egy Azure PaaS-szolgáltatáshoz, amely támogatja a privát kapcsolatot, vagy a saját privát kapcsolatszolgáltatásához.
- **[Azure Private Link Service](private-link-service-overview.md)**: Az Azure Private Link szolgáltatás egy szolgáltató által létrehozott szolgáltatás. Jelenleg egy Private Link szolgáltatás csatlakoztatható a standard terheléselosztó előtér-IP-konfigurációjához. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hogyan történik a forgalom küldése a Privát hivatkozás használata kor?
A forgalmat a Microsoft gerinchálózata alapján küldi el a rendszer. Nem halad át az interneten.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Mi a különbség a szolgáltatásvégpontok és a privát végpontok között?
- A magán-végpontok használata esetén a hálózati hozzáférés egy adott szolgáltatás mögötti erőforrásokhoz biztosít részletes szegmentálást, a forgalom is elérheti a szolgáltatás erőforrást a helyszínen nyilvános végpontok használata nélkül.
- A szolgáltatásvégpont továbbra is nyilvánosan irányítható IP-cím marad.  A magánvégpont egy privát IP a virtuális hálózat címterében, ahol a privát végpont konfigurálva van.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Milyen kapcsolat van a Private Link szolgáltatás és a Privát végpont között?
A Private Endpoint több privát kapcsolaterőforrás-típushoz biztosít hozzáférést, beleértve az Azure PaaS-szolgáltatásokat és a saját privát kapcsolati szolgáltatást. Ez egy egy-a-többhöz kapcsolat. Egy privát kapcsolat szolgáltatás több privát végpontról is fogadhat kapcsolatokat. Másrészt egy privát végpont csak egy privát kapcsolat szolgáltatáshoz tud csatlakozni.    

## <a name="private-endpoint"></a>Privát végpont 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Létrehozhatok több privát végpontot ugyanabban a virtuális hálózatban? Tudnak csatlakozni a különböző szolgáltatásokhoz? 
Igen. Több privát végpontot is rendelkezhet ugyanabban a virtuális hálózatban vagy alhálózaton. Különböző szolgáltatásokhoz csatlakozhatnak.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Szükségem van egy dedikált alhálózat a privát végpontok? 
Nem. Nincs szükség dedikált alhálózatra a privát végpontokhoz. A szolgáltatás üzembe helyezését kiszolgáló virtuális hálózatbármely alhálózatból választhat privát végpont IP-címét.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>A Private Endpoint csatlakozhat a Private Link szolgáltatáshoz az Azure Active Directory-bérlők között? 
Igen. A privát végpontok csatlakozhatnak a Private Link-szolgáltatásokhoz vagy az Azure PaaS-hoz az AD-bérlők között.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Privát végpont csatlakozhat az Azure PaaS-erőforrások hoz az Azure-régiók között?
Igen. A privát végpontok az Azure PaaS-erőforrásokhoz kapcsolódhatnak az Azure-régiókban.

## <a name="private-link-service"></a>Privát kapcsolati szolgáltatás
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Milyen előfeltételei vannak a Private Link szolgáltatás létrehozásának? 
A szolgáltatás háttérrendszereket kell egy virtuális hálózat és egy standard terheléselosztó mögött.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hogyan méretezhetem a Privát kapcsolat szolgáltatásomat? 
A Privát kapcsolat szolgáltatást többféleképpen is méretezheti: 
- Háttérszintű virtuális gépek hozzáadása a standard terheléselosztó mögötti készlethez 
- IP hozzáadása a Privát kapcsolat szolgáltatáshoz. Privát kapcsolatszolgáltatásonként legfeljebb 8 IP-t engedélyezünk.  
- Új Private Link szolgáltatás hozzáadása a Standard Load Balancer szolgáltatáshoz. Terheléselosztónként legfeljebb nyolc Private Link szolgáltatást engedélyezünk.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Csatlakoztathatom a szolgáltatásomat több privát végponthoz?
Igen. Egy privát kapcsolat szolgáltatás több privát végpontról is fogadhat kapcsolatokat. Azonban egy privát végpont csak egy privát kapcsolat szolgáltatáshoz tud csatlakozni.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hogyan szabályozhatom a Private Link szolgáltatásom expozícióját?
Az expozíciót a Private Link szolgáltatás láthatósági konfigurációjával szabályozhatja. A láthatóság három beállítást támogat:

- **Nincs** – csak az RBAC-hozzáféréssel rendelkező előfizetések találhatják meg a szolgáltatást. 
- **Korlátozó** – Csak az engedélyezési listán szereplő és RBAC-hozzáféréssel rendelkező előfizetések találhatják meg a szolgáltatást. 
- **All** Mindenki megtalálhatja a szolgáltatást. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Létrehozhatok privát kapcsolatszolgáltatást az alapszintű terheléselosztóval? 
Nem. A Private Link szolgáltatás alapszintű terheléselosztón keresztül nem támogatott.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Dedikált alhálózat szükséges a Private Link szolgáltatáshoz? 
Nem. A Private Link szolgáltatáshoz nincs szükség dedikált alhálózatra. A virtuális hálózat bármely olyan alhálózatát kiválaszthatja, ahol a szolgáltatás telepítve van.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Az Azure Private Link et használó szolgáltató vagyok. Meg kell győződnöm arról, hogy minden ügyfelem egyedi IP-területtel rendelkezik, és nem fedi át az IP-területemet? 
Nem. Az Azure Private Link ezt a funkciót biztosítja önnek. Ezért nem kell, hogy a vevő címterével átfedésben lévő címterülettel rendelkezjen. 

##  <a name="next-steps"></a>További lépések

- További információ az [Azure Privát hivatkozásról](private-link-overview.md)
