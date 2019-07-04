---
title: Mi az Azure Private DNS?
description: Az üzemeltetési szolgáltatás a Microsoft Azure saját DNS áttekintése.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: aedace031eaedf2709993b5185979e8777821759
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444840"
---
# <a name="what-is-azure-private-dns"></a>Mi az Azure Private DNS?

> [!IMPORTANT]
> Privát Azure DNS jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) a szolgáltatás nevét annak IP-címét.  Az Azure DNS a DNS-tartományok, a üzemeltetési szolgáltatás biztosítani a névfeloldást a Microsoft Azure infrastruktúráját használja. Internetre irányuló DNS-tartományok támogatása esetén mellett az Azure DNS privát DNS-zónák is támogatja.

Az Azure Private DNS kezeléséhez és a egy virtuális hálózatot, adjon hozzá egy egyéni DNS-megoldás nélkül tartomány neveinek feloldásához megbízható és biztonságos DNS szolgáltatást nyújt. Privát DNS-zónák használatával még ma elérhető az Azure által biztosított nevek helyett saját egyéni tartománynevet is használhatja. Egyéni tartománynevek használata segít testre szabni a virtuális hálózati architektúra ajánlott megfeleljen a szervezet igényei szerint. Névfeloldás biztosítja a virtuális gépek (VM) egy virtuális hálózaton belül és virtuális hálózatok között. Ezenkívül konfigurálhatja zónák nevek split zónaneveket nézetet, amely lehetővé teszi a névnek a megosztását a saját és a egy nyilvános DNS-zónát.

A virtuális hálózatról egy saját DNS-zóna rekordjait háríthatja el a virtuális hálózat, a zónát kell kapcsolni. Társított virtuális hálózatok teljes hozzáféréssel rendelkezik, és megoldhatja a saját zóna közzétett összes DNS-rekord. Ezenkívül is engedélyezheti a virtuális hálózat hivatkozáshoz automatikus regisztrációnak. Ha egy virtuális hálózati csatlakozás automatikus regisztrációnak engedélyezi, a virtuális gépek a virtuális hálózat DNS-rekordjait a privát zónában vannak regisztrálva. Automatikus regisztrációnak engedélyezve van, amikor az Azure DNS-ben is frissíti a zóna rekordok, amikor egy virtuális gép létrejött, módosításokat az "IP-címet, vagy törölték.

