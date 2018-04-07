---
title: 'A Microsoft Azure virtuális Datacenter: A hálózati perspektíva |} Microsoft Docs'
description: Ismerje meg, hogyan hozhat létre a virtuális adatközpont az Azure-ban
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jonor
ms.openlocfilehash: 7fcd8e12a7109218387788e47eddad48e72797bb
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="microsoft-azure-virtual-datacenter-a-network-perspective"></a>A Microsoft Azure virtuális Datacenter: Egy hálózati terv
**A Microsoft Azure**: gyorsabb, költségtakarékosabb munkavégzésben, integrálása a helyszíni alkalmazások és adatok

## <a name="overview"></a>Áttekintés
Az áttelepítést a helyszíni alkalmazások az Azure-ba, még akkor is jelentős módosításokat (egy, a "növekedési és az eltolás mértékét megadó" néven ismert megközelítés), anélkül biztosít azoknak a szervezeteknek a biztonságos és költséghatékony infrastruktúra előnyeit. Azonban a legtöbb az agilitást lehetővé teszik a felhőalapú informatika, a vállalatok kell alakítani az architektúrák Azure-képességek előnyeit. A Microsoft Azure nyújt kapacitású szolgáltatások és a infrastruktúra, a vállalati szintű képességet és a megbízhatóság és a számos választhat való hibrid kapcsolathoz. Az ügyfelek az interneten keresztül vagy Azure ExpressRoute, amely magánhálózati kapcsolatot biztosít a felhő szolgáltatásokról eléréséhez választhat. A Microsoft Azure platform lehetővé teszi, hogy az ügyfelek zökkenőmentesen bővítheti az infrastruktúrát kiterjeszti a felhőbe, és hozhat létre többrétegű architektúrák számára. Továbbá Microsoft partnerei biztonsági szolgáltatásait és a virtuális készülékek Azure rendszerben való futtatásra optimalizált felajánlásával bővített szolgáltatásokat adja meg.

Ez a cikk áttekintést nyújt mintákat és terveket, oldja meg a architekturális méretezés, a teljesítmény és a biztonsági aggályokon használható sok ügyfél arcfelismerési menet masse áthelyezéséről a felhőbe végezni. Egy áttekintést arról, hogyan illeszthető be a felügyeleti különböző szervezeti az informatikai szerepkörök és irányítása a rendszer a is ismertet, biztonsági követelményeinek hangsúlyt, és optimalizálási költség használja.

## <a name="what-is-a-virtual-data-center"></a>Mi az az Adatközpont virtuális?
A korai napokban megoldások tervezték, hogy a gazdagép egyetlen, viszonylag elkülönített, alkalmazások, a nyilvános pontszámot. Ez a módszer jól néhány évig előre. Azonban, a felhő megoldások nyilvánvalóvá vált, és több nagy méretű munkaterhelés a felhőben üzemeltetett címzési biztonsági, megbízhatóságát, teljesítményét, és valamelyik központi telepítések vonatkozik költség vagy további régiókban vált létfontosságú teljes életciklusa a felhőalapú szolgáltatás.

A következő felhő telepítési ábra szemlélteti a néhány példa biztonsági réseinek (vörös téglalappal) és optimalizálási hálózati virtuális készülékeket illetve a munkaterhelések (sárga mezőben) között.

[![0]][0]

A virtuális adatközpont (vDC) született szükségességét támogatja a vállalati számítási feladatok és a szükséges a vezetett be, amikor nagy méretű alkalmazás támogatja a nyilvános felhőben problémák kezelésére méretezhetők át.

A virtualizált tartományvezérlő nincs csak az alkalmazások és szolgáltatások a felhőben, de is a hálózati, biztonsági, felügyeleti és infrastruktúra (például a DNS- és a Directory Services). Általában is biztosít vissza egy helyi hálózati vagy data center magánhálózati kapcsolat. Több munkaterhelés mozgatása az Azure-ba, fontos gondolja át a támogató infrastruktúra és az objektumok, amelyek az ilyen terhelések kerülnek. A több száz "munkaterhelés-szigetek" felügyelni külön független adatfolyam, biztonsággal rendelkező modelljei és megfelelőségi kihívást elterjedése továbbléphetnek gondosan erőforrások felépítése hogyan elkerülése érdekében.

Egy virtuális adatközpont alapvetően a közös támogató funkciók, szolgáltatások és az infrastruktúra különálló, de kapcsolódó entitások gyűjteményét. A munkaterhelések megtekintésével, egy integrált virtualizált tartományvezérlő, vegye figyelembe költséghatékony miatt méretgazdaságossági, optimalizált biztonság az összetevő és az adatok folyamata központosítás, könnyebben műveleteit, felügyeleti és megfelelőségi ellenőrzések együtt.

> [!NOTE]
> Fontos tisztában lenni azzal, hogy a virtualizált tartományvezérlő **nem** egy különálló Azure terméket, de a különféle szolgáltatásait és funkcióit a pontos követelményeinek megfelelően kombinációja. Virtualizált tartományvezérlő módja a, a számítási feladatok és az Azure használati továbbléphetnek az erőforrások és a felhőalapú képességek maximalizálása érdekében. A virtuális tartományvezérlő nem ezért egy moduláris megközelítés fel informatikai services az Azure-beli szervezeti szerepkörök és felelősségek tiszteletben készítéséhez.

A virtualizált tartományvezérlő segítségével a vállalatok munkaterheléseket és alkalmazásokat az Azure lekérése a következő esetekben:

-   Több kapcsolódó munkaterhelések
-   Egy a helyszíni környezetből Azure-bA áttelepítése munkaterhelések
-   Megosztott és központosított biztonsági és a hozzáférési követelmények megvalósítása a munkaterhelések között
-   DevOps és a központi IT vállalat a megfelelő keverése

