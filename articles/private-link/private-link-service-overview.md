---
title: Mi az az Azure Private link Service?
description: Ismerje meg az Azure Private link Service-t.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 67bddea31a4bb375657b0df5e21fab1ab3d732f5
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265560"
---
# <a name="what-is-azure-private-link-service"></a>Mi az az Azure Private link Service?

Az Azure Private link Service a saját szolgáltatására vonatkozik, amelyet az Azure privát kapcsolata működtet. Az [Azure standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) mögött futó szolgáltatás engedélyezhető a magánjellegű kapcsolatokhoz való hozzáféréshez, így a szolgáltatás felhasználói saját virtuális hálózatok férhetnek hozzá. Ügyfelei létrehozhatnak egy privát végpontot a VNet belül, és leképezhetők a szolgáltatásba. Ez a cikk a szolgáltatói oldalával kapcsolatos fogalmakat ismerteti. 

## <a name="workflow"></a>Munkafolyamat

![Privát kapcsolati szolgáltatás munkafolyamata](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>A privát kapcsolat szolgáltatás létrehozása

- Konfigurálja úgy az alkalmazást, hogy az a virtuális hálózatban lévő standard Load Balancer mögött fusson. Ha már konfigurálta az alkalmazást egy standard Load Balancer mögött, akkor kihagyhatja ezt a lépést.   
- Hozzon létre egy privát hivatkozási szolgáltatást a fenti terheléselosztó hivatkozására. A terheléselosztó kiválasztási folyamata területen válassza ki azt a előtér-IP-konfigurációt, ahol a forgalmat el szeretné fogadni. Válasszon egy alhálózatot a privát kapcsolati szolgáltatás NAT IP-címeihez. Azt javasoljuk, hogy az alhálózaton legalább nyolc NAT IP-cím legyen elérhető. Úgy tűnik, hogy minden fogyasztói forgalom a magánhálózati IP-címek ezen készletéből származik a szolgáltatóhoz. Válassza ki a privát kapcsolati szolgáltatás megfelelő tulajdonságait/beállításait.    

    > [!NOTE]
    > Az Azure Private link Service csak standard Load Balancer esetén támogatott. 
    
### <a name="share-your-service"></a>A szolgáltatás megosztása

A privát kapcsolati szolgáltatás létrehozása után az Azure létrehoz egy "alias" nevű globálisan egyedi nevet a szolgáltatáshoz megadott név alapján. A szolgáltatás aliasát vagy erőforrás-URI-JÁT kapcsolat nélküli üzemmódban is megoszthatja ügyfeleivel. A felhasználók az alias vagy az erőforrás-URI használatával indíthatnak privát kapcsolati kapcsolatot.
 
### <a name="manage-your-connection-requests"></a>A kapcsolatkérelmek kezelése

Miután egy fogyasztó kezdeményezte a kapcsolatokat, a szolgáltató elfogadja vagy elutasítja a kapcsolatkérelem-kérést. Az összes kapcsolódási kérelem a Private link szolgáltatás **privateendpointconnections** tulajdonsága alatt jelenik meg.
 
### <a name="delete-your-service"></a>Szolgáltatás törlése

Ha a Private link Service már nem használatban van, törölheti. A szolgáltatás törlése előtt azonban győződjön meg arról, hogy nincs társítva saját végpont-kapcsolat. Elutasíthatja az összes kapcsolatot, és törölheti is a szolgáltatást.

## <a name="properties"></a>properties

A Private link Service a következő tulajdonságokat adja meg: 

|Tulajdonság |Magyarázat  |
|---------|---------|
|Kiépítési állapot (provisioningState)  |Egy csak olvasható tulajdonság, amely felsorolja a privát kapcsolati szolgáltatás aktuális kiépítési állapotát. A megfelelő kiépítési állapotok a következők: Törlése Sikertelen Sikerült A frissítése folyamatban van. Ha a kiépítési állapot "sikeres", sikeresen kiépített egy privát kapcsolati szolgáltatást.        |
|Alias (alias)     | Az alias a szolgáltatás globálisan egyedi, csak olvasható karakterlánca. Segít a szolgáltatáshoz tartozó ügyféladatok maszkolásában, és ezzel egy időben létrehoz egy könnyen megosztható nevet a szolgáltatás számára. Privát kapcsolati szolgáltatás létrehozásakor az Azure létrehozza a szolgáltatás aliasát, amelyet megoszthat ügyfeleivel. Az ügyfelek ezt az aliast használhatják a szolgáltatáshoz való kapcsolódás igényléséhez.          |
|Láthatóság (láthatóság)     | A láthatóság az a tulajdonság, amely a privát kapcsolati szolgáltatás expozíciós beállításait vezérli. A szolgáltatók dönthetnek úgy, hogy korlátozzák a szolgáltatásuk kitettségét a szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel, az előfizetések korlátozott készletével vagy az összes Azure-előfizetéssel.          |
|Automatikus jóváhagyás (automatikus jóváhagyás)    |   Az automatikus jóváhagyás vezérli a privát kapcsolati szolgáltatás automatikus elérését. Az automatikus jóváhagyási listán megadott előfizetéseket a rendszer automatikusan jóváhagyja, ha az előfizetésben lévő privát végpontokról kér le kapcsolatokat.          |
|Előtér-IP-konfiguráció Load Balancer (loadBalancerFrontendIpConfigurations)    |    A Private link Service egy standard Load Balancer előtérbeli IP-címéhez van kötve. A szolgáltatásnak szánt összes forgalom elérheti a SLB felületét. A SLB szabályokat úgy is konfigurálhatja, hogy a forgalmat a megfelelő háttér-készletekbe irányítsa, ahol az alkalmazásai futnak. A Load Balancer előtér-IP-konfigurációi eltérnek a NAT IP-konfigurációtól.      |
|NAT IP-konfiguráció (ipConfigurations)    |    Ez a tulajdonság a magánhálózati kapcsolati szolgáltatás NAT (hálózati címfordítás) IP-konfigurációjához hivatkozik. A NAT IP-címet a szolgáltató virtuális hálózatában található bármely alhálózatból lehet kiválasztani. A Private link Service a privát kapcsolat forgalmán végzi a cél oldali NAT-nek. Ez biztosítja, hogy a forrás (fogyasztói oldal) és a cél (szolgáltató) címterület között ne legyenek IP-ütközések. A cél oldalon (szolgáltató oldalon) a NAT IP-címe forrás IP-ként jelenik meg a szolgáltatás által fogadott összes csomaghoz és a cél IP-címéhez a szolgáltatás által küldött összes csomag esetében.       |
|Magánhálózati végponti kapcsolatok (privateEndpointConnections)     |  Ez a tulajdonság felsorolja a Private link Service-hez csatlakozó privát végpontokat. Több magánhálózati végpont is csatlakozhat ugyanahhoz a privát kapcsolati szolgáltatáshoz, és a szolgáltató szabályozhatja az egyes privát végpontok állapotát.        |
|||


### <a name="details"></a>Részletek

- A Private link Service az azonos régióban lévő jóváhagyott privát végpontokról érhető el. A privát végpontot elérheti ugyanabból a virtuális hálózatból, a regionálisan megtekinthető virtuális hálózatok, a globálisan felhasználható virtuális hálózatok és a privát VPN-vagy ExpressRoute-kapcsolatokat használó helyszíni környezetből. 
 
- Privát kapcsolati szolgáltatás létrehozásakor létrejön egy hálózati adapter az erőforrás életciklusához. Ez az illesztőfelület nem kezelhető az ügyfél által.
 
- A Private link Service-t a virtuális hálózattal és a standard Load Balancerával azonos régióban kell telepíteni.  
 
- Az egyetlen privát kapcsolati szolgáltatás több, különböző virtuális hálózatok, előfizetéshez és/vagy Active Directory bérlőhöz tartozó privát végpontból is elérhető. A kapcsolatok egy kapcsolatok munkafolyamatán keresztül lettek létrehozva. 
 
- Több magánhálózati kapcsolati szolgáltatás is létrehozható ugyanazon a standard Load Balancer különböző előtéri IP-konfigurációk használatával. A standard Load Balancer és előfizetések által létrehozott privát kapcsolati szolgáltatások száma korlátozott. Részletekért lásd: az [Azure korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).
 
- A Private link Service több NAT IP-konfigurációval is rendelkezhet. Ha egynél több NAT IP-konfigurációt választ ki, akkor a szolgáltatók méretezhetők. Jelenleg a szolgáltatók legfeljebb nyolc NAT IP-címet rendelhetnek hozzá privát kapcsolati szolgáltatásként. Az egyes NAT IP-címekkel több portot is hozzárendelhet a TCP-kapcsolatokhoz, így kibővítheti azokat. Miután több NAT IP-címet adott hozzá egy privát kapcsolati szolgáltatáshoz, nem törölheti a NAT IP-címeit. Ez azért szükséges, hogy az aktív kapcsolatok ne legyenek hatással a NAT IP-címeinek törlésekor.


## <a name="alias"></a>Alias

Az **alias** a szolgáltatás globálisan egyedi neve. Segít a szolgáltatáshoz tartozó ügyféladatok maszkolásában, és ezzel egy időben létrehoz egy könnyen megosztható nevet a szolgáltatás számára. Privát kapcsolati szolgáltatás létrehozásakor az Azure létrehoz egy aliast a szolgáltatáshoz, amelyet megoszthat ügyfeleivel. Az ügyfelek ezt az aliast használhatják a szolgáltatáshoz való kapcsolódás igényléséhez.

Az alias három részből áll: *Előtag*. *GUID azonosító*. *Utótag*

- Az előtag a szolgáltatás neve. Kiválaszthatja a saját előtagját. Az "alias" létrehozása után nem módosítható, ezért a megfelelő előtagot válassza ki.  
- A GUID-t a platform biztosítja. Ez segít a név globálisan egyedivé tételében. 
- Az utótagot az Azure: *region*. Azure. privatelinkservice fűzi hozzá 

Teljes alias:  *Előtag*. {GUID}. *régió*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Vezérlési szolgáltatás expozíciója

A Private link Service lehetőséget biztosít a szolgáltatás "láthatóság" beállítással való expozíciójának szabályozására. Saját maga is elvégezheti a szolgáltatást a saját maga által használt virtuális hálózatok (csak RBAC engedélyek esetén), korlátozhatja a megbízható előfizetések korlátozott készletét, vagy megteheti a nyilvánosságot, hogy minden Azure-előfizetés kérjen kapcsolatot a privát kapcsolaton keresztül. szolgáltatás. A láthatósági beállítások határozzák meg, hogy a fogyasztó tud-e csatlakozni a szolgáltatáshoz, vagy sem. 

## <a name="control-service-access"></a>Szolgáltatás-hozzáférés vezérlése

Azok a felhasználók, akik számára expozíció (láthatósági beállítás által vezérelt) a privát kapcsolati szolgáltatáshoz létrehozhatnak egy privát végpontot a virtuális hálózatok, és kapcsolatot igényelhetnek a privát kapcsolati szolgáltatással. A magánhálózati végpont kapcsolata "függőben" állapotban jön létre a Private link Service objektumon. A szolgáltató feladata a kapcsolatfelvételi kérelem elvégzése. Jóváhagyhatja a kapcsolatokat, elutasítja a kapcsolatokat, vagy törölheti a kapcsolatokat. Csak a jóváhagyott kapcsolatok küldhetnek forgalmat a privát kapcsolati szolgáltatásnak.

A kapcsolatok jóváhagyásának művelete automatizálható a privát kapcsolat szolgáltatás automatikus jóváhagyási tulajdonságának használatával. Az automatikus jóváhagyás lehetővé teszi, hogy a szolgáltatók előre jóváhagyják a szolgáltatáshoz való automatikus hozzáférést előfizetések készletét. Az ügyfeleknek meg kell osztaniuk az előfizetéseket a szolgáltatók számára az automatikus jóváhagyási listához való hozzáadáshoz. Az automatikus jóváhagyás a láthatósági tömb egy részhalmaza. A láthatóság szabályozza az expozíciós beállításokat, míg az automatikus jóváhagyás vezérli a szolgáltatás jóváhagyási beállításait. Ha egy ügyfél az automatikus jóváhagyási listán szereplő előfizetéshez kapcsolódik, a rendszer automatikusan jóváhagyja a kapcsolatokat, és a kapcsolatok létrejöttek. A szolgáltatóknak nem kell manuálisan jóváhagynia a kérést. Ha azonban egy ügyfél egy, a láthatósági tömbben lévő előfizetésből kér kapcsolatot, és nem az automatikus jóváhagyási tömbben, akkor a kérelem eléri a szolgáltatót, de a szolgáltatónak manuálisan kell jóváhagynia a kapcsolatokat.

## <a name="limitations"></a>Korlátozások

A Private link Service használatának ismert korlátai a következők:
- Csak standard Load Balancer támogatott 
- Csak az IPv4-forgalmat támogatja
- Csak az azonos régióban található privát végpontokból érhető el
- Nem támogatott a Azure Portali élmény létrehozása és kezelése
- A proxy protokollt használó ügyfelek kapcsolati adatai nem érhetők el a szolgáltató számára

## <a name="next-steps"></a>További lépések
- [Privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával](create-private-link-service-powershell.md)
- [Privát kapcsolati szolgáltatás létrehozása az Azure CLI-vel](create-private-link-service-cli.md)
