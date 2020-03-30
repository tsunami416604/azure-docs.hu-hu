---
title: Mi az Azure Private Link szolgáltatás?
description: További információ az Azure Private Link szolgáltatásról.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280429"
---
# <a name="what-is-azure-private-link-service"></a>Mi az Azure Private Link szolgáltatás?

Az Azure Private Link szolgáltatás a saját szolgáltatására mutató hivatkozás, amelyet az Azure Private Link működtet. Az Azure Standard [Load Balancer](../load-balancer/load-balancer-standard-overview.md) mögött futó szolgáltatás engedélyezhető a privát kapcsolat eléréséhez, hogy a szolgáltatás hozadékai a saját virtuális hálózatukból is hozzáférhetnek. Az ügyfelek létrehozhatnak egy privát végpontot a virtuális hálózatukon belül, és leképezhetik a szolgáltatáshoz. Ez a cikk a szolgáltatói oldallal kapcsolatos fogalmakat ismerteti. 

## <a name="workflow"></a>Munkafolyamat

![Privát hivatkozás szolgáltatás munkafolyamata](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>A privát kapcsolatszolgáltatás létrehozása

- Konfigurálja úgy az alkalmazást, hogy a virtuális hálózat szabványos terheléselosztója mögött fusson. Ha már van konfigurálva az alkalmazás egy szabványos terheléselosztó mögött, kihagyhatja ezt a lépést.   
- Hozzon létre egy privát kapcsolat szolgáltatás hivatkozva a terheléselosztó felett. A terheléselosztó kiválasztási folyamatában válassza ki azt az előtér-IP-konfigurációt, amelyen a forgalmat fogadni szeretné. Válasszon alhálózatot a Privát kapcsolat szolgáltatás NAT IP-címeihez. Ajánlott, hogy legalább nyolc NAT IP-cím legyen elérhető az alhálózatban. Úgy tűnik, hogy minden fogyasztói forgalom a privát IP-címek ezen készletéből származik a szolgáltatóhoz. Adja meg a privát kapcsolat szolgáltatás megfelelő tulajdonságait/beállításait.    

    > [!NOTE]
    > Az Azure Private Link szolgáltatás csak standard terheléselosztó esetén támogatott. 
    
### <a name="share-your-service"></a>A szolgáltatás megosztása

Miután létrehozott egy Privát kapcsolat szolgáltatást, az Azure létrehoz egy globálisan egyedi nevű becenevet, amelyet "alias" néven hoz létre a szolgáltatáshoz megadott név alapján. Megoszthatja a szolgáltatás alias- vagy erőforrásURI-ját az ügyfelekkel kapcsolat nélkül. A fogyasztók elindíthatják a privát kapcsolat kapcsolatot az alias vagy az erőforrás URI használatával.
 
### <a name="manage-your-connection-requests"></a>A csatlakozási kérelmek kezelése

Miután a fogyasztó kezdeményezi a kapcsolatot, a szolgáltató elfogadhatja vagy elutasíthatja a csatlakozási kérelmet. Minden kapcsolatkérés a Private Link szolgáltatás **privateendpointconnections** tulajdonsága alatt jelenik meg.
 
### <a name="delete-your-service"></a>A szolgáltatás törlése

Ha a Privát kapcsolat szolgáltatás már nincs használatban, törölheti azt. Azonban a szolgáltatás törlése előtt győződjön meg arról, hogy nincsenek privát végpontkapcsolatok társítva. Elutasíthatja az összes kapcsolatot, és törölheti a szolgáltatást.

## <a name="properties"></a>Tulajdonságok

A Private Link szolgáltatás a következő tulajdonságokat határozza meg: 

|Tulajdonság |Magyarázat  |
|---------|---------|
|Kiépítési állam (provisioningState)  |Írásvédett tulajdonság, amely a Private Link szolgáltatás aktuális kiépítési állapotát sorolja fel. Az alkalmazandó kiépítési állapotok a következők: "Törlés; Nem sikerült; Sikerült; Frissítés". Ha a létesítési állapot "Sikeres", sikeresen kiépítette a Private Link szolgáltatást.        |
|Alias (alias)     | Az alias a szolgáltatás globálisan egyedi írásvédett karakterlánca. Ez segít elfedni az ügyféladatokat a szolgáltatás, és ezzel egyidejűleg létrehoz egy könnyen megosztható nevet a szolgáltatás. Amikor létrehoz egy Privát kapcsolat szolgáltatást, az Azure létrehozza a szolgáltatás aliasát, amelyet megoszthat az ügyfelekkel. Az ügyfelek ezzel az aliassal kérhetnek kapcsolatot a szolgáltatással.          |
|Láthatóság (láthatóság)     | A láthatóság az a tulajdonság, amely a Private Link szolgáltatás expozíciós beállításait szabályozza. A szolgáltatók dönthetnek úgy, hogy a szolgáltatásuknak való kitettséget szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel, korlátozott előfizetésekkel vagy az összes Azure-előfizetéssel rendelkező előfizetésre korlátozzák.          |
|Automatikus jóváhagyás (automatikus jóváhagyás)    |   Az automatikus jóváhagyás vezérli a Privát kapcsolat szolgáltatás automatikus elérését. Az automatikus jóváhagyási listában megadott előfizetések automatikusan jóváhagyásra kerülnek, ha az előfizetések privát végpontjaitól kérnek kapcsolatot.          |
|Terheléselosztó előtér-IP-konfigurációja (loadBalancerFrontendIpConfigurations)    |    A Private Link szolgáltatás egy standard terheléselosztó előtér-IP-címéhez van kötve. A szolgáltatásra szánt összes forgalom eléri az SLB előt. SLB-szabályok konfigurálásával irányíthatja ezt a forgalmat a megfelelő háttérkészletek, ahol az alkalmazások futnak. A terheléselosztó előtér-IP-konfigurációi eltérnek a NAT IP-konfigurációktól.      |
|NAT IP-konfiguráció (ipConfigurations)    |    Ez a tulajdonság a Privát kapcsolat szolgáltatás NAT (Hálózati címfordítás) IP-konfigurációjára hivatkozik. A NAT IP-cím a szolgáltató virtuális hálózatának bármely alhálózatából választható ki. A Private Link szolgáltatás a privát kapcsolat forgalmán végzi a céloldali NAT-elt. Ez biztosítja, hogy ne legyen IP-ütközés a forrás (fogyasztói oldal) és a célhely (szolgáltató) címtér között. A céloldalon (szolgáltatói oldalon) a NAT IP-címe forrás IP-címként jelenik meg a szolgáltatás és a cél IP által a szolgáltatás által küldött összes csomag hoz a forrás IP-címén.       |
|Privát végpontkapcsolatok (privateEndpointConnections)     |  Ez a tulajdonság a Private Link szolgáltatáshoz csatlakozó magánvégpontokat sorolja fel. Több privát végpont is csatlakozhat ugyanahhoz a privát kapcsolat szolgáltatáshoz, és a szolgáltató szabályozhatja az egyes magánvégpontok állapotát.        |
|TCP proxy V2 (EnableProxyProtocol)     |  Ez a tulajdonság lehetővé teszi, hogy a szolgáltató tcp proxy v2 használatával kérje le a szolgáltatásfogyasztó kapcsolati információit. A Szolgáltató felelős a fogadókonfigurációk beállításáért, hogy a proxyprotokoll v2 fejlécét elemezhesse.        |
|||


### <a name="details"></a>Részletek

- A Private Link szolgáltatás ugyanabban a régióban jóváhagyott privát végpontokból érhető el. A privát végpont érhető el ugyanavirtuális hálózat, regionálistársviszony-létesítési virtuális hálózatok, globálisan társviszonyba vnets és a helyszínen, privát VPN- vagy ExpressRoute-kapcsolatok használatával. 
 
- Privát kapcsolatszolgáltatás létrehozásakor egy hálózati illesztő jön létre az erőforrás életciklusához. Ezt a felületet az ügyfél nem tudja kezelni.
 
- A Private Link szolgáltatást ugyanabban a régióban kell telepíteni, mint a virtuális hálózatot és a standard terheléselosztót.  
 
- Egyetlen privát kapcsolat szolgáltatás érhető el több privát végpontok tartozó különböző virtuális hálózatok, előfizetések és/vagy Active Directory-bérlők. A kapcsolat kapcsolati munkafolyamaton keresztül jön létre. 
 
- Több Private Link szolgáltatás hozható létre ugyanazon a standard terheléselosztón különböző előtér-IP-konfigurációk használatával. A Standard Load Balancer és előfizetésenként létrehozható Private Link-szolgáltatások száma korlátozott. További információt az [Azure-korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)ban talál.
 
- A Private Link szolgáltatáshoz egynél több NAT IP-konfiguráció is kapcsolódhat. Egynél több NAT IP-konfiguráció kiválasztásával a szolgáltatók skálázhatnak. Ma a szolgáltatók privát kapcsolatszolgáltatásonként legfeljebb nyolc NAT IP-címet rendelhetnek hozzá. Minden NAT IP-címhez további portokat rendelhet a TCP-kapcsolatokhoz, és így horizontálisan kioszthatja. Miután több NAT IP-címet adott hozzá egy privát kapcsolatszolgáltatáshoz, nem törölheti a NAT IP-címeket. Ez annak biztosítására történik, hogy az aktív kapcsolatokat ne befolyásolja a NAT IP-címek törlése.


## <a name="alias"></a>Alias

**Az alias** a szolgáltatás globálisan egyedi neve. Ez segít elfedni az ügyféladatokat a szolgáltatás, és ezzel egyidejűleg létrehoz egy könnyen megosztható nevet a szolgáltatás. Amikor létrehoz egy Privát kapcsolat szolgáltatást, az Azure létrehoz egy aliast a szolgáltatáshoz, amelyet megoszthat az ügyfelekkel. Az ügyfelek ezzel az aliassal kérhetnek kapcsolatot a szolgáltatással.

Az alias három részből áll: *Előtag*. *GUID*. *Utótag*

- Az előtag a szolgáltatás neve. Választhatsz saját előtagot. Az "Alias" létrehozása után nem módosíthatja, ezért válassza ki megfelelően az előtagot.  
- GUID biztosítja a platform. Ez segít abban, hogy a név globálisan egyedi legyen. 
- Utótagot az Azure fűz: *region*.azure.privatelinkservice 

Teljes alias: *Előtag*. {GUID}. *régió*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>A szolgáltatás expozíciójának szabályozása

A Private Link szolgáltatás lehetőséget biztosít a szolgáltatás expozíciójának szabályozására a "Láthatóság" beállítással. A szolgáltatást privátba teheti a saját különböző virtuális hálózataiból származó felhasználás (csak RBAC-engedélyek esetén), korlátozhatja a megbízható előfizetések korlátozott készletének kitettségét, vagy nyilvánossá teheti, hogy minden Azure-előfizetés kérhet kapcsolatot a Privát kapcsolaton Szolgáltatás. A láthatósági beállítások döntik el, hogy a fogyasztó csatlakozhat-e a szolgáltatáshoz. 

## <a name="control-service-access"></a>Szolgáltatás-hozzáférés szabályozása

A Privát kapcsolat szolgáltatásnak (láthatósági beállítás sal) rendelkező fogyasztók létrehozhatnak egy privát végpontot a virtuális hálózataikban, és kérhetnek kapcsolatot a Privát kapcsolat szolgáltatással. A privát végpontkapcsolat "Függőben" állapotban jön létre a Private Link szolgáltatásobjektumon. A szolgáltató felelős a csatlakozási kérelem teljesítéséért. Jóváhagyhatja a kapcsolatot, elutasíthatja a kapcsolatot, vagy törölheti a kapcsolatot. Csak a jóváhagyott kapcsolatok küldhetnek forgalmat a Privát kapcsolat szolgáltatásra.

A kapcsolatok jóváhagyásának művelete automatizálható a Private Link szolgáltatás automatikus jóváhagyási tulajdonságának használatával. Az automatikus jóváhagyás a szolgáltatók számára lehetővé teszi a szolgáltatásukhoz való automatikus hozzáféréshez szükséges előfizetések előre jóváhagyását. Az ügyfeleknek offline módban kell megosztaniuk előfizetéseiket ahhoz, hogy a szolgáltatók felvethessék őket az automatikus jóváhagyási listára. Az automatikus jóváhagyás a láthatósági tömb egy részhalmaza. A láthatóság vezérli az expozíciós beállításokat, míg az automatikus jóváhagyás a szolgáltatás jóváhagyási beállításait. Ha egy ügyfél az automatikus jóváhagyási listában szereplő előfizetéstől kér kapcsolatot, a kapcsolat automatikusan jóváhagyásra kerül, és létrejön a kapcsolat. A szolgáltatóknak már nem kell manuálisan jóváhagyniuk a kérelmet. Ha viszont egy ügyfél a láthatósági tömbben lévő előfizetéstől kér kapcsolatot, és nem az automatikus jóváhagyási tömbben, a kérés a szolgáltatóhoz fog érkezni, de a szolgáltatónak manuálisan jóvá kell hagynia a kapcsolatokat.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Kapcsolatinformáció beszerzése a TCP-proxy 2-es számával

Privát kapcsolatszolgáltatás használata esetén a magánvégpontról érkező csomagok forrás IP-címe a szolgáltató oldalán a szolgáltató virtuális hálózatából lefoglalt NAT IP-cím használatával lefordított hálózati cím (NAT). Ezért az alkalmazások a lefoglalt NAT IP-címet kapják a szolgáltatás felhasználóinak tényleges forrás IP-címe helyett. Ha az alkalmazásnak tényleges forrás IP-címre van szüksége a fogyasztói oldalról, engedélyezheti a proxyprotokollt a szolgáltatáson, és lekérheti az adatokat a proxyprotokoll fejlécéből. A forrás IP-cím mellett a proxyprotokoll fejléce a privát végpont LinkID azonosítóját is hordozza. A forrás IP-cím és a LinkID kombinációja segíthet a szolgáltatóknak abban, hogy egyedileg azonosítsák fogyasztóikat. További információ a Proxy Protokoll, látogasson el [ide](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Ez az információ egy egyéni Típushossz-érték (TLV) vektorral van kódolva az alábbiak szerint:

Egyéni TLV részletek:

|Mező |Hossz (oktettek)  |Leírás  |
|---------|---------|----------|
|Típus  |1        |PP2_TYPE_AZURE (0xEE)|
|Hossz  |2      |Az érték hossza|
|Érték  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |A privát végpont LINKID azonosítóját képviselő UINT32 (4 bájt). Kis endian formátumban kódolva.|

 > [!NOTE]
 > A szolgáltató felelős annak biztosításáért, hogy a szabványos terheléselosztó mögötti szolgáltatás úgy van beállítva, hogy a proxyprotokoll fejlécét a [specifikációnak](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) megfelelően elemezje, ha a proxyprotokoll engedélyezve van a magánkapcsolati szolgáltatásban. A kérés sikertelen lesz, ha a proxyprotokoll-beállítás engedélyezve van a magánhálózati szolgáltatásban, de a szolgáltató szolgáltatása nincs beállítva a fejléc elemzéséhez. Hasonlóképpen a kérés sikertelen lesz, ha a szolgáltató szolgáltatása proxyprotokoll-fejlécet vár, miközben a beállítás nincs engedélyezve a privát kapcsolatszolgáltatásban. Ha a proxyprotokoll-beállítás engedélyezve van, a proxyprotokoll-fejléc is szerepelni fog a HTTP/TCP állapotmintákban az állomástól a háttérvirtuális gépekig, még akkor is, ha a fejlécben nem lesznek ügyféladatok. 

## <a name="limitations"></a>Korlátozások

A Private Link szolgáltatás használata során az alábbi ismert korlátozások találhatók:
- Csak standard terheléselosztóesetén támogatott 
- Csak az IPv4-forgalmat támogatja
- Csak a TCP-forgalmat támogatja

## <a name="next-steps"></a>További lépések
- [Privát kapcsolatszolgáltatás létrehozása az Azure PowerShell használatával](create-private-link-service-powershell.md)
- [Privát kapcsolatszolgáltatás létrehozása az Azure CLI használatával](create-private-link-service-cli.md)
