---
title: Az Azure App Service hibrid kapcsolatok |} Microsoft Docs
description: "Hozzon létre és különálló hálózaton lévő erőforrások eléréséhez használhatnak hibrid kapcsolatokat"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Az Azure App Service hibrid kapcsolatok #

## <a name="overview"></a>Áttekintés ##

Hibrid kapcsolatok, mind az Azure-szolgáltatás, valamint az Azure App Service szolgáltatás.  Szolgáltatásként rendelkezik használ és képességekkel ruházzák, amely használja az Azure App Service-ben.  További információt a hibrid kapcsolatok és az Azure App Service itt kezdheti kívül használatát [Azure hibrid kapcsolatok][HCService]

Azure App Service-ben belül hibrid kapcsolatok használható más hálózatokkal alkalmazás erőforrásaihoz. Egy alkalmazás végpontjának a alkalmazás hozzáférést biztosít.  Nem engedélyezi egy másik képességet az alkalmazáshoz való hozzáféréshez.  Az App Service-ben használt, minden a hibrid kapcsolat felel meg egyetlen TCP-állomás és port kombinációját.  Ez azt jelenti, hogy a hibrid kapcsolat végpont lehet operációs rendszert, és bármilyen alkalmazás, feltéve, hogy elérte-e a TCP-figyelési port. Hibrid kapcsolatok nem tudja vagy nem fontos a protokoll van, vagy mi elérésére.  Azok a hálózati hozzáférést biztosító egyszerűen vannak.  


## <a name="how-it-works"></a>Működés ##
A hibrid kapcsolatok szolgáltatás a Service Bus Relay két kimenő hívásainak foglalja magában.  Van-e az állomáson, ahol a webalkalmazás az App Service szolgáltatásban működik, és van-e a Service Bus Relay a hibrid kapcsolat Manager (HCM) kapcsolat tárból kapcsolat.  A HCM a továbbítási szolgáltatás, amely telepít elérni kívánt erőforrást a hálózaton belül. 

A két illesztett kapcsolatokon keresztül az alkalmazás rendelkezik egy rögzített gazdagépet: port kombinációján TCP alagút a HCM másik oldalán.  A kapcsolat biztonsági és a SAS-kulcs a hitelesítési/engedélyezési használja a TLS 1.2-es.    

![A hibrid kapcsolat magas szintű folyamata][1]

Az alkalmazás, amely megfelel a konfigurált kötéssel a hibrid kapcsolat DNS-kérelmet küld, ha a kimenő TCP-forgalmat a rendszer átirányítja a hibrid kapcsolaton keresztül.  

> [!NOTE]
> Ez azt jelenti, hogy egy DNS-nevet a hibrid kapcsolat mindig használandó használja.  Néhány ügyfélszoftver nem teendő DNS-címkeresést, ha a végpont egy IP-címet használja helyette.
>
>

Hibrid kapcsolatok két típusa van: az új hibrid kapcsolatok felkínált Azure továbbítási és a régebbi BizTalk hibrid kapcsolatok szolgáltatásként.  A régebbi BizTalk hibrid kapcsolatok nevezzük klasszikus hibrid kapcsolatok a portálon.  Nincs további információ a jelen dokumentum azokat.

### <a name="app-service-hybrid-connection-benefits"></a>App Service a hibrid kapcsolat előnyei ###

Számos a hibrid kapcsolatok funkció előnyei többek között:

- Alkalmazások biztonságosan férjenek hozzá a helyszíni rendszer és a szolgáltatások biztonságosan.
- A szolgáltatás nem szükséges egy internetről elérhető végpontot.
- Fontos, hogy gyorsan és egyszerűen állíthat be. 
- Minden egyes hibrid kapcsolat felel meg egyetlen állomás: port kombinációjához, amelyek kiváló biztonsági eleme.
- Általában nem szükséges tűzfal lyuk a kapcsolatok vannak az összes kimenő szokásos webes portokon keresztül.
- Mivel a szolgáltatás hálózati szint, akkor független az alkalmazás által használt nyelv és a végpont által használt technológiára.
- Több hálózat egyetlen alkalmazásból hozzáférést biztosíthat használható. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Művelet nem hajtható végre a hibrid kapcsolatok ###

