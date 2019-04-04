---
title: Cloud service frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissíthető a cloud services, Azure-ban. Ismerje meg, hogyan egy felhőalapú szolgáltatás frissítésének abból rendelkezésre állásának biztosításához.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ff4dd571911719e4f2ec27952785432960a56d42
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917226"
---
# <a name="how-to-update-a-cloud-service"></a>Egy felhőalapú szolgáltatás frissítése

Frissítés a felhőszolgáltatás, beleértve a szerepkörök és a vendég operációs rendszer, akkor egy három lépésből álló folyamat. A bináris fájljait és az új felhőalapú szolgáltatás vagy az operációs rendszer verziója a konfigurációs fájlokat először fel kell tölteni. Ezután az Azure lefoglalja a számítási és hálózati erőforrásokat a felhőalapú szolgáltatás, az új verzió a felhőalapú szolgáltatás követelményei alapján. Végül az Azure-Növekményesen frissíteni a bérlő az új, illetve a vendég operációs rendszer, hogy a rendelkezésre állás megőrzése működés közbeni frissítésének végzi el. Ez a cikk ismerteti az utolsó lépés – a működés közbeni frissítés részleteit.

## <a name="update-an-azure-service"></a>Frissítés az Azure-szolgáltatások
Az Azure a szerepkörpéldányok frissítési tartományok (UD) nevű logikai csoportokba rendezik. Frissítési tartományok (UD), amelyeket logikai csoportként frissített szerepkörpéldányt.  Egy felhőbeli Azure-frissítések egy UD szolgáltatás egyszerre, amely lehetővé teszi a példány folytatja a forgalmat bonyolító más frissítési tartománnyal.

Az alapértelmezett frissítési tartományok száma érték az 5. Megadhat egy másik frissítési tartományok száma az upgradeDomainCount attribútum együtt a szolgáltatás definíciós fájl (.csdef). A upgradeDomainCount attribútum kapcsolatos további információkért lásd: [WebRole séma](/previous-versions/azure/reference/gg557553(v=azure.100)) vagy [WorkerRole séma](/previous-versions/azure/reference/gg557552(v=azure.100)).

