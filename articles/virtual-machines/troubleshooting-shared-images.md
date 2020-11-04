---
title: A megosztott rendszerképekkel kapcsolatos problémák elhárítása az Azure-ban
description: Ismerje meg, hogyan oldhatja meg a megosztott képtárakkal kapcsolatos problémákat.
author: olayemio
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 189fa12b1fc11e79ab64231a7ecd453113b8771a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336010"
---
# <a name="troubleshooting-shared-image-galleries-in-azure"></a>Megosztott rendszerkép-galériák hibaelhárítása Az Azure-ban

Ha problémát észlel a megosztott rendszerkép-katalógusokon, rendszerkép-definíciókon vagy rendszerképverziókon végzett műveletek végrehajtásakor, futtassa újból a sikertelen parancsot hibakeresési módban. A hibakeresési módot a parancssori felület `--debug` és a PowerShell-lel rendelkező kapcsoló átadásával aktiválja `-Debug` . A hiba megtalálása után kövesse ezt a dokumentumot a hibák elhárításához.


## <a name="issues-with-creating-or-modifying-a-gallery"></a>Katalógusok létrehozásával vagy módosításával kapcsolatos problémák ##

*A gyűjtemény neve érvénytelen. Az engedélyezett karakterek angol alfanumerikus karakterek, aláhúzások és a közepén engedélyezett időszakok, legfeljebb 80 karakter. Az összes többi speciális karakter (beleértve a kötőjeleket is) nem engedélyezett.*  
**OK** : a katalógus megadott neve nem felel meg az elnevezési követelményeknek.  
**Megkerülő megoldás** : válasszon egy olyan nevet, amely megfelel a következő feltételeknek: 1) 80-karakteres korlát, 2) csak angol betűket, számokat, aláhúzást és pontot, 3) indít, és a végén angol betűket vagy számokat tartalmaz.

*Az entitás neve (galleryName) érvénytelen az érvényesítési szabálya szerint: ^ [^ \_ \W] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**OK** : a katalógus neve nem felel meg az elnevezési követelményeknek.  
**Megkerülő megoldás** : válasszon egy olyan nevet a katalógusnak, amely megfelel a következő feltételeknek: 1.) 80 karakteres korlát, 2) csak angol betűket, számokat, aláhúzást és időszakokat, 3) indít, és a végén angol betűket vagy számokat tartalmaz.

*A megadott erőforrás neve <galleryName \> érvénytelen záró karaktereket tartalmaz: <karaktert \> . A név nem végződhet karakterrel: <karakter\>*  
**OK** : a katalógus neve pont vagy aláhúzás karakterrel végződik.  
**Megkerülő megoldás** : válasszon egy olyan nevet a katalógusnak, amely megfelel a következő feltételeknek: 1.) 80 karakteres korlát, 2) csak angol betűket, számokat, aláhúzást és időszakokat, 3) indít, és a végén angol betűket vagy számokat tartalmaz.

*A megadott hely <régiója \> nem érhető el a "Microsoft. számítás/galériák" erőforrástípus számára. Az erőforrástípus számára elérhető régiók listája...*  
**OK** : a katalógushoz megadott régió helytelen, vagy hozzáférési kérelemre van szükség.  
**Áthidaló megoldás** : Győződjön meg arról, hogy helyesen írta-e be a régió nevét. A parancs futtatásával megtekintheti, hogy mely régiókban férhet hozzá. Ha a régió nem szerepel a listában, küldje el a [hozzáférési kérést](/troubleshoot/azure/general/region-access-request-process).

*Nem lehet törölni az erőforrást a beágyazott erőforrások törlése előtt.*  
**OK** : olyan gyűjteményt próbált törölni, amely legalább egy meglévő rendszerkép-definíciót tartalmaz. A katalógusnak üresnek kell lennie ahhoz, hogy törölni lehessen.  
**Áthidaló megoldás** : törölje az összes rendszerkép-definíciót a katalógusban, majd folytassa a katalógus törlésével. Ha a rendszerkép definíciója képverziókat tartalmaz, a rendszerkép-definíciók törlése előtt törölni kell a képverziókat.

*Az erőforrás-<galleryName \> már létezik a (z \_ ) \> <resourceGroup <1. régiójában \> . Nem hozható létre azonos nevű erőforrás a (z \_ ) <2. régióban \> . Válasszon ki egy új erőforrás-nevet.*  
**OK** : már rendelkezik egy meglévő, azonos nevű katalógussal, és megpróbált létrehozni egy másik gyűjteményt ugyanazzal a névvel, de egy másik régióban.  
**Áthidaló megoldás** : használjon másik tárat vagy más erőforráscsoportot.

## <a name="issues-with-creating-or-modifying-image-definitions"></a>Rendszerkép-definíciók létrehozásával vagy módosításával kapcsolatos problémák ##