Néhány dolgot a hibrid kapcsolatok, beleértve a nem hajtható végre:

- a meghajtó csatlakoztatása
- UDP segítségével
- Fér hozzá a TCP dinamikus portok, például a passzív FTP-módban vagy passzív módban kiterjesztett használó szolgáltatások alapján
- LDAP-támogatás, ahogy néha szükséges UDP
- Active Directory-támogatás

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Hozzáadása és az alkalmazás egy hibrid kapcsolat létrehozása ##

Hibrid kapcsolatok App Service-alkalmazás az Azure portálon vagy az Azure-továbbító az Azure portálon keresztül is létrehozható.  Erősen ajánlott, hogy hozzon létre hibrid kapcsolatok révén az App Service-alkalmazást, amelynek szeretné használni a hibrid kapcsolat.  A hibrid kapcsolat létrehozásához, lépjen a [Azure-portálon] [ portal] , és válassza ki az alkalmazást.  Válassza ki **hálózati > hibrid kapcsolati végpontok konfigurálása**.  Itt láthatja a hibrid kapcsolatok konfigurált az alkalmazásra vonatkozóan.  

![A hibrid kapcsolat listája][2]

Új hibrid kapcsolat hozzáadásához kattintson a hibrid kapcsolat hozzáadása.  A felhasználói felület, a megnyitott a hibrid kapcsolatok már létrehozott sorolja fel.  Az alkalmazás egy vagy több hozzáadásához kattintson azokra szeretné, majd nyomja le **hozzáadása a hibrid kapcsolat kijelölt**.  

![A hibrid kapcsolat portál][3]

Új hibrid kapcsolat létrehozásához, kattintson a **új hibrid kapcsolat létrehozása**.  Itt adja meg a: 

- a végpont neve
- végpont állomásneve
- Végpont portja
- Service Bus-névtér szeretné használni

![A hibrid kapcsolat létrehozása][4]

Minden hibrid kapcsolat egy Service Bus-névtér van kötve, és minden egyes Service Bus-névtér az Azure-régióban van.  Fontos, próbálja meg, és a Service Bus-névtér használata ugyanabban a régióban, az alkalmazás előidézett hálózati késleltetés elkerülése érdekében.

Ha szeretné a hibrid kapcsolat eltávolítása az alkalmazásból, a jobb gombbal kattintson rá, és válassza ki **Disconnect**.  

A hibrid kapcsolat az alkalmazás hozzáadása után részletes információkat tekinthet meg egyszerűen kattintással. 

