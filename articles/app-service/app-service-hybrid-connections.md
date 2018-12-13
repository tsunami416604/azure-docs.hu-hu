---
title: Hibrid kapcsolatok – az Azure App Service |} A Microsoft Docs
description: Hogyan hozhat létre, és a hibrid kapcsolatok segítségével különböző hálózatokon lévő erőforrások eléréséhez
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 084d5e9453ea5a55bdeeff839e4c70890575c83d
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258015"
---
# <a name="azure-app-service-hybrid-connections"></a>Az Azure App Service hibrid kapcsolatai #

Hibrid kapcsolatok az Azure egy szolgáltatása, és a egy szolgáltatás az Azure App Service-ben. Szolgáltatás rendelkezik használ, és az App Service-ben használt képességeinél több képességet. A hibrid kapcsolatok és a használatuk kívül az App Service kapcsolatos további információkért lásd: [Azure Relay hibrid kapcsolatok][HCService].

App Service a hibrid kapcsolatok alkalmazás-erőforrások a más hálózatok eléréséhez használható. Egy alkalmazás végpontnak az alkalmazásból való elérést biztosít. Nem engedélyezi egy másik képesség elérhetik az alkalmazást. Az App Service-ben használ, egyetlen TCP-állomás és port kombinációja utal. minden hibrid kapcsolat. Ez azt jelenti, hogy a hibrid kapcsolat végpontja is lehet bármely operációs rendszerben, és minden alkalmazás, feltéve, hogy érnek el egy figyelő TCP-portot. Hibrid kapcsolatok szolgáltatását nem ismert, és gondoskodik a protokoll van, vagy amit Ön hozzáfér. Egyszerűen csak a számítógép hálózati hozzáférést.  


## <a name="how-it-works"></a>Működés ##
Hibrid kapcsolatok szolgáltatását az Azure Service Bus Relay két kimenő hívások áll. Van egy kapcsolat egy erőforrástárból a gazdagépen, hol futnak az alkalmazás az App Service-ben. A Service Bus Relay a hibrid kapcsolat Manager (HCM) kapcsolat is van. A HCM a továbbítási szolgáltatás úgy, hogy az elérni kívánt erőforrást üzemeltető a hálózaton belül. 

A két csatlakoztatott kapcsolatokon keresztül alkalmazása egy rögzített gazdagépet: port kombináció TCP vezető alagút rendelkezik a HCM másik oldalon. A kapcsolat a TLS 1.2 használja, a biztonság és a közös hozzáférésű jogosultságkód (SAS) kulcsokat a hitelesítéshez és engedélyezéshez.    

![Hibrid kapcsolat magas szintű folyamat ábrája][1]

Amikor az alkalmazás egy DNS-kérelmet, amely megfelel a konfigurált hibrid kapcsolódási végpontot, a kimenő TCP-forgalmat a rendszer átirányítja a hibrid kapcsolaton keresztül.  

> [!NOTE]
> Ez azt jelenti, hogy lehetőleg mindig használja a hibrid kapcsolat egy DNS-neve. Néhány ügyfél szoftver nem egy DNS-címkeresés Ha a végpont; ezek helyett használja az IP-címet.
>


### <a name="app-service-hybrid-connection-benefits"></a>App Service hibrid kapcsolat előnyei ###

Számos arra, hogy a hibrid kapcsolatok képesség, beleértve:

- Az alkalmazások hozzáférhessenek a helyszíni rendszerek és szolgáltatások biztonságos.
- A funkció nem igényel az internethez csatlakozó végponttal.
- Fontos, hogy gyors és könnyű őket beállítani. 
- Minden hibrid kapcsolat megegyezik az egyetlen fogadó: port kombinációjához, a biztonság hasznos.
- Általában nem szükséges tűzfal lyuk. A kapcsolatok összes kimenő szabványos webes portokon keresztül.
- Mivel a szolgáltatás hálózati szinten, a független az alkalmazás által használt nyelvet és a végpont által használt technológia.
- Több hálózat egyetlen alkalmazásból származó hozzáférést biztosíthat használható. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>A hibrid kapcsolatokkal nem hajtható végre dolgokat ###