*A (z) galleryImage. properties. <tulajdonság módosítása \> nem engedélyezett.*  
**OK** : megpróbálta módosítani az operációs rendszer típusát, az operációs rendszer állapotát, a Hyper V generációját, az ajánlatot, a közzétevőt és az SKU-t. Ezen tulajdonságok bármelyikének módosítása nem engedélyezett.  
**Áthidaló megoldás** : hozzon létre helyette egy új rendszerkép-definíciót.

*A (z) <galleryName/imageDefinitionName erőforrás \> már létezik a \_ (z) \> <resourceGroup erőforráscsoport <régiójában \> . Nem hozható létre azonos nevű erőforrás a (z \_ ) <2. régióban \> . Válasszon ki egy új erőforrás-nevet.*  
**OK** : már rendelkezik egy meglévő rendszerkép-definícióval ugyanabban a gyűjteményben és erőforráscsoporthoz ugyanazzal a névvel, és megpróbált létrehozni egy másik rendszerkép-definíciót ugyanazzal a névvel, és ugyanabban a gyűjteményben, de egy másik régióban.  
**Megkerülő megoldás** : használjon más nevet a képdefinícióhoz, vagy helyezze a rendszerkép definícióját egy másik gyűjteménybe vagy erőforráscsoporthoz

*A megadott erőforrás neve <galleryName \> /<imageDefinitionName \> érvénytelen záró karaktereket tartalmaz: <karakter \> . A név nem végződhet karakterrel: <karakter\>*  
**OK** : a megadott <imageDefinitionName \> pont vagy aláhúzás karakterrel végződik.  
**Áthidaló megoldás** : válasszon egy nevet a képdefinícióhoz, amely megfelel a következő feltételeknek: 1.) 80 karakteres korlát, 2) csak angol betűket, számokat, kötőjeleket, aláhúzásokat és időszakokat, 3) indít, és a végén angol betűket vagy számokat tartalmaz.

*Az entitás neve <imageDefinitionName \> érvénytelen az érvényesítési szabálya szerint: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**OK** : a megadott <imageDefinitionName \> pont vagy aláhúzás karakterrel végződik.  
**Áthidaló megoldás** : válasszon egy nevet a képdefinícióhoz, amely megfelel a következő feltételeknek: 1.) 80 karakteres korlát, 2) csak angol betűket, számokat, kötőjeleket, aláhúzásokat és időszakokat, 3) indít, és a végén angol betűket vagy számokat tartalmaz.

*Az eszköz neve galleryImage. properties. Identifier. <tulajdonság érvénytelen \> . Nem lehet üres. Az engedélyezett karakterek: nagybetűk, kisbetűk, számjegyek, kötőjel (-), pont (.), aláhúzás ( \_ ). A név nem végződhet ponttal (.). A név hossza nem lehet hosszabb <szám \> karakternél.*  
**OK** : a megadott közzétevő, ajánlat vagy SKU értéke nem felel meg az elnevezési követelményeknek.  
**Áthidaló megoldás** : válasszon olyan értéket, amely megfelel a következő feltételeknek: 1.) 128 – a Publisher vagy a 64 karakteres korlátja az ajánlathoz és az SKU-hoz, 2) csak angol betűket, számokat, kötőjeleket, aláhúzást és 3 pontot tartalmaz, és nem végződhet ponttal.

*Nem hajtható végre a kért művelet a beágyazott erőforráson. A szülő erőforrás <galleryName \> nem található.*  
**OK** : nincs olyan katalógus, amelynek a neve <galleryName az \> aktuális előfizetésben és az erőforráscsoporthoz.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a katalógus, az előfizetés és az erőforráscsoport neve helyes. Ellenkező esetben hozzon létre egy <galleryName nevű új katalógust \> .

*A megadott hely <régiója \> nem érhető el a "Microsoft. számítás/galériák" erőforrástípus számára. Az erőforrástípus számára elérhető régiók listája...*  
**OK** : a <régió \> helytelen, vagy hozzáférési kérelmet igényel  
**Áthidaló megoldás** : Győződjön meg arról, hogy helyesen írta-e be a régió nevét. A parancs futtatásával megtekintheti, hogy mely régiókban férhet hozzá. Ha a régió nem szerepel a listában, küldje el a [hozzáférési kérést](/troubleshoot/azure/general/region-access-request-process).

*Nem szerializálható az érték: <\> az érték típusa: "ISO-8601"., ISO8601Error: iso 8601 Time kijelölő nem hiányzik. Nem elemezhető a DateTime karakterlánc <értéke\>*  
**OK** : a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás** : adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)– érvényes formátumban.