A virtualizált tartományvezérlő, a következő előnyöket feloldásához kulcsa a központosított topológia (hub és küllők) Azure-szolgáltatások vegyesen: [Azure VNet][VNet], [NSG-k] [ NSG], [Vnetben társviszony-létesítés][VNetPeering], [felhasználó által definiált útvonalak (UDR)][UDR], és az Azure-identitás [szerep alapja Hozzáférés-vezérlés (RBAC)][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Kell egy virtuális adatközpont?
Bármely át kell helyeznie egynél több munkaterhelések az Azure használatával a közös erőforrások továbbléphetnek is kihasználhatja az Azure felhasználói. Attól függően, hogy a nagyságrendet akár egyetlen alkalmazás is kihasználhatja le a mintákat és egy virtualizált tartományvezérlő létrehozásához használt összetevőknek.

Ha a szervezete egy központi informatikai, hálózati, biztonsági és/vagy a megfelelőségi csoport/szervezeti egység, egy a virtualizált tartományvezérlő segítségével kényszerítése házirend pontok elkülönítése szolgálati, és miközben alkalmazásokat fejlesztő csapatoknak együtt mértékű az alapul szolgáló közös összetevők biztosítása és az igényeinek megfelelő vezérlőn.

A szervezeteknek, amelyek DevOps szeretné hasznosítani tudnak engedélyezett zsebe Azure-erőforrások, és biztosítja a rendelkeznek (előfizetés vagy az erőforrás csoport egy közös előfizetésben) csoport, de a hálózaton belül teljes ellenőrzést a virtualizált tartományvezérlő koncepciókról és biztonsági határokat maradnak megfelelő, egy hub virtuális hálózat és az erőforráscsoport egy központi házirend által definiált konfigurációjának kialakításához.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Szempontok a virtuális adatközpont megvalósítása
A virtualizált tartományvezérlő tervezésekor több kulcsfontosságú problémák vannak kell figyelembe venni:

-   Identitás- és címtárszolgáltatásokat
-   Biztonsági infrastruktúra
-   A felhő kapcsolata
-   Kapcsolat a felhőben

##### <a name="identity-and-directory-service"></a>*Identitás- és címtárszolgáltatás*
Identitás és szolgáltatások az összes adat kulcsfontosságú erőforrásokból, mind a helyszíni és a felhőben. Identitás minden szempontját hozzáférési és engedélyezési belül a virtualizált tartományvezérlő szolgáltatásokhoz kapcsolódik. Győződjön meg arról, hogy csak a jogosult felhasználók és a folyamatok hozzáférés az Azure-fiókot és az erőforrások érdekében Azure, számos különböző típusú hitelesítő adatokat használ. Ezek közé tartozik a jelszavak (az Azure-fiók eléréséhez), a titkosítási kulcsokat, a digitális aláírásokat és a tanúsítványok. [*Az Azure multi-factor Authentication* (MFA)] [ MFA] egy további rétegét biztonsági Azure-szolgáltatások eléréséhez. Az Azure MFA erős hitelesítés számos olyan egyszerű ellenőrzési lehetőséget kínál – a telefonhívás, szöveges üzenetet vagy mobilalkalmazásban megjelenő értesítést –, és lehetővé teszi az ügyfeleknek az előnyben részesített módszerek közötti választást.

A nagyvállalati kell megadása egy identity management tartozó egyedi azonosítók kezelését, a hitelesítési, engedélyezési, szerepkörök és jogosultságok belüli vagy a virtualizált tartományvezérlő ismerteti. Ez a folyamat célja biztonsági és a termelékenység növelése közben csökkenő költségek, állásidő és ismétlődő manuális feladatokat kell lennie.

Vállalat vagy szervezet különböző sor-az-vállalatok (LOB) szolgáltatások kibővített vegyesen lehet szükség, és gyakran alkalmazottaknak különböző szerepkörök esetén, a másik projektet. A virtualizált tartományvezérlő van szükség a különböző csapatok, az adott szerepkör-definíciók, a megfelelő irányítás rendszerekre eléréséhez megfelelő együttműködését. A mátrix felelősségi, a hozzáférés és a jogok rendkívül összetett feladat lehet. A virtualizált tartományvezérlő Identitáskezelés keresztül valósul [ *Azure Active Directory* (AAD)] [ AAD] és a szerepköralapú hozzáférés-vezérlést (RBAC).

A címtárszolgáltatás egy megosztott információk keresése, kezelése, felügyelete és mindennapi elemek és a hálózati erőforrások rendszerezése infrastruktúra. Ezeket az erőforrásokat kötetek, mappák, fájlok, nyomtatók, felhasználók, csoportok, eszközök és egyéb objektumok lehetnek. Az egyes erőforrások a hálózaton a kiszolgáló által tekinthető objektum. Ezzel az erőforrás vagy objektum tartozó attribútumok gyűjteménye erőforrásra vonatkozó adatokat tárolja.

Az összes Microsoft online szolgáltatás támaszkodjon az Azure Active Directory (AAD) a bejelentkezéshez, és más az identitásukat. Az Azure Active Directory egy átfogó, magas rendelkezésre állású, felhőalapú identitás- és hozzáférés-kezelő megoldás, amely ötvözi az alapvető címtárszolgáltatásokat, a fejlett identitáskezelést és az alkalmazáshozzáférés-felügyeletet. Aad-ben integrálható a helyszíni Active Directory egyszeri bejelentkezést az összes felhő alapú és helyileg üzemeltetett (helyszíni) engedélyezése az alkalmazások. A felhasználói attribútumok a helyszíni Active Directory is automatikusan szinkronizálhatja az aad-ben.

Egyetlen globális rendszergazdája nem szükséges a virtualizált tartományvezérlő összes engedélyek hozzárendelését. Helyette minden egyes adott osztály (vagy a felhasználók és a címtárszolgáltatás szolgáltatáscsoport) is rendelkezik saját belül a vDC-erőforrások kezeléséhez szükséges engedélyeket. Szerkezetének kialakítása engedélyek megköveteli a terheléselosztás. Túl sok engedélyek akadályozhatják a teljesítmény hatékonyságát, és túl kevés vagy laza engedélyek növelhető a biztonsági kockázatokat. Oldja meg a problémát, részletes hozzáféréskezelést az vDC erőforrások felajánlásával segít az Azure szerepköralapú hozzáférés-vezérlés (RBAC).

##### <a name="security-infrastructure"></a>*Biztonsági infrastruktúra*
Biztonsági infrastruktúra, a virtualizált tartományvezérlő környezetében főként kapcsolódik, a virtualizált tartományvezérlő adott virtuális hálózati szegmensben lévő forgalom elkülönítése, és hogyan szabályozzák a bemenő és kimenő forgalmat a virtualizált tartományvezérlő teljes. Azure alapul, amely megakadályozza a jogosulatlan és nem szándékos forgalom központi telepítések között több-bérlős architektúrák, használja a virtuális hálózatelkülönítés (VNet), hozzáférés-vezérlési listák (ACL), és töltse be a terheléselosztó és IP-szűrők forgalom folyamata házirendek együtt. Hálózati címfordítás (NAT) elválasztja a belső hálózati forgalmat a külső forgalom.

Az Azure-hálót bérlői munkaterheléseket az infrastruktúra-erőforrásokat foglal le, és a virtuális gépek (VM) érkező vagy oda irányuló kommunikációt kezeli. Az Azure hipervizor kikényszeríti a memória és a folyamat elkülönítése virtuális gépeket, és biztonságosan útvonalak hálózati forgalmat a vendég operációs rendszer bérlők számára.

##### <a name="connectivity-to-the-cloud"></a>*A felhő kapcsolata*
A virtualizált tartományvezérlő kell külső hálózatok szolgáltatásokat biztosítson a felhasználók, partnerek és/vagy a belső felhasználók kapcsolatát. Ez általában azt jelenti, hogy a kapcsolat nem csak az interneten, hanem a helyszíni hálózatokhoz és adatközpontok.

Az ügyfelek a vezérlő biztonsági házirendeket hozhat létre és hogyan adott vDC üzemeltetett szolgáltatások érhető el, az internetkapcsolat, és a virtuális hálózati berendezések (a szűrést és a forgalmi ellenőrzési) és a, és egyéni házirendek és a szűrés (hálózati útválasztást Felhasználó által definiált Útválasztás és a hálózati biztonsági csoportok).

A vállalatok gyakran kell vDCs csatlakozni a helyszíni adatközpont vagy más erőforrásokat. A kapcsolat az Azure és a helyszíni hálózat között az ezért egyik fontos szempontja tervezése során az hatékony architektúrát. Vállalat rendelkezik egy virtualizált tartományvezérlő és a helyszíni összekapcsolása létrehozása az Azure-ban két különböző módon: az interneten keresztül, vagy saját közvetlen kapcsolatot átvitel közben.

Egy [ **Azure telephelyek közötti VPN** ] [ VPN] összekapcsolási szolgáltatásnak a helyszíni hálózat között az interneten keresztül, és titkosítja a virtualizált tartományvezérlő biztonságos keresztül kapcsolatok száma (alagutak IPsec/IKE). Az Azure Site – hely kapcsolat rugalmas, gyors létrehozásához, és nincs szükség semmilyen további beszerzési, az összes kapcsolatot az interneten keresztül csatlakozni.

[**ExpressRoute** ] [ ExR] az Azure-kapcsolat szolgáltatása, amely lehetővé teszi a virtualizált tartományvezérlő és a helyszíni hálózat között magánhálózati kapcsolatok létrehozása. ExpressRoute-kapcsolatok nem nyissa meg a nyilvános interneten keresztül, és kínálnak a magasabb szintű biztonságra, megbízhatóságra és konzisztens késést és nagyobb sebesség (legfeljebb 10 GB/s). ExpressRoute nagyon hasznos a vDCs, mint ExpressRoute igényelhető a megfelelőségi szabályok társított titkos kapcsolatok előnyeit.

ExpressRoute kapcsolatok foglal magában, amelyek révén az ExpressRoute-szolgáltatóhoz. A Testreszabás gyorsan igénylő ügyfelek azt általános kezdetben a telephelyek közötti VPN használatával a virtualizált tartományvezérlő közötti kapcsolatot létrehozni a és a helyszíni erőforrásokkal, majd utána áttelepíteni az ExpressRoute-kapcsolatot.

##### <a name="connectivity-within-the-cloud"></a>*Kapcsolat a felhőben*
[Vnetek] [ VNet] és [Vnetben társviszony-létesítés] [ VNetPeering] belül egy virtualizált tartományvezérlő alapvető hálózati kapcsolat szolgáltatást. Egy VNet biztosítja, hogy a virtualizált tartományvezérlő erőforrások elkülönítési természetes határ, és Vnetben társviszony-létesítés lehetővé teszi, hogy a más Vnetekről belül az azonos Azure-régió közötti befolyásolása. Hozzáférés-vezérlési listái keresztül megadott biztonsági szabályok egyeznie kell a Vneten belül és között Vnetek forgalomirányítás ([hálózati biztonsági csoport][NSG]), [hálózati virtuális készülékek ] [ NVA], és egyéni útválasztási táblázataiba ([UDR][UDR]).

## <a name="virtual-data-center-overview"></a>Virtuális adatközpont – áttekintés

### <a name="topology"></a>topológia
Hub és küllők modell kiterjesztett a virtuális adatközpont egyetlen Azure régión belül

[![1]][1]

A központ a központi zónákhoz szabályozza, és megvizsgálja a különböző zónákhoz közötti érkező és/vagy a kimenő forgalom: Internet, a helyszíni és a küllők. A küllős topológia lehetőséget ad az IT-részleg közben csökkenhet az lehetséges, hogy a hiba a konfiguráció és a biztonsági házirendek egy központi helyen történő hatékony módot.

A központ tartalmazza a közös szolgáltatás-összetevők a küllők használni. Az alábbiakban néhány tipikus példája közös központi szolgáltatások:

-   A Windows Active Directory-infrastruktúrát (szolgáltatással a kapcsolódó AD FS) a felhasználók hitelesítését a harmadik felek számára nem megbízható hálózatokon a küllős a hozzáférést a munkaterhelések elérése előtt eléréséhez szükséges
-   A DNS-szolgáltatás a küllők hozzáférés erőforrásokat a helyszínen és az interneten a munkaterhelés elnevezési megoldásához
-   A nyilvános kulcsokra ÉPÜLŐ infrastruktúra, a munkaterhelések egyszeri bejelentkezés megvalósítása
-   Folyamatvezérlés (TCP/UDP) a küllők és az Internet között.
-   A küllős és a helyszíni közötti adatfolyam vezérlés
-   Ha szükséges, egy küllős közötti folyamatvezérlés

A virtualizált tartományvezérlő csökkenti a teljes költség szempontjából több küllők között megosztott hub-infrastruktúra használatával.

Minden egyes küllős szerepe állomás munkaterhelések eltérő típusú lehet. A küllők is megadhatja a moduláris megközelítés az ismétlődő telepítések (például a fejlesztéshez és teszteléshez, felhasználói tesztelés az üzem előtti és éles) azonos munkaterhelések. A küllők elkülönítse, és engedélyezze a szervezetben (például DevOps csoportok) különböző csoportok használatával is lehet. Egy küllős belül egy alapszintű munkaterhelés vagy a rétegek közötti forgalomirányítás rendelkező összetett többrétegű munkaterhelések üzembe.

##### <a name="subscription-limits-and-multiple-hubs"></a>Előfizetési korlátozásait és több hubok
Azure-ban minden összetevő, függetlenül a típus van telepítve az Azure-előfizetéssel. Az Azure-előfizetések az Azure összetevők elkülönítési is felel meg a különböző LOB, például a hozzáférési és engedélyezési szinteket beállítása.

Egyetlen vDC költenie nagyszámú küllők, bár, csakúgy, mint minden informatikai rendszer korlátai platformok vannak. A központi telepítési kötve van egy adott Azure-előfizetéshez, amelynek korlátozások és korlátozások (lásd például a Vnetben társviszony - maximális száma: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések] [ Limits] részletekért). Azokban az esetekben, ahol korlátokat lehet, hogy egy probléma, az architektúra méretezhető akár további kiterjesztésével a modell egyetlen hub-küllők a hub és küllők fürtre. Egy vagy több Azure-régiók több központok az Express Route vagy telephelyek közötti VPN használatával kell egymáshoz.

