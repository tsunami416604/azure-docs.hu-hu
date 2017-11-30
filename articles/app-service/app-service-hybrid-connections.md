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
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Az Azure App Service hibrid kapcsolatok #

Hibrid kapcsolatok egyben a szolgáltatás az Azure-ban és az Azure App Service szolgáltatás. Szolgáltatásként rendelkezik használ és képességekkel ruházzák, az App Service-ben használt. Hibrid kapcsolatok és az App Service kívül használatát kapcsolatos további információkért lásd: [Azure hibrid kapcsolatok][HCService].

App Service-ben belül hibrid kapcsolatok használható más hálózatokkal alkalmazás erőforrásaihoz. Egy alkalmazás végpontjának a alkalmazás hozzáférést biztosít. Nem engedélyezi egy másik képességet az alkalmazáshoz való hozzáféréshez. Az App Service-ben használt, minden a hibrid kapcsolat felel meg egyetlen TCP-állomás és port kombinációját. Ez azt jelenti, hogy a hibrid kapcsolat végpont lehet operációs rendszert, és bármilyen alkalmazás, feltéve, hogy a TCP-figyelési port elérésére. A hibrid kapcsolatok száma a szolgáltatás nem tudja, vagy ügyeljen arra, mi az a protokoll, vagy mi elérésére. Hálózati hozzáférés egyszerűen is ellát.  


## <a name="how-it-works"></a>Működés ##
Két kimenő hívások Azure Service Bus Relay a hibrid kapcsolatok szolgáltatás foglalja magában. Van-e kapcsolat a könyvtárból a gazdagépen, amelyen fut az alkalmazás az App Service-ben. Service Bus Relay a hibrid kapcsolat Manager (HCM) kapcsolat is van. A HCM a továbbítási szolgáltatás, amely telepít elérni kívánt erőforrást a hálózaton belül. 

A két illesztett kapcsolatokon keresztül az alkalmazás rendelkezik egy rögzített gazdagépet: port kombinációján TCP alagút a HCM másik oldalán. A kapcsolat a TLS 1.2 használja a biztonság és a közös hozzáférésű jogosultságkód (SAS) kulcsok a hitelesítéshez és engedélyezéshez.    

![A hibrid kapcsolat magas szintű folyamata ábrája][1]

Az alkalmazás, amely megfelel a konfigurált kötéssel a hibrid kapcsolat DNS-kérelmet küld, ha a kimenő TCP-forgalmat a rendszer átirányítja a hibrid kapcsolaton keresztül.  

> [!NOTE]
> Ez azt jelenti, hogy egy DNS-nevet a hibrid kapcsolat mindig használandó használja. Néhány ügyfélszoftver nem teendő DNS-címkeresést, ha a végpont egy IP-címet használja helyette.
>
>

A hibrid kapcsolatok szolgáltatás két típusa van: a hibrid kapcsolatok felkínált egy Service Bus Relay szolgáltatást, és a régebbi Azure BizTalk szolgáltatások hibrid kapcsolatok. Ez néven klasszikus hibrid kapcsolatok a portálon. Nincs további információkat a cikk későbbi részében.

### <a name="app-service-hybrid-connection-benefits"></a>App Service a hibrid kapcsolat előnyei ###

Számos a hibrid kapcsolatok funkció előnyei többek között:

- Alkalmazások férjenek hozzá a helyszíni rendszer és a szolgáltatások biztonságosan.
- A szolgáltatás nem szükséges egy internetről elérhető végpontot.
- Fontos, hogy gyorsan és egyszerűen állíthat be. 
- Minden egyes hibrid kapcsolat felel meg egyetlen állomás: port kombinációjához, a biztonsági hasznosak.
- Általában nem szükséges tűzfal lyuk. A kapcsolatok az összes kimenő szokásos webes portokon keresztül.
- Mivel a szolgáltatás hálózati szint, akkor független az alkalmazás által használt nyelv és a végpont által használt technológiára.
- Több hálózat egyetlen alkalmazásból hozzáférést biztosíthat használható. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Művelet nem hajtható végre a hibrid kapcsolatok ###

Néhány dolgot a hibrid kapcsolatok, beleértve a nem hajtható végre:

- A meghajtó csatlakoztatása.
- UDP segítségével.
- Dinamikus portok, például a passzív FTP-módban vagy passzív módban kiterjesztett használó TCP-alapú szolgáltatások eléréséhez.
- LDAP, támogató, mert UDP néha igényel.
- Active Directory támogatása.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adja hozzá, és a hibrid kapcsolatok létrehozása az alkalmazásban ##

Hibrid kapcsolatok App Service-alkalmazás az Azure portálon, vagy az Azure-továbbító az Azure portálon keresztül is létrehozhat. Azt javasoljuk, hogy hozzon létre hibrid kapcsolatok révén az App Service-alkalmazás, amelyet a hibrid kapcsolat használata. A hibrid kapcsolat létrehozásához, lépjen a [Azure-portálon] [ portal] , és válassza ki az alkalmazást. Válassza ki **hálózati** > **hibrid kapcsolati végpontok konfigurálása**. Itt láthatja a hibrid kapcsolatok konfigurált az alkalmazásra vonatkozóan.  

