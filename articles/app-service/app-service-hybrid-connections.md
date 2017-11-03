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
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Az Azure App Service hibrid kapcsolatok #

## <a name="overview"></a>Áttekintés ##

Hibrid kapcsolatok, mind az Azure-szolgáltatás, valamint az Azure App Service szolgáltatás.  Szolgáltatásként van használatban és képességekkel ruházzák, amely az Azure App Service használja.  További információt a hibrid kapcsolatok és az Azure App Service itt kezdheti kívül használatát [Azure hibrid kapcsolatok][HCService]

Az Azure App Service-ben belül hibrid kapcsolatok használható más hálózatokkal alkalmazás erőforrásaihoz. Egy alkalmazás végpontjának a alkalmazás hozzáférést biztosít.  Nem engedélyezi az alternatív magasabb az alkalmazás eléréséhez.  Az App Service az alkalmazott, minden egyes hibrid kapcsolat felel meg egyetlen TCP-állomás és port kombinációját.  Ez azt jelenti, hogy a hibrid kapcsolat végpont lehet operációs rendszert, és bármely alkalmazás feltéve, hogy elérte a TCP-figyelési port-e, hogy. Hibrid kapcsolatok nem tudja, vagy ügyeljen arra, a protokoll van, vagy mi elérésére.  Hálózati hozzáférés egyszerűen is ellát.  


## <a name="how-it-works"></a>Működés ##
A hibrid kapcsolatok szolgáltatás Service Bus Relay két kimenő hívásainak foglalja magában.  Van-e az állomáson, ahol az az app Service szolgáltatásban működik a webalkalmazás és majd van egy kapcsolat és a Service Bus relay a hibrid kapcsolat Manager(HCM) tárból kapcsolat.  A HCM a továbbítási szolgáltatás, amely telepít, a hálózati helyet adó belül 

A két illesztett kapcsolatokon keresztül az alkalmazás rendelkezik egy rögzített gazdagépet: port kombinációján TCP alagút a HCM másik oldalán.  A kapcsolat biztonsági és a SAS-kulcs a hitelesítési/engedélyezési használja a TLS 1.2-es.    

![][1]

Ha az alkalmazás, amely megfelel a konfigurálása a hibrid kapcsolat végpont DNS kérést, majd a kimenő TCP-forgalmat a rendszer átirányítja a hibrid kapcsolat le.  

> [!NOTE]
> Ez azt jelenti, hogy egy DNS-nevet a hibrid kapcsolat mindig használandó használja.  Néhány ügyfélszoftver nem teendő DNS-címkeresést, ha a végpont egy IP-címet használja helyette.
>
>

A hibrid kapcsolatok, az új hibrid kapcsolatok az Azure-továbbító szolgáltatás felajánlott és a régebbi BizTalk hibrid kapcsolatok két típusa van.  A régebbi BizTalk hibrid kapcsolatok nevezzük klasszikus hibrid kapcsolatok a portálon.  Nincs további információ a jelen dokumentum azokat.

### <a name="app-service-hybrid-connection-benefits"></a>App Service hibrid kapcsolat előnyei ###

A hibrid kapcsolatok funkció köztük előnyt több

- Alkalmazások biztonságosan férjenek hozzá a helyi rendszeren és a szolgáltatások biztonságosan
- a szolgáltatás nem szükséges az interneten elérhető végponton
- gyorsan és egyszerűen állíthat be  
- kiváló biztonsági eleme, amely egyetlen állomás: port kombinációjához megfelel minden a hibrid kapcsolat
- általában nem szükséges tűzfal lyuk a kapcsolatok vannak az összes kimenő szokásos webes portokon keresztül
- mivel a szolgáltatás, amely azt is jelenti, hogy a hálózati szint is független az alkalmazás által használt nyelv és a végpont által használt technológiára
- több hálózat egyetlen alkalmazásból hozzáférést biztosíthat használat 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Művelet nem hajtható végre a hibrid kapcsolatok ###

Néhány dolgot hibrid kapcsolatokkal nem hajtható végre, és tartalmazzák:

- a meghajtó csatlakoztatása
- UDP segítségével
- fér hozzá a TCP-alapú szolgáltatásokat, például a passzív FTP-módban, vagy passzív módban bővített dinamikus portok használatára
- LDAP-támogatás, ahogy néha szükséges UDP
- Active Directory-támogatás

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Hozzáadása és az alkalmazás egy hibrid kapcsolat létrehozása ##

Hibrid kapcsolatok is létrehozható, az alkalmazás portálon keresztül vagy a hibrid kapcsolat szolgáltatás portálján.  Erősen ajánlott, hogy az alkalmazás-portál használatával a hibrid kapcsolatok szeretné használni az alkalmazás létrehozása.  A hibrid kapcsolat hozhatja létre a [Azure-portálon] [ portal] és használja fel az alkalmazás felhasználói felülete.  Válassza ki **hálózati > hibrid kapcsolati végpontok konfigurálása**.  Itt látható az alkalmazás használatára konfigurált hibrid kapcsolatok  