![A hibrid kapcsolat adatai][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>A hibrid kapcsolat létrehozása a Azure Relay-portálon ###

Az alkalmazáson belül a portál élmény mellett is van olyan képesség a hibrid kapcsolatok létrehozása az Azure-továbbítási portálon. Ahhoz, hogy egy hibrid kapcsolat az Azure App Service használható annak meg kell felelnie két feltételt. Kell:

* Ügyfél-hitelesítés megkövetelése
* Rendelkezik egy metaadatelem nevű végpont állomás: port kombinációját, értéke

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és az App Service-csomagokról ##

Hibrid kapcsolatok a Basic, Standard, Premium és termékváltozatok árképzési elszigetelt csak érhetők el.  Nincsenek korlátai, a tarifacsomag kötődik.  

> [!NOTE] 
> Csak új hibrid kapcsolatok Azure továbbítási alapján hozhat létre. Új BizTalk hibrid kapcsolatok nem hozható létre.
>

| Terv díjszabása | A terv felhasználható hibrid kapcsolatok száma |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Prémium | 200 |
| Izolált | 200 |

Mivel az App Service-csomag korlátozások, is van az App Service-csomag, amely bemutatja, hogy hány hibrid kapcsolatok használatban vannak, és milyen alkalmazások által használt felhasználói felület.  

![Alkalmazás szolgálata van terv tulajdonságait][6]

A hibrid kapcsolat részletek kattintva tekintheti meg.  Itt látható tulajdonságai láthatja, hogy az alkalmazás megtekintése a látott összes információt, és azt is láthatja, hogy hány más alkalmazások az azonos App Service-csomag használ, hogy a hibrid kapcsolat.

Hibrid kapcsolati végpontok, amely egy App Service-csomag használható száma korlátozva van, amíg minden egyes használt hibrid kapcsolat segítségével tetszőleges számú alkalmazásaival kapcsolatban, hogy App Service-csomag.  Más szóval, amely egy App Service-csomag 5 külön alkalmazások szolgál egy hibrid kapcsolat 1 hibrid kapcsolat számít.

A hibrid kapcsolatot is használ további költségek van.  A hibrid kapcsolat az árakkal kapcsolatos részletekért nyissa meg itt: [Service Bus árképzési][sbpricing].

## <a name="hybrid-connection-manager"></a>Hibrid Csatlakozáskezelő ##

Ahhoz, hogy hibrid kapcsolatok működjön a hálózat, amelyen a hibrid kapcsolat végpont továbbítóügynök kell.  A továbbító ügynök neve a hibrid kapcsolat Manager (HCM).  Ez az eszköz letölthető a **hálózati > hibrid kapcsolati végpontok konfigurálása** érhető el az alkalmazást a felhasználói felület a [Azure-portálon][portal].  

Ez az eszköz fut, a Windows server 2012 és a Windows.  Egyszer az HCM fut a szolgáltatásként telepített.  Ez a szolgáltatás kapcsolódik az Azure Service Bus Relay a beállított végpontjaikra alapján.  A HCM közötti kapcsolatok kimenő Azure 443-as porton keresztül.    

A HCM rendelkezik konfigurálásának felhasználói Felületet.  Miután telepítette a HCM, van lehetősége a felhasználói felület, amely a HybridConnectionManagerUi.exe futtatásával a Hybrid Connection Manager telepítő könyvtárában.  Azt is könnyen elérésekor a Windows 10 beírásával *Hybrid Connection Manager felhasználói felületén* be a keresőmezőbe.  

![A hibrid kapcsolat portál][7]

A HCM felhasználói felületének indításakor az első nevezzük a hibrid kapcsolatok ezen példánya számára a HCM használatára konfigurált összes tábla.  Ha módosítani kívánja, szüksége lesz Azure szolgáltatással való hitelesítésre. 

Egy vagy több hibrid kapcsolat hozzáadása a HCM:

1. Indítsa el a HCM felhasználói felület
1. Kattintson egy másik hibrid kapcsolat konfigurálása ![egy HC adja hozzá a HCM][8]

1. Jelentkezzen be az Azure-fiókjával
1. Előfizetés kiválasztása
1. Kattintson a továbbítási HCM szeretné a hibrid kapcsolatok ![egy HC kiválasztása][9]

1. Kattintson a Save (Mentés) gombra.

Ekkor megjelenik a hibrid kapcsolatok hozzáadott.  Kattintson a konfigurált a hibrid kapcsolat is, és az informatikai kapcsolatos részletek megtekintéséhez.

![HC részletei][10]

A HCM tudjanak támogatják a hibrid kapcsolatok portadatokkal lesz konfigurálva, a szükséges:

- TCP 80-as és 443-as porton keresztül Azure eléréséhez
- A hibrid kapcsolat végpont TCP elérésére
- Ehhez a végponthoz állomás és az Azure Service Bus-névtér DNS look-ups lehetősége

A HCM támogatja az új hibrid kapcsolatok, valamint a régebbi BizTalk hibrid kapcsolatok.

> [!NOTE]
> Webes szoftvercsatornák csatlakozási Azure továbbítási támaszkodik. Ez a lehetőség csak akkor érhető el a Windows Server 2012 vagy újabb. Amely miatt a Hybrid Connection Manager nem támogatott semmit a Windows Server 2012-nél régebbi.
>

### <a name="redundancy"></a>Redundancia ###

Minden egyes HCM támogathatja több hibrid kapcsolatok.  Ezenkívül egy adott hibrid kapcsolathoz több HCMs használható.  A megadott végpont konfigurált HCMs keresztül ciklikus időszeletelési forgalom az alapértelmezés lesz.  Ha magas rendelkezésre állás a hibrid kapcsolatok használata esetén a hálózatról, egyszerűen hozható létre több HCMs külön gépeken. 

### <a name="manually-adding-a-hybrid-connection"></a>Hibrid kapcsolat manuális hozzáadása ###

Ha egy, az előfizetés HCM példány futtatásához egy adott hibrid kapcsolat kívül, az átjáró kapcsolati karakterlánca megoszthatja velük a hibrid kapcsolat.  Erre úgy tekinthet tulajdonságai a hibrid kapcsolat a [Azure-portálon][portal]. A karakterlánc használatához kattintson a **adja meg manuálisan** a HCM gombra, és illessze be az átjáró kapcsolati karakterlánca.


## <a name="troubleshooting"></a>Hibaelhárítás ##

A kapcsolat állapota a hibrid kapcsolat azt jelenti, hogy legalább egy HCM van konfigurálva, hogy hibrid szülőhelyhez képes Azure eléréséhez.  Ha a hibrid kapcsolat állapota nem kéri a **csatlakoztatva**, akkor a hibrid kapcsolat nincs konfigurálva a bármely HCM, amely hozzáfér az Azure-bA.

A legfőbb oka, hogy az ügyfelek nem tudnak csatlakozni a végpont az oka, hogy a végpont IP-cím helyett egy DNS-név lett megadva.  Ha az alkalmazás nem tudja elérni a kívánt végpont, és egy IP-címet, váltson a HCM futtató egy a gazdagépen érvényes DNS-név használatával.  Más ellenőrizze az alábbiakat, amelyek, hogy a DNS-név megfelelően feloldja a gazdagépen, amelyen fut a HCM, és hogy van-e kapcsolat a gazdagép, amelyen fut a HCM a hibrid kapcsolat végpontjához.  

Nincs olyan eszköz alkalmazásszolgáltatás, mely a speciális eszközök (a Kudu) nevű tcpping konzolról is elindítható.  Ez az eszköz segítségével megállapíthatja, hogy ha rendelkezik hozzáféréssel a TCP-végpontot, de azt nem közli, ha hozzáfér a hibrid kapcsolat végponthoz.  A konzolon, szemben a hibrid kapcsolat végpontjának használatakor a sikeres ping csak jelzi, hogy rendelkezik-e konfigurálva az alkalmazáshoz, a gazdagép és a portszám kombináció használó hibrid kapcsolat.  

## <a name="biztalk-hybrid-connections"></a>Hibrid kapcsolatok a BizTalk szolgáltatásokban ##

A régebbi BizTalk hibrid kapcsolatok funkció új BizTalk hibrid kapcsolatok lezárása ki.  A meglévő BizTalk hibrid kapcsolatok használata az alkalmazások továbbra is, de az új Azure továbbítási használó hibrid kapcsolatok kell áttelepíteni.  A BizTalk verzióra az új szolgáltatás előnye között a következők:

- Nincsenek további BizTalk fiókot kell megadni.
- A TLS 1.2-es 1.0 hasonlóan BizTalk hibrid kapcsolatok helyett.
- Kommunikációs port a 80-as és 443-as, a DNS-név használatával Azure helyett IP-címek és a további számos más eléréséhez portokon keresztül van.  

Meglévő BizTalk hibrid kapcsolat hozzáadása az alkalmazáshoz, keresse fel az alkalmazást a a [Azure-portálon] [ portal] kattintson **hálózati > hibrid kapcsolati végpontok konfigurálása**.  Kattintson a klasszikus hibrid kapcsolatok tábla **klasszikus hibrid kapcsolat hozzáadása**.  Itt látni fogja a BizTalk hibrid kapcsolatok listáját.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