[![2]][2]

Több hubok bevezetése növeli a költségeket és a felügyeleti elérhető, a rendszer, és csak akkor indokolja méretezhetőség (példák: rendszer korlátok vagy redundancia) és a regionális replikációs (példák: végfelhasználói teljesítmény- vagy katasztrófa utáni helyreállítás). A forgatókönyvek több hubok, a hubok igénylő törekedni kell a ugyanazokat a működési könnyű szolgáltatásokat kínál.

##### <a name="interconnection-between-spokes"></a>Küllők összekapcsolása
Belül egyetlen küllős az összetett több rétegek munkaterhelések valósíthat meg. Többrétegű konfigurációk ugyanazon virtuális alhálózatok (egy minden szinthez) használatával, és a szűrés a adatfolyamok NSG-k használatával valósítható meg.

Egy érdemes, másrészt a többrétegű alkalmazások és szolgáltatások telepítése több Vnetek között. Hálózatokból használ, küllők kapcsolódhatnak az ugyanabban a központban vagy különböző hubok más küllők. Ebben a forgatókönyvben például a helyzet alkalmazás feldolgozási kiszolgálók belül hol áll egy küllős (VNet), amíg az adatbázis telepítve van egy másik küllős (VNet). Ebben az esetben is könnyen kapcsolódnak össze a Vnetben társviszony-létesítés küllők, és ezáltal a a központ áthaladó elkerülése érdekében. Annak érdekében, hogy megkerüljék a központ nem megkerülése, fontos biztonsági vagy naplózási pontokat, előfordulhat, hogy csak a központban létezik gondos architektúra és biztonsági felülvizsgálat kell végezni.

[![3]][3]

Küllők is, amely központjaként egy küllős való kell egymáshoz. Ez a megközelítés létrehoz egy kétszintű hierarchia: (0. szint) magasabb szinten található küllős válnak a központ, a hierarchia alacsonyabb küllők (1. szint). A virtualizált tartományvezérlő küllők kell továbbítani a forgalmat a központi helyet alakíthatnak érheti el, vagy a helyi hálózaton vagy az internethez. Két szintű központ architektúrája vezet be, összetett útválasztási, amely eltávolítja egy egyszerű hub-küllős kapcsolat előnyeit.

Bár az Azure lehetővé teszi, hogy a komplex topológiákba, a vDC-fogalom alapelvek egyik ismételhetőség és az egyszerűség kedvéért. Energiabefektetést igénylő felügyelet minimalizálása érdekében az egyszerű hub-küllős terv a javasolt vDC referencia-architektúrában.

