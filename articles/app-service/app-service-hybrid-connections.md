---
title: Hibrid kapcsolatok
description: Megtudhatja, hogyan hozhat létre és használhat hibrid kapcsolatokat az Azure App Service-ben a különböző hálózatok erőforrásainak eléréséhez.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047777"
---
# <a name="azure-app-service-hybrid-connections"></a>Az Azure App Service hibrid kapcsolatai

A hibrid kapcsolatok az Azure-ban és az Azure App Service egyik szolgáltatása is. Szolgáltatásként az App Service-ben használt felhasználásokon és képességeken túlmutató funkciókkal rendelkezik. Ha többet szeretne megtudni a hibrid kapcsolatokról és azok Alkalmazásszolgáltatáson kívüli használatáról, olvassa el az [Azure Relay hibrid kapcsolatok][HCService].

Az App Service-en belül a hibrid kapcsolatok más hálózatok alkalmazáserőforrásainak elérésére is használhatók. Hozzáférést biztosít az alkalmazásból egy alkalmazás végpontjához. Nem teszi lehetővé egy alternatív képesség az alkalmazás eléréséhez. Az App Service-ben használt, minden hibrid kapcsolat korrelál egyetlen TCP-állomás és port kombinációja. Ez azt jelenti, hogy a hibrid kapcsolat végpontja bármely operációs rendszeren és bármely alkalmazáson lehet, feltéve, hogy egy TCP-figyelő porthoz fér hozzá. A Hibrid kapcsolatok szolgáltatás nem tudja, és nem érdekli, hogy mi az alkalmazásprotokoll, vagy mihez fér hozzá. Egyszerűen hálózati hozzáférést biztosít.  


## <a name="how-it-works"></a>Működés ##
A hibrid kapcsolatok szolgáltatás két kimenő hívásból áll az Azure Service Bus Relay számára. Van egy kapcsolat egy könyvtár a gazdagép, ahol az alkalmazás fut az App Service.There is a connection from a library on the host where your app is running in App Service. A hibrid kapcsolatkezelő (HCM) és a service bus relay között is van kapcsolat. A HCM egy továbbító szolgáltatás, amelyet a elérni kívánt erőforrást üzemeltető hálózaton belül telepít. 

A két összekapcsolt kapcsolaton keresztül az alkalmazás rendelkezik egy TCP-alagúttal a HCM másik oldalán lévő rögzített állomás:port kombinációhoz. A kapcsolat a TLS 1.2-es biztonsági és megosztott hozzáférés-hozzáférési (SAS) kulcsokat használja a hitelesítéshez és az engedélyezéshez.    

![A hibrid kapcsolat magas szintű áramlásának diagramja][1]

Ha az alkalmazás olyan DNS-kérelmet küld, amely megfelel egy konfigurált hibrid kapcsolatvégpontnak, a kimenő TCP-forgalom átlesz irányítva a hibrid kapcsolaton keresztül.  

> [!NOTE]
> Ez azt jelenti, hogy mindig dns-nevet kell használnia a hibrid kapcsolathoz. Egyes ügyfélszoftverek nem végeznek DNS-vizsgálatot, ha a végpont IP-címet használ helyette. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Az App Service hibrid kapcsolat előnyei ###

A hibrid kapcsolatok funkciónak számos előnye van, többek között:

- Az alkalmazások biztonságosan hozzáférhetnek a helyszíni rendszerekhez és szolgáltatásokhoz.
- A szolgáltatás nem igényel interneten elérhető végpontot.
- Gyorsan és egyszerűen beállítható. 
- Minden hibrid kapcsolat egyetlen állomás:port kombinációval egyezik, ami biztonsági okokból hasznos.
- Ez általában nem igényel tűzfal lyukak. A kapcsolatok mind kimenő szabványos webportok.
- Mivel a szolgáltatás hálózati szintű, az alkalmazás által használt nyelvhez és a végpont által használt technológiához képest független.
- Egyetlen alkalmazásból több hálózatban is biztosíthat hozzáférést. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>A hibrid kapcsolatokkal nem használható teendők ###

A hibrid kapcsolatokkal nem lehet:

- Szerelj fel egy meghajtót.
- UdP használata.
- A dinamikus portokat használó TCP-alapú szolgáltatások hoz való hozzáférés, például FTP passzív mód vagy kiterjesztett passzív mód.
- Támogatja az LDAP-t, mert udp-t igényelhet.
- Támogatja az Active Directoryt, mert nem tud tartományban csatlakozni egy App Service-dolgozóhoz.

### <a name="prerequisites"></a>Előfeltételek ###
 - A Windows App szolgáltatás szükséges. Ez csak a Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Hibrid kapcsolatok hozzáadása és létrehozása az alkalmazásban ##

