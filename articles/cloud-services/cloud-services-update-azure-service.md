---
title: "Egy felhőalapú szolgáltatás frissítése |} Microsoft Docs"
description: "Útmutató az Azure felhőszolgáltatások frissítése. Ismerje meg, hogyan egy frissítés egy felhőalapú szolgáltatás rendelkezésre állásának biztosításához folytatódik."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 2ba9676ed2afce7f18446642527971f5001b5ca7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-update-a-cloud-service"></a>Egy felhőalapú szolgáltatás frissítése

Frissítés egy felhőalapú szolgáltatás, beleértve a szerepkörök és a vendég operációs rendszer, akkor a három lépésben. Először a bináris fájljait és a konfigurációs fájlokat az új felhőalapú szolgáltatás vagy az operációs rendszer verziója fel kell tölteni. A következő Azure számítási és hálózati erőforrásokat a felhőalapú szolgáltatás, az új felhőalapú szolgáltatás verzió követelményei alapján foglalja le. Végül az Azure-ban a működés közbeni frissítés Növekményesen frissíteni a bérlő az új verzióra, vagy a vendég operációs rendszer, a rendelkezésre állás megőrzése mellett. Ez a cikk ismerteti, amelyek ezen utolsó lépésében – a működés közbeni frissítés részletes adatait.

## <a name="update-an-azure-service"></a>Frissítés az Azure-szolgáltatások
Azure rendezi a szerepkörpéldányok frissítési tartományok (UD) nevű logikai csoportosítással. Frissítési tartományok (UD), amelyeket logikai szerepkörpéldányt beállítani, hogy a csoport frissítése.  Az Azure töröl egy felhőalapú szolgáltatás egy UD egyszerre, így példányok más UDs szolgál a forgalom a folytatáshoz.