*Nem lehet konvertálni a karakterláncot DateTimeOffset: <értékre \> . Elérési út tulajdonságai. <\> tulajdonság*  
**OK** : a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás** : adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)– érvényes formátumban.

*A EndOfLifeDate egy jövőbeli dátumra kell beállítani.*  
**OK** : az elhasználódott Date tulajdonság nem megfelelően van formázva a mai dátum utáni dátumként.  
**Megkerülő megoldás** : adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)– érvényes formátumban.

*argumentum – <tulajdonság \> : érvénytelen int érték: <érték\>*  
**OK** : <tulajdonság \> csak egész értékeket fogad el, és \> a <érték nem egész szám.  
**Áthidaló megoldás** : válasszon egész értéket.

*<tulajdonság minimális értéke \> nem lehet nagyobb, mint <tulajdonság maximális értéke \> .*  
**OK** : a <tulajdonsághoz megadott minimális érték \> nagyobb, mint a <tulajdonsághoz megadott maximális érték \> .  
**Megkerülő megoldás** : módosítsa az értékeket úgy, hogy a minimum kisebb vagy egyenlő legyen a maximális értékkel.

*Galéria képe: <\> által azonosított imageDefinitionName (közzétevő: <közzétevő \> , ajánlat: <ajánlat \> , SKU: <SKU \> ) már létezik. Válasszon másik közzétevőt, ajánlatot, SKU-kombinációt.*  
**OK** : megpróbált létrehozni egy új rendszerkép-definíciót ugyanazzal a közzétevővel, ajánlattal, SKU tripla néven egy meglévő rendszerkép-definícióval ugyanabban a gyűjteményben.  
**Áthidaló megoldás** : egy adott katalóguson belül minden képdefiníciónak a kiadó, az ajánlat, az SKU egyedi kombinációjával kell rendelkeznie. Válasszon egyedi kombinációt, vagy válasszon ki egy új katalógust, és hozza létre újra a rendszerkép definícióját.

*Nem lehet törölni az erőforrást a beágyazott erőforrások törlése előtt.*  
**OK** : olyan rendszerkép-definíciót próbált meg törölni, amely tartalmazza a rendszerkép verzióját. A rendszerkép definíciójának üresnek kell lennie ahhoz, hogy törölni lehessen.  
**Áthidaló megoldás** : törölje az összes rendszerkép-verziót a rendszerkép-definícióban, majd folytassa a rendszerkép definíciójának törlésével.

*A paraméter <tulajdonság nem köthető \> . Az érték <érték nem konvertálható \> <propertyType típusra \> . Az azonosító neve <érték nem egyezhet \> meg egy érvényes enumerálási névvel. Adjon meg egyet a következő enumerálási nevek közül, majd próbálkozzon újra: <choice1 \> , <choice2 \> ,...*  
**OK** : a tulajdonság a lehetséges értékek korlátozott listáját tartalmazza, és a <érték \> nem tartozik ezek közé.  
**Áthidaló megoldás** : válassza ki az egyik lehetséges <választási \> értéket.

*A paraméter <tulajdonság nem köthető \> . A Value <érték nem konvertálható \> a &quot; System. datetime típusra.&quot;*  
**OK** : a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás** : adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)– érvényes formátumban.

*A paraméter <tulajdonság nem köthető \> . A Value <érték nem konvertálható \> &quot; System. Int32 típusra.&quot;*  
**OK** : <tulajdonság \> csak egész értékeket fogad el, és \> a <érték nem egész szám.  
**Áthidaló megoldás** : válasszon egész értéket.

*A ZRS nem támogatott ebben a régióban.*  
**OK** : standard ZRS választott egy olyan régióban, amely még nem támogatja azt.  
**Áthidaló megoldás** : módosítsa a Storage-fiók típusát a "prémium \_ LRS" vagy a "standard \_ LRS" értékre. Tekintse meg a dokumentációt a ZRS előzetes [verzióval rendelkező régiók legújabb listájához](/azure/storage/common/storage-redundancy#zone-redundant-storage) .

## <a name="issues-with-creating-or-updating-image-versions"></a>Rendszerkép-verziók létrehozásával vagy frissítésével kapcsolatos problémák ##

*A megadott hely <régiója \> nem érhető el a "Microsoft. számítás/galériák" erőforrástípus számára. Az erőforrástípus számára elérhető régiók listája...*  
**OK** : a <régió \> helytelen, vagy hozzáférési kérelmet igényel  
**Áthidaló megoldás** : Győződjön meg arról, hogy helyesen írta-e be a régió nevét. A parancs futtatásával megtekintheti, hogy mely régiókban férhet hozzá. Ha a régió nem szerepel a listában, küldje el a [hozzáférési kérést](/troubleshoot/azure/general/region-access-request-process).

*Nem hajtható végre a kért művelet a beágyazott erőforráson. A szülő erőforrás <galleryName/imageDefinitionName \> nem található.*  
**OK** : nincs olyan katalógus, amelynek a neve <GalleryName/imageDefinitionName a \> jelenlegi előfizetésben és az erőforráscsoporthoz.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a katalógus, az előfizetés és az erőforráscsoport neve helyes. Ellenkező esetben hozzon létre egy új katalógust <galleryName \> és/vagy rendszerkép-definíció néven <imageDefinitionName nevű \> csoportot a jelzett erőforráscsoporthoz.

*A paraméter <tulajdonság nem köthető \> . A Value <érték nem konvertálható \> a &quot; System. datetime típusra.&quot;*  
**OK** : a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás** : adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)– érvényes formátumban.

