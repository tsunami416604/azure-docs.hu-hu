---
title: Hibrid kapcsolatok
description: Megtudhatja, hogyan hozhat létre és használhat hibrid kapcsolatokat Azure App Service a különböző hálózatokban lévő erőforrások eléréséhez.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80047777"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Hibrid kapcsolatok

Hibrid kapcsolatok a szolgáltatás az Azure-ban és egy Azure App Service szolgáltatásban is. Szolgáltatásként a App Service-ban használt alkalmazások és képességek túlmutatnak. Ha többet szeretne megtudni a Hibrid kapcsolatokről és azok használatáról App Servicen kívül, tekintse meg a [Azure Relay hibrid kapcsolatok][HCService]című témakört.

A App Serviceon belül Hibrid kapcsolatok más hálózatokban lévő alkalmazás-erőforrások elérésére is használható. Hozzáférést biztosít az alkalmazástól az alkalmazások végpontja számára. Ez nem teszi lehetővé az alkalmazáshoz való hozzáférés alternatív lehetőségét. A App Serviceban használt módon minden hibrid csatlakozás egyetlen TCP-gazdagéphez és porthoz kapcsolódik. Ez azt jelenti, hogy a hibrid kapcsolatok végpontja bármely operációs rendszeren és alkalmazáson is lehet, ha a TCP-figyelési porthoz fér hozzá. A Hibrid kapcsolatok funkció nem tudja, vagy nem érdekli az alkalmazás protokollja, vagy hogy mi a hozzáférése. Egyszerűen biztosít hálózati hozzáférést.  


## <a name="how-it-works"></a>Működés ##
A Hibrid kapcsolatok szolgáltatás két kimenő hívást tartalmaz Azure Service Bus Relay-re. Van egy olyan könyvtár a gazdagépen, amelyen az alkalmazás fut App Serviceban. A hibridkapcsolat-kezelő (HCM) is kapcsolódik a Service Bus Relayhoz. A HCM egy továbbító szolgáltatás, amelyet az elérni kívánt erőforrást üzemeltető hálózaton belül telepíthet. 

A két csatlakoztatott kapcsolaton keresztül az alkalmazásnak van egy TCP-alagútja egy rögzített gazdagéphez: Port kombináció a HCM másik oldalán. A kapcsolat TLS 1,2 biztonsági és közös hozzáférési aláírási (SAS-) kulcsokat használ a hitelesítéshez és az engedélyezéshez.    

![A hibrid kapcsolatok magas szintű folyamatának ábrája][1]

Ha az alkalmazás olyan DNS-kérelmet tesz elérhetővé, amely megfelel egy konfigurált hibrid kapcsolódási végpontnak, a kimenő TCP-forgalom a hibrid kapcsolatban lesz átirányítva.  

> [!NOTE]
> Ez azt jelenti, hogy a hibrid kapcsolatok mindig DNS-nevet kell használniuk. Bizonyos ügyfélszoftverek nem végeznek DNS-keresést, ha a végpont IP-címet használ. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service hibrid kapcsolatok előnyei ###

A Hibrid kapcsolatok képesség számos előnnyel jár, többek között:

- Az alkalmazások biztonságosan érhetik el a helyszíni rendszereket és szolgáltatásokat.
- A szolgáltatáshoz nem szükséges internetről elérhető végpont.
- Gyorsan és egyszerűen beállítható. 
- Minden hibrid csatlakozás egyetlen gazdagéphez illeszkedik: a port kombinációja, amely hasznos a biztonság érdekében.
- Általában nincs szükség tűzfal-lyukakra. A kapcsolatok mindegyike kimenő a szabványos webes portoknál.
- Mivel a szolgáltatás hálózati szintű, az alkalmazás által használt nyelv és a végpont által használt technológia egyaránt független.
- A szolgáltatás használatával több hálózatban is biztosítható a hozzáférés egyetlen alkalmazásból. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>A Hibrid kapcsolatokkal nem rendelkező dolgok ###

Hibrid kapcsolatok többek között az alábbiakat teheti meg:

- Meghajtó csatlakoztatása.
- UDP használata.
- A dinamikus portokat használó TCP-alapú szolgáltatások, például az FTP-passzív mód vagy a kiterjesztett passzív üzemmód elérése.
- Támogassa az LDAP-t, mert az UDP protokollt igényelhet.
- A Active Directory támogatása, mert nem lehet tartományhoz csatlakozni App Service-feldolgozóhoz.

### <a name="prerequisites"></a>Előfeltételek ###
 - A Windows app Service-t kötelező megadni. Csak Windows rendszerben érhető el.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Hibrid kapcsolatok hozzáadása és létrehozása az alkalmazásban ##