A szolgáltatás egy vagy több szerepkör helybeni frissítést hajt végre, ha az Azure frissíti a csoportok szerepkörpéldányt a frissítési tartomány, amelyhez tartoznak. Azure-frissítések összes példánya a megadott frissítési tartomány – leállítása, frissítése, hozza őket – online biztonsági majd helyez be a következő tartomány. Csak a példányai a jelenlegi frissítési tartománya leállításával Azure gondoskodik arról, hogy egy frissítés akkor fordul elő, a lehető legkisebb hatással legyen a futó szolgáltatásokra. További információkért lásd: [hogyan halad a frissítés](#howanupgradeproceeds) a cikk későbbi részében.

> [!NOTE]
> Miközben a használati **frissítése** és **frissítése** némileg eltérő jelentéssel rendelkeznek Azure környezetben, azok felcserélhetők a folyamatok és a jelen dokumentum funkcióinak leírása.
>
>

A szolgáltatás meg kell határoznia legalább két példányt frissíteni kell a szerepkör egy szerepkör helyi üzemkimaradás nélkül. Ha a szolgáltatás tartalmaz egy szerepkörnek csak egy példánya, a szolgáltatás nem lehet a helyben frissítés befejeződött.

Ez a témakör ismerteti az Azure-frissítések a következő információkat:

* [Frissítés közben a szolgáltatás módosítása engedélyezett](#AllowedChanges)
* [Hogyan egy frissítés előrehalad az](#howanupgradeproceeds)
* [Frissítés visszaállítása](#RollbackofanUpdate)
* [Egy folyamatos üzembe helyezés több mutating műveletek indítása](#multiplemutatingoperations)
* [Frissítési tartományok között szerepkörök terjesztése](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Frissítés közben a szolgáltatás módosítása engedélyezett
Az alábbi táblázatban láthatók engedélyezett egy szolgáltatás frissítése közben:

| Módosítások üzemeltető, szolgáltatásokat és szerepköröket engedélyezett | Frissítés helyben | A kétlépcsős (virtuális IP-címcsere) | Törlés és ismételt üzembe helyezése |
| --- | --- | --- | --- |
| Operációs rendszer verziója |Igen |Igen |Igen |
| .NET megbízhatósági szint |Igen |Igen |Igen |
| Virtuálisgép-méret<sup>1</sup> |Igen<sup>2</sup> |Igen |Igen |
| Helyi tároló beállításai |Csak növelni<sup>2</sup> |Igen |Igen |
| Hozzáadása vagy eltávolítása a szerepkörök a szolgáltatás |Igen |Igen |Igen |
| Egy adott szerepkör példányainak száma |Igen |Igen |Igen |
| Vagy egy szolgáltatás végpontjainak típus |Igen<sup>2</sup> |Nem |Igen |
| Nevei és értékei a konfigurációs beállítások |Igen |Igen |Igen |
| Értékek (de nem neveket) konfigurációs beállítások |Igen |Igen |Igen |
| Hozzáadhat új tanúsítványokat |Igen |Igen |Igen |
| Létező tanúsítványok módosítása |Igen |Igen |Igen |
| Új kód üzembe helyezése |Igen |Igen |Igen |

<sup>1</sup> mérete korlátozott részhalmaza alapján elérhető, a felhőszolgáltatás-méretek módosítása.

<sup>2</sup> igényel az Azure SDK 1.5 vagy újabb verzió.

> [!WARNING]
> A virtuális gép méretének módosítása megsemmisíti a helyi adatokat.
>
>

Frissítés közben nem támogatott a következő elemek:

* A szerepkör nevének módosítása. Távolítsa el, majd adja hozzá a szerepkört az új névre.
* A frissítési tartományok száma megváltoztatása.
* A helyi erőforrások mérete csökken.

Ha kapcsolatos egyéb frissítésekről a szolgáltatás definíciós, például a helyi erőforrás méretét ehelyett egy virtuális IP-CÍMEK lapozófájl-kapacitás frissítést kell végrehajtania. További információkért lásd: [felcserélése telepítési](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hogyan egy frissítés előrehalad az
Eldöntheti, hogy szeretné-e a szerepkörök a szolgáltatási vagy a szolgáltatás egyetlen szerepkörben az összes frissítése. Mindkét esetben minden egyes szerepkör, amely frissítés alatt áll, és ugyanahhoz az első frissítési tartomány összes példánya leállt, frissítve, és újra online állapotba. K online állapotba, a második frissítési tartományban található példányokhoz leállt, frissítve, és online állapotba kerüléséig. Egy felhőalapú szolgáltatás legfeljebb egy frissítés aktív egyszerre is rendelkezhet. A frissítés a rendszer mindig elvégzi a legújabb verzióra a felhőszolgáltatás ellen.

A következő ábra szemlélteti, hogy a frissítés előrehalad az összes szerepkört a szolgáltatás frissítése esetén:

![Szolgáltatás frissítése](media/cloud-services-update-azure-service/IC345879.png "service frissítése")

A következő ábra mutatja be, hogyan halad a frissítés, ha csak egy szerepkör:

![Frissítési szerepkör](media/cloud-services-update-azure-service/IC345880.png "frissítési szerepkör")  

Az automatikus frissítése közben az Azure Fabric Controller rendszeresen kiértékeli a felhőszolgáltatás határozható meg, hogy biztonságosan megtudhatja, hogyan végzi a következő UD állapotát. Ez állapotának kiértékelését szerepkörönkénti alapon történik, és úgy ítéli meg, csak a példányokat a legújabb verzió (azaz a frissítési tartománnyal, amely rendelkezik már üzlet elévült példányok). Ellenőrzi, hogy legalább a megadott számú szerepkörpéldányt, az egyes szerepkörökhöz el a megfelelő állapotot.

### <a name="role-instance-start-timeout"></a>Szerepkör példány indítása időtúllépés
A Hálóvezérlő minden egyes szerepkör-példány egy elindítva állapot eléréséhez 30 percet várakozik. Ha az időtúllépési időtartam lejárta, a Hálóvezérlő továbbra is a következő szerepkörpéldány a the walking.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Hatással van a Felhőszolgáltatás alatt meghajtó adatainak frissítése

Ha szolgáltatás frissítése egy példányból több példányra a szolgáltatás kerül, amíg megtörténik a frissítés módja az Azure-frissítések szolgáltatások miatt. A szolgáltatás szolgáltatásiszint-szerződés garanciavállaló szolgáltatás rendelkezésre állása csak érvényes, ha egynél több példánya telepített szolgáltatások. Az alábbi lista ismerteti, hogyan befolyásolják az egyes Azure-szolgáltatás frissítési forgatókönyv az adatok minden olyan meghajtó:

|Forgatókönyv|C Drive|D Drive|E Drive|
|--------|-------|-------|-------|
|Virtuális gép újraindítása|Megmaradnak|Megmaradnak|Megmaradnak|
|Portál újraindítás|Megmaradnak|Megmaradnak|Megsemmisül|
|Portál reimage|Megmaradnak|Megsemmisül|Megsemmisül|
|Frissítés helyben|Megmaradnak|Megmaradnak|Megsemmisül|
|Csomópont-áttelepítés|Megsemmisül|Megsemmisül|Megsemmisül|

Vegye figyelembe, hogy a fenti listában, az e meghajtón a szerepkör gyökérmeghajtóján jelöli, és nem szabad változtatható. Ehelyett használja a **RoleRoot %** környezeti változót, amely a meghajtót jelöli.

Az állásidő minimálisra csökkentése érdekében, amikor egypéldányos szolgáltatás frissítése, új többpéldányos szolgáltatás központi telepítése az átmeneti kiszolgálón, és hajtsa végre a virtuális IP-címcsere.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Frissítés visszaállítása
Az Azure-szolgáltatások kezeléséhez frissítés közben azáltal, hogy egy szolgáltatást, további műveleteket kezdeményezheti, miután a kezdeti frissítési kérelmet elfogadta az Azure Fabric Controller rugalmasságot biztosít. Csak akkor hajtható végre egy visszaállítása, ha egy frissítés (konfigurációváltozás) vagy a frissítés szerepel a **folyamatban** állapot az üzemelő példányon. Frissítés vagy verziófrissítés minősül folyamatban mindaddig, amíg a szolgáltatás, amely még nem lett frissítve az új verzió legalább egy példánya. Annak megállapítására, hogy a visszaállítás engedélyezett-e, ellenőrizze a RollbackAllowed jelző, által visszaadott értékének [első üzembe helyezés](/previous-versions/azure/reference/ee460804(v=azure.100)) és [felhőalapú szolgáltatás tulajdonságainak lekérése](/previous-versions/azure/reference/ee460806(v=azure.100)) műveletek, értéke igaz.

> [!NOTE]
> Csak logikus visszaállítási hívja meg az **helyben** frissítése és verziófrissítése, mert a virtuális IP-CÍMEK lapozófájl-kapacitás frissítések között, és cserélje le a szolgáltatás egy teljes üzemelő példányt egy másik.
>
>

Visszaállítás folyamatban lévő frissítés a következő hatásai vannak az üzemelő példányon van:

* Minden korábban még nem frissített vagy új verzióra frissíteni szerepkörpéldányok nem frissíthető, illetve frissítve, mert ezekhez a példányokhoz már fut a szolgáltatás a cél verzióját.
* Bármely szerepkörpéldányok rendelkezett már frissítve, vagy a csomag új verziójának frissítése (\*.cspkg) fájl vagy a szolgáltatás konfigurációs (\*.cscfg) fájl (vagy mindkét fájlt) visszavonja ezeket a fájlokat a frissítés előtti verziójú.

Ez funkcionálisan biztosítják a következő funkciókat:

* A [visszaállítás frissítés vagy frissítési](/previous-versions/azure/reference/hh403977(v=azure.100)) művelet, amely konfigurációs frissítési (meghívásával aktivált [telepítési konfiguráció módosítása](/previous-versions/azure/reference/ee460809(v=azure.100))) vagy a verziófrissítésre (meghívásával aktivált [ Frissítés telepítési](/previous-versions/azure/reference/ee460793(v=azure.100))) mindaddig, amíg a szolgáltatás, amely még nem lett frissítve az új verzió legalább egy példánya szerepel.
* A zárolt elem és a RollbackAllowed elem, amely a válasz törzse részeként adja vissza a [első üzembe helyezés](/previous-versions/azure/reference/ee460804(v=azure.100)) és [felhőalapú szolgáltatás tulajdonságainak lekérése](/previous-versions/azure/reference/ee460806(v=azure.100)) műveletek:

  1. A zárolt elem lehetővé teszi annak észlelése, ha egy mutating művelet is elindítható egy adott üzemelő példányon.
  2. A RollbackAllowed elem lehetővé teszi, hogy mikor észleli a [visszaállítás frissítés vagy frissítési](/previous-versions/azure/reference/hh403977(v=azure.100)) művelet nem hívható meg egy adott üzemelő példányon.

  Visszaállítás végrehajtásához nem rendelkezik a lezárt és a RollbackAllowed elemeket is ellenőrizheti. Elegendő, ha a győződjön meg arról, hogy RollbackAllowed értéke igaz. Ezek az elemek csak adott vissza, ha ezek a metódusok kerül meghívásra, a kérelem fejlécét, állítsa a "x-ms-version: 2011-10-01 "vagy egy újabb verziója. Verziókezelés fejlécek kapcsolatos további információkért lásd: [Service Management Versioning](/previous-versions/azure/gg592580(v=azure.100)).

Vannak olyan helyzetek, ha egy frissítés visszaállítás vagy a frissítés nem támogatott, ezek a következők:

* Helyi erőforrások – Ha a frissítés nő egy szerepkörhöz az Azure platform a helyi erőforrások csökkenése nem engedélyezi a visszaállításakor.
* Kvótakorlátok - Ha a frissítés a vertikális leskálázási művelet már nem Ön volt elegendő számítási kvótával rendelkezik a visszaállítási művelet végrehajtásához. Minden Azure-előfizetés magok, amely képes használni az előfizetéshez tartozó összes üzemeltetett szolgáltatások maximális számát megadó társítva kvótával rendelkezik. Ha a visszaállítás egy adott frissítés szeretne az előfizetés keresztül kvóta majd, hogy a visszaállítás nem lesz engedélyezve.
* Versenyhelyzet – Ha a kezdeti frissítése befejeződött, visszaállítás esetén nem lehet.

Ha egy frissítés visszaállításának hasznos lehet például, ha az a [frissítés telepítési](/previous-versions/azure/reference/ee460793(v=azure.100)) szabályozni azt a sebességet, amellyel a fő helyszíni frissítését az Azure-ban üzemeltetett szolgáltatás manuális módban művelet átállása.

A frissítés a kibocsátás közben hívás [frissítés telepítési](/previous-versions/azure/reference/ee460793(v=azure.100)) manuális módban és kezdje el a frissítési tartomány bejárása. Ha olyan pont, ahogy figyeli a frissítést, jegyezze fel az első frissítési tartomány, amely megvizsgálja az egyes szerepkörpéldányok válaszol, meghívhatja a [visszaállítás frissítés vagy frissítési](/previous-versions/azure/reference/hh403977(v=azure.100)) műveletet az üzemelő példányon, amely hagyja a példányok, amelyek korábban még nem frissített és a visszaállítás-példányok volt frissítve lett az előző service-csomag és a konfigurációs érintetlenül.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Egy folyamatos üzembe helyezés több mutating műveletek indítása
Bizonyos esetekben előfordulhat, hogy szeretné elindítani egy folyamatos üzembe helyezés több egyidejű mutating műveleteket. Például végezhet előfordulhat, hogy a szolgáltatás frissítése és, míg a frissítési keresztül a szolgáltatás tesszük elérhetővé, szeretné-e módosítás bizonyos, például szeretné visszaállítani a biztonsági frissítés, egy másik frissítés alkalmazásához, vagy akár törölhetik is az üzembe helyezés. Egy esetet, ahol erre akkor lehet szükség, ha egy szolgáltatás frissítése egy frissített szerepkörpéldány ismételten összeomlási hatására buggy kódot tartalmazza. Ebben az esetben az Azure Fabric Controller nem halad az alkalmazásában, amelyet frissíteni, mert a példány a frissített tartományban elegendő számú kifogástalan állapotú. Ebben az állapotban a neve egy *elakadt a központi telepítési*. Akkor is húznia az üzemelő példány visszaállítása a frissítést, vagy egy friss frissítés alkalmazása a sikertelen első keresztül egyet.

Miután az Azure Fabric Controller frissítéséhez, vagy frissítse a szolgáltatást a kezdeti kérelmet kapott, későbbi mutating műveletek is elindítható. Azt jelenti nem kell várnia a kezdeti művelet befejeződik, egy másik mutating művelet megkezdése előtt.

Egy másik frissítési művelet indítása folyamatban az első frissítés közben hasonló a visszaállítási művelet fogja végrehajtani. Ha a második frissítés automatikus üzemmódban van, az első frissítési tartomány lesz frissítve, azonnal valószínűleg vezető példányok több frissítési tartomány szerinti ugyanabban offline állapotú.

A mutating műveletek a következők: [Telepítési konfiguráció módosítása](/previous-versions/azure/reference/ee460809(v=azure.100)), [központi telepítő frissítése](/previous-versions/azure/reference/ee460793(v=azure.100)), [központi telepítő állapotának frissítése](/previous-versions/azure/reference/ee460808(v=azure.100)), [telepítési művelet törlése](/previous-versions/azure/reference/ee460815(v=azure.100)), és [visszaállítása Frissítési vagy verziófrissítési](/previous-versions/azure/reference/hh403977(v=azure.100)).

Két művelet [első üzembe helyezés](/previous-versions/azure/reference/ee460804(v=azure.100)) és [felhőalapú szolgáltatás tulajdonságainak lekérése](/previous-versions/azure/reference/ee460806(v=azure.100)), a zárolt jelzőt, amely megfelel-e mutating művelet egy adott üzemelő példányon hívható meghatározásához adja vissza.

Annak érdekében, hogy ezen metódusok közül a verziót, amely visszaadja a zárolt jelző hívja, először be kell állítania kérelem fejléce "x-ms-version: 2011-10-01 "vagy egy újabb verziója. Verziókezelés fejlécek kapcsolatos további információkért lásd: [Service Management Versioning](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Frissítési tartományok között szerepkörök terjesztése
Az Azure egyenlően elosztja példánya egy frissítési tartományt, amely a szolgáltatás definíciós (.csdef) fájl részeként konfigurálható számú. A frissítési tartományok maximális száma 20 és az alapértelmezett érték az 5. A szolgáltatásdefiníciós fájl módosításával kapcsolatos további információkért lásd: [Azure szolgáltatásdefiníciós sémában (.csdef fájl)](cloud-services-model-and-package.md#csdef).

Például, ha a szerepkör tíz példánnyal rendelkezik, alapértelmezés szerint mindegyik frissítési tartományon tartalmaz két példánnyal. Ha a szerepkör 14 példányok, négy a frissítési tartományok három példányban tartalmazni, és egy ötödik tartományt tartalmaz két.

Frissítési tartományok jelöli egy nulla alapú index: az első frissítési tartomány 0 Azonosítót, pedig a második frissítési tartomány Azonosítóval és 1, és így tovább.

A következő ábra szemlélteti, hogyan rendszer terjessze a szolgáltatás két szerepkört tartalmaz, mint a szolgáltatás két frissítési tartományok definiál. A szolgáltatás fut, a feldolgozói szerepkör példányai kilenc és a webes szerepkör nyolc példányai.

![A frissítési tartományok terjesztési](media/cloud-services-update-azure-service/IC345533.png "frissítési tartományok eloszlása")

> [!NOTE]
> Vegye figyelembe, hogy az Azure vezérlők példányok kiosztás frissítési tartományok között. Nem alkalmas adja meg az előfordulások lefoglalásának, melyik tartományhoz.
>
>

## <a name="next-steps"></a>További lépések
[A Cloud Services kezelése](cloud-services-how-to-manage-portal.md)  
[A Cloud Services monitorozása](cloud-services-how-to-monitor.md)  
[A Cloud Services konfigurálása](cloud-services-how-to-configure-portal.md)  