![][2]

Új hibrid kapcsolat hozzáadásához kattintson a hibrid kapcsolat hozzáadása.  A felhasználói felület, a megnyitott a hibrid kapcsolatok már létrehozott sorolja fel.  Az alkalmazás egy vagy több hozzáadásához kattintson azokra szeretné, majd nyomja le **hozzáadása a hibrid kapcsolat kijelölt**.  

![][3]

Új hibrid kapcsolat létrehozásához, kattintson a **új hibrid kapcsolat létrehozása**.  Itt adja meg a: 

- a végpont neve
- végpont állomásneve
- Végpont portja
- a szolgáltatásbusz-névtér szeretné használni

![][4]

Minden hibrid kapcsolat egy service bus-névtér van kötve, és minden egyes service bus-névtér az Azure-régióban van.  Fontos, próbálja meg, és a service bus-névtér használata ugyanabban a régióban, az alkalmazás előidézett hálózati késleltetés elkerülése érdekében.

Ha szeretné a hibrid kapcsolat eltávolítása az alkalmazásból, a jobb gombbal kattintson rá, és válassza ki **Disconnect**.  

A hibrid kapcsolat hozzáadása a webalkalmazáshoz után részletes információkat tekinthet meg egyszerűen kattintással.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és az App Service-csomagokról ##

A csak hibrid kapcsolatok hajtsa végre az új hibrid kapcsolatok.  Csak azok a Basic, Standard, Premium és termékváltozatok árképzési elszigetelt érhető el.  Nincsenek korlátai, a tarifacsomag kötődik.  

| Terv díjszabása | A terv felhasználható hibrid kapcsolatok száma |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Prémium | 200 |
| Izolált | 200 |

Mivel az App Service-csomag korlátozások is van az App Service-csomag, amely bemutatja, hogy hány hibrid kapcsolatok használatban vannak, és milyen alkalmazások által használt felhasználói felület.  

![][6]

Ahogy a alkalmazás nézet, részletes információkat tekinthet a hibrid kapcsolat azt.  Az itt látható tulajdonságok is az összes információt, az alkalmazás megtekintése volt, de is láthatja, hogy hány más alkalmazások az azonos App Service-csomag használ, hogy a hibrid kapcsolat.

![][7]

Hibrid kapcsolati végpontok, amely egy App Service-csomag használható száma korlátozva van, amíg minden egyes használt hibrid kapcsolat az adott App Service-csomag alkalmazások tetszőleges számú segítségével.  Tegyük fel például, hogy ha 1 hibrid kapcsolat az App Service-csomag az 5 külön alkalmazásokban használt, ez továbbra is csak 1 hibrid kapcsolat is.

A hibrid kapcsolatok túl alatt csak a Basic, Standard, Premium vagy elkülönített SKU felhasználható további költségek van.  A hibrid kapcsolat árakkal kapcsolatos részletekért nyissa meg itt: [Service Bus árképzési][sbpricing].

## <a name="hybrid-connection-manager"></a>Hibrid Csatlakozáskezelő ##

Ahhoz, hogy hibrid kapcsolatok működéséhez van szüksége a hálózat, amelyen a hibrid kapcsolat végpont továbbító ügynök.  A továbbító ügynök neve a hibrid kapcsolat Manager (HCM).  Ez az eszköz letölthető a **hálózati > hibrid kapcsolati végpontok konfigurálása** érhető el az alkalmazást a felhasználói felület a [Azure-portálon][portal].  

Ez az eszköz fut, a Windows server 2008 R2 és a Windows.  Egyszer az HCM fut a szolgáltatásként telepített.  Ez a szolgáltatás a beállított végpontjaikra alapján Azure szolgáltatásbusz-továbbító csatlakozik.  A HCM közötti kapcsolatok kimenő 80-as és 443-as portra.    

A HCM rendelkezik konfigurálásának felhasználói Felületet.  Miután telepítette a HCM van lehetősége a felhasználói felület a HybridConnectionManagerUi.exe, amely futtatja a Hybrid Connection Manager telepítő könyvtárában.  Azt is könnyen elérésekor a Windows 10 beírásával *Hybrid Connection Manager felhasználói felületén* be a keresőmezőbe.  

A HCM felhasználói felületének indításakor az első nevezzük a hibrid kapcsolatok ezen példánya számára a HCM használatára konfigurált összes tábla.  Ha módosítani kívánja szüksége lesz Azure szolgáltatással való hitelesítésre. 

Egy vagy több hibrid kapcsolat hozzáadása a HCM:

1. Indítsa el a HCM felhasználói felület
1. Kattintson egy másik hibrid kapcsolat konfigurálása![][8]