Hibrid kapcsolatok létrehozásához nyissa meg a [Azure Portal][portal] , és válassza ki az alkalmazást. Válassza a **hálózat** > **beállítása a hibrid kapcsolati végpontok**lehetőséget. Itt láthatja az alkalmazáshoz konfigurált Hibrid kapcsolatok.  

![Képernyőkép a hibrid kapcsolatok listájáról][2]

Új hibrid kapcsolatok hozzáadásához válassza a **[+] hibrid kapcsolódás hozzáadása**elemet.  Ekkor megjelenik a már létrehozott Hibrid kapcsolatok listája. Ha hozzá szeretne adni egy vagy több alkalmazást az alkalmazáshoz, válassza ki a kívánt beállításokat, majd válassza a **kiválasztott hibrid kapcsolatok hozzáadása**lehetőséget.  

![Képernyőkép a hibrid kapcsolatok portálján][3]

Ha új hibrid kapcsolatokat szeretne létrehozni, válassza az **új hibrid kapcsolatok létrehozása**lehetőséget. Határozza meg a következőket: 

- Hibrid kapcsolatok neve.
- Végponti állomásnév.
- Végpont portja.
- Service Bus a használni kívánt névteret.

![Az új hibrid kapcsolatok létrehozása párbeszédpanel képernyőképe][4]

Minden hibrid kapcsolódás egy Service Bus névtérhez van kötve, és minden Service Bus névtér egy Azure-régióban található. Fontos, hogy megpróbáljon egy Service Bus névteret használni az alkalmazással megegyező régióban, hogy elkerülje a hálózat által okozott késést.

Ha el szeretné távolítani a hibrid kapcsolatot az alkalmazásból, kattintson rá a jobb gombbal, és válassza a **Leválasztás**lehetőséget.  

Ha hibrid kapcsolat van hozzáadva az alkalmazáshoz, egyszerűen kiválaszthatja a részleteket. 

