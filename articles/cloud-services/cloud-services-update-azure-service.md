---
title: Felhőszolgáltatás frissítése | Microsoft dokumentumok
description: Ismerje meg, hogyan frissítheti a felhőszolgáltatásokat az Azure-ban. Ismerje meg, hogyan folytatódik a felhőszolgáltatás frissítése a rendelkezésre állás biztosítása érdekében.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 731f4e8cc8a93f33d6887f44fc8d09585e92a75a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360344"
---
# <a name="how-to-update-a-cloud-service"></a>Felhőszolgáltatás frissítése

Egy felhőalapú szolgáltatás frissítése, beleértve a szerepkörök és a vendég operációs rendszer frissítése, egy három lépésből áll. Először fel kell tölteni az új felhőszolgáltatás vagy operációsrendszer-verzió bináris fájljait és konfigurációs fájljait. Ezután az Azure az új felhőszolgáltatás-verzió követelményei alapján fenntartja a számítási és hálózati erőforrásokat a felhőszolgáltatás számára. Végül az Azure működés közbeni frissítést hajt végre, hogy növekményesen frissítse a bérlőt az új verzióra vagy a vendég operációs rendszerre, miközben megőrzi a rendelkezésre állást. Ez a cikk ismerteti az utolsó lépés – a működés közbeni frissítés részleteit.

## <a name="update-an-azure-service"></a>Azure-szolgáltatás frissítése
Az Azure a szerepkörpéldányokat a frissítési tartományoknak (UD) nevezett logikai csoportokba rendezi. A frissítési tartományok (UD) olyan szerepkörpéldányok logikai készletei, amelyek csoportként frissülnek.  Az Azure egyszerre egy UD-t frissít egy felhőalapú szolgáltatásra, amely lehetővé teszi, hogy más UD-k példányai továbbra is szolgálják a forgalmat.

A frissítési tartományok alapértelmezett száma 5. A frissítési tartományt különböző számú frissítési tartományt adhat meg, ha a upgradeDomainCount attribútumot a szolgáltatás definíciós fájljába (.csdef) is beírja. Az upgradeDomainCount attribútumról további információt az [Azure Cloud Services definition séma (.csdef fájl)](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file)című témakörben talál.