*A paraméter <tulajdonság nem köthető \> . A Value <érték nem konvertálható \> &quot; System. Int32 típusra.&quot;*  
**OK** : <tulajdonság \> csak egész értékeket fogad el, és \> a <érték nem egész szám.  
**Áthidaló megoldás** : válasszon egész értéket.

*A katalógus rendszerkép-közzétételi profiljának régiói <publishingRegions \> tartalmaznia kell a rendszerkép verziójának helyét <sourceRegion\>*  
**OK** : a forrás rendszerképének (<sourceRegion) helyét \> szerepelnie kell a <publishingRegions listán. \>  
**Áthidaló megoldás** : < sourceRegion belefoglalása \> a <publishingRegions \> listába.

*A \> paraméter <tulajdonság értéke <értéke \> kívül esik a megengedett tartományon. Az értéknek <minValue és <maxValue között kell lennie \> \> .*  
**OK** : <\> az érték a <tulajdonság lehetséges értékeinek tartományán kívül esik \> .  
**Megkerülő megoldás** : válasszon olyan értéket, amely <minValue \> és <maxValue tartományon belül van \> .

*\>Nem található a forrás <resourceID. Győződjön meg arról, hogy a forrás létezik, és ugyanabban a régióban található, mint a katalógus rendszerkép-verziója.*  
**OK** : nincs forrás a <resourceID, \> vagy a forrás <resourceID \> nem ugyanabban a régióban található, mint a létrehozott katalógus-rendszerkép.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a <resourceID \> megfelelő, és hogy a katalógus rendszerképének verziószáma megegyezik a <resourceID.\>

*A "galleryImageVersion. properties. storageProfile. <diskImage \> . Source.id" tulajdonság módosítása nem engedélyezett.*  
**OK** : a létrehozás után a katalógus rendszerképének verziószáma nem módosítható.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a forrás-azonosító megegyezik a már meglévő forrás-azonosítóval, vagy módosítsa a rendszerkép verziószámát.

*A rendszer duplikált LUN-számokat észlelt a bemeneti adatlemezeken. A LUN számának egyedinek kell lennie az egyes adatlemezek esetében.*  
**OK** : a lemezkép verziójának lemezes és/vagy lemezes Pillanatképek használatával történő létrehozásakor két vagy több lemez vagy lemez pillanatképe azonos LUN-számokkal rendelkezik.  
**Áthidaló megoldás** : távolítsa el vagy módosítsa az ismétlődő LUN-számokat.

*Duplikált forrás azonosítók találhatók a bemeneti lemezeken. A forrás azonosítójának minden lemez esetében egyedinek kell lennie.*  
**OK** : a lemezkép verziójának lemezes és/vagy lemezes Pillanatképek használatával történő létrehozásakor két vagy több lemez vagy lemez pillanatképe ugyanazzal az erőforrás-azonosítóval rendelkezik.  
**Áthidaló megoldás** : távolítsa el vagy módosítsa az ismétlődő lemezes források azonosítóit.

*Érvénytelen a tulajdonság azonosítója <resourceID \> a következő elérési úton: "Properties. storageProfile. <diskimages \> . Source.id". A "/subscriptions/{subscriptionId}" vagy a "/providers/{resourceProviderNamespace}/" kezdetű teljes erőforrás-azonosító várható.*  
**OK** : a <resourceID \> formátuma helytelen.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a resourceID helyes.

*A forrás azonosítója: <a resourceID \> felügyelt rendszerképnek, virtuális gépnek vagy más katalógus-rendszerképnek kell lennie*  
**OK** : a <resourceID \> formátuma helytelen.  
**Megkerülő megoldás** : Ha a virtuális gép, a felügyelt rendszerkép vagy a katalógus rendszerképének verziószámát használja forrásként, ellenőrizze, hogy helyes-e a virtuális gép, a felügyelt rendszerkép vagy a gyűjtemény rendszerképének erőforrás-azonosítója.

