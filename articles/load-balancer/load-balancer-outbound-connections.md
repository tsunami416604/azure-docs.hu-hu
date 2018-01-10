---
title: "Az Azure-ban kimenő kapcsolatok ismertetése |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan Azure lehetővé teszi a virtuális gépek nyilvános internetes szolgáltatásokkal kommunikálni."
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b8e225ba4374c73dbabac3dddab9ba37fa798a5a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Kimenő kapcsolatok áttekintése az Azure-ban

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

A virtuális gép (VM) az Azure-végpontok nyilvános IP-címtér az Azure-on kívüli kommunikációt. Ha egy virtuális Gépet egy kimenő folyam lévő nyilvános IP-címtér célra, Azure a virtuális magánhálózati IP-címet rendel egy nyilvános IP-cím, és lehetővé teszi, hogy a forgalom a virtuális gép eléréséhez.

Azure kimenő kapcsolat eléréséhez három különböző módszert biztosít. Minden metódus rendelkezik, saját képességek és megkötések. Tekintse át az egyes módszerek gondosan, és válassza ki, melyik felel meg az igényeinek.

| Forgatókönyv | Módszer | Megjegyzés |
| --- | --- | --- |
| Önálló virtuális gép (nem a terheléselosztóhoz, példány szint nyilvános IP-cím) |Alapértelmezett SNAT |Azure egy nyilvános IP-cím a SNAT társítja. |
| Elosztott terhelésű virtuális gép (nem példány szint nyilvános IP-cím a virtuális gép) |Használ a load balancer SNAT |Azure SNAT használ a Load Balancer egy nyilvános IP-címe |
| Virtuális gép példány szint nyilvános IP-cím (vagy anélkül terheléselosztó) |SNAT nem használatos. |Azure használja a virtuális Géphez rendelt nyilvános IP-cím |