Elvégzésére nem használatával hibrid kapcsolatokat létesítenek a következők:

- Meghajtót csatlakoztatni.
- Használja a UDP.
- Hozzáférés a TCP-alapú használó szolgáltatások dinamikus portok, például az FTP passzív mód vagy a kiterjesztett Paszív mód.
- Támogatja az LDAP, mert ehhez szükség lehet a UDP.
- Támogatja az Active Directory, mert a tartományhoz való csatlakozás egy App Service-feldolgozó nem.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adja hozzá, és a hibrid kapcsolatok létrehozása az alkalmazásban ##

Hibrid kapcsolat létrehozása, lépjen a [az Azure portal] [ portal] válassza ki az alkalmazást. Válassza ki **hálózatkezelés** > **a hibrid kapcsolati végpontok konfigurálása**. Itt láthatja az alkalmazás a hibrid kapcsolatok konfigurált.  

![Képernyőfelvétel: a hibrid kapcsolat listája][2]

Új hibrid kapcsolat hozzáadásához válassza **[+] hibrid kapcsolat hozzáadása**.  A már létrehozott hibrid kapcsolatok listáját láthatja. Egy vagy több őket az alkalmazás hozzáadásához válassza ki azokat, és válassza **hozzáadása a kiválasztott hibrid kapcsolat**.  

![A portál képernyőképe a hibrid kapcsolat][3]

Ha szeretne létrehozni egy új hibrid kapcsolatot, jelölje be **új hibrid kapcsolat létrehozása**. Adja meg a: 

- Hibrid kapcsolat neve.
- Végpont gazdaneve.
- Végponti port.
- Service Bus-névtér létrehozásához használni szeretne.

![Képernyőkép, hozzon létre új hibrid kapcsolat párbeszédpanel][4]

Service Bus-névtér minden hibrid kapcsolat van kötve, és minden egyes Service Bus-névtér egy Azure-régióban. Fontos próbálja használni a Service Bus-névtér ugyanabban a régióban, az alkalmazás által kiváltott hálózati késés csökkentése érdekében.

Ha el kívánja távolítani a hibrid kapcsolat az alkalmazásból, kattintson a jobb gombbal, és válassza ki **Disconnect**.  

Hibrid kapcsolat van adva az alkalmazáshoz, ha részletes információkat tekinthet, egyszerűen, ha kiválasztja. 

