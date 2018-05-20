---
title: A privát tartományok Azure DNS-sel |} Microsoft Docs
description: A Microsoft Azure szolgáltatást tartalmazó titkos DNS áttekintése.
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
ms.openlocfilehash: 677fc66b66d6c17806a313f2fac3a15e8e1775ba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="using-azure-dns-for-private-domains"></a>A privát tartományok Azure DNS-sel
A tartománynévrendszer, vagy a DNS-, felelős fordítása (vagy feloldása) a szolgáltatás nevét az IP-címét. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával.  Mellett az internetre irányuló DNS-tartományok Azure DNS-ben mostantól is támogatja a saját DNS-tartományok, előzetes verziójú funkciók.  
 
Az Azure DNS biztosít egy megbízható, biztonságos DNS-szolgáltatás kezeléséhez, és nem kell egyéni DNS megoldás hozzáadása egy virtuális hálózati tartomány neveinek feloldásához. Saját DNS-zónák történő ma használható Azure által biztosított neve helyett a saját egyéni tartománynevek használatát teszi lehetővé.  Egyéni tartománynevek használatával segítségével testre szabni a virtuális hálózati architektúrát a legjobban megfeleljen a szervezet igényeinek. Névfeloldás biztosít a virtuális gépek a virtuális hálózaton belül, és virtuális hálózatok között. Emellett zónák neve konfigurálható egy vegyes-horizon nézet – egy olyan magánhálózat és a nyilvános DNS-zóna neve azonos.