Hibrid kapcsolat létrehozásához nyissa meg az [Azure Portalon,][portal] és válassza ki az alkalmazást. Válassza **a Hálózati** > **a hibrid kapcsolat végpontjainak konfigurálása lehetőséget.** Itt láthatja az alkalmazáshoz konfigurált hibrid kapcsolatokat.  

![A hibrid kapcsolatok listájának képernyőképe][2]

Új hibrid kapcsolat hozzáadásához válassza **a [+] Hibrid kapcsolat hozzáadása**lehetőséget.  Megjelenik a már létrehozott hibrid kapcsolatok listája. Ha egy vagy több elemet szeretne hozzáadni az alkalmazáshoz, jelölje ki a kívánt at, majd válassza a **Kijelölt hibrid kapcsolat hozzáadása**lehetőséget.  

![Képernyőkép a Hibrid kapcsolat portálról][3]

Ha új hibrid kapcsolatot szeretne létrehozni, válassza **az Új hibrid kapcsolat létrehozása**lehetőséget. Adja meg a következőket: 

- Hibrid kapcsolat neve.
- Végpont állomásneve.
- Végpontport.
- A használni kívánt Service Bus-névtér.

![Képernyőkép: Új hibrid kapcsolat létrehozása párbeszédpanel][4]

Minden hibrid kapcsolat egy Service Bus-névtérhez van kötve, és minden Service Bus-névtér egy Azure-régióban található. Fontos, hogy próbálja meg használni a Service Bus névtér ugyanabban a régióban, mint az alkalmazás, a hálózati okozta késés elkerülése érdekében.

Ha el szeretné távolítani a hibrid kapcsolatot az alkalmazásból, kattintson rá a jobb gombbal, és válassza a **Kapcsolat bontása parancsot.**  

Hibrid kapcsolat hozzáadásakor egyszerűen megtekintheti a részleteket az alkalmazáson. 