Ha nem szeretné, hogy a virtuális gépek végpontok nyilvános IP-címtér az Azure-on kívüli kommunikálni, használhatja a hálózati biztonsági csoportok (NSG) letiltja a hozzáférést. NSG-k használatával részletesen tárgyalt [nyilvános kapcsolódási akadályozza meg, hogy](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>Önálló virtuális gép nem példány szint nyilvános IP-címmel

Ebben a forgatókönyvben a virtuális gép nem része egy Azure Load Balancer címkészletet, és nincs hozzárendelve példány szint nyilvános IP (ILPIP) címe. Ha a virtuális Gépet hoz létre egy kimenő folyam, Azure lefordítja a nyilvános IP-forráscím kimenő folyam titkos forrás IP-címét. A nyilvános IP-cím, a kimenő folyam használt nem konfigurálható, és nem tartoznak bele az előfizetés nyilvános IP-erőforrás korlátját ellen. Azure forrás hálózati cím fordítási (SNAT) használja ezt a funkciót. A nyilvános IP-cím elmúló port segítségével különböztetheti meg egymástól a virtuális gép által az egyes adatfolyamok. SNAT dinamikusan kioszt elmúló port, adatfolyamok jönnek létre. Ebben a környezetben használt SNAT porttartomány nevezzük SNAT portok.

SNAT portjait is nagyon gyorsan kimerítették véges erőforrás. Fontos tudni, hogyan használják fel. Egy SNAT port felhasznált ne egy egyetlen cél IP-címhez. Több adatfolyamok azonos cél IP-cím és port, az egyes feldolgozó SNAT egyetlen port. Ez biztosítja, hogy az adatfolyamok egyedi, ha azonos cél IP-cím és port az azonos nyilvános IP-cím származik. Több adatfolyamok, egy másik cél IP-cím és port, hogy mindegyik egyetlen SNAT port megosztása. A cél IP-cím és port egyedivé teszi a forgalom.

Használhat [terheléselosztóhoz tartozó Naplóelemzési](load-balancer-monitor-log.md) és [riasztási eseménynaplójában, figyelje a SNAT port Erőforrásfogyás üzenetek](load-balancer-monitor-log.md#alert-event-log) figyelheti kimenő kapcsolatok állapotát. SNAT port erőforrások elfogytak, amikor a kimenő forgalom sikertelen, amíg a meglévő forgalom által kiadott SNAT portok. Terheléselosztó 4 perces üresjárati időtúllépés VISSZAIGÉNYLÉSE SNAT portokat használ.  Felülvizsgálati [példány szint nyilvános IP-címmel (vagy anélkül terheléselosztó) virtuális gép](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer) a következő szakaszban, valamint a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>Virtuális gép elosztott terhelésű és példány szint nyilvános IP-cím

Ebben a forgatókönyvben a virtuális Gépet az Azure Load Balancer beállított része.  A virtuális gép nem rendelkezik a nyilvános IP-cím hozzárendelve. A terheléselosztó erőforrás olyan terheléselosztó szabályhoz a nyilvános IP-előtérbeli a háttérkészlet közötti kapcsolat létrehozásához kell konfigurálni. Ha nem tölti ki ezt a konfigurációt, a rendszer nem az előző szakaszban leírtak szerint [önálló virtuális gép, és nem példány szint nyilvános IP-cím](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address).

A terheléselosztással rendelkező virtuális Gépet egy kimenő folyam hoz létre, amikor az Azure eszköz a személyes forrás IP-címét a kimenő folyam a nyilvános terheléselosztó előtérbeli nyilvános IP-címére. Azure forrás hálózati cím fordítási (SNAT) használja ezt a funkciót. A Load Balancer nyilvános IP-cím elmúló port segítségével különböztetheti meg egymástól a virtuális gép által az egyes adatfolyamok. SNAT dinamikusan kioszt elmúló port a kimenő forgalom létrehozásakor. Ebben a környezetben használt SNAT porttartomány nevezzük SNAT portok.

SNAT portjait is nagyon gyorsan kimerítették véges erőforrás. Fontos tudni, hogyan használják fel. Egy SNAT port felhasznált folyamat egyetlen cél IP-cím és port /. Több adatfolyamok azonos cél IP-cím és port, az egyes feldolgozó SNAT egyetlen port. Ez biztosítja, hogy az adatfolyamok egyedi, ha azonos cél IP-cím és port az azonos nyilvános IP-cím származik. Több adatfolyamok, egy másik cél IP-cím és port, hogy mindegyik egyetlen SNAT port megosztása. A cél IP-cím és port egyedivé teszi a forgalom.

Használhat [terheléselosztóhoz tartozó Naplóelemzési](load-balancer-monitor-log.md) és [riasztási eseménynaplójában, figyelje a SNAT port Erőforrásfogyás üzenetek](load-balancer-monitor-log.md#alert-event-log) figyelheti kimenő kapcsolatok állapotát. SNAT port erőforrások elfogytak, amikor a kimenő forgalom sikertelen, amíg a meglévő forgalom által kiadott SNAT portok. Terheléselosztó 4 perces üresjárati időtúllépés VISSZAIGÉNYLÉSE SNAT portokat használ.  Tekintse át az alábbi szakaszt, valamint [kezelése SNAT Erőforrásfogyás](#snatexhaust).

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>Virtuális gép példány szint nyilvános IP-címmel (vagy anélkül terheléselosztó)

Ebben a forgatókönyvben a virtuális Gépnek legyen egy példány szint nyilvános IP (ILPIP) rendelve. Nem számít, hogy a virtuális gép elosztott terhelésű-e. Egy ILPIP használata esetén a forrás hálózati cím fordítási (SNAT) nem használatos. A virtuális Gépet a ILPIP minden kimenő forgalom használ. Ha az alkalmazás számos kimenő forgalom kezdeményez, és SNAT Erőforrásfogyás tapasztal, érdemes lehet egy ILPIP SNAT megkötések mérséklése hozzárendelése.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>A nyilvános IP-címet használja egy adott virtuális gép felderítése

Számos módon határozza meg a nyilvános IP-forráscím egy kimenő kapcsolat. OpenDNS láthatja a nyilvános IP-címet a virtuális gép egy szolgáltatást biztosít. Az nslookup parancs használata, elküldheti a név myip.opendns.com DNS-lekérdezést a OpenDNS feloldó. A szolgáltatás az IP-forráscím kérés küldése használt adja vissza. A virtuális gépről a következő lekérdezés végrehajtásakor, a rendszer a választ a nyilvános IP-címet használja ezt a virtuális gépet.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Megakadályozza a nyilvános kapcsolódási

Néha a virtuális gépek számára hozzon létre egy kimenő folyam engedélyezett nemkívánatos is, vagy előfordulhat, hogy mely célok elérhető a kimenő forgalom kezeléséhez követelmény, vagy mely célok megkezdheti a bejövő forgalom. Ebben az esetben [hálózati biztonsági csoportok (NSG)](../virtual-network/virtual-networks-nsg.md) kezelheti a célhoz, amely a virtuális gép is el lehet érni mely nyilvános célként is kezdeményezhető a bejövő forgalom. Amikor egy NSG-t alkalmaz egy elosztott terhelésű virtuális gép, szeretné-e nagy figyelmet fordítani az a [alapértelmezett címkék](../virtual-network/virtual-networks-nsg.md#default-tags) és [alapértelmezett szabályok](../virtual-network/virtual-networks-nsg.md#default-rules).

Győződjön meg arról, hogy a virtuális gép állapotának mintavételi kérések fogadhat Azure Load Balancer. Ha egy NSG blokkolja az egészségügyi mintavételi kérelmeit a AZURE_LOADBALANCER az alapértelmezett címke, a virtuális gép állapotmintáihoz sikertelen lesz, és a virtuális gép van megjelölve. Terheléselosztó leállítja az új forgalom küldése ezt a virtuális Gépet.

## <a name="snatexhaust"></a>Elfogyott a SNAT kezelése

Rövid élettartamú SNAT használt portjait egy kimeríthető erőforrás leírtak [önálló virtuális gép nem példány szint nyilvános IP-címmel](#standalone-vm-with-no-instance-level-public-ip-address) és [virtuális gép elosztott terhelésű és példány szint nyilvános IP-cím](#standalone-vm-with-no-instance-level-public-ip-address).

Ha ismeri az vannak kezdeményezése sok kimenő TCP vagy UDP-kapcsolata ugyanahhoz a cél IP-cím és port, tekintse át a sikertelen kimenő kapcsolatok vagy végigvitelével támogatási skálázását SNAT portok áll, több lehetőség közül választhat általános megoldás.  Tekintse át ezeket a beállításokat, és döntse el, a forgatókönyvéhez leginkább.  Lehetséges egy vagy több segítséget nyújtanak ebben a forgatókönyvben.

### <a name="modify-application-to-reuse-connections"></a>Alkalmazás kapcsolatok újból módosítása 
Igény szerinti SNAT újból felhasználja a kapcsolatot az alkalmazás által használt rövid élettartamú port csökkentése érdekében.  Ez különösen igaz, protokollok, mint a HTTP 1.1, ahol kapcsolat felhasználhatja az alapértelmezett beállítás.  És egyéb protokollok HTTP-t (azaz REST) használó pedig előnyei.  Újbóli mindig jobb, mint az egyes kérelmek egyedi, atomi TCP-kapcsolatok, és további performant, nagyon hatékony TCP tranzakciók eredményez.

### <a name="modify-application-to-use-connection-pooling"></a>Használja a kapcsolatkészletezést módosítása
A rendszer az alkalmazásban, ahol kérelmek belső elosztott kapcsolatokat (egyes újból felhasználja a ahol csak lehetséges) készletét kapcsolatkészlet is alkalmazhat.  A megkötések a elmúló használt portok és környezetet hoz létre a megfelelő.  Ez is növelheti a kapacitásának több egyidejű művelet tételével, amikor egyetlen kapcsolatból blokkolja-e a művelet a válasz.  Az alkalmazás vagy a konfigurációs beállítások, az alkalmazás fejlesztéséhez használ keretében kapcsolatkészletezést már létezik.  Kombinálhatja a kapcsolat újbóli, és a több kérelmek rögzített, előre jelezhető több portok az azonos IP-címre és a port felhasználását is élvező csökkenti a késést és erőforrás TCP tranzakciók nagyon hatékony használata során kihasználtság.

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>Módosítsa az alkalmazást, hogy az kisebb agresszív újrapróbálkozási logika
Alkalmazáshibák fordul elő, vagy SNAT használt elmúló port elfogytak, során agresszív vagy találgatásos kényszerített decay nevű és leállítási logika OK Erőforrásfogyás fordulhat elő, vagy megőrzése nélkül újrapróbálkozik. Igény szerinti rövid élettartamú port egy kisebb agresszív újrapróbálkozási logika használatával csökkentése érdekében.   Elmúló port (nem állítható) 4 perces üresjárati időtúllépés rendelkezik, és ha az újrapróbálkozások túl agresszív, az Erőforrásfogyás rendelkezik-e nincs lehetőség arra, hogy a saját törlődnek.  Annak eldöntéséhez, hogy milyen gyakran és hogyan lehet az alkalmazás újrapróbálja tranzakciók ezért fontos szempont a kialakítás.

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>Egy példányszintű nyilvános IP-cím hozzárendelése az egyes virtuális gépek
A forgatókönyvhöz a értékre változik [példányszintű nyilvános IP-cím egy virtuális géphez](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer).  A nyilvános IP-címet használja az egyes virtuális gépek összes porttartomány érhetők el a virtuális gép (nem forgatókönyvek, ahol a nyilvános IP-elmúló port megosztott módon működik a VM megfelelő háttérkészlet társítva).  Nincsenek kell figyelembe venni, például az IP-címek további költség- és nagyszámú egyedi IP-címek engedélyezése gyakorolt lehetséges hatásának kompromisszumot.

## <a name="limitations"></a>Korlátozások

Ha [több (nyilvános) IP-címek társítva a terheléselosztó](load-balancer-multivip-overview.md), bármely, a nyilvános IP-címei kimenő forgalom jelöltségét ellenőrző.

Azure algoritmust használ, a készlet mérete alapján SNAT port számának megállapításához.  Ez jelenleg nem konfigurálható.

Kimenő kapcsolatok 4 perces üresjárati időtúllépés rendelkezik.  Ez nem módosítható.

Fontos megjegyezni, hogy SNAT elérhető portok száma nem fordíthatók le, közvetlenül a kapcsolatok száma. Tekintse meg az előző szakaszokban arról, mikor és hogyan SNAT portok foglal le a és [kezelése a kimeríthető erőforrás](#snatexhaust).