### <a name="components"></a>Összetevők
Egy virtuális adatközpont négy alapvető összetevőtípus épül fel: **infrastruktúra**, **szegélyhálózat**, **munkaterhelések**, és **figyelés**.

Minden egyes összetevő típusa különböző Azure-szolgáltatások és erőforrások áll. A virtualizált tartományvezérlő többféle összetevők és az azonos típusú összetevő több módosításokat tevődik össze. Például előfordulhat, hogy sok, amelyek megfelelnek a különböző alkalmazások különböző, logikailag elkülönített, a munkaterhelés-példány. A különböző összetevők és példányok használni a virtualizált tartományvezérlő végső soron létrehozásához.

[![4]][4]

A virtualizált tartományvezérlő előző magas szintű architektúrájának különböző zónákhoz a hub-küllők topológia használható különböző összetevő típusok jeleníti meg. Az infrastruktúra-összetevőihez látható a architektúra különböző részein.

Jó gyakorlat (egy helyi tartományvezérlő vagy a virtualizált tartományvezérlő) teszik lehetővé a hozzáférést és jogosultságokkal kell biztonságicsoport-alapú. Foglalkozó csoportok, ne pedig egyéni felhasználóknak segít a hozzáférési házirendek karbantartására következetesen csoportok és segédeszközök minimalizálja a konfigurációs hibák közötti. Rendelni, illetve a felhasználók a megfelelő csoportokban segítséget nyújt egy megadott felhasználó jogosultságával frissítése és eltávolítása.

Minden egyes szerepkör csoporthoz lehetnek egyedi előtag a nevük, így könnyen mely társítva mely alkalmazások és szolgáltatások azonosításához. Például egy olyan hitelesítési szolgáltatás üzemeltető munkaterhelés rendelkezhet nevű csoport *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps és AuthServiceInfraOps.* Hasonlóképpen a szerepköröket, vagy egy adott szolgáltatással kapcsolatos szerepköröket központosított, sikerült végrehajtásával lehet kerüli meg a "Corp" *CorpNetOps* például.

Számos szervezet a következő csoportok változata segítségével adja meg a főbb információkat a szerepkörök:

-   A *központi IT-részleg (vállalati)* a tulajdonosi jogai (például a hálózati és biztonsági) infrastruktúra-összetevőihez szabályozására, és az ezért közreműködői szerepkört az előfizetés (és a központ irányítását rendelkezésére) és a küllők hálózati közreműködői jogokat. Nagy szervezet gyakran e felügyeleti feladatokat, többek között a; több csapat közötti megosztása a hálózati műveletek (CorpNetOps) csoport (kizárólagos összpontosít a hálózatkezelés) és a biztonsági műveletek (CorpSecOps) csoport (tűzfal és a biztonsági házirend felelős). Ebben az esetben két különböző csoportok kell ezeket egyéni szerepkörök hozzárendelése hozható létre.
-   A *fejlesztői & Tesztcsoport (AppDevOps)* felelőssége munkaterhelések (alkalmazások vagy szolgáltatások) telepítése. Ez a csoport időt vesz igénybe, a IaaS-telepítések és/vagy egy virtuális gép közreműködő szerepkört vagy további PaaS közreműködői szerepkör (lásd: [átruházásához hozzáférés-vezérlés beépített szerepkörök][Roles]). Opcionálisan a fejlesztési és tesztelési team szükség lehet a biztonsági házirendek (NSG-k) és a központi vagy egy adott küllős belül útválasztási házirend (UDR) láthatóságát. Ezért mellett munkaterhelések közreműködői szerepkört, ennek a csoportnak kell a hálózati olvasó szerepkört.
-   A *és karbantartása csoport (CorpInfraOps vagy AppInfraOps)* feladata a munkaterhelések éles környezetben kezelése. Ennek a csoportnak kell lennie egy előfizetés közreműködőjének éles előfizetése munkaterheléseket. Egyes szervezetek is előfordulhat, hogy kiértékelheti, hogy egy további eszkalációs támogatási csoport csoportja szerepkörrel rendelkező előfizetés közreműködői éles környezetben, és a központi helyet alakíthatnak előfizetés van szükségük ahhoz, hogy javítsa ki a lehetséges konfigurációs problémák az éles környezetben környezet.

A virtualizált tartományvezérlő van felépítve, hogy a központi informatikai csoportok kezelése a központ létrehozott csoportok megfelelő csoportok számítási feladatok szintjén. Hub erőforrások kezelése mellett csak a központi IT-csoport tudná külső hozzáférés és a legfelső szintű engedélyek az előfizetésben. Azonban munkaterhelés-csoport tudná erőforrások és a virtuális hálózat, egymástól függetlenül a központi IT engedélyeinek ellenőrzése.

A virtualizált tartományvezérlő kell particionálni a biztonságos tárolásához több projektet különböző sor-az-vállalatok (LOB) között. Minden projekt különböző elkülönített környezetek (fejlesztői, ellenőrzését, éles) szükséges. Az egyes ezekben a környezetekben külön Azure-előfizetések természetes elkülönítést biztosítani.

[![5]][5]

A fenti ábrán egy szervezet projekteket, felhasználók, csoportok és a környezetben, amelyekre központilag telepítették az Azure összetevők közötti kapcsolatot mutatja be.

Általában az informatikai környezetben (vagy réteg), amelyben több alkalmazás telepített és hajtotta végre a rendszer. A nagyobb vállalatok használja a fejlesztési környezetet (ahol módosításait eredetileg és tesztelt) és egy éles környezetben (mi végfelhasználók használja). Adott környezetben elkülönül egymástól, gyakran több átmeneti környezet Between szakaszos bevezetéshez (Bevezetés) engedélyezését, tesztelése és visszaállítási problémák. Központi telepítés architektúrák jelentős eltérések lehetnek, de általában az alapvető folyamat fejlesztési (fejlesztés) kezdődő, és a záró dátum (termék) éles továbbra is követi.

Egy közös architektúra az ilyen típusú többrétegű környezetek DevOps (fejlesztéshez és teszteléshez), a UAT (átmeneti) és a termelési környezetben áll. A szervezetek kihasználhatják a egy vagy több Azure AD-bérlő hozzáférési és jogosultsága ahhoz, hogy ezekhez a környezetekhez. Az előző ábrán látható egy esetben ha két különböző az Azure AD bérlők használják: egy a DevOps és ellenőrzését, és egy másik kizárólag az éles.

A jelen másik Azure ad bérlők eredményezi különböző környezetek között. A ugyanazt a felhasználói csoportot (például központi informatikai) kell hitelesítenie magát, a különböző URI elérni egy másik AD-bérlő módosítása a szerepkörök vagy engedélyek a DevOps vagy az éles környezetben a projekt. A különböző környezetek eléréséhez különböző felhasználói hitelesítés jelenlétét csökkenti a lehetséges kimaradások és egyéb emberi hibák által okozott problémákat.

#### <a name="component-type-infrastructure"></a>Összetevő típusa: infrastruktúra
Ez az összetevő típus támogató infrastruktúra többségét tartalmazó. Is ahol a központi IT, biztonsági, megfelelőségi csapat a legmagasabbak az az idő, illetve.

[![6]][6]

Infrastruktúra-összetevőihez nyújtanak egy összekapcsolása egy virtualizált tartományvezérlő különböző összetevői között, és a központ és az küllők is szerepel. A felelősséget kezelése és az infrastruktúra-összetevőihez karbantartása általában hozzá van rendelve a központi IT és/vagy biztonsági csoporttal.