1. Jelentkezzen be az Azure-fiókjával
1. Előfizetés kiválasztása
1. Kattintson a HCM továbbítani szeretné a hibrid kapcsolatok használata esetén![][9]

1. Kattintson a Save (Mentés) gombra.

Ekkor megjelenik a hibrid kapcsolatok hozzáadott.  Kattintson a konfigurált hibrid kapcsolaton is, és a kapcsolatra vonatkozó részletes.

![][10]

A HCM tudjanak támogatják a hibrid kapcsolatok portadatokkal lesz konfigurálva, a szükséges:

- TCP 80-as és 443-as porton keresztül Azure eléréséhez
- A hibrid kapcsolat végpont TCP elérésére
- Ehhez a DNS képes a végpont-állomás és az azure szolgáltatásbusz-névtér keresések

A HCM támogatja az új hibrid kapcsolatok, valamint a régebbi BizTalk hibrid kapcsolatok.

### <a name="redundancy"></a>Redundancia ###

Minden egyes HCM támogathatja több hibrid kapcsolatok.  Ezenkívül egy adott hibrid kapcsolathoz több HCMs használható.  A megadott végpont konfigurált HCMs keresztül ciklikus időszeletelési forgalom az alapértelmezés lesz.  Ha magas rendelkezésre állás a hibrid kapcsolatok használata esetén a hálózatról, egyszerűen hozható létre több HCMs külön gépeken. 

### <a name="manually-adding-a-hybrid-connection"></a>Hibrid kapcsolat manuális hozzáadása ###

Ha valaki kívül az előfizetés egy adott a hibrid kapcsolat HCM példány futtatására, megoszthatja velük a hibrid kapcsolat átjáró kapcsolati karakterlánca.  Ez a hibrid kapcsolat tulajdonságaiban megtekintheti a [Azure-portálon][portal]. A karakterlánc használatához kattintson a **kézzel konfigurálásához** a HCM gombra, és illessze be az átjáró kapcsolati karakterlánca.


## <a name="troubleshooting"></a>Hibaelhárítás ##

Ha egy futó alkalmazáshoz be van állítva a a hibrid kapcsolat legalább egy HCM, amely rendelkezik a hibrid kapcsolat konfigurálva van, majd megtudhatja, hogy az állapot **csatlakoztatva** a portálon.  Ha ezt nem kéri a **csatlakoztatva** az azt jelenti, hogy vagy az alkalmazás nem működik, vagy az Azure-ba, a 80-as vagy 443-as port az a HCM nem lehet kapcsolódni.  

A legfőbb oka, hogy az ügyfelek nem tudnak csatlakozni a végpont az oka, hogy a végpont IP-cím helyett egy DNS-név lett megadva.  Ha az alkalmazás nem tudja elérni a kívánt végpont, és egy IP-címet, váltson a HCM futtató egy a gazdagépen érvényes DNS-név használatával.  Más ellenőrizze az alábbiakat, amelyek, hogy a DNS-név megfelelően feloldja a gazdagépen, amelyen fut a HCM, és hogy van-e kapcsolat a gazdagép, amelyen fut a HCM a hibrid kapcsolat végpontjához.  

Egy eszköz nincs az App Service-tcpping nevű konzolról is elindítható.  Ez az eszköz segítségével megállapíthatja, hogy ha egy TCP-végpont elérésére rendelkezik, de nem közli, ha hozzáfér a hibrid kapcsolat végponthoz.  Ha használja a konzol a hibrid kapcsolat végpontjának ellen, a sikeres ping csak jelzi, hogy rendelkezik-e konfigurálva az alkalmazáshoz, a gazdagép és a portszám kombináció használó hibrid kapcsolat.  

## <a name="biztalk-hybrid-connections"></a>Hibrid kapcsolatok a BizTalk szolgáltatásokban ##

A régebbi BizTalk hibrid kapcsolatok funkció le van zárva ki a további BizTalk a hibrid kapcsolat létrehozása.  A már meglévő BizTalk hibrid kapcsolatok használata az alkalmazások továbbra is, de át kell telepíteni az új szolgáltatáshoz.  A BizTalk verzióra az új szolgáltatás előnye között a következők:

- Nincsenek további BizTalk fiókot kell megadni
- A TLS 1.2-es 1.0 hasonlóan BizTalk hibrid kapcsolatok helyett
- Kommunikációs port a 80-as és 443-as, a DNS-név használatával Azure helyett IP-címek és a további számos más eléréséhez portokon keresztül van.  

A BizTalk a hibrid kapcsolat hozzáadása az alkalmazáshoz, keresse fel az alkalmazást a a [Azure-portálon] [ portal] kattintson **hálózati > hibrid kapcsolati végpontok konfigurálása**.  Kattintson a klasszikus hibrid kapcsolatok tábla **klasszikus hibrid kapcsolat hozzáadása a**.  Itt látni fogja a BizTalk hibrid kapcsolatok listáját.  


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

