---
title: Mi az Azure Private DNS?
description: Ebben a cikkben ismerkedje meg a Microsoft Azure magánDNS-tárhelyszolgáltatásának áttekintésével.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 97b266398b3ea46d09b04524dad34922f21b1a95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939289"
---
# <a name="what-is-azure-private-dns"></a>Mi az Azure Private DNS?

A tartománynév-rendszer (DNS) felelős a szolgáltatásnév IP-címre történő fordításáért (vagy feloldásáért).  Az Azure DNS egy dns-tartományok üzemeltetési szolgáltatása, amely névfeloldást biztosít a Microsoft Azure-infrastruktúra használatával. Az internetre néző DNS-tartományok támogatása mellett az Azure DNS támogatja a privát DNS-zónákat is.

Az Azure Private DNS megbízható, biztonságos DNS-szolgáltatást biztosít a virtuális hálózat tartományneveinek kezeléséhez és feloldásához anélkül, hogy egyéni DNS-megoldást kellene hozzáadnia. A magánDNS-zónák használatával használhatja a saját egyéni tartománynevek helyett az Azure által biztosított nevek ma elérhető. Az egyéni tartománynevek használatával a szervezet igényeinek megfelelően alakíthatja a virtuális hálózati architektúrát. A virtuális hálózaton belüli és a virtuális hálózatok közötti virtuális gépek (VM-ek) névfeloldását biztosítja. Ezenkívül a zónák nevét osztott horizontú nézetben is konfigurálhatja, amely lehetővé teszi, hogy egy magán- és egy nyilvános DNS-zóna megossza a nevet.

A saját DNS-zóna rekordjainak a virtuális hálózatról történő feloldásához össze kell kapcsolnia a virtuális hálózatot a zónával. A csatolt virtuális hálózatok teljes hozzáféréssel rendelkeznek, és feloldhatják a privát zónában közzétett összes DNS-rekordot. Ezenkívül engedélyezheti az automatikus regisztrációt egy virtuális hálózati kapcsolaton is. Ha engedélyezi az automatikus regisztrációt egy virtuális hálózati kapcsolaton, a virtuális hálózat virtuális gépei DNS-rekordjai regisztrálva lesznek a privát zónában. Ha az automatikus regisztráció engedélyezve van, az Azure DNS is frissíti a zónarekordokat, amikor egy virtuális gép jön létre, módosítja annak IP-címét, vagy törlődik.