Amikor a szolgáltatás egy vagy több szerepkörének helybeni frissítését hajtja végre, az Azure frissíti a szerepkörpéldányok készleteit attól függően, hogy melyik frissítési tartományhoz tartoznak. Az Azure frissíti az adott frissítési tartomány összes példányát – leállítja, frissíti, újrahozza őket –, majd áthelyezi őket a következő tartományra. Leállításával csak a futó példányok az aktuális frissítési tartományban, az Azure gondoskodik arról, hogy egy frissítés történik a lehető legkisebb hatással a futó szolgáltatás. További információt a cikk későbbi részében található [A frissítés folytatása](#howanupgradeproceeds) című témakörben talál.

> [!NOTE]
> Míg a feltételek **frissítése** és **frissítése** némileg eltérő jelentéssel rendelkezik az Azure környezetben, ezek szinonimaként használhatók a folyamatok és a funkciók leírása ebben a dokumentumban.
>
>

A szolgáltatásnak meg kell határoznia legalább két szerepkör-példányt ahhoz, hogy a szerepkör helyben, állásidő nélkül frissíthető legyen. Ha a szolgáltatás egy szerepkörnek csak egy példányából áll, a szolgáltatás nem lesz elérhető, amíg a helyben i frissítés be nem fejeződik.

Ez a témakör az Alábbi információkat ismerteti az Azure-frissítésekről:

* [A szolgáltatás engedélyezett módosításai a frissítés során](#AllowedChanges)
* [A frissítés folytatása](#howanupgradeproceeds)
* [Frissítés visszaállítása](#RollbackofanUpdate)
* [Több mutáns művelet megtervezése folyamatban lévő telepítésen](#multiplemutatingoperations)
* [Szerepkörök elosztása a frissítési tartományok között](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>A szolgáltatás engedélyezett módosításai a frissítés során
Az alábbi táblázat a frissítés során engedélyezett módosításokat mutatja be:

| A tárhelyen, a szolgáltatásokon és a szerepkörökben engedélyezett módosítások | Helybeni frissítés | Szakaszos (VIP csere) | Törlés és újratelepítés |
| --- | --- | --- | --- |
| Operációs rendszer verziója |Igen |Igen |Igen |
| .NET megbízhatósági szint |Igen |Igen |Igen |
| Virtuális gép mérete<sup>1</sup> |Igen<sup>2</sup> |Igen |Igen |
| Helyi tárolási beállítások |Csak növelés<sup>2</sup> |Igen |Igen |
| Szerepkörök hozzáadása és eltávolítása egy szolgáltatásban |Igen |Igen |Igen |
| Adott szerepkör példányainak száma |Igen |Igen |Igen |
| Egy szolgáltatás végpontjainak száma vagy típusa |Igen<sup>2</sup> |Nem |Igen |
| A konfigurációs beállítások neve és értéke |Igen |Igen |Igen |
| A konfigurációs beállítások értékei (de nevei nem) |Igen |Igen |Igen |
| Új tanúsítványok hozzáadása |Igen |Igen |Igen |
| Meglévő tanúsítványok módosítása |Igen |Igen |Igen |
| Új kód telepítése |Igen |Igen |Igen |

<sup>1</sup> A méretváltozás a felhőszolgáltatás számára elérhető méretek részhalmazára korlátozódik.

<sup>2</sup> Azure SDK 1.5-ös vagy újabb verziókat igényel.

> [!WARNING]
> A virtuális gép méretének módosítása megsemmisíti a helyi adatokat.
>
>

A frissítés során a következő elemek nem támogatottak:

* Szerepkör nevének módosítása. Távolítsa el, majd adja hozzá a szerepkört az új névvel.
* A frissítési tartományok számának módosítása.
* A helyi erőforrások méretének csökkentése.

Ha a szolgáltatás definíciójának más frissítéseit hajtja végre, például a helyi erőforrás méretének csökkentését, helyette egy VIP-cserefrissítést kell végrehajtania. További információ: [Csere telepítés](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>A frissítés folytatása
Eldöntheti, hogy a szolgáltatás összes szerepkörét vagy a szolgáltatás egyetlen szerepkörét szeretné-e frissíteni. Mindkét esetben a frissítés alatt álló és az első frissítési tartományhoz tartozó szerepkörök összes példánya leáll, frissül, és újra online állapotba kerül. Miután újra online állapotba kerültek, a második frissítési tartomány példányai leállnak, frissülnek, és újra online állapotba kerülnek. Egy felhőszolgáltatás egyszerre legtöbb frissítésaktív lehet. A frissítés mindig a felhőszolgáltatás legújabb verziójával történik.

Az alábbi ábra bemutatja, hogyan történik a frissítés, ha a szolgáltatás összes szerepkörét frissíti:

![Frissítési szolgáltatás](media/cloud-services-update-azure-service/IC345879.png "Frissítési szolgáltatás")

A következő ábra azt mutatja be, hogyan történik a frissítés, ha csak egyetlen szerepkört frissít:

![Frissítési szerepkör](media/cloud-services-update-azure-service/IC345880.png "Frissítési szerepkör")  

Az automatikus frissítés során az Azure Fabric Controller rendszeres időközönként kiértékeli a felhőszolgáltatás állapotát, hogy meghatározza, mikor biztonságos a következő UD-n való séta. Ez az állapotfelmérés szerepkörenként történik, és csak a legújabb verzióban szereplő példányokat veszi figyelembe (azaz a már járt ud-k példányait). Ellenőrzi, hogy az egyes szerepkörök minimális száma elérte-e a megfelelő terminálállapotot.

### <a name="role-instance-start-timeout"></a>Szerepkörpéldány kezdő időmegállása
A fabric vezérlő 30 percet vár az egyes szerepkörpéldányok számára, hogy elérjék az Elindított állapotot. Ha az idő-idő haa eltelik, a hálóvezérlő továbbra is a következő szerepkörpéldányra lép.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>A felhőszolgáltatás-frissítések során az adatok meghajtóira gyakorolt hatás

Ha egy szolgáltatást egyetlen példányról több példányra frissít, a szolgáltatás a frissítés végrehajtása közben megszűnik az Azure-szolgáltatások frissítése miatt. A szolgáltatás elérhetőségét garantáló szolgáltatásiszint-szerződés csak az okra a szolgáltatásokra vonatkozik, amelyek egynél több példánysal vannak telepítve. Az alábbi lista bemutatja, hogy az egyes meghajtókon lévő adatokat hogyan érinti az egyes Azure-szolgáltatásfrissítési forgatókönyvek:

|Forgatókönyv|C meghajtó|D meghajtó|E meghajtó|
|--------|-------|-------|-------|
|Virtuális gép újraindítása|Tartósított|Tartósított|Tartósított|
|Portál újraindítása|Tartósított|Tartósított|Megsemmisült|
|Portál újraképe|Tartósított|Megsemmisült|Megsemmisült|
|Helybeni frissítés|Tartósított|Tartósított|Megsemmisült|
|Csomópont áttelepítése|Megsemmisült|Megsemmisült|Megsemmisült|

Ne feledje, hogy a fenti listában az E: meghajtó a szerepkör gyökérmeghajtóját jelöli, és nem lehet kódolni. Ehelyett használja a **%RoleRoot%** környezeti változót a meghajtó ábrázolására.

Az egypéldányos szolgáltatás frissítésekor az állásidő minimalizálása érdekében telepítsen egy új többpéldányos szolgáltatást az átmeneti kiszolgálóra, és hajtson végre egy VIP-swapot.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Frissítés visszaállítása
Az Azure rugalmasságot biztosít a szolgáltatások kezelése a frissítés során azáltal, hogy lehetővé teszi, hogy további műveleteket kezdeményezzen egy szolgáltatáson, miután az Azure Fabric Controller elfogadta a kezdeti frissítési kérelmet. A visszaállítás csak akkor hajtható végre, ha egy frissítés (konfigurációmódosítása) vagy frissítés **folyamatban van** a központi telepítés. A frissítés vagy frissítés folyamatban van, feltéve, hogy a szolgáltatásnak legalább egy olyan példánya van, amely még nem lett frissítve az új verzióra. Annak ellenőrzéséhez, hogy a visszaállítás engedélyezett-e, ellenőrizze a Telepítés és [a Felhőszolgáltatás tulajdonságainak beolvasása](/previous-versions/azure/reference/ee460806(v=azure.100)) művelet által visszaadott RollbackAllowed jelző értékét, igaz értékre van állítva. [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100))

> [!NOTE]
> Csak akkor van értelme, hogy hívja visszaad egy **helybeni** frissítés vagy frissítés, mert VIP swap frissítések et kell cserélni egy teljes futó példányát a szolgáltatás egy másik.
>
>

A folyamatban lévő frissítések visszaállítása a következő hatással van a telepítésre:

* Azok a szerepkörpéldányok, amelyek még nem lettek frissítve vagy frissítve az új verzióra, nem frissülnek vagy nem lettek frissítve, mert ezek a példányok már a szolgáltatás célverzióját futtatják.
* Azok a szerepkörpéldányok, amelyek már frissítve vagy frissítve\*voltak a szolgáltatáscsomag ( .cspkg) fájl vagy a szolgáltatáskonfiguráció ( .cscfg) fájl (vagy\*mindkét fájl) új verziójára, visszaállnak a fájlok frissítés előtti verziójára.

Ezt funkcionálisan a következő funkciók biztosítják:

* A [visszaállítási frissítés vagy frissítés](/previous-versions/azure/reference/hh403977(v=azure.100)) művelet, amely egy konfigurációs frissítés [(a Központi telepítés módosítása konfigurációjának módosítása](/previous-versions/azure/reference/ee460809(v=azure.100))által kiváltott) vagy egy frissítés (a [frissítési központi telepítés](/previous-versions/azure/reference/ee460793(v=azure.100))hívásával kiváltott) hívására hívható, feltéve, hogy legalább egy példány van a szolgáltatásban, amely még nem lett frissítve az új verzióra.
* A Zárolt elem és a RollbackAllowed elem, amelyek a [Telepítés beolvasása](/previous-versions/azure/reference/ee460804(v=azure.100)) és a [Felhőszolgáltatás tulajdonságainak beolvasása](/previous-versions/azure/reference/ee460806(v=azure.100)) művelet választörzsének részeként jönnek vissza:

  1. A Zárolt elem lehetővé teszi, hogy észlelje, ha egy mutálódó művelet meghívható egy adott központi telepítésen.
  2. A RollbackAllowed elem lehetővé teszi, hogy észlelje, ha a [visszaállítási frissítés vagy frissítés művelet](/previous-versions/azure/reference/hh403977(v=azure.100)) hívható egy adott központi telepítés.

  A visszaállítás végrehajtásához nem kell ellenőriznie mind a Zárolt, mind a RollbackAllowed elemeket. Elegendő annak megerősítése, hogy a RollbackAllowed értéke igaz. Ezek az elemek csak akkor adják vissza, ha ezeket a metódusokat az "x-ms-version: 2011-10-01" vagy egy újabb verzióra beállított kérelemfejléc használatával hívja meg a rendszer. A fejlécek verziószámozásáról a [Szolgáltatáskezelési verziószámozás című](/previous-versions/azure/gg592580(v=azure.100))témakörben talál további információt.

Vannak olyan helyzetek, amikor egy frissítés vagy frissítés visszaállítása nem támogatott, ezek a következők:

* Helyi erőforrások csökkentése – Ha a frissítés növeli a helyi erőforrásokat egy szerepkörhöz, az Azure platform nem teszi lehetővé a visszaállítást.
* Kvótakorlátozások – Ha a frissítés leskálázási művelet volt, előfordulhat, hogy már nem rendelkezik elegendő számítási kvótával a visszaállítási művelet végrehajtásához. Minden Azure-előfizetéshez tartozik egy kvóta, amely meghatározza az adott előfizetéshez tartozó összes üzemeltetett szolgáltatás által felhasználható magok maximális számát. Ha egy adott frissítés visszaállítása az előfizetést a kvótán túlra helyezi, akkor a visszaállítás nem lesz engedélyezve.
* Versenyhelyzet – Ha a kezdeti frissítés befejeződött, a visszaállítás nem lehetséges.

Egy példa arra, hogy egy frissítés visszaállítása hasznos lehet, ha a [frissítési központi telepítési](/previous-versions/azure/reference/ee460793(v=azure.100)) művelet kézi üzemmódban, hogy szabályozhatja, milyen sebességgel egy jelentős helyszíni frissítés az Azure-üzemeltetett szolgáltatás bevezetésre.

A frissítés bevezetése során manuális módban hívja meg a [frissítési központi telepítést,](/previous-versions/azure/reference/ee460793(v=azure.100)) és kezdje nőb-es frissítési tartományokat. Ha egy bizonyos ponton, a frissítés figyelése során, vegye figyelembe, hogy az első vizsgált frissítési tartományok egyes szerepkörpéldányai nem válaszolnak, meghívhatja a [visszaállítási frissítés vagy frissítés](/previous-versions/azure/reference/hh403977(v=azure.100)) műveletet a központi telepítésen, amely érintetlenül hagyja a még nem frissített példányokat és az előző szolgáltatáscsomagra és konfigurációra frissített visszaállítási példányokat.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Több mutáns művelet megtervezése folyamatban lévő telepítésen
Bizonyos esetekben előfordulhat, hogy több egyidejű mutálódó műveletet szeretne kezdeményezni egy folyamatban lévő telepítésen. Például végrehajthat egy szolgáltatásfrissítést, és afrissítés bevezetése közben a szolgáltatásban, szeretne néhány módosítást végrehajtani, például vissza szeretné igényelni a frissítést, másik frissítést alkalmazni, vagy akár törölni is szeretné a központi telepítést. Egy olyan eset, amelyben erre szükség lehet, ha a szolgáltatásfrissítés hibás kódot tartalmaz, amely a frissített szerepkörpéldány ismételt összeomlását okozza. Ebben az esetben az Azure Fabric Controller nem lesz képes előrelépni a frissítés alkalmazásában, mert a frissített tartományban a példányok elégtelen száma kifogástalan. Ezt az állapotot *beragadt telepítésnek nevezzük.* A központi telepítés leoldható a frissítés visszagörgetésével vagy egy friss frissítés alkalmazásával a sikertelen felett.

Miután az Azure Fabric-vezérlő megkapta a szolgáltatás frissítésére vagy frissítésére vonatkozó kezdeti kérést, megkezdheti a további mutáns műveleteket. Ez azt jelenti, hogy nem kell megvárnia a kezdeti művelet befejezését, mielőtt újabb mutálódó műveletet indíthatna el.

Egy második frissítési művelet megkezdése, amíg az első frissítés folyamatban van, a visszaállítási művelethez hasonlóan fog működni. Ha a második frissítés automatikus módban van, az első frissítési tartomány azonnal frissül, ami valószínűleg azt eredményezi, hogy több frissítési tartomány példányai offline állapotba kerülnek ugyanazon az időpontban.

A mutálódó műveletek a következők: [A központi telepítés konfigurációjának módosítása](/previous-versions/azure/reference/ee460809(v=azure.100)), a központi telepítés [frissítése,](/previous-versions/azure/reference/ee460793(v=azure.100))a [központi telepítés](/previous-versions/azure/reference/ee460808(v=azure.100)) [törlése](/previous-versions/azure/reference/ee460815(v=azure.100))és a frissítés vagy [frissítés visszaállítása](/previous-versions/azure/reference/hh403977(v=azure.100)).

Két művelet, a [Telepítés beküldése](/previous-versions/azure/reference/ee460804(v=azure.100)) és a [Felhőszolgáltatás tulajdonságainak beküldése](/previous-versions/azure/reference/ee460806(v=azure.100)), adja vissza a Zárolt jelzőt, amely megvizsgálható annak megállapításához, hogy egy mutáns művelet meghívható-e egy adott telepítésen.

A zárolt jelzőt visszaadó metódusok verziójának hívásához a kérelem fejlécét az "x-ms-version: 2011-10-01" vagy egy későbbi értékre kell állítania. A fejlécek verziószámozásáról a [Szolgáltatáskezelési verziószámozás című](/previous-versions/azure/gg592580(v=azure.100))témakörben talál további információt.

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Szerepkörök elosztása a frissítési tartományok között
Az Azure egy szerepkör példányait egyenletesen osztja el meghatározott számú frissítési tartomány között, amelyek a szolgáltatásdefiníció (.csdef) fájl részeként konfigurálhatók. A frissítési tartományok maximális száma 20, az alapértelmezett érték pedig 5. A szolgáltatásdefiníciós fájl módosításáról az [Azure Service Definition Séma (.csdef fájl)](cloud-services-model-and-package.md#csdef)című témakörben talál további információt.

Ha például a szerepkör tíz példányt tartalmaz, alapértelmezés szerint minden frissítési tartomány két példányt tartalmaz. Ha a szerepkör 14 példányt tartalmaz, akkor a frissítési tartományok közül négy három példányt tartalmaz, egy ötödik tartomány pedig kettőt.

A frissítési tartományok nulla alapú indexszel vannak azonosítva: az első frissítési tartomány azonosítója 0, a második frissítési tartományazonosítója pedig 1, és így tovább.

Az alábbi ábra azt mutatja be, hogy egy két szerepkört tartalmazó szolgáltatás hogyan kerül elosztásra, amikor a szolgáltatás két frissítési tartományt határoz meg. A szolgáltatás a webes szerepkör nyolc példányát és a feldolgozói szerepkör kilenc példányát futtatja.

![A frissítési tartományok terjesztése](media/cloud-services-update-azure-service/IC345533.png "A frissítési tartományok terjesztése")

> [!NOTE]
> Vegye figyelembe, hogy az Azure szabályozza, hogyan vannak lefoglalva a példányok a frissítési tartományok között. Nem lehet megadni, hogy mely példányok melyik tartományhoz vannak lefoglalva.
>
>

## <a name="next-steps"></a>További lépések
[A Cloud Services felügyelete](cloud-services-how-to-manage-portal.md)  
[A felhőszolgáltatások figyelése](cloud-services-how-to-monitor.md)  
[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)  