![Képernyőkép a hibrid kapcsolatok részleteiről][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Hibrid kapcsolatok létrehozása a Azure Relay portálon ###

Az alkalmazáson belüli portálon kívül Hibrid kapcsolatok is létrehozhat a Azure Relay portálon. A App Service által használandó hibrid kapcsolatok esetében a következőket kell tennie:

* Ügyfél-hitelesítés megkövetelése.
* Rendelkeznie kell egy végpont nevű metaadat-elemmel, amely a gazdagép: Port kombinációt tartalmazza értékként.

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és App Service csomagok ##

A App Service Hibrid kapcsolatok csak az alapszintű, a standard, a prémium és az elkülönített díjszabási SKU-ban érhető el. A díjszabási csomaghoz kötött korlátok vannak kötve.  

| Díjszabási csomag | A tervben használható Hibrid kapcsolatok száma |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Prémium | 200 |
| Elkülönített | 200 |

A App Service terv felhasználói felületén megtekintheti, hogy hány Hibrid kapcsolatok használatban van, és milyen alkalmazásokkal.  

![Képernyőkép a App Service terv tulajdonságairól][6]

A részletek megtekintéséhez válassza a hibrid kapcsolat lehetőséget. Láthatja az összes információt, amelyet az alkalmazás nézetében látott. Azt is megtudhatja, hogy a csomagban hány más alkalmazás használja a hibrid kapcsolatokat.

A App Service-csomagban használható hibrid kapcsolatok végpontok száma korlátozott. Az egyes használt hibrid kapcsolatok azonban tetszőleges számú alkalmazásban használhatók a csomagban. Például egy App Service-csomagban lévő öt különálló alkalmazásban használt egyetlen hibrid kapcsolatok egy hibrid kapcsolatokként számítanak.

### <a name="pricing"></a>Díjszabás ###

Amellett, hogy App Service terv SKU-követelménye van, a Hibrid kapcsolatok használatának további díja is van. A hibrid kapcsolatok által használt minden figyelőért díjat számítunk fel. A figyelő a hibridkapcsolat-kezelő. Ha öt Hibrid kapcsolatok támogatta két hibrid Csatlakozáskezelő, ez 10 figyelő lenne. További információ: [Service Bus díjszabása][sbpricing].

## <a name="hybrid-connection-manager"></a>hibridkapcsolat-kezelő ##

A Hibrid kapcsolatok szolgáltatáshoz a hibrid kapcsolati végpontot üzemeltető továbbító ügynökre van szükség a hálózaton. A továbbító ügynök neve hibridkapcsolat-kezelő (HCM). Az HCM letöltéséhez az alkalmazásból a [Azure Portalban][portal]válassza a **hálózat** > **beállítása a hibrid kapcsolati végpontok**lehetőséget.  

Ez az eszköz a Windows Server 2012-es és újabb verzióin fut. A HCM szolgáltatásként fut, és az 443-as porton csatlakozik a kimenő Azure Relayhoz.  

A HCM telepítése után a HybridConnectionManagerUi. exe futtatásával használhatja az eszköz felhasználói felületét. Ez a fájl a hibridkapcsolat-kezelő telepítési könyvtárában található. A Windows 10-es verzióban egyszerűen kereshet *hibridkapcsolat-kezelő felhasználói felületet* is a keresőmezőbe.  

![Képernyőkép a hibridkapcsolat-kezelőról][7]

Amikor elindítja a HCM felhasználói felületét, az első dolog, amit látni fog egy tábla, amely felsorolja az összes olyan Hibrid kapcsolatok, amely a HCM ezen példányával van konfigurálva. Ha módosításokat szeretne végezni, először hitelesítenie kell magát az Azure-ban. 

Egy vagy több Hibrid kapcsolatok hozzáadása a HCM-hez:

1. Indítsa el a HCM felhasználói felületét.
2. Válassza a **másik hibrid kapcsolatok konfigurálása**lehetőséget.
![Képernyőfelvétel az új Hibrid kapcsolatok konfigurálásáról][8]

1. Jelentkezzen be az Azure-fiókjával, hogy a Hibrid kapcsolatok elérhető legyen az előfizetésében. A HCM nem folytatja az Azure-fiók használatát ezen túlmenően. 
1. Válasszon előfizetést.
1. Válassza ki azt a Hibrid kapcsolatok, amelyre a HCM-t továbbítani kívánja.
![Képernyőkép a Hibrid kapcsolatokról][9]

1. Kattintson a **Mentés** gombra.

Ekkor megjelenik a hozzáadott Hibrid kapcsolatok. A részletek megtekintéséhez a konfigurált hibrid kapcsolat lehetőséget is választhatja.

![Képernyőkép a hibrid kapcsolat részleteiről][10]

A-ben konfigurált Hibrid kapcsolatok támogatásához HCM szükséges:

- TCP-hozzáférés az Azure-hoz a 443-as porton keresztül.
- TCP-hozzáférés a hibrid kapcsolati végponthoz.
- DNS-keresések lehetősége a végponti gazdagépen és a Service Bus névtérben.

> [!NOTE]
> Azure Relay a kapcsolódáshoz használt webes szoftvercsatornán alapul. Ez a funkció csak a Windows Server 2012-es vagy újabb verzióiban érhető el. Emiatt az HCM nem támogatott a Windows Server 2012-nél korábbi verziókon.
>

### <a name="redundancy"></a>Redundancia ###

Mindegyik HCM több Hibrid kapcsolatokt is támogat. Az adott hibrid kapcsolatokat is több HCMs is támogathatja. Az alapértelmezett viselkedés az, hogy átirányítsa a forgalmat az adott végpont konfigurált HCMs. Ha magas rendelkezésre állást szeretne a Hibrid kapcsolatok a hálózatról, több HCMs is futtathat külön gépeken. A továbbítási szolgáltatás által a HCMs való adatforgalom elosztásához használt terheléselosztási algoritmus véletlenszerű hozzárendelés. 

### <a name="manually-add-a-hybrid-connection"></a>Hibrid kapcsolatok manuális hozzáadása ###

Ha engedélyezni szeretné, hogy az előfizetésen kívüli személy egy HCM-példányt működtessen egy adott hibrid kapcsolathoz, ossza meg a hibrid kapcsolathoz tartozó átjáró kapcsolati karakterláncát. Az átjáró-kapcsolatok karakterláncát a [Azure Portal][portal]hibrid kapcsolatok tulajdonságainál tekintheti meg. A karakterlánc használatához válassza a **manuális bevitel** a HCM-ben lehetőséget, majd illessze be az átjáró kapcsolódási karakterláncát.

![Hibrid kapcsolatok manuális hozzáadása][11]

### <a name="upgrade"></a>Frissítés ###

Az hibridkapcsolat-kezelő rendszeres időközönként frissülnek a problémák megoldásához vagy a javításokhoz. Amikor megjelent a frissítések, megjelenik egy előugró ablak a HCM felhasználói felületen. A frissítés alkalmazása alkalmazza a módosításokat, majd újraindítja a HCM-t. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Hibrid kapcsolatok hozzáadása az alkalmazáshoz programozott módon ##

Az alábbi API-k közvetlenül használhatók az alkalmazásokhoz kapcsolódó Hibrid kapcsolatok kezelésére. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

A hibrid kapcsolatok társított JSON-objektuma a következőképpen néz ki:

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

Az adatok használatának egyik módja a armclient, amelyet a [armclient][armclient] GitHub-projektből szerezhet be. Íme egy példa arra, hogy az alkalmazáshoz egy már meglévő hibrid csatlakozást csatolunk. Hozzon létre egy JSON-fájlt a fenti sémában, például:

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

Az API használatához szüksége lesz a küldési kulcs és a továbbító erőforrás-AZONOSÍTÓra. Ha mentette az adatait a hctest. JSON fájlnévvel, a parancs kiadásával csatlakoztassa a hibrid kapcsolatokat az alkalmazáshoz: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>A Hibrid kapcsolatok biztonságossá tétele ##

Meglévő hibrid kapcsolatok más App Service Web Appshoz is hozzáadhatók olyan felhasználók számára, akik megfelelő engedélyekkel rendelkeznek az alapul szolgáló Azure Service Bus-továbbítóhoz. Ez azt jelenti, hogy ha meg kell akadályoznia, hogy mások újra használják ugyanezt a hibrid kapcsolatot (például ha a célként megadott erőforrás olyan szolgáltatás, amely nem rendelkezik a jogosulatlan hozzáférés megakadályozása érdekében szükséges további biztonsági intézkedésekkel), akkor le kell zárnia az Azure Service Bus-továbbítóhoz való hozzáférést.

Bárki, `Reader` aki hozzáféréssel rendelkezik a továbbítóhoz, _megtekintheti_ a hibrid kapcsolatot, amikor megpróbál hozzáadni a webalkalmazáshoz az Azure Portalon, de nem fogja tudni _felvenni_ , mert nincs engedélye a továbbítási kapcsolat létrehozásához használt kapcsolati karakterlánc lekérésére. A hibrid kapcsolatok sikeres hozzáadásához `listKeys` engedélyekkel kell rendelkezniük (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`). A `Contributor` továbbítón a szerepkör vagy bármely más szerepkör, amely tartalmazza ezt az engedélyt, lehetővé teszi a felhasználók számára, hogy a hibrid kapcsolatokat használják, és hozzáadja a saját web Appshoz.

## <a name="troubleshooting"></a>Hibaelhárítás ##

A "Connected" állapot azt jelenti, hogy legalább egy HCM a hibrid kapcsolattal van konfigurálva, és képes elérni az Azure-t. Ha a hibrid kapcsolat állapota nem **kapcsolódik a hálózathoz**, a hibrid kapcsolat nincs KONFIGURÁLVA olyan HCM-re, amely hozzáfér az Azure-hoz.

Annak az elsődleges oka, hogy az ügyfelek nem tudnak csatlakozni a végponthoz, mert a végpontot IP-cím használatával adták meg a DNS-név helyett. Ha az alkalmazás nem tudja elérni a kívánt végpontot, és IP-címet használt, váltson olyan DNS-névre, amely érvényes azon a gazdagépen, amelyen a HCM fut. Győződjön meg arról is, hogy a DNS-név megfelelően van feloldva azon a gazdagépen, amelyen a HCM fut. Ellenőrizze, hogy van-e kapcsolat azon a gazdagépen, amelyen az HCM fut a hibrid kapcsolat végpontján.  

App Service a **tcpping** parancssori eszközt a speciális eszközök (kudu) konzolról lehet meghívni. Ez az eszköz tudja megállapítani, hogy van-e hozzáférése egy TCP-végponthoz, de nem mondja el, hogy van-e hozzáférése hibrid kapcsolati végponthoz. Ha az eszközt a-konzolon egy hibrid kapcsolódási végponton használja, csak egy gazdagép: Port kombinációt használ.  

Ha rendelkezik parancssori ügyféllel a végponthoz, tesztelheti a kapcsolatot az App Console használatával. A webkiszolgáló-végpontokhoz való hozzáférést például a curl használatával ellenőrizheti.

## <a name="biztalk-hybrid-connections"></a>BizTalk Hibrid kapcsolatok ##

A szolgáltatás korai formáját BizTalk Hibrid kapcsolatok hívták. Ez a képesség a 2018-es és a megszüntetett műveleteknek az élettartam vége. A BizTalk Hybrid Connections szolgáltatás minden alkalmazásból el lett távolítva, és nem érhető el a portálon vagy az API-n keresztül. Ha továbbra is ezek a régebbi kapcsolatok vannak konfigurálva a hibridkapcsolat-kezelőban, akkor a megszűnt állapot jelenik meg, és az alján megjelenik a Life (Befejezés) utasítás.

![BizTalk Hibrid kapcsolatok a HCM-ben][12]


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