*A forrás azonosítója: <a resourceID \> felügyelt lemeznek vagy pillanatképnek kell lennie.*  
**OK** : a <resourceID \> formátuma helytelen.  
**Megkerülő megoldás** : ha lemezeket és/vagy lemez-pillanatképeket használ a lemezkép verziójának forrásaként, ellenőrizze, hogy helyesek-e a lemezek és/vagy a lemez-Pillanatképek erőforrás-azonosítói.

*A katalógus rendszerképének verziója nem hozható létre a következőből: <resourceID, \> mert a szülő katalógus rendszerképében lévő operációs rendszer állapota (<OsState \_ 1 \> ) nem <\_ 2 \> .*  
**OK** : az operációs rendszer állapota (általánosított vagy specializált) nem egyezik meg a rendszerkép definíciójában megadott operációsrendszer-állapottal.  
**Áthidaló megoldás** : válasszon egy olyan virtuális gépet, amelynek operációs rendszere <OsState \_ 1, \> vagy hozzon létre egy új rendszerkép-definíciót a virtuális gépekhez <OsState \_ 2 alapján \> .

*A (z) "<resourceID \> " azonosítójú erőforrás eltérő hypervisor-generációval rendelkezik ["<V # \_ 1 \> "], mint a szülő katalógus képfájljának hypervisor-generációja [' <V # \_ 2 \> ']*  
**OK** : a rendszerkép verziója nem felel meg a rendszerkép definíciójában megadott hypervisor-generációnak. A rendszerkép-definíció operációs rendszer <V # \_ 1 \> , a rendszerkép operációs rendszere pedig <v # \_ 2 \> .  
**Áthidaló megoldás** : válasszon egy olyan forrást, amelynek a hypervisor-generációja ugyanaz, mint a rendszerkép definíciója, vagy hozzon létre/válasszon egy új rendszerkép-definíciót, amelynek a hypervisor-generációja megegyezik a rendszerkép verziójával.

*A (z) "<resourceID \> " azonosítójú erőforrás eltérő operációsrendszer-típussal rendelkezik, \_ mint a (z) ["<OsType 1 \> "], mint a (z) ["<OsType \_ 2 \> "]*  
**OK** : a rendszerkép verziója nem felel meg a rendszerkép definíciójában megadott hypervisor-generációnak. A rendszerkép-definíció operációs rendszer <\_ 1 \> . OsType, és a rendszerkép verziója operációs rendszer <OsType \_ 2 \> .  
**Megkerülő megoldás** : válasszon ki egy azonos operációs rendszerrel (Linux/Windows) rendelkező forrást, vagy hozzon létre/válasszon egy új rendszerkép-definíciót, amelynek a rendszerkép verziója azonos az operációsrendszer-generációval.

*A forrásként szolgáló virtuális gép <resourceID \> nem tartalmazhat ideiglenes operációsrendszer-lemezt.*  
**OK** : a (z) "<resourceID" helyen található forrás \> egy ideiglenes operációsrendszer-lemezt tartalmaz. A megosztott képgyűjtemény jelenleg nem támogatja az elmúló operációsrendszer-lemezt.  
**Áthidaló megoldás** : válasszon másik forrást olyan virtuális gép alapján, amely nem használ ELmúló operációsrendszer-lemezt.

*A forrásként szolgáló virtuális gép <resourceID \> nem tartalmazhatja a (z) ["<Beskid \> "] egy UltraSSD-fiókban tárolt lemezét.*  
**OK** : a <a Beskid lemez \> egy UltraSSD-lemez. A megosztott képgyűjtemény jelenleg nem támogatja ultra SSD lemezeket.  
**Megkerülő megoldás** : használjon olyan forrást, amely csak prémium SSD, standard SSD és/vagy standard HDD által felügyelt lemezeket tartalmaz.

*A forrásként szolgáló virtuális gépet \> a Managed Disksból kell létrehozni <resourceID.*  
**OK** : a <resourceID lévő virtuális gép nem \> felügyelt lemezeket használ.  
**Megkerülő megoldás** : használjon olyan virtuális gépeken alapuló forrást, amely csak prémium SSD, standard SSD és/vagy standard HDD által felügyelt lemezeket tartalmaz.

*Túl sok kérelem érkezett a (z) <resourceID forráshoz \> . Csökkentse a kérések számát a forráson, vagy várjon egy ideig az újrapróbálkozás előtt.*  
**OK** : a rendszerkép forrása jelenleg túl sok kérelem miatt van szabályozva.  
**Áthidaló megoldás** : próbálkozzon később a rendszerkép verziójának létrehozásával.

