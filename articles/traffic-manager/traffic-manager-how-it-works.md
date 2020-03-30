---
title: Az Azure Traffic Manager működése | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy a Traffic Manager hogyan irányítja a forgalmat a webes alkalmazások nagy teljesítménye és rendelkezésre állása érdekében
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: rohink
ms.openlocfilehash: 4863ffd383cfcd46bad462156e26293d145fd418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294855"
---
# <a name="how-traffic-manager-works"></a>A Traffic Manager működése

Az Azure Traffic Manager lehetővé teszi, hogy szabályozhatja a forgalom eloszlását az alkalmazás végpontjai között. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

A Traffic Manager két fő előnnyel jár:

- A forgalom elosztása a számos [forgalom-útválasztási módszer](traffic-manager-routing-methods.md) egyikének megfelelően
- [A végpont állapotának folyamatos figyelése](traffic-manager-monitoring.md) és automatikus feladatátvétel, ha a végpontok sikertelenek

Amikor egy ügyfél megkísérel a szolgáltatáshoz kapcsolódni, először IP-címmé kell feloldania a szolgáltatás DNS-nevét. Az ügyfél ez után ehhez az IP-címhez kapcsolódva éri el a szolgáltatást.

**A legfontosabb szempont, hogy megértsék, hogy a Traffic Manager működik a DNS szintjén.**  A Traffic Manager a DNS segítségével irányítja az ügyfeleket adott szolgáltatásvégpontokra a forgalom-útválasztási módszer szabályai alapján. Az ügyfelek **közvetlenül**csatlakoznak a kijelölt végponthoz. A Traffic Manager nem proxy vagy átjáró. A Traffic Manager nem látja az ügyfél és a szolgáltatás közötti forgalmat.

## <a name="traffic-manager-example"></a>Példa a Traffic Manager

A Contoso Corp új partnerportált fejlesztett ki. A portál URL-címe `https://partners.contoso.com/login.aspx`. Az alkalmazás az Azure három régiójában található. A rendelkezésre állás javítása és a globális teljesítmény maximalizálása érdekében a Traffic Manager segítségével osztják el az ügyfélforgalmat a legközelebbi elérhető végpontra.

A konfiguráció eléréséhez hajtsák végre a következő lépéseket:

1. A szolgáltatásuk három példányának üzembe helyezése. Ezeknek a központi telepítéseknek a DNS-nevei "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" és "contoso-asia.cloudapp.net".
1. Hozzon létre egy "contoso.trafficmanager.net" nevű Traffic Manager-profilt, és konfigurálja úgy, hogy a "Teljesítmény" forgalom-útválasztási módszert használja a három végpont között.
1. Konfigurálja a "partners.contoso.com" hiúsági tartománynevet, hogy dns CNAME rekord használatával a "contoso.trafficmanager.net" pontra mutasson.

![Traffic Manager DNS-konfigurációja][1]

> [!NOTE]
> Ha az Azure Traffic Manager relaláda-tartományt használ, cname-t kell használnia a hiúsági tartománynév nek a Traffic Manager tartománynevére való rámutatásához. A DNS-szabványok nem teszik lehetővé a CNAME létrehozását a tartomány "csúcsán" (vagy gyökérénél). Így nem hozhat létre CNAME-t a "contoso.com" (más néven "meztelen" tartományhoz). A "contoso.com" alatt csak "contoso.com" tartományhoz hozhat létre CNAME nevet, például "www.contoso.com". A korlátozás megkerüléséhez javasoljuk, hogy a DNS-tartományt az [Azure DNS-en](../dns/dns-overview.md) üzemeltetje, és [aliasrekordok használatával](../dns/tutorial-alias-tm.md) mutasson a forgalomkezelő profiljára. Másik lehetőségként használhat egy egyszerű HTTP-átirányítást a "contoso.com" kéréseinek egy másik névre, például a "www.contoso.com" utasítására.

### <a name="how-clients-connect-using-traffic-manager"></a>Hogyan kapcsolódnak az ügyfelek a Traffic Manager használatával?

Az előző példából folytatva, amikor `https://partners.contoso.com/login.aspx`egy ügyfél kéri a lapot, az ügyfél a következő lépéseket hajtja végre a DNS-név feloldásához és a kapcsolat létrehozásához:

![Csatlakozáslétesítés a Traffic Manager használatával][2]