![Képernyőfelvétel a hibrid kapcsolat listája][2]

Új hibrid kapcsolat hozzáadásához válassza **adja hozzá a hibrid kapcsolat**.  Látni fogja, hogy már létrehozta a hibrid kapcsolatok listáját. Egy vagy több hozzáadása az alkalmazáshoz, jelölje be a meglévők közül, és válassza **hozzáadása a hibrid kapcsolat kijelölt**.  

![Képernyőfelvétel a hibrid kapcsolat portál][3]

Ha az új hibrid kapcsolat létrehozásához, jelölje be **új hibrid kapcsolat létrehozása**. Adja meg a: 

- Végpont nevét.
- Végpont állomásneve.
- Végpont portja.
- Service Bus-névtér szeretne használni.

![Képernyőfelvétel a hozzon létre új hibrid kapcsolat párbeszédpanel][4]

Minden hibrid kapcsolat egy Service Bus-névtér van kötve, és minden egyes Service Bus-névtér az Azure-régióban van. Fontos próbálja használni a Service Bus-névtér az alkalmazás ugyanabban a régióban előidézett hálózati késleltetés elkerülése érdekében.

Ha szeretné a hibrid kapcsolat eltávolítása az alkalmazásból, a jobb gombbal kattintson rá, és válassza ki **Disconnect**.  

A hibrid kapcsolat való hozzáadásakor az alkalmazást, részletes információkat tekinthet meg által egyszerűen jelölje ki. 