*A (z) "<diskEncryptionSetID" lemezes titkosítási készletnek \> ugyanabban az előfizetésben (<subscriptionID) kell lennie \> , mint a gyűjtemény erőforrásának.*  
**OK** : a lemezes titkosítási készletek csak abban az előfizetésben és régióban használhatók, amelyben létrehozták őket.  
**Megkerülő megoldás** : hozzon létre vagy használjon titkosítási készletet ugyanabban az előfizetésben és régióban, mint a rendszerkép verziója

*Titkosított forrás: a (z) "<resourceID \> " eltérő előfizetés-azonosítóval rendelkezik, mint az aktuális katalógus-rendszerkép "<subscriptionID 1" előfizetése \_ \> . Próbálkozzon újra egy titkosítatlan forrással, vagy használja a forrás "<subcriptionID \_ 2" előfizetését \> a katalógus rendszerkép-verziójának létrehozásához.*  
**OK** : a megosztott rendszerkép-katalógus jelenleg nem támogatja a rendszerkép-verziók más előfizetésben való létrehozását más forrásokból, ha a forrás képe titkosítva van.  
**Megkerülő megoldás** : használjon titkosítatlan forrást, vagy hozza létre a rendszerkép verzióját a forrással megegyező előfizetésben.

*Nem található a lemez titkosítási készlete <diskEncryptionSetID \> .*  
**OK** : lehet, hogy helytelen a lemez titkosítása.  
**Áthidaló megoldás** : Győződjön meg arról, hogy helyes a lemez titkosítási készletének erőforrás-azonosítója.

*A rendszerkép-verzió neve érvénytelen. A rendszerkép verziószámának a következőnek kell lennie: nagy (int). Másodlagos (int). Patch (int) formátum, például: 1.0.0, 2018.12.1 stb.*  
**OK** : a rendszerkép verziójának érvényes formátuma 3 egész szám, amelyet pont választja el egymástól. A rendszerkép-verzió neve nem felelt meg az érvényes formátumnak.  
**Áthidaló megoldás** : használjon olyan rendszerkép-verziót, amely a Major (int) formátumú. Másodlagos (int). Patch (int), például: 1.0.0. vagy 2018.12.1.

*A galleryArtifactVersion. properties. publishingProfile. targetRegions. encryption. dataDiskImages. diskEncryptionSetId paraméter értéke érvénytelen.*  
**OK** : az adatlemez-lemezképben használt lemez titkosítási készlet erőforrás-azonosítója érvénytelen formátumot használ.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a lemez titkosítási készletének erőforrás-azonosítója a következő formátumot követi:/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

*A galleryArtifactVersion. properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId paraméter értéke érvénytelen.* 
 **OK** : az operációsrendszer-lemez lemezképén használt lemez titkosítási készlet erőforrás-azonosítója érvénytelen formátumot használ  
**Áthidaló megoldás** : Győződjön meg arról, hogy a lemez titkosítási készletének erőforrás-azonosítója a következő formátumot követi:/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

*Nem adható meg az új adatlemez-lemezkép titkosítási LUN [<szám \> ] a (<régió] régióban található, lemezes titkosítási készlettel a \> frissítési katalógus lemezkép-verziójának kéréséhez. A verzió frissítéséhez távolítsa el az új logikai egységet. Ha módosítania kell az adatlemez-lemezkép titkosítási beállításait, létre kell hoznia egy új katalógus-rendszerkép verzióját a megfelelő beállításokkal.*  
**OK** : a rendszer a meglévő lemezkép-verzió adatlemezéhez adta a titkosítást. Nem lehet titkosítást hozzáadni egy meglévő rendszerkép-verzióhoz.  
**Megkerülő megoldás** : hozzon létre egy új katalógus-rendszerkép verzióját, vagy távolítsa el a hozzáadott titkosítási beállításokat.

*A katalógus összetevő verziójának forrása csak közvetlenül a storageProfile alatt, illetve az egyes operációs rendszereken vagy adatlemezeken lehet megadni. Egy és csak egy Forrástípus (felhasználói lemezkép, pillanatkép, lemez, virtuális gép) adható meg.*  
**OK** : a forrás azonosítója hiányzik.  
**Megkerülő megoldás** : Győződjön meg arról, hogy a forrás azonosítója megtalálható.

*A forrás nem található: <resourceID \> . Győződjön meg arról, hogy a forrás létezik.*  
**OK** : lehet, hogy a forrás resourceID helytelen.  
**Áthidaló megoldás** : Győződjön meg arról, hogy a forrás resourceID helyes.

*A galleryArtifactVersion. properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId a (z) "<1. régió" \_ <a (z) " \> \> <region \_ 2" régió megfelelő lemezéhez használt lemez-titkosítási készlet szükséges. \>*  
**OK** : a (z) <2. régió operációsrendszer-lemezén titkosítást használtak \_ \> , de nem az \_ 1. <régióban \> .  
**Áthidaló megoldás** : Ha az operációsrendszer-lemezen titkosítást használ, használja a titkosítást minden régióban.