![DNS – áttekintés](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Ajánlott eljárásként ne használjon *.local* tartományt a saját DNS-zónához. Nem minden operációs rendszer támogatja ezt.

## <a name="benefits"></a>Előnyök

Az Azure Private DNS a következő előnyöket nyújtja:

* **Szükségtelennek az egyéni DNS-megoldások**. Korábban sok ügyfél egyéni DNS-megoldásokat hozott létre a DNS-zónák kezelésére a virtuális hálózatban. Most már kezelheti a DNS-zónák at a natív Azure-infrastruktúra, amely megszünteti a terhet az egyéni DNS-megoldások létrehozása és kezelése.

* **Használja az összes általános DNS-rekordtípust.** Az Azure DNS támogatja az A, AAAA, CNAME, MX, PTR, SOA, SRV és TXT rekordokat.

* **Automatikus állomásnévrekord-kezelés**. Az egyéni DNS-rekordok üzemeltetése mellett az Azure automatikusan üzemelteti a virtuális gépek állomásnévrekordjait a megadott virtuális hálózatokban. Ebben az esetben egyéni DNS-megoldások létrehozása vagy alkalmazások módosítása nélkül optimalizálhatja a használt tartományneveket.

* **Állomásnév feloldása a virtuális hálózatok között**. Az Azure által megadott állomásnevekkel ellentétben a privát DNS-zónák megoszthatók a virtuális hálózatok között. Ez a funkció leegyszerűsíti a hálózatok közötti és a szolgáltatásfelderítési forgatókönyveket, például a virtuális hálózati társviszony-létesítést.

* **Ismerős eszközök és felhasználói élmény**. A tanulási görbe csökkentése érdekében ez a szolgáltatás jól megalapozott Azure DNS-eszközöket használ (Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager-sablonok és a REST API).

* **Split-horizon DNS-támogatás**. Az Azure DNS-sel azonos nevű zónákat hozhat létre, amelyek a virtuális hálózaton belül és a nyilvános interneten belül különböző válaszokat oldanak meg. Az osztott horizontú DNS tipikus forgatókönyve, hogy egy szolgáltatás dedikált verzióját biztosítja a virtuális hálózaton belüli használatra.

* **Minden Azure-régióban elérhető.** Az Azure DNS-alapú privát zónák funkció az Azure nyilvános felhőjének minden Azure-régiójában elérhető.

## <a name="capabilities"></a>Funkciók

Az Azure DNS a következő lehetőségeket biztosítja:

* **Virtuális gépek automatikus regisztrációja olyan virtuális hálózatról, amely egy privát zónához van kapcsolva, és az automatikus regisztráció engedélyezve van.** A virtuális gépek regisztrálva vannak (hozzáadva) a privát zónába A rekordként, amely a saját IP-címükre mutat. Ha egy virtuális hálózati kapcsolatban lévő virtuális hálózati kapcsolat automatikus regisztráció engedélyezve van törlődik, az Azure DNS is automatikusan eltávolítja a megfelelő DNS-rekordot a csatolt privát zónából.

* **A továbbítási DNS-feloldás a privát zónához kapcsolódó virtuális hálózatokon is támogatott.** A virtuális hálózatok dns-feloldása esetén nincs kifejezett függőség, így a virtuális hálózatok egyenrangúak lennének egymással. Előfordulhat azonban, hogy más forgatókönyvekhez (például HTTP-forgalomhoz) is szeretné társviszonyt létesíteni a virtuális hálózatokat.

* **A névfeloldási DNS-keresése a virtuális hálózati hatókörön belül támogatott.** A privát zónához rendelt virtuális hálózaton belüli privát IP-cím fordított DNS-keresése a teljes tartománynevet adja vissza, amely tartalmazza az állomás/rekord nevét és a zóna nevét utótagként.

## <a name="other-considerations"></a>Egyéb szempontok

Az Azure DNS a következő korlátozásokkal rendelkezik:

* Egy adott virtuális hálózat csak egy privát zónához kapcsolható, ha a virtuális gép DNS-rekordjainak automatikus regisztrálása engedélyezve van. Több virtuális hálózatot azonban összekapcsolhat egyetlen DNS-zónával.
* A DNS-sztornírozás csak a csatolt virtuális hálózat privát IP-területénműködik
* A csatolt virtuális hálózat magánhálózati IP-címének dns-címe *internal.cloudapp.net* a virtuális gép alapértelmezett utótagjaként. Az automatikus regisztrációval rendelkező privát zónához kapcsolódó virtuális hálózatok esetében a privát IP-cím fordított DNS-e két teljes tartománynt ad vissza: az egyiken az alapértelmezett *internal.cloudapp.net utótag,* a másik pedig a privát zóna utótaggal rendelkezik.
* A feltételes továbbítás jelenleg nem natív módon támogatott. Az Azure és a helyszíni hálózatok közötti megoldás engedélyezése. Lásd: [Névfeloldás virtuális gépekhez és szerepkörpéldányokhoz](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Díjszabás

A díjszabásról az [Azure DNS-díjszabás című](https://azure.microsoft.com/pricing/details/dns/)témakörben talál.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre privát zónát az Azure DNS-ben az [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* Az Azure DNS-ben a privát zónákkal megvalósítható gyakori [privát zónaforgatókönyvekről](./private-dns-scenarios.md) olvashat.

* Az Azure DNS-ben a privát zónákkal kapcsolatos gyakori kérdésekről és válaszokról, beleértve az adott típusú műveleteknél elvárható viselkedést, olvassa el a [Privát DNS gyakori kérdések című témakört.](./dns-faq-private.md)

* A DNS-zónákról és -rekordokról a [DNS-zónák és rekordok áttekintése című témakörben olvashat.](dns-zones-records.md)

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