Az IT-infrastruktúra csoport elsődleges feladatai egyik IP-cím sémák egységességének biztosításához a vállalaton belül. A privát IP-címek a virtualizált tartományvezérlő kell lennie a konzisztens rendelve, és nem átfedésben vannak hozzárendelve a helyszíni hálózatokon magánhálózati IP-címek.

A helyszíni peremhálózati útválasztók vagy az Azure-környezetek NAT IP-címütközés elkerülése érdekében, miközben komplikációk hozzá az infrastruktúra-összetevőihez. Egyszerű felügyeleti a egyike a főbb célok a virtualizált tartományvezérlő, ezért ajánlott megoldás használ a hálózati Címfordítás IP aggályokat kezeléséhez nincs.

Infrastruktúra-összetevőihez a következő funkciókat tartalmazza:

-   [**Identitás- és a directory services**][AAD]. Az Azure-ban minden erőforráshoz való hozzáférés a címtárszolgáltatásban tárolt identitás vezérli. A címtárszolgáltatás nem csak a felhasználók listáját, de a hozzáférési jogok is tárol egy adott Azure-előfizetéshez. Ezeket a szolgáltatásokat is létezik, csak felhőalapú, vagy azokat az Active Directoryban tárolja, a helyszíni identitás szinkronizálhatók.
-   [**Virtuális hálózati**][VPN]. Virtuális hálózatok a virtualizált tartományvezérlő fő összetevői, és lehetővé teszik a forgalom elkülönítése határ létrehozása az Azure platformon. Virtuális hálózat egy vagy több virtuális hálózati szegmensen, az egy adott IP-hálózati előtag (alhálózat) áll. A virtuális hálózat határozza meg egy belső szegélyhálózati területre, ahol IaaS virtuális gépek és PaaS szolgáltatások létesíthet a személyes kommunikációt. Virtuális gépek (és PaaS szolgáltatások) egy virtuális hálózat nem tud kommunikálni közvetlenül a virtuális gépek (és PaaS szolgáltatások) egy másik virtuális hálózatot, még akkor is, ha mindkét virtuális hálózatok jöttek létre az azonos ügyfél, az ugyanazon előfizetésben található. Elkülönítési kritikus tulajdonság, amely biztosítja a felhasználói virtuális gépeket, kommunikációs titkos virtuális hálózaton belül marad.
-   [**UDR**][UDR]. Virtuális hálózati adatforgalom a rendszer útválasztási táblázat alapján alapértelmezés szerint történik. Felhasználó megadása útvonal egy egyéni útválasztási táblázat, amely a hálózati rendszergazdák társítható egy vagy több alhálózatból felülírja a rendszer útvonaltábla viselkedését, és meg a virtuális hálózaton belül a kommunikációs elérési utat. Udr-EK jelenléte biztosítja, hogy a kimenő forgalom a meghatározott egyéni virtuális gépek és/vagy a virtuális hálózati berendezések és a jelen, a központban és a küllők terheléselosztók küllős átmenő.
-   [**NSG**][NSG]. Hálózati biztonsági csoport, amely a szűrést az IP-adatforrások, IP-cél, protokollok, IP-forrás portok és IP-cél portok forgalom összekötőként biztonsági szabályok listáját. Az NSG alhálózathoz, egy Azure virtuális Gépen, vagy mindkettőt társított virtuális hálózati kártya alkalmazhatók. Az NSG-k nélkülözhetetlenek a központban és a küllők megfelelő forgalomszabályozás végrehajtásához. Az NSG által biztosított biztonsági szintje, nyisson meg portokat, és milyen célból. További-a virtuális Gépenkénti szűrők például IPtables állomásalapú tűzfalaknak vagy a Windows tűzfal célszerű telepíteni.
-   **DNS**. A névfeloldás az erőforrásoknak a Vnetek egy virtualizált tartományvezérlő, DNS-en keresztül valósul meg. A virtuális hálózat névfeloldását a DNS alapértelmezett körének korlátozódik. Általában központban közös szolgáltatások részeként telepíteni kell egy egyéni DNS-szolgáltatás, de a DNS-szolgáltatások fő fogyasztóinak a küllős találhatók. Ha szükséges, az ügyfelek DNS hierarchikus tudja létrehozni a DNS-zónák a küllők delegálását.
-   [** Előfizetés] [ SubMgmt] és [erőforráscsoport felügyeleti][RGMgmt]**. Előfizetés az erőforrások több csoport létrehozása az Azure-ban a természetes határ határozza meg. Előfizetés az erőforrások erőforráscsoportok nevű logikai tárolókban lévő együtt tartanak. Az erőforráscsoport egy virtualizált tartományvezérlő erőforrások rendszerezéséhez logikai csoport jelöli.
-   [**RBAC**][RBAC]. Szerepalapú, keresztül térkép szervezeti szerepkörhöz adott Azure-erőforrások, hogy lehetővé teszi a felhasználók korlátozása csak egy bizonyos részét műveletek hozzáférjenek együtt. Az RBAC a megfelelő szerepkör hozzárendelése a felhasználók, csoportok és alkalmazások megfelelő hatókörén belül szerint engedélyezheti a hozzáférést. Szerepkör-hozzárendelés hatóköre lehet az Azure-előfizetéssel, egy erőforráscsoport vagy egy erőforrást. Az RBAC lehetővé teszi, hogy az engedélyek öröklődése. A szülő hatókörben szerepkörrel is benne a gyermekek számára engedélyezi a hozzáférést. Az RBAC használata, elkülönítse a feladatokat, és csak olyan mértékű hozzáférést biztosítania a felhasználóknak a feladataik elvégzéséhez szükséges. Például, hogy egy előfizetésben található virtuális gépek kezeléséhez, miközben egy másik SQL-adatbázisok kezelhető egyazon előfizetésen belül egy alkalmazott RBAC használatát.
-   [**VNet-társviszony létesítése –**][VNetPeering]. Az alapvető egy virtualizált tartományvezérlő infrastruktúrája létrehozásához használt szolgáltatása Vnetben társviszony-létesítés, egy olyan mechanizmus, amely összeköti a két virtuális hálózatokról (Vnetekről) ugyanabban a régióban Azure adatközpont a hálózaton keresztül.

#### <a name="component-type-perimeter-networks"></a>Összetevő típusa: Szegélyhálózat
[Szegélyhálózaton] [ DMZ] összetevők (más néven DMZ-hálózat), hogy adja meg a hálózati kapcsolat a helyszíni vagy a fizikai adatközpont-hálózatot, és az internetről érkező vagy oda irányuló kapcsolatok engedélyezése. Akkor is ahol a hálózati és biztonsági csoportok, valószínűleg a legmagasabbak az időt.

Bejövő csomagok a küllők háttér-kiszolgálóinak elérése előtt kell áramlása központban, például a tűzfal, Azonosítók és IP-CÍMEK, a biztonsági készülékek. A munkaterhelések Internet adathoz kötött csomagok kell is keresztül folyik a biztonsági készülékek a szegélyhálózaton a házirendek betartatását, vizsgálati és naplózási célokra, mielőtt elhagynák a hálózaton.

Külső hálózati összetevők az alábbi szolgáltatásokat biztosítja:

-   [Virtuális hálózatok][VNet], [UDR][UDR], [NSG][NSG]
-   [Virtuális hálózati készülékek][NVA]
-   [Terheléselosztó][ALB]
-   [Application Gateway][AppGW] / [WAF][WAF]
-   [Nyilvános IP-címek][PIP]

Általában a központi IT és biztonsági csoportok követelmény definíciója és a szegélyhálózat működésére.

[![7]][7]