1. Az ügyfél DNS-lekérdezést küld a konfigurált rekurzív DNS-szolgáltatásnak a "partners.contoso.com" név feloldásához. A rekurzív DNS-szolgáltatás, más néven "helyi DNS" szolgáltatás, nem tartalmaz közvetlenül DNS-tartományokat. Ehelyett az ügyfél kitölti a munkát a kapcsolatot a különböző mérvadó DNS-szolgáltatások az interneten keresztül szükséges feloldani a DNS-nevet.
2. A DNS-név feloldásához a rekurzív DNS-szolgáltatás megkeresi a contoso.com" tartomány névkiszolgálóit. Ezután kapcsolatba lép a névkiszolgálókkal, hogy kérje a "partners.contoso.com" DNS-rekordot. A contoso.com DNS-kiszolgálók a contoso.trafficmanager.net ponthoz tartozó CNAME rekordot adják vissza.
3. Ezután a rekurzív DNS-szolgáltatás megkeresi a "trafficmanager.net" tartomány névkiszolgálóit, amelyeket az Azure Traffic Manager szolgáltatás biztosít. Ezután a "contoso.trafficmanager.net" DNS-rekordra vonatkozó kérést küld ezeknek a DNS-kiszolgálóknak.
4. A Traffic Manager névkiszolgálói megkapják a kérést. A következők alapján választanak ki egy végpontot:

    - Az egyes végpontok konfigurált állapota (a letiltott végpontok nem adnak vissza)
    - Az egyes végpontok aktuális állapota, a Traffic Manager állapot-ellenőrzések által meghatározott. További információ: [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md).
    - A kiválasztott forgalom-útválasztási módszer. További információt a [Traffic Manager útválasztási módszerei című témakörben talál.](traffic-manager-routing-methods.md)

5. A kiválasztott végpont ot egy másik DNS CNAME rekordként adja vissza a rendszer. Ebben az esetben tegyük fel, contoso-us.cloudapp.net vissza.
6. Ezután a rekurzív DNS-szolgáltatás megkeresi a "cloudapp.net" tartomány névkiszolgálóit. Kapcsolatba lép a névkiszolgálókkal, hogy kérje a "contoso-us.cloudapp.net" DNS-rekordot. Az usa-beli szolgáltatásvégpont IP-címét tartalmazó DNS"A" rekord kerül visszaadásra.
7. A rekurzív DNS-szolgáltatás összesíti az eredményeket, és egyetlen DNS-választ ad vissza az ügyfélnek.
8. Az ügyfél megkapja a DNS-eredményeket, és csatlakozik a megadott IP-címhez. Az ügyfél közvetlenül, nem a Traffic Manageren keresztül csatlakozik az alkalmazásszolgáltatás végpontjához. Mivel https-végpontról van szó, az ügyfél végrehajtja a szükséges SSL/TLS kézfogást, majd http GET-kérelmet küld a "/login.aspx" laphoz.

A rekurzív DNS-szolgáltatás gyorsítótárazza a kapott DNS-válaszokat. Az ügyféleszköz DNS-feloldója gyorsítótárazza az eredményt is. A gyorsítótárazás lehetővé teszi a későbbi DNS-lekérdezések gyorsabb megválaszolását a gyorsítótárból származó adatok használatával, nem pedig más névkiszolgálók lekérdezésével. A gyorsítótár időtartamát az egyes DNS-rekordok "élettartama" (TTL) tulajdonsága határozza meg. A rövidebb értékek gyorsabb gyorsítótárlejárati sebességet eredményeznek, és így több körutat eredményeznek a Traffic Manager névkiszolgálóihoz. A hosszabb értékek azt jelentik, hogy hosszabb ideig tarthat a forgalom irányítása egy sikertelen végpontról. A Traffic Manager lehetővé teszi, hogy a Traffic Manager DNS-válaszaiban használt TTL-t akár 0 másodpercre és 2 147 483 647 másodpercre (az [RFC-1035 szabványnak](https://www.ietf.org/rfc/rfc1035.txt)megfelelő maximális tartományra) állítsa be, így kiválaszthatja azt az értéket, amely a legjobban megfelel az alkalmazás igényeinek.

## <a name="faqs"></a>Gyakori kérdések

* [Milyen IP-címet használ a Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Milyen típusú forgalmat lehet irányítani a Traffic Manager használatával?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Támogatja a Traffic Manager a "ragadós" munkameneteket?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Miért jelenik meg HTTP-hiba a Traffic Manager használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Milyen hatással van a Traffic Manager használatára?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Milyen alkalmazásprotokollokat használhatok a Traffic Managerrel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Használhatom a Traffic Managert "meztelen" tartománynévvel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [A Traffic Manager figyelembe veszi az ügyfél alhálózati címet a DNS-lekérdezések kezelésekor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Mi az a DNS TTL, és milyen hatással van a felhasználókra?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Milyen magasra vagy alacsonyra állíthatom be a Traffic Manager-válaszok TTL-jét?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Hogyan tudom megérteni a profilomra érkező lekérdezések mennyiségét?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>További lépések

További információ a Traffic Manager [végpontfigyelésről és az automatikus feladatátvételről.](traffic-manager-monitoring.md)

További információ a Traffic Manager [forgalomirányítási módszereiről.](traffic-manager-routing-methods.md)

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