*Egy lemezes titkosítási készlet szükséges a (z) "<régió 1" terület "LUN <száma" lemezéhez \> , \_ mert a (z) " \> <diskEncryptionSetID" nevű lemezes titkosítási csoport a (z) \> "<régió \_ 2" régió megfelelő lemezére van felhasználva. \>*  
**OK** : a (z) <régióban a LUN <számú adatlemez titkosítása a \> \_ 2 \> . <régióban nem található \_ \> .  
**Megkerülő megoldás** : Ha adatlemezen titkosítást használ, használja a titkosítást minden régióban.

*Érvénytelen LUN [<szám \> ] volt megadva a encryption. dataDiskImages. A LUN értékének a következő értékek egyikének kell lennie: [' 0, 9 '].*  
**OK** : a titkosításhoz megadott LUN-szám nem felel meg a virtuális géphez csatolt lemezek logikai egységének.  
**Megkerülő megoldás** : módosítsa a titkosításban lévő LUN számát a virtuális gépen található adatlemez LUN-számával.

*A (z) "<száma" duplikált logikai egységeket \> adta meg a (z) "<régió \> " titkosítási. dataDiskImages.*  
**OK** : <régióban használt titkosítási beállítások \> legalább kétszer megadták a LUN-számot.  
**Megkerülő megoldás** : módosítsa a lun számát <régióban \> , és győződjön meg arról, hogy az összes LUN-szám egyedi a <régióban \> .

*A OSDiskImage és a DataDiskImage nem mutathat ugyanarra a blobra <SourceId forrásazonosító\>*  
**OK** : az operációsrendszer-lemez és legalább egy adatlemez forrása nem egyedi.  
**Áthidaló megoldás** : módosítsa az operációsrendszer-lemez és/vagy az adatlemez forrását, és győződjön meg arról, hogy az operációsrendszer-lemez és az egyes adatlemezek egyediek.

*Ismétlődő régiók nem engedélyezettek a célhely közzétételi régióiban.*  
**OK** : egynél többször szerepel a közzétételi régiók között a régió.  
**Áthidaló megoldás** : távolítsa el az ismétlődő régiót.

*Új adatlemezek hozzáadása vagy egy adatlemez logikai egységének módosítása meglévő lemezképben nem engedélyezett.*  
**OK** : a lemezkép-verzióra irányuló frissítési hívás egy új adatlemezt tartalmaz, vagy egy lemez új LUN-számmal rendelkezik.  
**Megkerülő megoldás** : használja a meglévő rendszerkép-verzió logikai egységének számát és adatlemezét.

*A lemez titkosítási készletének <diskEncryptionSetID \> ugyanabban az előfizetési <subscriptionID kell lennie \> , mint a Gallery-erőforrásnak.*  
**OK** : a megosztott képtárat jelenleg nem támogatja egy másik előfizetésben lévő lemezes titkosítási csoport használata.  
**Áthidaló megoldás** : hozza létre a lemezkép verzióját és a lemez titkosítási készletét ugyanabban az előfizetésben.

## <a name="issues-creating-or-updating-a-vm-or-scale-sets-from-image-version"></a>VIRTUÁLIS gépek vagy méretezési csoportok rendszerkép-verzióból való létrehozásának vagy frissítésének problémái ##

*Az ügyfél jogosult a (z) "Microsoft. számítás/galériák/lemezképek/verziók/olvasás" művelet végrehajtására a hatókörön <resourceID \> , de a jelenlegi bérlő <tenantId1 \> nem jogosult a csatolt előfizetés <a subscriptionId2 elérésére \> .*  
**OK** : a virtuális gép vagy a méretezési csoport egy másik bérlőben található SIG-lemezképpel lett létrehozva. A virtuális gép vagy a méretezési csoport módosítására tett kísérletet, de nem fér hozzá a képet birtokló előfizetéshez.  
**Megkerülő megoldás** : lépjen kapcsolatba a rendszerkép-verzió előfizetésének tulajdonosával, hogy olvasási hozzáférést biztosítson a rendszerkép verziójához.

*A Gallery <resourceID \> nem érhető el <régió \> régiójában. Kérjük, lépjen kapcsolatba a rendszerkép tulajdonosával, hogy replikálja ezt a régiót, vagy módosítsa a kért régiót.*  
**OK** : a virtuális gép olyan régióban jön létre, amely nem szerepel a katalógus rendszerképének közzétett régiói között.  
**Áthidaló megoldás** : replikálja a rendszerképet a régióba, vagy hozzon létre egy virtuális gépet a katalógus rendszerképének közzétételi régióiban található egyik régióban.