A fenti ábrán két kialakítását hozzáféréssel rendelkező végrehajtásának az internetről és egy a helyszíni hálózathoz is megtalálhatók a központ a jelenít meg. Egy egyetlen helyen a peremhálózat és az internet legfeljebb LOB webalkalmazási tűzfalak (WAFs) és/vagy a tűzfalak több farmok használatával nagy mennyiségű támogatja.

[**Virtuális hálózatok** ] [ VNet] a központ általában épülő egy Vnetet, több alhálózattal szűrés, és tanulmányozza a bejövő és kimenő forgalmat az interneten keresztül NVAs, WAFs, és az Azure szolgáltatások különböző típusú üzemeltetéséhez Alkalmazásátjárót.

[**UDR** ] [ UDR] használatával UDR központi telepítését a tűzfalakra, Azonosítók/IP-címet, és más virtuális készülékek és a hálózati forgalom e biztonsági készülékek a biztonsági határ házirend betartatása keresztül naplózás, és a vizsgálat. Udr-EK a hub és garantálja, hogy a meghatározott egyéni virtuális gépek, virtuális hálózati berendezések és a virtualizált tartományvezérlő által használt terheléselosztók forgalom továbbítására küllők is létrehozhatók. Garantálja, hogy a megfelelő virtuális készülékek megtalálhatók a küllős átvitel közben a virtuális gépek létre forgalom, egy UDR kell állítani a küllős szabályzatinformációjára úgy, hogy a belső terheléselosztó előtér-IP-címét a következő ugrásként. A belső terheléselosztó osztja el a belső forgalmat a virtuális készülékek (betöltés terheléselosztó háttér-készletből).

[![8]][8]

[**Virtuális készülékekre** ] [ NVA] a központ, a peremhálózat és az internet-hozzáféréssel rendelkező általában kezelhetők a farmhoz, tűzfalak, illetve a webalkalmazási tűzfalak (WAFs).

Különböző LOB a gyakran használt sok webes alkalmazásokhoz, és ezeket az alkalmazásokat az egyes érinti a különböző biztonsági réseket és potenciális biztonsági réseket. Webes alkalmazások tűzfalak egy különös fajta annak webalkalmazásra (HTTP/HTTPS) támadások észlelésére használnak, mint egy általános tűzfal mélyebben termék. Összehasonlítja hagyomány tűzfal technológia, WAFs rendelkeznek egy belső webkiszolgálók fenyegetésekkel szembeni hatékony védelmét a funkciók.

A tűzfal farmhoz csoport tűzfalak alatt található a küllők a munkaterhelések védelme érdekében biztonsági szabályok vannak beállítva, a azonos általános felügyeleti párhuzamosan működik, amely hozzáférés a helyszíni hálózatokhoz. A tűzfal farmhoz rendelkezik kisebb kifejezetten egy WAF képest szoftver, de a szűrésére, és vizsgálja meg a hálózati forgalom kilépő és belépő bármilyen széleskörű alkalmazás hatókörrel rendelkezik. Tűzfal farmok általában segítségével valósíthatók meg az Azure-hálózat virtuális készülékeket (NVAs) érhetők el az Azure piactéren.

Javasoljuk, hogy az internetes forgalmat egy elosztott NVAs használja, és egy másik származó forgalmat a helyszíni. Biztonsági kockázatot jelent, csak egy NVAs használatával is, lehetővé teszi a hálózati forgalom két készlet között nincs biztonsági szegélyhálózati. Külön NVAs használata csökkenti a biztonsági szabályok keresése összetettségét, és törölje, mely szabályokat mely bejövő hálózati kérelem felelnek meg teszi.

Most large enterprises manage multiple domains. Azure DNS can be used to host the DNS records for a particular domain. Példa a Virtual IP Address (VIP) az Azure külső terheléselosztó (vagy a WAFs) regisztrálni lehet az Azure DNS-rekord az A rekordot.

[**Az Azure terheléselosztó** ] [ ALB] Azure terheléselosztó kínál a magas rendelkezésre állású réteg 4 (TCP, UDP) szolgáltatást, amely egy elosztott terhelésű készlet definiált szolgáltatáspéldányok között bejövő forgalom elosztását. A terheléselosztó előtér-végpontok (nyilvános IP-végpontok vagy privát IP-végpontok) a küldött forgalmat szabadon terjeszthető, vagy anélkül címfordítás, hogy egy háttér-IP-címkészlet (példák folyamatban; Virtuális hálózati berendezések vagy virtuális gépeken).

Az Azure terheléselosztó is mintavétel, valamint a különböző kiszolgálópéldányok állapotát, és egy mintavételt nem válaszol a terheléselosztó leállítja a nem megfelelő példányához olyan adatforgalmat küldenie. A virtualizált tartományvezérlő, a van egy külső terheléselosztó jelenlétét a központ (például egyenleg NVAs forgalmat), és a küllők (például egy többrétegű alkalmazást a különböző virtuális gépek közötti forgalmat terheléselosztás feladatok végrehajtásához).

[**Alkalmazásátjáró** ] [ AppGW] Microsoft Azure Application Gateway egy dedikált virtuális készülék biztosító alkalmazás kézbesítési vezérlő (LÉPETT) szolgáltatásként, az ajánlat különböző réteg 7 terheléselosztás az alkalmazás lehetőségeit. Lehetővé teszi a webkiszolgáló farm termelékenység optimalizálása kiürítésével a CPU intenzív SSL-lezárást az Alkalmazásátjáró. Ezen túlmenően egyéb 7. rétegbeli útválasztási lehetőségeket is kínál, beleértve a bejövő forgalom ciklikus időszeleteléses elosztását, a cookie-alapú munkamenet-affinitást, az URL-alapú útválasztást, valamint egyetlen Application Gateway mögött több webhelyet is üzemeltethet. Az Application Gateway WAF termékváltozata tartalmaz egy webalkalmazási tűzfalat is, A Termékváltozat védelmet biztosít a webalkalmazások közös webes biztonsági rések és biztonsági réseket. Az Application Gateway szolgáltatást internetes átjáróként, csak belső használatú átjáróként vagy a kettő kombinációjaként lehet konfigurálni. 

[**Nyilvános IP-címek** ] [ PIP] egyes Azure-szolgáltatások lehetővé teszik a rendelje hozzá a végpontok egy nyilvános IP-címet, amely lehetővé teszi, hogy az erőforrás elérését az internetről. Ezt a végpontot a forgalom irányításához a belső cím és port az Azure virtuális hálózat hálózati címfordítás (NAT) használ. Ez az elérési út külső adatforgalmát adja át a virtuális hálózat az elsődleges módszer. A nyilvános IP-címek beállítható úgy, hogy határozzák meg, mely forgalom átadott, és hol és hogyan lehet virtuális hálózati lefordítva.

#### <a name="component-type-monitoring"></a>Összetevő típusa: figyelése
Figyelési összetevők látható, és az egyéb összetevők közül riasztás adja meg. Az összes csoport rendelkezzenek hozzáféréssel az a-összetevők figyelésének és szolgáltatások hozzáférhetnek. Ha egy központi súgó ügyfélszolgálat vagy a műveletek csapat, azokat kellene férhetnek hozzá az adatokhoz, ezek az összetevők által biztosított van integrálva.

Naplózás és az Azure viselkedésének nyomon-szolgáltatások figyelésének különböző típusú Azure ajánlatok üzemeltetett erőforrásokhoz. Cégirányítási és vezérelhető a munkaterhelések az Azure-ban alapján csak a gyűjtését naplóadatokat, hanem az adott jelentésben szereplő események alapján műveleteket képes.

