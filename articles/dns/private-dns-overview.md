---
title: Titkos tartományok Azure DNS használatához |} Microsoft Docs
description: A Microsoft Azure-szolgáltatást futtató titkos DNS áttekintése.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 0ee3b18b7f874c4f6b7b2c9c559aa7e393ad7d8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700604"
---
# <a name="use-azure-dns-for-private-domains"></a>A titkos tartományok Azure DNS használata
A tartománynévrendszer, vagy a DNS-, felelős fordítása (vagy feloldása) a szolgáltatás nevét az IP-címét. DNS-tartományok üzemeltetési szolgáltatás, az Azure DNS névfeloldás biztosít a Microsoft Azure-infrastruktúra használatával. Mellett az internetre irányuló DNS-tartományok támogatása, Azure DNS-ben mostantól is támogatja a saját DNS-tartományok, előzetes verziójú funkciók. 
 
Az Azure DNS egy megbízható, biztonságos DNS-szolgáltatás kezeléséhez, és a virtuális hálózati tartomány neveinek feloldásához anélkül a hozzáadása egy egyéni DNS-megoldást biztosít. Saját DNS-zónák használatával ma használható Azure által biztosított neve helyett a saját egyéni tartománynevekkel is használhatja. Egyéni tartománynevek használatával segítségével testre szabni a virtuális hálózati architektúrát a legjobban megfeleljen a szervezet igényeinek. Virtuális gépek (VM) a virtuális hálózaton belül, és virtuális hálózatok közötti névfeloldás biztosít. Emellett konfigurálhatja zónák nevek vegyes-horizon nézetet, amely lehetővé teszi egy személyes és a nyilvános DNS-zóna neve megegyezik.