![Képernyőfelvétel a hibrid kapcsolatok részletei][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>A hibrid kapcsolat létrehozása az Azure-továbbítási portálon ###

Az alkalmazáson belül a portál élmény mellett a hibrid kapcsolatok a Azure Relay-portálon is létrehozhat. A hibrid kapcsolat App Service által használható kell:

* Ügyfél-hitelesítés szükséges.
* Rendelkezik egy metaadatelem nevű végpont, gazdagép: port kombinációját, értéke.

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és az App Service-csomagok ##

A hibrid kapcsolatok funkció a Basic, Standard, Premium és termékváltozatok árképzési elszigetelt csak érhető el. Nincsenek korlátai, a tarifacsomag kötődik.  

> [!NOTE] 
> Csak új hibrid kapcsolatok Azure továbbítási alapján hozhat létre. Új BizTalk hibrid kapcsolatok nem hozható létre.
>

| terv díjszabása | A terv felhasználható hibrid kapcsolatok száma |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Prémium | 200 |
| Izolált | 200 |

Vegye figyelembe, hogy az App Service-csomag bemutatja, hogy hány hibrid kapcsolatok használatban vannak, és alkalmazásokat.  

![Képernyőkép az App Service-csomag tulajdonságai][6]

A hibrid kapcsolat részleteinek kiválasztása Láthatja, hogy az alkalmazás megtekintése a látott összes információt. A csomagot hány más alkalmazásokat használ, hogy a hibrid kapcsolat is látható.

Az App Service-csomag használható hibrid kapcsolati végpontok száma korlátozva van. Minden egyes használt, a hibrid kapcsolat azonban használható tetszőleges számú alkalmazások között, hogy a tervben. Például öt külön alkalmazásokban használt az App Service-csomag egy hibrid kapcsolat egy hibrid kapcsolat számít.

A hibrid kapcsolatot is használ további költségek van. További információkért lásd: [Service Bus árképzési][sbpricing].

## <a name="hybrid-connection-manager"></a>Hibrid Csatlakozáskezelő ##

A hibrid kapcsolatok a hálózat, amelyen a hibrid kapcsolat végpont továbbító ügynök szolgáltatáshoz. A továbbító ügynök neve a hibrid kapcsolat Manager (HCM). HCM, az alkalmazást a töltheti le a [Azure-portálon][portal], jelölje be **hálózati** > **hibridkapcsolativégpontokkonfigurálása**.  

Ez az eszköz a Windows Server 2012 vagy újabb fut. Amikor telepíti, HCM szolgáltatásként fut, amely kapcsolódik a Service Bus Relay, a beállított végpontjaikra alapján. HCM közötti kapcsolatok kimenő Azure 443-as porton keresztül.    

Miután telepítette a HCM, a felhasználói felület használata az eszköz HybridConnectionManagerUi.exe is futtathatja. A fájl a Hybrid Connection Manager telepítő könyvtárában van. Windows 10-ben csak is kereshet *Hybrid Connection Manager felhasználói felületén* be a keresőmezőbe.  

![Képernyőfelvétel a Hybrid Connection Manager][7]

A HCM felhasználói felületének indításakor az első nevezzük ezen példánya számára a HCM használatára konfigurált összes hibrid kapcsolatok listáját. Ha azt szeretné, hogy hajtsa végre a módosításokat, először hitelesíteniük az Azure-ral. 

Egy vagy több hibrid kapcsolat hozzáadása a HCM:

1. Indítsa el a HCM felhasználói felületén.
1. Válassza ki **egy másik hibrid kapcsolat konfigurálása**.
![Képernyőkép a konfigurálása az új hibrid kapcsolatok][8]

1. Jelentkezzen be az Azure-fiókjával.
1. Válasszon egy előfizetést.
1. Válassza ki a hibrid kapcsolatok, amelyet továbbítani HCM.
![Képernyőkép a hibrid kapcsolatok][9]

1. Kattintson a **Mentés** gombra.

Most már megtekintheti a hibrid kapcsolatok hozzáadott. Igény szerint kiválaszthatja a konfigurált hibrid kapcsolat részletek megtekintéséhez.

![Képernyőkép a hibrid kapcsolat adatai][10]

A hibrid kapcsolatok konfigurálja azokat támogatásához HCM van szükség:

- TCP Azure-hozzáférést 80-as és 443-as porton keresztül.
- A hibrid kapcsolat végpont TCP elérésére.
- A végpont gazdagép és a Service Bus-névtér DNS look-ups tegye lehetővé teszi.

HCM támogatja az új hibrid kapcsolatok és BizTalk hibrid kapcsolatok.

> [!NOTE]
> Webes szoftvercsatornák csatlakozási Azure továbbítási támaszkodik. Ez a lehetőség csak akkor érhető el a Windows Server 2012 vagy újabb. Miatt, amely HCM nem támogatott semmit a Windows Server 2012-nél régebbi.
>

### <a name="redundancy"></a>Redundancia ###

Minden egyes HCM támogathatja több hibrid kapcsolatok. Ezenkívül egy adott hibrid kapcsolathoz több HCMs használható. Az alapértelmezett viselkedés bármely adott végpont konfigurált HCMs közötti forgalom irányítására. Ha magas rendelkezésre állás a hibrid kapcsolatok használata esetén a hálózatról, több HCMs külön gépeken futnak. 

### <a name="manually-add-a-hybrid-connection"></a>A hibrid kapcsolat manuális hozzáadása ###

Valaki kívül az előfizetés HCM példány futtatásához egy adott hibrid kapcsolat engedélyezéséhez megoszthatja velük a hibrid kapcsolat az átjáró kapcsolati karakterlánca. Erre úgy tekinthet tulajdonságai a hibrid kapcsolat a [Azure-portálon][portal]. A karakterlánc használatához válassza **adja meg manuálisan** HCM, és illessze be az átjáró kapcsolati karakterlánca.


## <a name="troubleshooting"></a>Hibaelhárítás ##

"Csatlakoztatott" állapotának azt jelenti, hogy, hogy legalább egy HCM, hogy a hibrid kapcsolat van konfigurálva, és képes Azure eléréséhez. Ha a hibrid kapcsolat állapota nem kéri a **csatlakoztatva**, a hibrid kapcsolat nincs konfigurálva a bármely HCM, amely hozzáfér az Azure-bA.

A legfőbb oka, hogy az ügyfelek nem tudnak csatlakozni a végpont nem, mert a végpont IP-cím segítségével, a DNS-név lett megadva. Ha az alkalmazás nem tudja elérni a kívánt végpont, és egy IP-címet, váltson a HCM futtató egy a gazdagépen érvényes DNS-név használatával. Emellett ellenőrizze, hogy a gazdagépen, amelyen fut a HCM megfelelően feloldja a DNS-nevét. Győződjön meg arról, hogy nincs-e kapcsolat a gazdagép, amelyen fut a HCM a hibrid kapcsolat végpontjához.  

Az App Service-ben a tcpping eszköz is elindítható a speciális eszközök (a Kudu) konzolról. Ez az eszköz segítségével megállapíthatja, hogy ha rendelkezik hozzáféréssel a TCP-végpontot, de azt nem közli, ha hozzáfér a hibrid kapcsolat végponthoz. Az eszköz a konzolon, szemben a hibrid kapcsolat végpontjának használatakor csak megerősíti, hogy használja-e a gazdagép és a portszám kombináció.  

## <a name="biztalk-hybrid-connections"></a>Hibrid kapcsolatok a BizTalk szolgáltatásokban ##

A régebbi BizTalk hibrid kapcsolatok funkció le van zárva, új BizTalk hibrid kapcsolatok. A meglévő BizTalk hibrid kapcsolatok használata az alkalmazások továbbra is, de át kell telepíteni az új Azure továbbítási használó hibrid kapcsolatok. A BizTalk verzióra az új szolgáltatás előnye között a következők:

- Nincsenek további BizTalk fiókot kell megadni.
- A TLS 1.2-es verziója helyett az 1.0-s verziója.
- Kommunikációs 80-as és 443-as porton keresztül, és használja a DNS-név Azure helyett IP-címek és további portokat számos eléréséhez.  

Meglévő BizTalk hibrid kapcsolat hozzáadása az alkalmazáshoz, keresse fel az alkalmazást a a [Azure-portálon][portal], és válassza ki **hálózati** > **konfigurálása a hibrid kapcsolati végpontok**. A klasszikus hibrid kapcsolatok kijelölése **klasszikus hibrid kapcsolat hozzáadása**. A BizTalk hibrid kapcsolatok listáját megtekintheti.  


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