Az alapértelmezett frissítési tartományok száma érték az 5. A szolgáltatás definíciós fájl (.csdef) belefoglalja a upgradeDomainCount attribútum különböző hány frissítési tartományt is megadhat. A upgradeDomainCount attribútum kapcsolatos további információkért lásd: [webrole típusról séma](https://msdn.microsoft.com/library/azure/gg557553.aspx) vagy [WorkerRole séma](https://msdn.microsoft.com/library/azure/gg557552.aspx).

A szolgáltatás egy vagy több szerepkör helybeni frissítést hajt végre, amikor Azure frissítések beállítása szerint a frissítési tartomány, amelyhez tartoznak szerepkörpéldányt beállítani. Azure-példánya a megadott frissítési tartomány – leállítása, frissítése, állapotba hozza őket az összes online – biztonsági frissítések, a következő tartomány helyezi. Csak a jelenlegi frissítési tartománya futó példányok leállításával Azure gondoskodik arról, hogy egy frissítés során fordul elő a lehető legkisebb hatással legyen a futó szolgáltatásokra. További információkért lásd: [hogyan eltérő lehet a frissítés](#howanupgradeproceeds) című cikkben.

> [!NOTE]
> A feltételek során **frissítése** és **frissítése** némileg eltérő jelentéssel bírnak az Azure környezetben, akkor felcserélhetők a folyamatok és a jelen dokumentum a szolgáltatások leírása.
>
>

A szolgáltatás meg kell adnia legalább két példánya egy adott szerepkör frissítenie kell a helyszíni állásidő nélkül. Ha a szolgáltatás egy szerepkörnek csak egy példánya, a szolgáltatás nem lehet a helyben frissítés befejeződött.

Ez a témakör ismerteti az Azure-hírt kapcsolatos alábbi információkat:

* [A szolgáltatás módosítása engedélyezett frissítése közben](#AllowedChanges)
* [Hogyan egy frissítés előrehalad az](#howanupgradeproceeds)
* [Frissítés visszaállítása](#RollbackofanUpdate)
* [Az egy folyamatban lévő telepítés több mutating művelet kezdeményezése](#multiplemutatingoperations)
* [Kapcsolódó szerepkörök frissítési tartományok között](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>A szolgáltatás módosítása engedélyezett frissítése közben
Az alábbi táblázat az engedélyezett végrehajtott módosításokat a frissítés közben:

| Üzemeltetési, szolgáltatások és szerepkörök engedélyezi a módosításokat | Frissítés helyben | Előkészített (virtuális IP-címcsere) | Törölje és hozza létre |
| --- | --- | --- | --- |
| Operációs rendszer verziója |Igen |Igen |Igen |
| .NET megbízhatósági szintjét. |Igen |Igen |Igen |
| Virtuálisgép-méret<sup>1</sup> |Igen<sup>2</sup> |Igen |Igen |
| Helyi tárolási beállításai |Csak növelése<sup>2</sup> |Igen |Igen |
| Hozzáadása vagy eltávolítása a szerepkörök szolgáltatás |Igen |Igen |Igen |
| Egy adott szerepkör példányainak száma |Igen |Igen |Igen |
| Vagy a szolgáltatás végpontjainak típus |Igen<sup>2</sup> |Nem |Igen |
| Neveit és értékeit a konfigurációs beállítások |Igen |Igen |Igen |
| Értékek (de nem nevek) a konfigurációs beállítások |Igen |Igen |Igen |
| Hozzáadhat új tanúsítványokat |Igen |Igen |Igen |
| Meglévő tanúsítványok módosítása |Igen |Igen |Igen |
| Új kód telepítése |Igen |Igen |Igen |

<sup>1</sup> méretének módosítása a felhőalapú szolgáltatáshoz elérhető méretek részhalmazát korlátozódik.

<sup>2</sup> szükséges Azure SDK 1.5 vagy újabb verzió.

> [!WARNING]
> A virtuális gép méretének módosítása törlődnek a helyi adatokat.
>
>

A következő elemek nem támogatottak a frissítés közben:

* A szerepkör nevének módosítása. Távolítsa el, és adja hozzá a szerepkört az új nevet.
* A frissítési tartományok számának módosításához.
* A helyi erőforrások méretének csökkentésével.

Ha a szolgáltatás-definíció, például a helyi erőforrás méretének csökkentésével kapcsolatos egyéb frissítésekről a VIP-csere frissítése helyette kell elvégeznie. További információkért lásd: [felcserélése telepítési](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hogyan egy frissítés előrehalad az
Eldöntheti, hogy szeretné-e frissíti az összes, a szerepkörök, a szolgáltatás vagy a szolgáltatás egyetlen szerepkör. Mindkét esetben minden frissítés alatt áll, és ugyanahhoz az első frissítési tartomány szerepkör példányainak leállt, frissítése, és automatikusan visszaáll online állapotba. Miután ismét online állapotban, a második frissítési tartomány a példányok leállt, frissítése, és automatikusan visszaáll online állapotba. Egy felhőalapú szolgáltatás lehet aktív egyszerre legfeljebb egy frissítés. Mindig megtörténik a frissítés a legújabb verzióra a felhőalapú szolgáltatás ellen.

A következő diagram azt ábrázolja, hogyan a frissítés előrehalad az összes szolgáltatási szerepkör frissítése esetén:

![Service frissítése](media/cloud-services-update-azure-service/IC345879.png "service frissítése")

A következő diagram azt ábrázolja, hogyan lehet a frissítés, hogy csak egy szerepkör frissíti:

![Frissítési szerepkör](media/cloud-services-update-azure-service/IC345880.png "frissítési szerepkör")  

Az automatikus frissítése közben a Azure Fabric Controller rendszeresen kiértékeli a felhőszolgáltatással az határozható meg, hogy biztonságosan ismerteti a következő UD állapotát. Az állapot kiértékelésekor szerepkör alapon történik, és úgy ítéli meg, csak a legújabb verzió (azaz példányok, amely már telefonon UDs) példányokat. Ellenőrzi, hogy a szerepkörpéldányok, az egyes szerepkörökhöz minimális száma elérte a kielégítő terminál állapotba.

### <a name="role-instance-start-timeout"></a>Szerepkör példány Start időtúllépés
A Fabric Controller Várjon 30 perc minden szerepkörpéldányhoz állapotot elérni a elindítva. Ha az időkorlát tartama eltelte után a Fabric Controller továbbra is a következő szerepkör-példányra érdekében.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Frissíti a meghajtó adatainak során a felhőalapú szolgáltatás gyakorolt hatás

A szolgáltatás a egyetlen példányából származó több példány való frissítéskor a szolgáltatás kerül a szolgáltatásokat milyen módon Azure frissítések miatt megtörténik a frissítés során. A szolgáltatás szolgáltatásiszint-szerződés garanciavállaló szolgáltatás rendelkezésre állása csak érvényes, ha egynél több példánya telepített szolgáltatások. Az alábbi lista ismerteti, hogyan befolyásolják az egyes Azure-szolgáltatások frissítési forgatókönyv minden meghajtón található adatokat:

|Forgatókönyv|C meghajtó|D meghajtó|E meghajtó|
|--------|-------|-------|-------|
|Virtuális gép újraindítása|Megőrzi|Megőrzi|Megőrzi|
|Portál újraindítás|Megőrzi|Megőrzi|Megsemmisül|
|Lemezkép-visszaállítási portál|Megőrzi|Megsemmisül|Megsemmisül|
|Frissítés helyben|Megőrzi|Megőrzi|Megsemmisül|
|Csomópont áttelepítése|Megsemmisül|Megsemmisül|Megsemmisül|

Vegye figyelembe, hogy a fenti listában, a E: meghajtót jelenti. a szerepkör gyökérmeghajtóján, és nem lehet változtatható. Ehelyett használja a **RoleRoot %** környezeti változót határoz meg a meghajtót.

Az állásidő minimálisra csökkentése érdekében, egy egypéldányos szolgáltatás frissítésekor, új többpéldányos szolgáltatás központi telepítése az átmeneti kiszolgálón, és végezze el a virtuális IP-címcsere.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Frissítés visszaállítása
Azure frissítés közben szolgáltatások kezelése azáltal, hogy kezdeményezni egy szolgáltatás, további műveleteket, miután a kezdeti frissítési kérelem elfogadta a Azure Fabric Controller rugalmasságot biztosít. Csak akkor hajtható végre a visszaállítást, ha egy frissítés (konfigurációváltozás) vagy a frissítés megtalálható a **folyamatban** állapot az üzemelő példányon. Egy frissítést, illetve tekinthető folyamatban mindaddig, amíg a szolgáltatás, amely még nincs frissítve az új verzióra legalább egy példánya. Annak megállapítására, hogy a visszaállítás engedélyezett, ellenőrizze a RollbackAllowed jelző által visszaadott értékének [első központi telepítési](https://msdn.microsoft.com/library/azure/ee460804.aspx) és [felhőalapú szolgáltatás tulajdonságainak beolvasása](https://msdn.microsoft.com/library/azure/ee460806.aspx) műveletek, értéke TRUE.

> [!NOTE]
> Csak az így visszaállítási hívni egy **helyben** frissítési vagy verziófrissítési, mert a VIP-csere frissítéseket tartalmaz, amely a szolgáltatás egy teljes futó példányát cseréje egy másik.
>
>

A folyamatban lévő frissítés visszaállítása a következő hatásai vannak az üzemelő példányon rendelkezik:

* Bármely szerepkörpéldányok, amelyen korábban még nem frissített vagy új verzióra frissíteni nem frissítése vagy frissítés, mert azokat a példányokat már fut a szolgáltatás a cél verzióját.
* A szerepkör-példányok rendelkezett már frissítése vagy a service-csomag új verzióra frissíteni (\*.cspkg) fájl vagy a szolgáltatás konfigurációs (\*.cscfg) fájl (vagy mindkét fájl) ezeket a fájlokat a frissítés előtti verzióját állnak vissza.

Ez gyakorlatilag biztosítja a következő szolgáltatásokat:

* A [visszaállítási módosítása vagy frissítése](https://msdn.microsoft.com/library/azure/hh403977.aspx) művelet, amely nem hívható meg a konfiguráció frissítése a (meghívásával indított [telepítési konfiguráció módosítása](https://msdn.microsoft.com/library/azure/ee460809.aspx)) vagy a verziófrissítésre (meghívásával indított [ Frissítés telepítési](https://msdn.microsoft.com/library/azure/ee460793.aspx)), amíg a szolgáltatás, amely még nincs frissítve az új verzióra van legalább egy példányát.
* A zárolt elemet, és a RollbackAllowed elem válasz törzsében részeként visszaküldött a [első központi telepítési](https://msdn.microsoft.com/library/azure/ee460804.aspx) és [felhőalapú szolgáltatás tulajdonságainak beolvasása](https://msdn.microsoft.com/library/azure/ee460806.aspx) műveletek:

  1. A zárolt elem észlelés, ha egy mutating művelet a megadott központi telepítés is elindítható teszi lehetővé.
  2. A RollbackAllowed elem lehetővé teszi, hogy mikor a [visszaállítás frissítés vagy frissítési](https://msdn.microsoft.com/library/azure/hh403977.aspx) művelet nem hívható meg egy adott központi telepítés a.

  A visszaállítás végrehajtásához nincs a zárolt, mind a RollbackAllowed elemek kereséséhez. Elegendő, ha a győződjön meg arról, hogy RollbackAllowed értéke TRUE. Ezek az elemek csak adott vissza, ha ezek a módszerek hívják használata a kérelem fejléc értéke "x-ms-version: 2011-10-01" vagy egy újabb verziója. Versioning fejlécek kapcsolatos további információkért lásd: [Service Management Versioning](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Vannak olyan helyzetek, ahol egy frissítést a visszaállítás vagy a frissítés nem támogatott, ezek a következők:

* Helyi erőforrások – Ha a frissítés növeli a helyi erőforrások az Azure platformon szerepkör csökkentése nem engedélyezi a visszaállítása.
* Sablonkérelem -, ha a frissítési művelet már nem lehet egy vertikális rendelkezik-e elegendő számítási kvótával a visszaállítási művelet befejezéséhez. Minden Azure-előfizetéssel rendelkezik kvóta társítva, amely meghatározza a maximális számát mag, amely szerint az adott előfizetéshez tartozó összes üzemeltetett szolgáltatás képes használni. A visszaállítás egy adott frissítés végrehajtása után kerülne az előfizetés keresztül kvóta, majd a visszaállítás nem engedélyezhető.
* Versenyhelyzet - Ha a kezdeti frissítése befejeződött, a visszaállítás nincs lehetőség.

Ha egy frissítés visszaállításának hasznosak lehetnek példa: Ha az a [frissítés telepítési](https://msdn.microsoft.com/library/azure/ee460793.aspx) művelet átviteli sebességét, ahol a fő helybeni frissítést Azure üzemeltetett szolgáltatás manuális üzemmódban lesz állítva.

A frissítés a bevezetés alatt hívható [frissítés telepítési](https://msdn.microsoft.com/library/azure/ee460793.aspx) manuális üzemmódban és veszik át a frissítési tartományok ismerteti. Ha bármikor, ahogy figyeli a frissítést, vegye figyelembe az első frissítési tartományok, amely megvizsgálja az egyes szerepkörpéldányokat válaszol, akkor meghívhatja a [visszaállítás frissítés vagy frissítési](https://msdn.microsoft.com/library/azure/hh403977.aspx) marad központi telepítési művelet a példányok kellett még nincsenek frissítve és visszaállítási példányok volt frissítve az előző service-csomag és a konfigurációs érintetlenül.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Az egy folyamatban lévő telepítés több mutating művelet kezdeményezése
Bizonyos esetekben érdemes lehet egy folyamatban lévő telepítés több egyidejű mutating műveletek kezdeményezésére. Például szolgáltatásfrissítés hajthat végre és, amíg a szolgáltatásban, ez a frissítés van megkezdődött, szeretne módosítást bizonyos, például állíthatja vissza a frissítést, egy másik frissítés, vagy akkor törölje a központi telepítést. Egy esetet, ahol erre akkor lehet szükség, ha a szolgáltatás frissítése egy frissített szerepkörpéldányon ismételten összeomlás okozó buggy kódját tartalmazza-e. Ebben az esetben az Azure Fabric Controller nem tudnak halad, az alkalmazásában, amelyet frissíteni, mivel a példányok a frissített tartományban elegendő számú kifogástalan. Ebben az állapotban nevezzük egy *elakadt a központi telepítési*. A frissítés visszaállítása vagy egy friss frissítés alkalmazása a sikertelen első keresztül is követően a a központi telepítés egy.

Miután az Azure Fabric Controller frissítéséhez, vagy frissítse a szolgáltatást az eredeti kérelem érkezett, elindíthatja a későbbi szótárból elemeinek módosítása műveletek. Ez azt jelenti, hogy nem rendelkezik a kezdeti művelet elvégzéséhez, egy másik mutating művelet megkezdése előtt várjon.

A második-frissítési művelet kezdeményezése, amíg az első frissítés folyamatban lévő hasonló a visszaállítási műveletet hajt végre. Ha a második frissítés automatikus üzemmódban van, az első frissítési tartomány lesz frissítve azonnal, valószínűleg vezető példányok nem kapcsolódott ugyanabban az idő több frissítési tartományt.

A mutating műveletek a következők: [telepítési konfiguráció módosítása](https://msdn.microsoft.com/library/azure/ee460809.aspx), [frissítés telepítési](https://msdn.microsoft.com/library/azure/ee460793.aspx), [központi telepítésének állapota](https://msdn.microsoft.com/library/azure/ee460808.aspx), [telepítési törlése ](https://msdn.microsoft.com/library/azure/ee460815.aspx), és [visszaállítási vagy Verziófrissítésekor](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Két műveletek [első központi telepítési](https://msdn.microsoft.com/library/azure/ee460804.aspx) és [felhőalapú szolgáltatás tulajdonságainak beolvasása](https://msdn.microsoft.com/library/azure/ee460806.aspx), térjen vissza a zárolt jelzőt, amely képes vizsgálja meg, hogy mutating művelet a megadott központi telepítés is elindítható.

Ahhoz, hogy ezek a módszerek a verziója, ami visszaadja a zárolt jelző hívja, be kell kérelemfejléc "x-ms-version: 2011-10-01" vagy egy újabb verziója. Versioning fejlécek kapcsolatos további információkért lásd: [Service Management Versioning](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Kapcsolódó szerepkörök frissítési tartományok között
Azure példánya egy egyenletesen elosztja a készlet hány frissítési tartományt, a szolgáltatás definíciós (.csdef) fájl részeként állítható be. A frissítési tartományok maximális száma: 20, és az alapértelmezett érték 5. A szolgáltatásdefiníciós fájl módosításával kapcsolatos további információkért lásd: [Azure szolgáltatás definíciós séma (.csdef fájl)](cloud-services-model-and-package.md#csdef).

Például ha a szerepkör tíz példánnyal rendelkezik, alapértelmezés szerint minden frissítési tartomány két példányait tartalmazza. Ha a szerepkör 14 példánnyal rendelkezik, majd négy a frissítési tartományok három példányokat tartalmazhatják, és ötödik tartomány két tartalmazza.

Frissítési tartományok azonosítják a nulla alapú indexét: az első frissítési tartomány 0 Azonosítót tartalmaz, és a második frissítési tartomány 1 és így tovább Azonosítót.

A következő ábra bemutatja, hogyan két szerepköröket tartalmazza, mint szolgáltatás terjessze a szolgáltatás két frissítési tartományt határoz meg. A szolgáltatás fut a webes szerepkör nyolc példányai és a feldolgozói szerepkör kilenc példányai.

![A frissítési tartományok terjesztési](media/cloud-services-update-azure-service/IC345533.png "frissítési tartományok terjesztése")

> [!NOTE]
> Vegye figyelembe, hogy az Azure vezérlők példányok frissítési tartományok közötti elosztását vezérli. Nincs lehetőség a adja meg, melyik tartománya számára kiosztott előfordulások.
>
>

## <a name="next-steps"></a>Következő lépések
[A Cloud Services felügyelete](cloud-services-how-to-manage.md)  
[Felhőszolgáltatások figyelése](cloud-services-how-to-monitor.md)  
[A Cloud Services Konfigurálása](cloud-services-how-to-configure.md)  