A naplók az Azure-ban két fő típusa van:

-   [**Tevékenységi naplóit** ] [ ActLog] (említett is, mint a "Műveleti napló") az Azure-előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak. Ezek a naplók az előfizetések vezérlő-vezérlősík eseményeinek jelentést. Minden Azure-erőforrás naplókat hoz létre.

-   [**Az Azure diagnosztikai naplók** ] [ DiagLog] erőforrás a művelet részletes, gyakori adatait adja meg az erőforrás által létrehozott naplók. Ezek a naplók tartalmának erőforrástípusok szerint változik.

[![9]][9]

A virtualizált tartományvezérlő, a rendkívül fontos nyomon követéséhez az NSG-ket naplókat, különösen ezt az információt:

-   [**Eseménynaplók**][NSGLog]: információt nyújt a virtuális gépek és a MAC-címe alapján példány szerepkörök milyen NSG-szabályok érvényesek.
-   [**A teljesítményszámláló naplók**][NSGLog]: követi nyomon, hogy hányszor egyes NSG-szabályok végre lett hajtva, a forgalom engedélyezése vagy megtagadása.

Összes naplófájlt is tárolható Azure Storage-fiókok, a naplózási, statikus elemzési vagy a biztonsági másolat létrehozása céljából. A naplók az Azure storage-fiókok vannak tárolva, amikor az ügyfelek segítségével keretrendszerek különböző típusú beolvasni, előkészítése, elemzése, és ezen adatok jelenti az állapot és a felhőben lévő erőforrások állapotának megjelenítése.

A nagyobb vállalatok szabványos keretrendszere, amely a helyszíni rendszerek figyelése már szerezték, és kiterjesztheti a keretrendszer integrálható a felhőalapú telepítések által létrehozott naplók. Olyan szervezeteknek, amelyek a naplózási tartani a felhőben [Naplóelemzési] [ LogAnalytics] kiváló választás. Naplóelemzési felhőalapú szolgáltatásként valósul meg, mert akkor is, működik, és gyorsan az infrastruktúra-szolgáltatásokat a minimális befektetési. A Naplóelemzési is integrálható a System Center-összetevők, például a System Center Operations Manager kiterjesztése a meglévő felügyeleti beruházások kiterjeszti a felhőbe.

A Naplóelemzési egy olyan szolgáltatás, amely segít gyűjtése, összefüggéseket, keresése és operációs rendszerek, alkalmazások és a felhő infrastruktúra-összetevőihez által létrehozott napló és a teljesítmény adatok intézkedjen Azure-ban. Az ügyfelek biztosít az integrált keresés és az egyéni irányítópultok a rekordok elemezni egy virtualizált tartományvezérlő a munkaterhelés, a valós idejű operational insights szolgáltatással.

#### <a name="component-type-workloads"></a>Összetevő típusa: munkaterhelések
Munkaterhelés-összetevők, ahol a tényleges alkalmazásokhoz és szolgáltatásokhoz találhatók. Akkor is ahol az alkalmazást a fejlesztési csapat legmagasabbak az időt.

A munkaterhelés lehetőségek valóban végtelen. A lehetséges számítási feladattípust néhány a következők:

**Belső ÜZLETÁGI alkalmazások**

Az üzletági alkalmazások a folyamatban lévő műveletet, a vállalatok kritikus számítógép alkalmazások. LOB-alkalmazások néhány közös jellemzőkkel rendelkezik:

-   **Interaktív**. LOB-alkalmazások rendszer természetüknél interaktív: adatok, és a visszaadott eredmény/jelentéseket.
-   **Adatalapú**. LOB-alkalmazások olyan intenzív a gyakori hozzáférést az adatbázisok vagy egyéb tárolási adatok.
-   **Integrált**. LOB-alkalmazások ajánlat integrációs belül vagy kívül a szervezet más rendszerekkel.

**Ügyfelek által használt (Internet vagy belső hozzáférhető) webhelyek** az internetes legtöbb alkalmazásokat olyan webhelyek. Azure lehetőséget nyújt a futtatásához egy webhely, az infrastruktúra-szolgáltatási virtuális gép vagy a egy [Azure Web Apps] [ WebApps] hely (PaaS). Az Azure Web Apps támogatja a Vneteket, mely lehetővé teszi a webalkalmazások telepítését az egy virtualizált tartományvezérlő küllős integrálását. Az virtuális integráció nem kell tenni az alkalmazások Internet végpont, de is inkább az erőforrások titkos nem internetes irányítható címeit a titkos virtuális hálózat.

**A big Data/Analytics** Ha adatokat növelheti a nagyon nagy, adatbázisok előfordulhat, hogy nem vertikális felskálázás megfelelően. Hadoop technológia kínál az elosztott lekérdezések csomópontok nagy száma párhuzamos futhat a rendszer. Az ügyfelek data-számítási feladatok futtatását IaaS virtuális gépeket vagy PaaS lehetőség van ([HDInsight][HDI]). HDInsight támogatja az olyan a helyalapú Vneten üzembe, a fürt egy küllős, a virtualizált tartományvezérlő is telepíthető.

**Események és üzenetekkel**
[Azure Event Hubs] [ EventHubs] kapacitású telemetriai adatfeldolgozást szolgáltatás, amely összegyűjti, átalakítja, és több millió esemény tárolja. Elosztott adatfolyam platformként biztosít alacsony késéssel és konfigurálható idő megőrzési, amely lehetővé teszi nagy mennyiségű telemetriai betöltési az Azure és, hogy adatokat olvasni több alkalmazást. Az Event Hubs egy egyetlen adatfolyam valós időben és kötegelt alapú folyamatok is támogatja.

Üzenetküldési szolgáltatás között alkalmazásokat és szolgáltatásokat, nagymértékben megbízható felhő keresztül valósítható [Azure Service Bus] [ ServiceBus] , hogy aszinkron ajánlatok közvetítőalapú üzenetküldési ügyfél és kiszolgáló között valamint strukturált első-first out (FIFO) üzenetküldési és közzétételi/előfizetési képességeket.

[![10]][10]

### <a name="multiple-vdc"></a>Több virtualizált tartományvezérlő
Ebben a cikkben eddig egy virtualizált tartományvezérlő alapvető összetevői és architektúra, amely egy rugalmas vDC hozzájárulnak leíró fordította. Azure-szolgáltatások például az Azure load balancer, NVAs, rendelkezésre állási csoportok esetében méretezési csoportok együtt más mechanizmusok hozzájárulnak a rendszer, amely lehetővé teszi a termelési szolgáltatás teli SLA szintek létrehozásához.

Egyetlen vDC azonban egyetlen régión belül található, és ki vannak téve az nagyobb leállás, amelyek hatással lehetnek a teljes régió. Szeretné, hogy magas SLA-k eléréséhez szükséges helyezni különböző régiókban két (vagy több) vDCs ugyanabban a projektben a központi telepítéséhez a szolgáltatások védelmét.

SLA vonatkozik kívül számos gyakori forgatókönyvek, ahol üzembe helyezése több vDCs szabálykészletében:

-   Területi/globális jelenlét
-   Vészhelyreállítás
-   A tartományvezérlő közötti forgalom átirányít mechanizmus

#### <a name="regionalglobal-presence"></a>Területi/globális jelenlét
Azure-adatközpont a világ számos régióban. Ha több Azure-adatközpont választja, az ügyfelek figyelembe kell vennie a két kapcsolódó tényezők: földrajzi távolság és késés mellett. Az ügyfelek kell kiértékelni a földrajzi távolsága a vDCs a virtualizált tartományvezérlő és a végfelhasználók számára, hogy a legjobb felhasználói élményt nyújtson közötti távolságot.