*A "osProfile" paraméter használata nem engedélyezett.*  
**OK** : rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot adott meg egy speciális rendszerkép-verzióból létrehozott virtuális géphez.  
**Áthidaló megoldás** : ne adja meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot, ha létre szeretne hozni egy virtuális gépet a rendszerképből. Ellenkező esetben használjon általánosított rendszerkép-verziót, és adja meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot.

*A kötelező "osProfile" paraméter hiányzik (null).*  
**OK** : a virtuális gép általánosított rendszerképből lett létrehozva, és hiányzik a rendszergazdai Felhasználónév, jelszó vagy SSH-kulcs. Mivel az általánosított rendszerképek nem őrzik meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsokat, ezeket a mezőket meg kell adni egy virtuális gép vagy méretezési csoport létrehozásakor.  
**Megkerülő megoldás** : határozza meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot, vagy használjon speciális rendszerkép-verziót.

*A katalógus rendszerképének verziója nem hozható létre a következőből: <resourceID, \> mert a szülő-katalógus rendszerképében lévő operációs rendszer állapota ("Specialized") nem "általánosított".*  
**OK** : a rendszerkép verziója egy általánosított forrásból jön létre, de a szülő definíciója speciális.  
**Áthidaló megoldás** : hozza létre a rendszerkép verzióját egy speciális forrás használatával, vagy használjon általánosított szülő-definíciót.

*A virtuálisgép-méretezési csoport nem frissíthető <vmssName, mert a virtuálisgép- \> méretezési csoport aktuális operációsrendszer-állapota általánosítva van, amely nem azonos a frissített katalógus-RENDSZERKÉP operációsrendszer-állapotával.*  
**OK** : a méretezési csoport aktuális forrásának képe egy általánosított forrás képe, de a frissítése egy speciális forrás-lemezképpel történik. Az aktuális forrás rendszerképnek és a méretezési csoport tartozó új forrásnak azonos állapotban kell lennie.  
**Áthidaló megoldás** : a méretezési csoport frissítéséhez használjon általánosított rendszerkép-verziót.

*A lemezes titkosítási készlet <diskEncryptionSetId \> a megosztott képtárban <versionId az \> előfizetés <subscriptionId1 tartozik, \> és nem használható a (z) "" erőforrással az előfizetésben <subscriptionId2\>*  
**OK** : a lemezkép verziójának titkosításához használt lemezes titkosítási készlet egy másik előfizetésben található, mint a lemezkép verziójának futtatásához szükséges előfizetés.  
**Megkerülő megoldás** : használja ugyanazt az előfizetést a lemezkép verziója és a lemez titkosítási készlete számára.

*A virtuális gép vagy a virtuálisgép-méretezési csoport létrehozása hosszú időt vesz igénybe.*  
**Áthidaló megoldás** : Ellenőrizze, hogy a virtuális gép vagy virtuálisgép-méretezési csoport létrehozásához használni kívánt **OSType** megegyezik-e a rendszerkép verziójának létrehozásához használt **OSType** . 

## <a name="issues-creating-a-disk-from-an-image-version"></a>Problémák egy lemez lemezkép-verzióból való létrehozásakor ##

*A imageReference paraméter értéke érvénytelen.*  
**OK** : egy SIG-lemezkép verzióról lemezre való exportálást próbált meg használni, de a lemezképen nem létező LUN-pozíciót használt.    
**Megkerülő megoldás** : Ellenőrizze a rendszerkép verziószámát, hogy milyen logikai egységeket használ.

## <a name="unable-to-share-resources"></a>Az erőforrások nem oszthatók meg

Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md)használatával megoszthatja a megosztott képtárat, a képdefiníciót és a rendszerkép verziójának erőforrásait az előfizetések között. 

## <a name="replication-is-slow"></a>A replikálás lassú

A **--Expand ReplicationStatus** jelzővel ellenőrizze, hogy befejeződött-e a replikáció az összes megadott régióban. Ha nem, várjon legfeljebb 6 órát, amíg a feladatok befejeződik. Ha nem sikerül, aktiválja újra a parancsot a rendszerkép verziójának létrehozásához és replikálásához. Ha sok célpont van a lemezkép verziójának replikálására, a replikálást fázisokban érdemes elvégezni.

## <a name="azure-limits-and-quotas"></a>Az Azure-ra vonatkozó korlátok és kvóták 

Az [Azure korlátai és kvótái](../azure-resource-manager/management/azure-subscription-service-limits.md) az összes megosztott képkatalógusra, a képdefinícióra és a rendszerkép verzió-erőforrásaira érvényesek. Győződjön meg arról, hogy az előfizetések korlátain belül van. 


## <a name="next-steps"></a>További lépések

További információ a [megosztott képtárakról](./linux/shared-image-galleries.md).