![DNS áttekintése](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Előnyök

* **Eltávolítja az egyéni DNS-megoldások szükségességét.** Korábban számos ügyfél létrehozott virtuális hálózatban lévő DNS-zónák kezelése egyéni DNS-megoldásokat.  DNS-zóna kezelési most végezhető Azure natív infrastruktúrája, amely eltávolítja a okozta terheket létrehozása és kezelése az egyéni DNS-megoldásokat használ.

* **Használni közös DNS rekord típusát.**  Az Azure DNS támogatja A, AAAA, CNAME, MX, NS, PTR, SOA, SRV és TXT rekord.

* **Automatikus állomásnév rekord kezelése.** Az egyéni DNS-rekordokat tartalmazó, valamint Azure automatikusan is kezeli állomásnév rögzíti a megadott virtuális hálózatokon lévő virtuális géphez.  Ez lehetővé teszi, hogy optimalizálja a tartománynevek használatával anélkül egyéni DNS-megoldások létrehozásához, vagy módosítsa az alkalmazás.

* **Állomásnév-feloldási virtuális hálózatok között.** Azure által biztosított állomásnevek, eltérően saját DNS-zónák virtuális hálózatok között megosztható legyen.  Ez a funkció egyszerűbbé teszi a kereszt-hálózat és a szolgáltatás felderítése forgatókönyvek például a virtuális hálózati társviszony-létesítés.

* **Jól ismert eszközökkel és a felhasználói élményre.** A tanulási görbére csökkentése érdekében az új ajánlat által használt a már neves Azure DNS-eszközök (PowerShell Resource Manager-sablonok, a REST API-t).

* **Vegyes-horizon DNS-támogatás.** Az Azure DNS zónák létrehozása ezzel a névvel, a különböző választ a virtuális hálózaton belül, és a nyilvános internetről feloldható teszi lehetővé.  A felosztott-horizon DNS jellemző forgatókönyv, hogy adja meg a virtuális hálózaton belüli használatra szolgáltatás dedikált verzióját.

* **Minden Azure-régió érhető el.** Minden Azure-régió az Azure nyilvános felhőben Azure saját DNS-zónák nem áll rendelkezésre. 


## <a name="capabilities"></a>Funkciók 
* A regisztrációs virtuális hálózatként titkos zónához társított egyetlen virtuális hálózat virtuális gépek automatikus regisztrációja. A virtuális gépek lesz regisztrált (hozzáadott) a személyes zónához a privát IP-címek mutató rögzíti. Továbbá, ha egy virtuális gépről a virtuális hálózat törlése regisztráció, Azure is automatikusan eltávolítja a megfelelő DNS-rekord a csatolt titkos zónájából. Vegye figyelembe, hogy regisztrációs virtuális hálózatok is alapértelmezés szerint működjön, és névfeloldási virtuális hálózatok abban, hogy a DNS-feloldás eredménye alapján a zóna bármelyik a regisztrációs virtuális hálózaton belüli virtuális gépek fognak működni. 
* A privát zóna feloldási virtuális hálózatokként csatolt virtuális hálózatok közötti támogatott DNS-feloldás továbbítja. Kereszt-virtuális hálózat DNS-feloldás, nincs nincs explicit függőség, amely a virtuális hálózatok társítottak, egymással. Azonban az ügyfelek virtuális hálózatok az egyéb forgatókönyvek egyenrangú érdemes (pl.: HTTP-forgalom).
* Névkeresési DNS-címkeresést, a virtuális hálózat hatókörén belül támogatott. Egy privát IP-cím a saját zónához hozzárendelt virtuális hálózaton belül névkeresési DNS ad vissza, amely tartalmazza a gazda-rekorddal, valamint utótagként Zónanév teljes Tartománynevét. 


## <a name="limitations"></a>Korlátozások
* 1 regisztrációs virtuális hálózati saját zónánkénti
* Legfeljebb 10 feloldási virtuális hálózatok saját zónánkénti
* Egy adott virtuális hálózathoz csak kapcsolható egy saját zóna regisztrációs virtuális hálózati
* Egy adott virtuális hálózati lehet társítani legfeljebb 10 saját zónák virtuális hálózati feloldás
* Ha egy regisztrációs virtuális hálózathoz van megadva, a DNS-rekordokat, a virtuális gépek a virtuális hálózatban, a saját zónához regisztrált nem lesz megtekinthető vagy a Powershell vagy parancssori felület/API-lekérhető, de a virtuális gép rekordokat valóban regisztrálja, és feloldja sikeresen megtörtént
* Névkeresési DNS csak így fog működni az privát IP-címtér regisztrációs virtuális hálózatban
* Névkeresési DNS egy privát IP-címhez, amely nincs regisztrálva a saját zónában (pl.: privát IP-Címek egy virtuális gép virtuális hálózati feloldási titkos zónához kapcsolódó virtuális hálózatban) ad vissza "internal.cloudapp.net" DNS-utótagként, azonban ez utótag csak akkor tudják feloldani.   
* Virtuális hálózati kell lennie az üres (azaz nincs virtuális gép rekordok) kezdeti (azaz az első alkalommal) regisztrációja vagy névfeloldási virtuális hálózatként saját zónához csatolása. Azonban a virtuális hálózati majd lehet nem üres jövőbeli kapcsolásához regisztrációs vagy névfeloldási virtuális hálózati, egyéb személyes zónákhoz. 
* Ilyenkor feltételes továbbítás nem támogatott, például Azure és a helyi üzemeltetésű hálózatok felbontást engedélyezéséhez. Hogyan ügyfelek is valósíthat meg ebben a forgatókönyvben keresztül más mechanizmusok dokumentációja, lásd: [virtuális gépek és a Szerepkörpéldányok névfeloldás](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

Javasoljuk továbbá akkor is olvassa a a [gyakran ismételt kérdések](./dns-faq.md#private-dns) néhány gyakori kérdések és válaszok a saját zónákat az Azure DNS-ben, a beleértve az adott DNS névregisztráció viselkedés várható bizonyos fajta műveleteket. 


## <a name="pricing"></a>Díjszabás

A nyilvános előzetes saját DNS-zónák az ingyenesen elérhető. Általános rendelkezésre állás érdekében során ez a szolgáltatás a használat alapú árképzési modellt a meglévő Azure DNS-kínál hasonló fogja használni. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan hozzon létre egy titkos zónát az Azure DNS-ben a [PowerShell](./private-dns-getstarted-powershell.md) vagy [CLI](./private-dns-getstarted-cli.md).

Néhány gyakori forgatókönyv ([Privát zónák – Forgatókönyvek](./private-dns-scenarios.md)), amelyek megvalósíthatók az Azure DNS privát zónáiban.

Olvassa a a [gyakran ismételt kérdések](./dns-faq.md#private-dns) a néhány gyakori kérdések és válaszok a saját zónákat az Azure DNS-ben, beleértve az adott viselkedést számíthat bizonyos műveletek típusú. 

További információk a DNS-zónák és rekordok ellátogatva: [DNS-zónák és áttekintése rögzíti](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