![Képernyőfelvétel: a hibrid kapcsolatok részletei][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Hibrid kapcsolat létrehozása az Azure Relay-portálon ###

Az alkalmazáson belül a portál élmény mellett az Azure Relay Portalon hibrid kapcsolatokat hozhat létre. A hibrid kapcsolat az App Service által használandó kell:

* Ügyfél-hitelesítés szükséges.
* Rendelkezik egy metaadatelem nevű végpontot, értékeként gazdagépet: port kombinációját.

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és az App Service-csomagok ##

App Service hibrid kapcsolataira csak alapszintű, Standard, prémium szintű és elkülönített díjszabású termékváltozatban érhető el. Vannak kötve, a díjszabással korlátozva van.  

| Díjszabási csomag | A csomag használható a hibrid kapcsolatok száma |
|----|----|
| Alapszintű | 5 |
| Standard | 25 |
| Prémium | 200 |
| Izolált | 200 |

Az App Service-csomag felhasználói felület megtudhatja, hány hibrid kapcsolatot használ, és milyen alkalmazásokat.  

![Képernyőkép az App Service-csomag tulajdonságai][6]

Válassza ki a hibrid kapcsolat részleteinek megtekintéséhez. Láthatja, hogy az alkalmazás megtekintése a látott összes információt. Hány más alkalmazásokat a csomagot használ, hogy a hibrid kapcsolat is megjelenik.

Az App Service-csomagban használható hibrid kapcsolati végpontok száma korlátozva van. Minden egyes használt, a hibrid kapcsolat azonban használható alkalmazások bármennyi-csomag. Az App Service-csomag öt különböző alkalmazásokban használt egyetlen hibrid kapcsolat például egy hibrid kapcsolat számít.

### <a name="pricing"></a>Díjszabás ###

Folyamatban van egy App Service-csomag SKU követelmény itt kívül a rendszer a hibrid kapcsolatok használatával további költségekkel járnak. Nincs a hibrid kapcsolat által használt minden egyes figyelő esetén számítunk fel. A figyelő a Hibridkapcsolat-kezelő rendszer. Ha a két Hibridkapcsolat-kezelők által támogatott 5 hibrid kapcsolatot, 10 figyelői lenne. További információkért lásd: [Service Bus díjszabásáról][sbpricing].

## <a name="hybrid-connection-manager"></a>Hibridkapcsolat-kezelő ##

A hibrid kapcsolatok szolgáltatást a hálózaton, amelyen a hibrid kapcsolat végpontja továbbító ügynök szükséges. A továbbító ügynök a hibrid kapcsolat Manager (HCM) nevezzük. HCM, az alkalmazás töltheti le a [az Azure portal][portal], jelölje be **hálózatkezelés** > **a hibrid kapcsolati végpontokkonfigurálása**.  

Ez az eszköz fut, a Windows Server 2012 és újabb verziók. A HCM szolgáltatásként fut, és kapcsolódik az Azure Relay a 443-as porton a kimenő.  

HCM telepítés után a felhasználói felület használata az eszköz HybridConnectionManagerUi.exe is futtathatja. Ez a fájl a Hibridkapcsolat-kezelő telepítési könyvtárban van. A Windows 10-ben is kereshet *Hibridkapcsolat-kezelő felhasználói felületén* kifejezést a keresőmezőbe.  

![Képernyőkép a Hibridkapcsolat-kezelő][7]

A HCM felhasználói felületének indításakor megjelenik az első lépésben egy táblázat listázza az adott a HCM-példány konfigurált összes hibrid kapcsolatok. Ha semmilyen módosítást szeretne, először az Azure-hitelesítésre. 

Egy vagy több hibrid kapcsolat hozzáadása a HCM:

1. Indítsa el a HCM felhasználói felületén.
2. Válassza ki **konfigurálása egy másik hibrid kapcsolat**.
![Képernyőkép az új hibrid kapcsolatok konfigurálása][8]

1. Jelentkezzen be az Azure-fiókjával.
1. Válasszon egy előfizetést.
1. Válassza ki a hibrid kapcsolatok a HCM továbbítani kívánt.
![Képernyőkép a hibrid kapcsolatok][9]

1. Kattintson a **Mentés** gombra.

Most már megtekintheti a hibrid kapcsolatok hozzáadott. Választhatja a konfigurált hibrid kapcsolat részleteinek megtekintéséhez.

![Képernyőkép a hibrid kapcsolat részleteivel][10]

A konfigurált hibrid kapcsolatok támogatásához HCM van szükség:

- TCP hozzáférés az Azure-bA 443-as porton keresztül.
- A hibrid kapcsolat végpontja TCP hozzáférés.
- Lehetővé teszi a végponti gazdagép és a Service Bus-névtér DNS look-ups.

> [!NOTE]
> Az Azure Relay támaszkodik a Web Sockets, a hálózati kapcsolatot. Ez a lehetőség csak akkor érhető el a Windows Server 2012 vagy újabb. Ezért HCM nem támogatott az semmit a Windows Server 2012-nél régebbi.
>

### <a name="redundancy"></a>Redundancia ###

Minden egyes HCM több hibrid kapcsolatot támogat. Emellett bármely adott hibrid kapcsolat több HCMs támogatható. Az alapértelmezett viselkedést, hogy a konfigurált HCMs bármely adott végponton keresztül irányítja a forgalmat. Ha magas rendelkezésre állás a hibrid kapcsolatok a hálózatról, több HCMs külön gépeken fussanak. Betöltés terjesztési használt algoritmust határozza meg a továbbítási szolgáltatás által a HCMs forgalom elosztása véletlenszerű hozzárendelés. 

### <a name="manually-add-a-hybrid-connection"></a>Adja hozzá manuálisan a hibrid kapcsolat ###

Ahhoz, hogy valakinek, aki nem az előfizetés egy adott a hibrid kapcsolat HCM példány üzemeltetésére, ossza meg az átjáró kapcsolati karakterlánca velük a hibrid kapcsolat. A hibrid kapcsolat tulajdonságai az átjáró kapcsolati karakterlánca látható a [az Azure portal][portal]. A karakterlánc használatához válassza **adja meg manuálisan** a HCM, és illessze be az átjáró kapcsolati karakterlánca.

![Adja hozzá manuálisan a hibrid kapcsolat][11]

### <a name="upgrade"></a>Frissítés ###

Nincsenek időszakos frissítések, a Hibridkapcsolat-kezelő kapcsolatos problémák megoldása vagy biztosítanak. Frissítések jelennek meg, amikor egy előugró ablak jelennek meg a HCM felhasználói felületén. A frissítés alkalmazása a alkalmazni a módosításokat, és indítsa újra a HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Hibrid kapcsolat hozzáadása az alkalmazáshoz programozott módon ##

Az alábbi esetekben API-k segítségével közvetlenül kezelheti a hibrid kapcsolatok a web apps csatlakozik. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

A hibrid kapcsolat társított JSON-objektum hasonlóan néz ki:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Egy ezzel az információval módja a armclient, amelyeket kérhet le a [ARMClient] [ armclient] GitHub-projekt. Íme egy példa egy már létező hibrid kapcsolat csatolására a webalkalmazáshoz. Hozzon létre egy JSON-fájlt, mint például a fenti séma szerint:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Az API használatához, van szükség a küldési kulcs és a relay erőforrás-azonosítója. Ha mentette az adatait a filename hctest.json rendelkező, adja ki a hibrid kapcsolat az alkalmazáshoz csatolja ezt a parancsot: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Hibaelhárítás ##

Az állapot "Csatlakoztatva", azt jelenti, hogy legalább egy HCM, hogy a hibrid kapcsolat van konfigurálva, és képes éri el az Azure. Ha a hibrid kapcsolat állapota nem tegyük fel, hogy **csatlakoztatva**, a hibrid kapcsolat nincs konfigurálva minden olyan HCM, amely hozzáfér az Azure-bA.

Az elsődleges célja, hogy az ügyfelek nem tudnak csatlakozni a végpont az oka, hogy a végpont IP-cím helyett egy DNS-név lett megadva. Ha az alkalmazás nem érhető el a kívánt végpontot, és egy IP-címet használva, váltson a HCM futtató kiszolgáló, amely a gazdagépen érvényes DNS-név használatával. Emellett ellenőrizze, hogy a gazdagépen, amelyen a HCM fut megfelelően feloldja a DNS-nevét. Győződjön meg arról, hogy nincs-e kapcsolat a gazdagép a HCM futtató kiszolgáló, a hibrid kapcsolat végpontja.  

Az App Service-ben a tcpping eszköz a speciális eszközök (Kudu) konzolról is hívható. Ez az eszköz eláruljuk, ha hozzáfér a TCP-végpont, de azt nem mondja el, ha egy hibrid kapcsolat végpontja hozzáféréssel rendelkezik. Az eszköz a konzolon, a hibrid kapcsolat végponton való használatakor csak megerősíti, hogy a gazdagép: port kombinációját használja.  

## <a name="biztalk-hybrid-connections"></a>Hibrid kapcsolatok a BizTalk szolgáltatásokban ##

Ez a funkció korai formájában BizTalk hibrid kapcsolatok lett meghívva. Ez a funkció 2018. május 31-ig. a végfelhasználók az élettartam történt, és operations megszűnt. BizTalk hibrid kapcsolatok a web Apps-alkalmazások összes el lettek távolítva, és nem a portál vagy API-n keresztül elérhető. Ha továbbra is ezeket a a Hibridkapcsolat-kezelő konfigurált régebbi kapcsolatokat, majd meg Kifutott állapotot és egy záró az élettartam utasítás megjelenítéséhez a lap alján.

![BizTalk hibrid kapcsolatok a HCM a][12]


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
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
