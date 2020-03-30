---
title: Az Azure API-kezelés használata az Azure Kubernetes szolgáltatásban üzembe helyezett mikroszolgáltatásokkal | Microsoft dokumentumok
description: Ez a cikk az API Management AKS-sel történő üzembe helyezésének lehetőségeit ismerteti
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480995"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Az Azure API Management használata az Azure Kubernetes szolgáltatásban üzembe helyezett mikroszolgáltatásokkal

A mikroszolgáltatások tökéletesek API-k létrehozásához. Az [Azure Kubernetes-szolgáltatás](https://azure.microsoft.com/services/kubernetes-service/) (AKS) segítségével gyorsan üzembe helyezhet és működtethet [mikroszolgáltatásokon alapuló architektúrát](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) a felhőben. Ezután az [Azure API Management](https://aka.ms/apimrocks) (API Management) segítségével közzéteheti a mikroszolgáltatásokat API-kként belső és külső felhasználáshoz. Ez a cikk az API Management AKS-sel való üzembe helyezésének lehetőségeit ismerteti. A Kubernetes, az API Management és az Azure-hálózat alapvető ismereteit feltételezi. 

## <a name="background"></a>Háttér

Mikroszolgáltatások api-kként való közzétételekor a felhasználás érdekében, kihívást jelenthet a mikroszolgáltatások és az azokat használó ügyfelek közötti kommunikáció kezelése. Számos több területet érintő aggályok, mint például a hitelesítés, engedélyezés, szabályozás, gyorsítótárazás, átalakítás és figyelés. Ezek az aggályok érvényesek, függetlenül attól, hogy a mikroszolgáltatások vannak kitéve a belső vagy külső ügyfelek. 

Az [API-átjáró](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) minta ezeket az aggályokat. Az API-átjáró a mikroszolgáltatások bejárati ajtaját szolgálja, leválasztja az ügyfeleket a mikroszolgáltatásokról, további biztonsági réteget ad hozzá, és csökkenti a mikroszolgáltatások összetettségét azáltal, hogy eltávolítja a több vágással kapcsolatos problémák kezelésének terhét. 

[Az Azure API Management](https://aka.ms/apimrocks) egy kulcsrakész megoldás az API-átjáró igényeinek kielégítésére. Gyorsan létrehozhat egy konzisztens és modern átjárót a mikroszolgáltatásokhoz, és api-ként közzéteheti őket. Teljes életciklusú API-felügyeleti megoldásként további képességeket is biztosít, beleértve az API-felderítéshez, az API életciklus-kezeléséhez és az API-elemzéshez való önkiszolgáló fejlesztői portált.

Ha együtt használják, az AKS és az API Management platformot biztosít a mikroszolgáltatásokon alapuló API-k üzembe helyezéséhez, közzétételéhez, védelméhez, figyeléséhez és kezeléséhez. Ebben a cikkben az AKS API Managementtel együtt történő üzembe helyezésének néhány lehetőségén fogunk átmenni. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes szolgáltatások és API-k

A Kubernetes-fürtben a tárolók [podok,](https://kubernetes.io/docs/concepts/workloads/pods/pod/)amelyek rövid élettartamúak és életciklussal rendelkeznek. Ha egy feldolgozó csomópont meghal, a csomóponton futó podok elvesznek. Ezért a pod IP-címe bármikor megváltozhat. Nem támaszkodhatunk rá, hogy kommunikáljon a kabinnal. 

A probléma megoldásához a Kubernetes bevezette a [Szolgáltatások](https://kubernetes.io/docs/concepts/services-networking/service/)fogalmát. A Kubernetes-szolgáltatás egy absztrakciós réteg, amely meghatározza a podok logikai csoportját, és lehetővé teszi a külső forgalom kielégesését, a terheléselosztást és a szolgáltatásfelderítést a podok számára. 

Amikor készen állunk arra, hogy közzétegyék mikroszolgáltatásainkat API-ként az API Managementen keresztül, át kell gondolnunk, hogyan képezzük le a Kubernetes-ben lévő szolgáltatásainkat az API Management API-k API-kAPI-khoz való hozzárendelésére. Nincsenek meghatározott szabályok. Attól függ, hogyan tervezte meg és particionálta az üzleti képességeket vagy tartományokat mikroszolgáltatásokba az elején. Például ha a podok mögött egy szolgáltatás felelős az összes művelet egy adott erőforrás (például, Ügyfél), a szolgáltatás lehet leképezve egy API-t. Ha egy erőforrás-műveletek particionált több mikroszolgáltatások (például GetOrder, PlaceOrder), majd több szolgáltatás logikailag összesíthető egyetlen API-t API-kezelés (lásd: 1. ábra). 

A leképezések is fejlődhetnek. Mivel az API Management egy homlokzatot hoz létre a mikroszolgáltatások előtt, lehetővé teszi számunkra, hogy újraés megfelelő méretű mikroszolgáltatásaink idővel. 

![Szolgáltatások hozzárendelése API-khoz](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Az API Management telepítése az AKS előtt

Az API Management telepítése az AKS-fürt előtt néhány lehetőség közül választhat. 

Míg egy AKS-fürt mindig egy virtuális hálózatban (VNet) van telepítve, az API Management-példány nem szükséges virtuális hálózatban üzembe helyezése. Ha az API Management nem a fürt virtuális hálózatán belül található, az AKS-fürtnek nyilvános végpontokat kell közzétennie az API Management számára, hogy csatlakozzon. Ebben az esetben szükség van az API Management és az AKS közötti kapcsolat védelmére. Más szóval biztosítanunk kell, hogy a fürt csak az API Managementen keresztül érhető el. Vegyük át a lehetőségeket. 

### <a name="option-1-expose-services-publicly"></a>1. lehetőség: Szolgáltatások nyilvános felfedése

Az AKS-fürt szolgáltatásai nyilvánosan elérhetővé tehetők a NodePort, LoadBalancer vagy ExternalName [szolgáltatástípusokkal.](https://docs.microsoft.com/azure/aks/concepts-network) Ebben az esetben a Szolgáltatások közvetlenül a nyilvános internetről érhetők el. Miután üzembe helyezte az API Managementet a fürt előtt, biztosítanunk kell, hogy az összes bejövő forgalom az API Managementen keresztül történjen a mikroszolgáltatásokhitelesítés alkalmazásával. Például az API Management tartalmazhat egy hozzáférési jogkivonatot a fürthöz benyújtott minden egyes kérelemben. Minden mikroszolgáltatás felelős a jogkivonat érvényesítéséért a kérelem feldolgozása előtt. 


Ez lehet a legegyszerűbb lehetőség az API Management telepítése az AKS előtt, különösen akkor, ha már rendelkezik hitelesítési logikával a mikroszolgáltatásokban. 

![Szolgáltatások közvetlen közzététele](./media/api-management-aks/direct.png)

Profik:
* Egyszerű konfiguráció az API Management oldalon, mert nem kell beadni a fürt virtuális hálózatába
* Nincs változás az AKS-oldalon, ha a szolgáltatások már nyilvánosan vannak elérhetővé téve, és a hitelesítési logika már létezik a mikroszolgáltatásokban

Hátránya:
* A szolgáltatási végpontok nyilvános láthatóságából eredő potenciális biztonsági kockázat
* Nincs egybelépésű pont a bejövő fürtforgalomhoz
* Megnehezíti a mikroszolgáltatásokat duplikált hitelesítési logikával

### <a name="option-2-install-an-ingress-controller"></a>2. lehetőség: Be- és betolatási vezérlő telepítése

Bár az 1- es lehetőség könnyebb lehet, figyelemre méltó hátrányai vannak, mint a fent említett. Ha egy API Management-példány nem található a fürt virtuális hálózatában, a kölcsönös TLS-hitelesítés (mTLS) robusztus módja annak, hogy a forgalom biztonságos és megbízható legyen mindkét irányban egy API Management-példány és egy AKS-fürt között. 

A kölcsönös TLS-hitelesítést az API Management [natív módon támogatja,](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) és a Kubernetes-ben [egy bejövő vezérlő telepítésével](https://docs.microsoft.com/azure/aks/ingress-own-tls) engedélyezhető (3. ábra). Ennek eredményeképpen a hitelesítés a bejövő adatkezelőben történik, ami leegyszerűsíti a mikroszolgáltatásokat. Emellett hozzáadhatja az API Management IP-címeit a bejárás itiltott listájához, hogy csak az API Management férhessen hozzá a fürthöz.  

 
![Közzététel be- és visszahálózaton keresztül](./media/api-management-aks/ingress-controller.png)


Profik:
* Egyszerű konfiguráció az API Management oldalon, mert nem kell beadni a fürt virtuális hálózatába, és az mTLS natívan támogatott
* Központosítja a bejövő fürtforgalom védelmét a bejövő vezérlő rétegben
* Csökkenti a biztonsági kockázatot a nyilvánosan látható fürtvégpontok minimalizálásával

Hátránya:
* Növeli a fürtkonfiguráció összetettségét az mTLS-hez használt tanúsítványok telepítéséhez, konfigurálásához és karbantartásához szükséges többletmunka miatt
* A befektvezérlő végpontjainak nyilvános láthatóságából eredő biztonsági kockázat


Ha API-n keresztül teszi közzé az API-kat, az előfizetési kulcsok használatával egyszerűen és általánosan biztosíthatja az API-khoz való hozzáférést. Azoknak a fejlesztőknek, akiknek fel kell használniuk a közzétett API-kat, érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor hívásokat kezdeményeznek ezekhez az API-khoz. Ellenkező esetben a hívásokat az API Management átjáró azonnal elutasítja. Nem továbbítják őket a háttérszolgáltatásoknak.

Az API-k eléréséhez előfizetési kulcs lekérni, előfizetés szükséges. Az előfizetés lényegében egy elnevezett tároló egy pár előfizetési kulcsok. Azok a fejlesztők, akiknek fel kell használniuk a közzétett API-kat, előfizetéseket kaphatnak. És nem kell jóváhagyást API-közzétevők. Az API-közzétevők közvetlenül is létrehozhatnak előfizetéseket az API-fogyasztók számára.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>3. lehetőség: APIM telepítése a fürt virtuális hálózatán belül

Bizonyos esetekben a szabályozási korlátozásokkal vagy szigorú biztonsági követelményekkel rendelkező ügyfelek az 1. Másokban az AKS-fürt és a mikroszolgáltatásokat használó alkalmazások előfordulhat, hogy ugyanazon a virtuális hálózaton belül, ezért nincs ok arra, hogy a fürt nyilvánosan elérhető, mivel az összes API-forgalom a virtuális hálózaton belül marad. Ezekben a forgatókönyvekben az API Management a fürt virtuális hálózatába. [Az API Management Premium tier](https://aka.ms/apimpricing) támogatja a virtuális hálózat telepítését. 

Az API Management [virtuális hálózatra való üzembe helyezésének](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) két módja van – külső és belső. 

Ha az API-felhasználók nem tartózkodnak a fürt virtuális hálózatában, a külső módot (4. ábra) kell használni. Ebben a módban az API Management átjáró a fürt virtuális hálózatába kerül, de egy külső terheléselosztón keresztül érhető el a nyilvános internetről. Segít teljesen elrejteni a fürtöt, miközben továbbra is engedélyezi a külső ügyfelek számára a mikroszolgáltatások felhasználását. Emellett használhatja az Azure hálózati képességek, például a hálózati biztonsági csoportok (NSG) a hálózati forgalom korlátozásához.

![Külső virtuális hálózat mód](./media/api-management-aks/vnet-external.png)

Ha az összes API-fogyasztó a fürt virtuális hálózatán belül található, akkor a belső mód (5. ábra) használható. Ebben a módban az API Management átjáró a fürt virtuális hálózatába kerül, és csak a virtuális hálózaton belül érhető el egy belső terheléselosztón keresztül. Az API Management átjáróvagy az AKS-fürt nem érhető el nyilvános internetről. 

![Belső virtuális hálózat mód](./media/api-management-aks/vnet-internal.png)

 Az AKS-fürt mindkét esetben nem látható nyilvánosan. Lehetőséghez képest előfordulhat, hogy a be- és a be- és a tőzsdevezérlőre nincs szükség. A forgatókönyvtől és a konfigurációtól függően továbbra is szükség lehet a hitelesítésre az API Management és a mikroszolgáltatások között. Ha például egy szolgáltatáshálót alkalmaznak, mindig kölcsönös TLS-hitelesítést igényel. 

Profik:
* A legbiztonságosabb beállítás, mivel az AKS-fürt nek nincs nyilvános végpontja
* Leegyszerűsíti a fürtkonfigurációt, mivel nincs nyilvános végpontja
* Az API Management és az AKS elrejtésének lehetősége a virtuális hálózaton belül a Belső mód használatával
* A hálózati forgalom vezérlésének lehetősége az Azure hálózati képességei, például a hálózati biztonsági csoportok (NSG) használatával

Hátránya:
* Növeli az API Management üzembe helyezésének és konfigurálásának összetettségét a virtuális hálózaton belüli munka érdekében

## <a name="next-steps"></a>További lépések

* További információ az [AKS-ben található alkalmazások hálózati fogalmairól](https://docs.microsoft.com/azure/aks/concepts-network)
* További információ [az API Management virtuális hálózatokkal való használatáról](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