![DNS áttekintése](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Előnyök

Az Azure DNS a következő előnyöket nyújtja:

* **Nem kell egyéni DNS-megoldásokat a**. Korábban számos ügyfél létrehozott virtuális hálózatban lévő DNS-zónák kezelése egyéni DNS-megoldásokat. DNS-zóna kezelési is képes lemezvizsgálatok elvégzésére, a natív Azure-infrastruktúra, amely eltávolítja az terheket létrehozásának és kezelésének egyéni DNS-megoldásokat használatával.

* **Minden gyakori DNS rekordok tárolótípus**. Az Azure DNS támogatja A, AAAA, CNAME, MX, NS, PTR, SOA, SRV és TXT rekord.

* **Automatikus állomásnév rekord felügyeleti**. Az egyéni DNS-rekordokat tartalmazó, valamint Azure automatikusan is kezeli állomásnév rögzíti a megadott virtuális hálózatokon lévő virtuális géphez. Ebben a forgatókönyvben a tartománynevek használatával anélkül egyéni DNS-megoldások létrehozásához, vagy megváltoztatni azokat az alkalmazásokat is optimalizálhatja.

* **Virtuális hálózatok közötti állomásnév-feloldási**. Azure által biztosított állomásnevek, eltérően saját DNS-zónák virtuális hálózatok között megosztható legyen. Ez a funkció egyszerűbbé teszi a kereszt-hálózat és a szolgáltatásészlelés forgatókönyvek, például a virtuális hálózati társviszony-létesítés.

* **Jól ismert eszközökkel és a felhasználói élmény**. A tanulási görbére csökkentése érdekében az új ajánlat által használt neves Azure DNS-eszközök (PowerShell, az Azure Resource Manager sablonok és a REST API-t).

* **Vegyes-horizon DNS-támogatás**. Azure DNS zónák azonos nevű másik választ a virtuális hálózaton belül, és a nyilvános interneten keresztül feloldható hozhat létre. A felosztott-horizon DNS jellemző forgatókönyv, hogy adja meg a virtuális hálózaton belüli használatra szolgáltatás dedikált verzióját.

* **Minden Azure régióban elérhető**. Az Azure DNS titkos zónák áll rendelkezésre az Azure nyilvános felhőjében minden Azure régióban. 


## <a name="capabilities"></a>Funkciók

Az Azure DNS a következő lehetőségeket biztosítja:
 
* **Egyetlen virtuális hálózat, amely csatolva van egy titkos zóna regisztrációs virtuális hálózatként származó virtuális gépek automatikus regisztrációjának**. A virtuális gépek regisztrált (hozzáadott) a személyes zónához a magánhálózati IP-címek mutató rögzíti. Ha egy virtuális gépről a virtuális hálózat törlése regisztráció, Azure is automatikusan eltávolítja a megfelelő DNS rekord, a csatolt titkos zónájából. 

  > [!NOTE]
  > Alapértelmezés szerint regisztrációs virtuális hálózatok is összekötőként névfeloldási lévő virtuális hálózatok, lehet, hogy a DNS-feloldás eredménye alapján a zóna bármelyik a regisztrációs virtuális hálózaton belüli virtuális gépek működik. 

* **A privát zóna feloldási virtuális hálózatokként csatolt virtuális hálózatok közötti támogatott előrehaladó DNS-feloldás**. Kereszt-virtuális hálózat DNS-feloldás nincs nincs explicit függőségi úgy, hogy a virtuális hálózatok nincsenek társviszonyban, egymással. Az ügyfelek azonban célszerű egyenrangú virtuális hálózatok az egyéb forgatókönyvek (például a HTTP-forgalom).

* **A virtuális hálózat hatókörén belül támogatott névkeresési DNS**. Magánhálózati IP-címe, a titkos zónájához rendelt virtuális hálózaton belül a névkeresési DNS adja vissza, amely tartalmazza a gazda-rekorddal, valamint utótagként Zónanév teljes Tartománynevét. 


## <a name="limitations"></a>Korlátozások

Az Azure DNS rendszerben a következő korlátozások vonatkoznak:

* Virtuális hálózat csak egy regisztrációszolgáltatóhoz engedélyezett-e személyes zóna.
* Legfeljebb 10 feloldási virtuális hálózatok titkos zóna használható.
* Egy adott virtuális hálózathoz csak egy titkos zóna regisztrációs virtuális hálózati lehet társítani.
* Egy adott virtuális hálózati feloldási virtuális hálózatként legfeljebb 10 titkos zónák lehet társítani.
* Ha regisztrációs virtuális hálózathoz van megadva, a DNS-rekordokat, a virtuális gépek a virtuális hálózatban, a titkos zónához regisztrált nincsenek megjeleníthető vagy lekérhető az Azure Powershell és az Azure CLI API-k, de a virtuális gép rekordokat valóban regisztrálja, és fog Hárítsa el sikeresen.
* Névkeresési DNS csak működik privát IP-címtér regisztrációs virtuális hálózatban.
* Névkeresési DNS, egy magánhálózati IP-címe, amely nincs regisztrálva a titkos zónában (például egy virtuális hálózatot, amely csatolva van megoldás virtuális hálózatként egy olyan magánhálózat zóna a virtuális gépek magánhálózati IP-címe), és visszaadja *internal.cloudapp.net* a DNS-utótagként. Ez utótag viszont nem oldható fel. 
* A virtuális hálózathoz kell megadni (Ez azt jelenti, hogy nincs Virtuálisgép-rekordok megtalálhatók) amikor azt kezdetben (Ez azt jelenti, hogy először) regisztrációja vagy névfeloldási virtuális hálózatként titkos zóna mutató hivatkozásokat tartalmaz. Azonban a virtuális hálózati majd lehet nem üres jövőbeli kapcsolásához regisztrációs vagy névfeloldási virtuális hálózati, egyéb személyes zónákhoz. 
* Most a feltételes továbbítás (például az Azure és a helyi üzemeltetésű hálózatok felbontást engedélyezése) nem támogatott. Hogyan ügyfelek is valósíthat meg ebben a forgatókönyvben más mechanizmusok használatával kapcsolatos információkért lásd: [névfeloldását virtuális gépek és a szerepkörpéldányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

A gyakori kérdések és válaszok titkos, az Azure DNS-zónák kapcsolatban, beleértve az adott DNS névregisztráció viselkedés várható bizonyos fajta műveleteket is lásd: [gyakran ismételt kérdések](./dns-faq.md#private-dns).  


## <a name="pricing"></a>Díjszabás

A nyilvános előzetes a saját DNS-zónák szolgáltatás az ingyenesen elérhető. A szolgáltatás a ajánlat általánosan rendelkezésre álló verzió alatt felajánlja a használat alapú árképzési modellt hasonlóak a meglévő Azure DNS. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan titkos zóna létrehozása az Azure DNS használatával [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy [Azure CLI](./private-dns-getstarted-cli.md).

További információ a néhány gyakori [titkos zóna forgatókönyvek](./private-dns-scenarios.md) , amely a személyes, az Azure DNS-zónák kell megvalósítani.

A gyakori kérdések és válaszok titkos, az Azure DNS-zónák kapcsolatban, beleértve az adott viselkedést várható bizonyos fajta műveleteket is lásd: [gyakran ismételt kérdések](./dns-faq.md#private-dns). 

További információk a DNS-zónák és rekordok ellátogatva [DNS-zónák és áttekintése rögzíti](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg. 