![Képernyőkép a hibrid kapcsolatok részleteiről][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Hibrid kapcsolat létrehozása az Azure Relay portálon ###

A portálon belüli élmény mellett az alkalmazáson belül hibrid kapcsolatokat is létrehozhat az Azure Relay portálon belül. Ahhoz, hogy az App Service hibrid kapcsolatot használjon, a következőket kell tennie:

* Ügyfél-hitelesítés szükséges.
* Van egy végpont nevű metaadat-elem, amely értékként állomás:port kombinációt tartalmaz.

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és App Service-csomagok ##

Az App Service hibrid kapcsolatok csak alapszintű, standard, prémium és elkülönített díjszabású termékváltozataiban érhetők el. Az árképzési tervhez korlátozások vannak kötve.  

| Árképzési terv | A tervben használható hibrid kapcsolatok száma |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Prémium | 200 |
| Elkülönített | 200 |

Az App Service-csomag felhasználói felülete megmutatja, hogy hány hibrid kapcsolatok at használnak, és milyen alkalmazások.  

![Képernyőkép az App Service-csomag tulajdonságairól][6]

A részletek megtekintéséhez válassza a hibrid kapcsolatot. Az alkalmazásnézetben látható összes információ látható. Azt is láthatja, hogy hány más alkalmazás használja ugyanazt a hibrid kapcsolatot ugyanabban a csomagban.

Az App Service-csomagban használható hibrid kapcsolati végpontok száma korlátozva van. Minden egyes használt hibrid kapcsolat azonban az adott csomagban tetszőleges számú alkalmazásban használható. Például egy hibrid kapcsolat, amely öt különböző alkalmazások ban egy App Service-csomag egy hibrid kapcsolat számít egy hibrid kapcsolat.

### <a name="pricing"></a>Díjszabás ###

Amellett, hogy van egy App Service-csomag Termékváltozat követelmény, a hibrid kapcsolatok használata további költségekkel jár. A hibrid kapcsolat által használt minden egyes figyelődíjat számít fel. A figyelő a hibrid kapcsolatkezelő. Ha két hibrid kapcsolatkezelő támogatja, akkor öt hibrid kapcsolatkezelők, az 10 figyelők. További információ: [Service Bus pricing][sbpricing].

## <a name="hybrid-connection-manager"></a>Hibrid kapcsolatkezelő ##

A hibrid kapcsolatok szolgáltatáshoz szükség van egy továbbítóügynökre a hibrid kapcsolat végpontját tartalmazó hálózatban. Ezt a továbbítóügynököt hibrid kapcsolatkezelőnek (HCM) nevezik. A HCM letöltéséhez válassza a Hibrid**kapcsolat végpontjainak**hálózati **konfigurálása** > lehetőséget az alkalmazásból az [Azure Portalon.][portal]  

Ez az eszköz Windows Server 2012-es és újabb verziókon fut. A HCM szolgáltatásként fut, és a 443-as porton csatlakozik az Azure Relay-hez.  

A HCM telepítése után futtathatja a HybridConnectionManagerUi.exe programot az eszköz felhasználói felületének használatához. Ez a fájl a Hibrid kapcsolatkezelő telepítési könyvtárában található. A Windows 10-ben egyszerűen rákereshet a *Hibrid kapcsolatkezelő felhasználói felületére* is a keresőmezőben.  

![A Hibrid kapcsolatkezelő képernyőképe][7]

A HCM felhasználói felületének indításakor az első dolog, amit látni egy táblázat, amely felsorolja az összes hibrid kapcsolatok, amelyek konfigurálva vannak a HCM ezen példányát. Ha módosításokat szeretne végrehajtani, először hitelesítse magát az Azure-ral. 

Ha egy vagy több hibrid kapcsolatot szeretne hozzáadni a HCM-hez:

1. Indítsa el a HCM felhasználói felületét.
2. Válassza **a Másik hibrid kapcsolat konfigurálása**lehetőséget.
![Képernyőkép: Új hibrid kapcsolatok konfigurálása][8]

1. Jelentkezzen be Azure-fiókjával, hogy hibrid kapcsolatai elérhetők az előfizetéseivel. A HCM ezen túlmenően nem használja tovább az Azure-fiókját. 
1. Válasszon előfizetést.
1. Válassza ki azt a hibrid kapcsolatokat, amelyeket a HCM-nek továbbítania kell.
![A hibrid kapcsolatok képernyőképe][9]

1. Kattintson a **Mentés** gombra.

Most már láthatja a hozzáadott hibrid kapcsolatokat. A részletek megtekintéséhez kiválaszthatja a konfigurált hibrid kapcsolatot is.

![A hibrid kapcsolat részleteinek képernyőképe][10]

A konfigurált hibrid kapcsolatok támogatásához a HCM a következőket igényli:

- TCP-hozzáférés az Azure-hoz a 443-as porton keresztül.
- TCP-hozzáférés a hibrid kapcsolat végpontjához.
- A végpontgazdagépen és a Service Bus-névtérben a DNS-keresések lehetővé teszi.

> [!NOTE]
> Az Azure Relay a webszoftvercsatornákra támaszkodik a kapcsolathoz. Ez a funkció csak Windows Server 2012-es vagy újabb rendszeren érhető el. Emiatt a HCM nem támogatott a Windows Server 2012-nél korábbi akta.
>

### <a name="redundancy"></a>Redundancia ###

Minden HCM több hibrid kapcsolatot is támogathat. Emellett bármely adott hibrid kapcsolat több HCM-del is támogatható. Az alapértelmezett viselkedés az, hogy a forgalmat a konfigurált HCMs-ek között bármely adott végpontra. Ha azt szeretné, hogy a hibrid kapcsolatok a hálózatról, több HCM-ek futtatásához külön gépeken. A továbbító szolgáltatás által a HCM-ek közötti forgalom elosztására használt terheléselosztási algoritmus véletlenszerű hozzárendelés. 

### <a name="manually-add-a-hybrid-connection"></a>Hibrid kapcsolat manuális hozzáadása ###

Ha engedélyezni szeretné, hogy az előfizetésén kívüli személy egy adott hibrid kapcsolat HCM-példányát üzemeltetze, ossza meg vele a hibrid kapcsolat átjárókapcsolati karakterláncát. Az átjáró kapcsolati karakterlánca az Azure [Portalon][portal]a hibrid kapcsolat tulajdonságaiban látható. A karakterlánc használatához jelölje be a **Manuális bevitel** a HCM-be jelölőnégyzetet, és illessze be az átjáró kapcsolati karakterláncába.

![Hibrid kapcsolat manuális hozzáadása][11]

### <a name="upgrade"></a>Frissítés ###

A hibrid kapcsolatkezelő rendszeres enrendszeres frissítéseket tartalmaz a problémák megoldásához vagy a fejlesztések biztosításához. A frissítések megjelenésekor egy felugró ablak jelenik meg a HCM felhasználói felületén. A frissítés alkalmazása alkalmazza a módosításokat, és indítsa újra a HCM-et. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Hibrid kapcsolat hozzáadása az alkalmazáshoz programozott módon ##

Az alábbiakban ismert API-k közvetlenül használhatók az alkalmazásokhoz csatlakoztatott hibrid kapcsolatok kezelésére. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

A hibrid kapcsolathoz társított JSON-objektum a következőképpen néz ki:

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

Az információ használatának egyik módja az armclient, amely az [ARMClient][armclient] GitHub projektből szerezhető be. Íme egy példa egy már meglévő hibrid kapcsolat csatlakoztatására az alkalmazáshoz. Hozzon létre egy JSON-fájlt a fenti séma szerint, például:

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

Az API használatához szükség van a küldési kulcs és a továbbító erőforrás-azonosító. Ha az adatokat a hctest.json fájlnévvel mentette, adja ki ezt a parancsot a hibrid kapcsolat alkalmazáshoz való csatolásához: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>A hibrid kapcsolatok védelme ##

Egy meglévő hibrid kapcsolat adható hozzá más App Service Web Apps bármely felhasználó, aki rendelkezik a megfelelő engedélyekkel az alapul szolgáló Azure Service Bus Relay. Ez azt jelenti, hogy ha meg kell akadályoznia, hogy mások újra felhasználják ugyanazt a hibrid kapcsolatot (például ha a célerőforrás olyan szolgáltatás, amely nem rendelkezik további biztonsági intézkedésekkel a jogosulatlan hozzáférés megakadályozására), akkor le kell zárnia az Azure-hoz való hozzáférést. Szervizbusz-továbbító.

Bárki, `Reader` aki hozzáfér a továbbító lesz képes _látni_ a hibrid kapcsolat, amikor megpróbálja hozzáadni a webalkalmazáshoz az Azure Portalon, de nem lesz képes _felvenni,_ mivel nem rendelkeznek az engedélyekkel a kapcsolati karakterlánc, amely létrehozásához használt továbbító kapcsolat. A hibrid kapcsolat sikeres hozzáadásához `listKeys` engedéllyel (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`) kell rendelkezniük. A `Contributor` közvetítőre vonatkozó engedélyt tartalmazó szerepkör vagy bármely más szerepkör lehetővé teszi a felhasználók számára a hibrid kapcsolat használatát, és hozzáadhatják azt a saját webalkalmazásaikhoz.

## <a name="troubleshooting"></a>Hibaelhárítás ##

A "Csatlakoztatott" állapota azt jelenti, hogy legalább egy HCM van konfigurálva, hogy a hibrid kapcsolat, és képes elérni az Azure-t. Ha a hibrid kapcsolat állapota nem mondja **ki a Csatlakoztatott,** a hibrid kapcsolat nincs konfigurálva az Azure-hoz hozzáféréssel rendelkező HCM-en.

Az elsődleges oka annak, hogy az ügyfelek nem tudnak csatlakozni a végpontjukhoz, az az, hogy a végpontot DNS-név helyett IP-címmel adták meg. Ha az alkalmazás nem éri el a kívánt végpontot, és IP-címet használt, váltson olyan DNS-névre, amely érvényes azon az állomáson, ahol a HCM fut. Ellenőrizze azt is, hogy a DNS-név megfelelően feloldódik-e azon az állomáson, ahol a HCM fut. Ellenőrizze, hogy van-e kapcsolat attól a helytől, ahol a HCM fut a hibrid kapcsolat végpontjára.  

Az App Service szolgáltatásban a **tcpping** parancssori eszköz meghívható az Advanced Tools (Kudu) konzolról. Ez az eszköz meg tudja mondani, hogy van-e hozzáférése a TCP-végponthoz, de azt nem, hogy rendelkezik-e a hibrid kapcsolat végpontjával. Ha az eszközt a konzolon hibrid kapcsolat végpontja ellen használja, csak azt erősíti meg, hogy állomás:port kombinációt használ.When you use the tool in the console against a Hybrid Connection endpoint, you are only confirmthat that it uses a host:port combination.  

Ha rendelkezik parancssori ügyféllel a végponthoz, tesztelheti a kapcsolatot az alkalmazáskonzolról. Tesztelheti például a webkiszolgáló végpontjaihoz való hozzáférést a curl használatával.

## <a name="biztalk-hybrid-connections"></a>BizTalk hibrid kapcsolatok ##

A szolgáltatás korai formáját BizTalk hibrid kapcsolatoknak hívták. Ez a képesség 2018. május 31-én ment el az End of Life-on, és beszüntette működését. A BizTalk hibrid kapcsolatok at eltávolították az összes alkalmazásból, és nem érhetők el a portálon vagy API-n keresztül. Ha ezek a régebbi kapcsolatok továbbra is konfigurálva vannak a hibrid kapcsolatkezelőben, akkor a Megszűnt állapot jelenik meg, és az élettartam vége utasítás jelenik meg az alján.

![BizTalk hibrid kapcsolatok a HCM-ben][12]


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