VDCs a rendszer hol tárolja az Azure-régióban kell is felel meg a szabályozási követelmények szerint bármely jogi joghatóság, amely alatt a szervezet működik.

#### <a name="disaster-recovery"></a>Vészhelyreállítás
A vész-helyreállítási terv végrehajtásának erősen kapcsolódó érintett munkaterhelés típusától és a munkaterhelés állapot között különböző vDCs szinkronizálhatók. Ideális esetben a legtöbb ügyfél központi telepítések megvalósításához egy gyors feladatátvételi mechanizmus két különböző vDCs futó alkalmazás adatszinkronizálás szeretné. Legtöbb alkalmazás késleltetésű különbözőnek számítanak, és, amely okozhatnak lehetséges időkorlát és a késleltetés az adatok szinkronizálását.

Szinkronizálás vagy szívverésfigyelés különböző vDCs alkalmazásai igényel a köztük folyó kommunikációt. Két, különböző régiókban vDCs keresztül csatlakozhat:

-   Az ExpressRoute magánhálózati társviszony-létesítés a virtualizált tartományvezérlő hubok csatlakoztatott ExpressRoute-kapcsolatcsoportot
-   több ExpressRoute-Kapcsolatcsoportok keresztül a vállalati gerincét csatlakoztatva, és a virtualizált tartományvezérlő háló kapcsolódik az ExpressRoute-Kapcsolatcsoportok
-   A virtualizált tartományvezérlő hubok minden Azure-régióban közötti pont-pont VPN-kapcsolatok

Általában az ExpressRoute-kapcsolat esetén az előnyben részesített mechanizmus nagyobb sávszélességet és egységes késés miatt a Microsoft gerincét áthaladó.

Nincs nincs magic módszereivel különböző régiókban található két (vagy több) különböző vDCs között elosztott alkalmazás érvényesítéséhez. Az ügyfelek hálózati minősítési tesztek ellenőrzése a késés és a sávszélesség a kapcsolatok és a célkiszolgáló megfelelő-e adatok szinkron vagy aszinkron replikáció, és mi az optimális helyreállítási idő célkitűzése (RTO) is lehet a munkaterhelések kell futtatnia.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>A tartományvezérlő közötti forgalom átirányít mechanizmus
A forgalom bejövő egy tartományvezérlő a átállításával egyik hatékony módszer a DNS-alapul. [Az Azure Traffic Manager] [ TM] át tudja irányítani a leginkább megfelelő nyilvános végpontot a végfelhasználói forgalmat egy adott vDC a a tartománynévrendszer (DNS) mechanizmust alkalmaz. Keresztül mintavételek menüpontban a Traffic Manager rendszeresen ellenőrzi a szolgáltatás állapotát a különböző vDCs nyilvános végpontok, és ezekre a végpontokra meghiúsulásának, esetén pedig küldi automatikusan a másodlagos virtualizált tartományvezérlő.

A TRAFFIC Manager működik-e az Azure nyilvános végpontok, és használható, például a megfelelő vDC Azure virtuális gépek és a Web Apps forgalom vezérlő/átirányít. A TRAFFIC Manager is lehetséges, akár egy teljes Azure-régiót hibás állásuk, és szabályozhatja a felhasználó forgalmat a végpontok a különböző vDCs (például sikertelen volt-e egy adott virtualizált tartományvezérlő, vagy válassza a virtualizált tartományvezérlő szolgáltatás több feltétel alapján az a legkisebb hálózati késést a ügyfél számára).

### <a name="conclusion"></a>Összegzés
A virtuális adatközpont egy data center áttelepítésének módjáról a felhőbe, amely a méretezhető architektúra létrehozása az Azure-ban, amely maximalizálja a felhőalapú erőforrások használatával, csökkenti a költségeket, és egyszerűsítse a rendszer irányítás funkciók és képességek kombinációját alkalmazza. A virtualizált tartományvezérlő koncepció hub-küllők topológia, közös megosztott szolgáltatások központban, és lehetővé teszi bizonyos alkalmazások vagy munkaterhelések az a küllők alapul. A virtualizált tartományvezérlő vállalati szerepkörök, ahol különböző részlegek számára (központi informatikai, DevOps, üzemeltetése és karbantartása) működnek együtt, egy adott szerepkörök listáját és feladatokat az szerkezete megfelel. A virtualizált tartományvezérlő megfelel az "Növekedési és Shift" áttelepítéshez, de emellett az számos előnye natív felhőben történő alkalmazáshoz.

## <a name="references"></a>Referencia
A következő funkciókat a dokumentumban ismertetett volt. További hivatkozásokra kattintva.

| | | |
|-|-|-|
|Hálózati szolgáltatások|Terheléselosztás|Kapcsolatok|
|[Egy Azure virtuális hálózatot][VNet]</br>[Hálózati biztonsági csoportok][NSG]</br>[NSG-naplók][NSGLog]</br>[Felhasználó által definiált Útválasztás][UDR]</br>[Virtuális hálózati készülékek][NVA]</br>[Nyilvános IP-címek][PIP]|[Azure Load Balancer (3.) ][ALB]</br>[Az Alkalmazásátjáró (7.) ][AppGW]</br>[Webalkalmazási tűzfal][WAF]</br>[Az Azure Traffic Manager][TM] |[VNet-társviszony létesítése –][VNetPeering]</br>[Virtuális magánhálózat][VPN]</br>[ExpressRoute][ExR]
|Identitás</br>|Figyelés</br>|Ajánlott eljárások</br>|
|[Azure Active Directory][AAD]</br>[Többtényezős hitelesítés][MFA]</br>[Szerepkör alap hozzáférés-vezérlést][RBAC]</br>[Alapértelmezett AAD-szerepkörök][Roles] |[Tevékenység-naplók][ActLog]</br>[Diagnosztikai naplók][DiagLog]</br>[A Naplóelemzési][LogAnalytics]</br> |[Külső hálózatok gyakorlati tanácsok][DMZ]</br>[Előfizetés-kezelés][SubMgmt]</br>[Erőforrás-csoportok kezelése][RGMgmt]</br>[Azure-előfizetésre vonatkozó korlátok][Limits] |
|Más Azure-szolgáltatásokkal|
|[Azure Web Apps][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Szolgáltatásbusz][ServiceBus]|



## <a name="next-steps"></a>További lépések
 - Fedezze fel [Vnetben társviszony-létesítés][VNetPeering], a megerősítő technológiák a virtualizált tartományvezérlő küllős tervek
 - Alkalmazzon [AAD] [ AAD] használatába [RBAC] [ RBAC] feltárása
 - A előfizetésbe és erőforráscsoportba felügyeleti modellezése, és Szerepalapú teljesítéséhez a struktúra, a követelmények, a modell, és a szervezet házirendeket. A legfontosabb tevékenység tervezi. Minél nagyobb gyakorlati tervezze meg a átszervezések, Összevonások, új sorokban, stb.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "összetevő átfedés példák" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "magas szintű küllős vDC – példa"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "hubok és küllők fürt"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "Spoke-to-spoke"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "blokk szint a virtualizált tartományvezérlő ábrája"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "felhasználók, csoportok, előfizetések és projektek"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "magas szintű infrastruktúra diagramja"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "magas szintű infrastruktúra diagramja"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "Vnetben társviszony-létesítést és a külső hálózatok"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "figyelés magas szintű diagramja"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "az alkalmazások és szolgáltatások magas szintű diagramját"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg 
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[LogAnalytics]: https://docs.microsoft.com/azure/log-analytics/log-analytics-overview
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