![DNS áttekintése](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Ajánlott eljárásként, ne használjon egy *.local* tartomány és a privát DNS-zónához. Nem minden operációs rendszer támogatja ezt.

## <a name="benefits"></a>Előnyök

Privát Azure DNS a következő előnyöket nyújtja:

* **Nincs szükség az egyéni DNS-megoldások**. Korábban a számos ügyfél létrehozott egyéni DNS-megoldások a virtuális hálózat DNS-zónák kezelése. DNS-zónák a natív Azure-infrastruktúra, amely eltávolítja a létrehozása és kezelése az egyéni DNS-megoldások terhe használata kezelheti.

* **Minden gyakori DNS rekordjait típust használja**. Azure DNS supports A, AAAA, CNAME, MX, PTR, SOA, SRV, and TXT records.

* **Automatikus állomásnév-rekord-kezelésre**. Egyéni DNS-rekordjait üzemelteti, valamint az Azure automatikusan is kezeli gazdanév-rekordokat a megadott virtuális hálózatokat a virtuális gépek. Ebben a forgatókönyvben optimalizálhatja a tartománynevek egyéni DNS-megoldások létrehozása és alkalmazások módosítása nélkül használhatja.

* **Állomásnév-feloldási virtuális hálózatok közötti**. Ellentétben az Azure által biztosított állomásnevek privát DNS-zónák virtuális hálózatok között is megoszthatók. Ez a funkció egyszerűbbé teszi a kereszt-hálózat és a szolgáltatásészlelés forgatókönyvek, például a virtuális hálózatok közötti társviszony.

* **Ismerős eszközökkel és a felhasználói élmény**. Gyorsan elsajátítható csökkentése érdekében ez a szolgáltatás jól bevált Azure DNS-eszközök (az Azure portal, Azure PowerShell-lel, az Azure CLI, Azure Resource Manager-sablonok és a REST API-t) használ.

* **Split zónaneveket DNS-támogatás**. Az Azure DNS használata esetén létrehozhat zónák ugyanazzal a névvel, hogy különböző választ az egy virtuális hálózaton belül, és a nyilvános interneten keresztül. Split zónaneveket DNS jellemzően olyan helyzetben, hogy a virtuális hálózaton belüli használatra a szolgáltatás egy dedikált verziója.

* **Az összes Azure-régióban elérhető**. Az Azure DNS saját zónák funkció az Azure nyilvános felhő összes Azure-régióban érhető el.

## <a name="capabilities"></a>Funkciók

Az Azure DNS az alábbi képességeket biztosítja:

* **Automatikus regisztráció a privát zónák automatikus regisztrációnak engedélyezve van a csatolt virtuális hálózatból a virtuális gépek**. A virtuális gépek, regisztrált (hozzáadott) a privát zónához a magánhálózati IP-címek mutató rögzíti. Automatikus regisztrációnak engedélyezve van a virtuális hálózat hivatkozást a virtuális gép törlésekor az Azure DNS automatikusan eltávolítja a megfelelő DNS-rekord a társított saját zóna.

* **A privát zónához kapcsolódó virtuális hálózatok között támogatott továbbítása DNS-feloldás**. Közötti – virtuális hálózat DNS-feloldás nincs nincs explicit függőség úgy, hogy a virtuális hálózatok társviszonyban állnak egymással. Érdemes azonban, más esetekben (például HTTP-forgalom) virtuális hálózatok társviszonyba állítása.

* **A virtuális hálózat hatókörén belül támogatott névkeresési DNS**. A privát zónák rendelt virtuális hálózaton belül egy privát IP-címet a DNS-névkeresési adja vissza, amely tartalmazza a gazdagép/rekord neve és a zóna nevét a utótagként teljes Tartománynevét.

## <a name="known-issues"></a>Ismert problémák
A következő elemek ismert hibák és az előzetes kiadásban problémák:
* Ha törli a privát DNS-zónához társított virtuális hálózat, azzal nem törli a saját DNS-zóna mutató hivatkozásokat. A hivatkozás sikertelen lesz, ha hozza létre újra a virtuális hálózat azonos nevű és az erőforráscsoportot, és próbálja meg újra csatolása bármely saját DNS-zóna. A probléma megkerüléséhez, hozzon létre a virtuális hálózat egy másik erőforráscsoportban vagy egy másik nevet a ugyanabban az erőforráscsoportban.
* Virtuális hálózat egy másik erőforráscsoportba vagy előfizetésbe helyezi át, ha nem frissíti a hivatkozásokat, a saját DNS-zóna. A névfeloldás az áthelyezett virtuális hálózat továbbra is működik, azonban megjelenik a virtuális hálózat régi ARM-azonosítók megtekintheti a virtuális hálózati kapcsolatok a privát DNS-zóna.
* Jelenleg társított virtuális hálózatok, az Egyesült Arab Emírségek északi régiója, Egyesült Arab Emírségek középső, Dél-Afrika nyugati régiója, Dél-Afrika északi régiója, kelet-Kanadában, Dél-Franciaország lévő üzemeltetett sikertelen lehet, és láthatja a DNS-feloldási időszakos probléma. 


## <a name="other-considerations"></a>Egyéb szempontok

Az Azure DNS a következő korlátozások vonatkoznak:

* Egy adott virtuális hálózati kapcsolhatók csak egy privát zónához, mint ha a virtuális gép DNS-rekordok automatikus regisztráció engedélyezve van. Több virtuális hálózat azonban csatolása egyetlen DNS-zónát.
* Fordított DNS csak működik a társított virtuális hálózat privát IP-címtér
* Fordított DNS, a társított virtuális hálózati magánhálózati IP-cím a virtuális gép alapértelmezett utótagját "internal.cloudapp.net" értéket ad vissza. Virtual Network hálózatokhoz használható, amely automatikus regisztrációnak engedélyezve van a privát zónához van csatolva, fordított DNS egy magánhálózati IP-címet visszaadja 2 teljes tartományneveket, egy alapértelmezett utótag *internal.cloudapp.net* és a egy másik, a privát zónák utótagot.
* Feltételes továbbítás natív módon nem támogatott abban a pillanatban. Ahhoz, hogy a megoldás az Azure és helyszíni hálózat között. Lásd: [névfeloldás virtuális gépek és szerepkörpéldányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg a [Azure DNS díjszabási](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre a privát zónák az Azure DNS használatával [Azure PowerShell-lel](./private-dns-getstarted-powershell.md) vagy [Azure CLI-vel](./private-dns-getstarted-cli.md).

* Olvassa el néhány gyakori [privát zónák – forgatókönyvek](./private-dns-scenarios.md) , amelyek megvalósíthatók az Azure DNS saját zónáival kapcsolatos.

* A gyakori kérdések és válaszok az Azure DNS privát zónái kapcsolatban, beleértve a viselkedést is várható bizonyos típusú műveletek, lásd: [Private DNS – gyakori kérdések](./dns-faq-private.md).

* További információ a DNS-zónák és rekordok funkcionáló [DNS-zónák és rekordok áttekintése](dns-zones-records.md).

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
