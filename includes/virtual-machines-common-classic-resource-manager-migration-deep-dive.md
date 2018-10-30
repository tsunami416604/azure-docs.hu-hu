---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: dc871b29cdafa57d337f9be6cf01e76212f31b67
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227376"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS-erőforrások áttelepítése a klasszikus üzemi modellből Azure Resource Manager
Először is fontos, az infrastruktúra-szolgáltatás (IaaS) erőforrásként adatsíkon és a felügyeleti sík műveleteket közötti különbségek megértése.

* *Felügyeleti/vezérlősík* a felügyeleti/vezérlősík vagy az API az erőforrások módosítását végző érkező hívásokat írja le. Például az olyan műveletek, mint a virtuális gépek létrehozása vagy újraindítása, vagy a virtuális hálózatok új alhálózattal való frissítése a futó erőforrások felügyeletét szolgálják. Ezek nem csatlakozásra közvetlenül nincsenek hatással a virtuális gépekhez.
* *Az adatsík* (alkalmazás) magának az alkalmazásnak a futtatókörnyezetét írja le, és osztályt, amely nem az Azure API-n keresztül interakciót foglal magában. Például felkeresik a webhelyet, vagy alkalmazásinterakciónak futó SQL Server-példányt vagy egy MongoDB-kiszolgálóról származó adatok olyan adatok adatsík interakciókat. További példák lehetnek egy blob átmásolása egy tárfiókot, és a távoli asztal protokoll (RDP) vagy a Secure Shell (SSH) a virtuális géppel használandó nyilvános IP-cím elérése. Ezek a műveletek működtetik az alkalmazást a számítási, hálózati és tárolási erőforrásokon.

Az adatsík megegyezik a klasszikus üzemi modell és a Resource Manager-vermek között. A különbség az, hogy az áttelepítési folyamat során a Microsoft átalakítja az erőforrásokat a klasszikus üzemi modellből, amely a Resource Manager veremben reprezentációja. Ennek eredményeképpen kell új eszközök, API-k és SDK-k használata a Resource Manager veremben az erőforrások kezeléséhez.

![Bemutató diagram, amelyek a felügyeleti/vezérlősík és az adatsík közötti különbség](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Egyes migrálási forgatókönyvekben az Azure platform leállítja, felszabadítja és újraindítja a virtuális gépeket. Ennek hatására az adatsíkon rövid idejű leállást.
>

## <a name="the-migration-experience"></a>A migrálási folyamat
Mielőtt megkezdi az áttelepítést:

* Győződjön meg róla, hogy a migrálni kívánt erőforrások nem használnak nem támogatott szolgáltatásokat vagy konfigurációkat. A platform általában észleli ezeket a problémákat, és hibát jelez.
* Ha rendelkezik, amelyek nem virtuális hálózatban lévő virtuális gépek, ezeket leállítva és felszabadítva az előkészítési művelet részeként. Ha nem szeretné elveszteni a nyilvános IP-címe, fontolja meg az IP-cím foglalása az előkészítési művelet elindítása előtt. Ha a virtuális gépek a virtuális hálózatban, ezeket nem leállítva és felszabadítva.
* A migrálást ütemezze munkaidőn kívülre, hogy a migrálás során esetlegesen felmerülő nem várt hibák kezelhetőek legyenek.
* Töltse le a virtuális gépek aktuális konfigurációját a PowerShell vagy a parancssori felület (CLI) parancsai, illetve REST API-k segítségével, hogy könnyebb legyen az érvényesítés az előkészítési lépés után.
* Frissítse az automatizálási és operacionalizálás parancsfájlok a Resource Manager üzemi modell kezelésére a migrálás megkezdése előtt. GET műveleteket is használhat, ha az erőforrások már előkészített állapotban vannak.
* Értékelje ki a szerepköralapú hozzáférés-vezérlés (RBAC) házirendekben, amelyek a klasszikus üzemi modellben az IaaS-erőforrások vannak konfigurálva, és tervezze meg, az áttelepítés befejezése után.

A migrálási munkafolyamat a következőképpen:

![A migrálási munkafolyamatot bemutató ábra](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> A műveletek a következő szakaszokban ismertetett összes idempotens. Ha egy nem támogatott funkció vagy konfigurációs hibának olyan probléma lép fel, próbálkozzon újra az előkészítési, megszakítása vagy a véglegesítési műveletet. Azure újrapróbálkozik a művelettel.
>
>

### <a name="validate"></a>Érvényesítés
Az érvényesítés művelet a migrálási folyamat első lépése. Ez a lépés célja, elemezheti az erőforrások, amelyeket szeretné áttelepíteni a klasszikus üzemi modellben állapotát. A művelet kiértékeli az erőforrások (sikeres vagy sikertelen) migrálásra való alkalmasságának-e.

Válassza a virtuális hálózat vagy a felhőszolgáltatás (Ha nem a virtuális hálózatban), amelyet szeretne érvényesíteni az áttelepítéshez. Ha az erőforrás nem migrálható, az Azure miért felsorolja az okokat.

#### <a name="checks-not-done-in-the-validate-operation"></a>Ellenőrzi, hogy az érvényesítés művelet a nem kész

Az érvényesítés művelet csak elemzi a klasszikus üzemi modellben található erőforrások állapotát. Ellenőrizheti, hogy az összes hiba és a klasszikus üzemi modellben a különféle konfigurációk miatt nem támogatott forgatókönyveket. Nem alkalmas az összes olyan problémákat, amelyek az Azure Resource Manager-készletben az erőforrások áttelepítése során előfordulhat, hogy kivetett. Ezek a problémák csak a rendszer ellenőrzi, ha az erőforrásokat az áttelepítés (az előkészítési művelet) a következő lépésben átalakítási mennek keresztül. Az alábbi táblázat nincs megadva az érvényesítés művelet a hibák:


|Hálózatkezelés ellenőrzi az ellenőrzési művelet nem található|
|-|
|ER- és VPN-átjárókkal rendelkező virtuális hálózat.|
|A virtuális hálózati átjáró kapcsolat megszakad.|
|Minden ER-kapcsolatcsoportokat előre migrálta az Azure Resource Manager-készletben.|
|Az Azure Resource Manager-kvóta keres a hálózati erőforrások. Például: statikus nyilvános IP-címet, a dinamikus nyilvános IP-címek, load balancer, hálózati biztonsági csoportok, útvonaltáblákat és hálózati adapterek. |
| Üzembe helyezés és a virtuális hálózat összes load balancer-szabályok érvényesek. |
| Ütköző magánhálózati IP-címek között a virtuális gépek felszabadítva az azonos virtuális hálózatba. |

### <a name="prepare"></a>Előkészítés
Az előkészítés művelet a migrálási folyamat második lépése. Ebben a lépésben az a célja, hogy az IaaS-erőforrások a klasszikus üzemi modellből Resource Manager-erőforrásokkal történő átalakításának szimulálása. További az előkészítési művelet megadja a egymás mellett vizuálisan megjelenítse.

> [!NOTE] 
> A klasszikus üzemi modellben az erőforrások e lépés során nem módosulnak. Ez biztonságos lépése el áttelepítési kívánt üzemeltetéséhez. 

Akkor válassza ki a virtuális hálózat vagy a felhőszolgáltatás (Ha nem virtuális hálózat), hogy szeretné-e előkészítése az áttelepítésre.

* Ha az erőforrás nem migrálható, az Azure leállítja a migrálási folyamatot, és felsorolja az előkészítés művelet sikertelenségének okát.
* Ha az erőforrás migrálható, az Azure zárolja a felügyeleti sík műveleteit a migrálás alatt álló erőforrásokhoz. Például nem csatolhat adatlemezt egy migrálás alatt álló virtuális géphez.

Az Azure ezután elindítja a metaadatok a migrálás a klasszikus üzemi modellből Resource Manager a migrált erőforrások.

Az előkészítési művelet befejezése után lehetősége van a klasszikus üzemi modell és a Resource Manager az erőforrások megjelenítésére. Az Azure platform a klasszikus üzemi modellben minden egyes felhőszolgáltatáshoz létrehoz egy erőforráscsoport-nevet a következő mintának megfelelően: `cloud-service-name>-Migrated`.

> [!NOTE]
> Már nem, válassza ki a migrált erőforrásokhoz létrehozott erőforráscsoport nevét (például "-át"). Áttelepítés befejezése után azonban használhatja az áthelyezés szolgáltatásával az Azure Resource Manager át kell helyeznie az erőforrásokat bármely erőforráscsoportban szeretné. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../articles/resource-group-move-resources.md).

A következő két képernyőképeken az eredmény megjelenítése, miután a művelet sikeres előkészítéséhez. Az első utótagcímkéjét jeleníti meg egy erőforráscsoportot, amely az eredeti felhőszolgáltatást tartalmazza. A másodikban az új "-át" a megfelelő Azure Resource Manager-erőforrásokat tartalmazó erőforráscsoportot.

![Eredeti felhőszolgáltatást bemutató képernyőkép](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Az előkészítési művelet az Azure Resource Manager-erőforrásokat megjelenítő képernyőkép](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Íme az erőforrások háttérbeli tekintse meg az előkészítési fázis befejezése után. Vegye figyelembe, hogy az adatsík az erőforrás azonos. A felügyeleti sík (klasszikus üzembe helyezési modell), mind a vezérlősík (Resource Manager) a jelzi.

![Az előkészítési fázisába ábrája](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuális gépek, amelyek nem egy virtuális hálózatot a klasszikus üzemi modellben lesznek állítva és az áttelepítés ezen szakaszában felszabadítva.
>

### <a name="check-manual-or-scripted"></a>Ellenőrzés (manuális vagy szkriptalapú)
Az ellenőrzés lépésben lehetősége van a korábban letöltött konfiguráció használatával helyesnek tűnik, hogy az áttelepítés ellenőrzése. Azt is megteheti bejelentkezhet a portálra, és szúrópróbaszerű ellenőrzésével megerősítheti a tulajdonságok és az erőforrások ellenőrzése, hogy a metaadatok migrálása rendben.

Ha virtuális hálózatot migrál, a virtuális gépek legtöbb konfigurációja nem indul újra. A virtuális gépeken alkalmazások esetén ellenőrizheti, hogy az alkalmazás továbbra is fut.

Tekintse meg, ha a virtuális gépek a várt módon működnek, és a frissített parancsfájlok megfelelő működéséhez figyelési és működési szkriptek tesztelheti. Csak a GET műveletek támogatottak, amikor az erőforrások az előkészített állapotban vannak.

Nincs beállított ablak, amely előtt a migrálás véglegesítése szükséges időt. Bármennyi időt eltölthet ebben az állapotban. A felügyeleti sík azonban zárolva van ezekhez az erőforrásokhoz, amíg meg nem szakítja vagy véglegesíti a műveletet.

Ha bármilyen problémát észlel, mindig megszakíthatja a migrálást, és visszatérhet a klasszikus üzemi modellhez. Miután visszatér, az Azure megnyitja a felügyeleti sík műveleteket az erőforráson, úgy, hogy folytathassa a normál üzemeltetést a virtuális gépek a klasszikus üzemi modellben.

### <a name="abort"></a>Megszakítás
Ez az egy választható lépés, ha azt szeretné, állítsa vissza a módosítást a klasszikus üzemi modellre és leállíthatja a migrálást. Ez a művelet törli a Resource Manager-metaadatokat (létrehozott az előkészítési lépés) az erőforrások. 

![Megszakítási lépés ábra](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Ez a művelet nem hajtható végre, Miután elindította a véglegesítés műveletet.     
>

### <a name="commit"></a>Véglegesítés
Az ellenőrzés befejezése után véglegesítheti a migrálást. Erőforrások ezután nem jelennek meg a klasszikus üzemi modellben, és csak a Resource Manager-alapú üzemi modellben elérhető. A migrált erőforrások csak az új portálon kezelhetők.

> [!NOTE]
> Ez egy idempotens művelet. Ha sikertelen, próbálja megismételni a műveletet. Ha továbbra is sikertelen, hozzon létre egy támogatási jegyet, vagy hozzon létre egy fórumbejegyzést az "ClassicIaaSMigration" címkével, a saját [VM fórumunkon](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Véglegesítés lépés ábra](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Áttelepítésének folyamatábrája

A következő folyamatábra bemutatja, hogyan a migrálás folytatásához:

![Képernyőkép a migrálási lépésekről](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>A klasszikus üzemi modell a Resource Manager-erőforrások fordítása
A klasszikus üzemi modell és az erőforrások Resource Manager alapú ábrázolását a következő táblázatban találja. Az egyéb szolgáltatások és erőforrások jelenleg nem támogatottak.

| Klasszikus ábrázolás | Resource Manager-ábrázolás | Megjegyzések |
| --- | --- | --- |
| Felhőszolgáltatás neve |DNS-név |A migrálás során minden felhőszolgáltatáshoz egy új erőforráscsoport jön létre a következő elnevezési mintának megfelelően: `<cloudservicename>-migrated`. Ez az erőforráscsoport tartalmazza az összes erőforrást. A felhőszolgáltatás egy DNS-névvé alakul, amely a nyilvános IP-címhez van társítva. |
| Virtuális gép |Virtuális gép |A virtuális gépre jellemző tulajdonságok a migrálás során nem változnak. Bizonyos osProfile-adatok, például a számítógép neve, a klasszikus üzemi modellben nem tárolja, és üresek maradnak a migrálás után. |
| A virtuális géphez csatolt lemezerőforrások |A virtuális géphez csatolt implicit lemezek |A lemezek nem legfelső szintű erőforrásként vannak modellezve a Resource Manager-alapú üzemi modellben. A virtuális gép implicit lemezeiként lesznek migrálva. Jelenleg csak a virtuális géphez csatolt lemezek támogatottak. Resource Manager virtuális gépek most már a klasszikus üzemi modell, amely lehetővé teszi, hogy a lemezek egyszerű migrálását frissítések nélkül a storage-fiókok használatához. |
| Virtuálisgép-bővítmények |Virtuálisgép-bővítmények |Az összes erőforrás-bővítmény, az XML-bővítmények kivételével, migrálva lett a klasszikus üzemi modellből. |
| A virtuális gép tanúsítványai |Tanúsítványok az Azure Key Vaultban |Ha egy felhőszolgáltatás szolgáltatási tanúsítványokat tartalmaz, az áttelepítés hoz létre egy új, az Azure key vault egy felhőszolgáltatás, és a tanúsítványok helyezi át a key vaultban. A virtuális gépek frissülnek, hogy a kulcstartóban található tanúsítványokra hivatkozzanak. <br><br> Ne törölje a key vaultban. Emiatt a virtuális gép egy sikertelen állapotba kerülnek. |
| WinRM-konfiguráció |WinRM-konfiguráció osProfile alatt |A Rendszerfelügyeleti webszolgáltatások konfiguráció az áthelyezésekor változatlan marad a migrálás során. |
| Rendelkezésre állási csoport tulajdonsága |Rendelkezésre állási csoport erőforrás | Rendelkezésre állási csoport specifikációja egy tulajdonság a virtuális gépen a klasszikus üzemi modellben. A rendelkezésre állási csoportok legfelső szintű erőforrásokká alakulnak a migrálás részeként. A következő konfigurációk nem támogatottak: több rendelkezésre állási csoport felhőszolgáltatásonként, illetve egy vagy több rendelkezésre állási csoport olyan virtuális gépekkel, amelyek nem tartoznak egy rendelkezésre állási csoporthoz sem egy felhőszolgáltatásban. |
| Hálózati konfiguráció egy virtuális gépen |Elsődleges hálózati adapter |A hálózati konfiguráció egy virtuális gépen az elsődleges hálózati adapter erőforrásként jelenik meg a migrálás után. Azoknál a virtuális gépeknél, amelyek nem tagjai virtuális hálózatnak, a belső IP-cím módosul a migrálás során. |
| Több hálózati adapter egy virtuális gépen |Hálózati illesztők |Ha egy virtuális gépen több hálózati adapter társítva, mindegyik hálózati interfész lesz az összes tulajdonsággal együtt az áttelepítés részeként legfelsőbb szintű erőforráshoz. |
| Elosztott terhelésű végpont csoport |Terheléselosztó |A klasszikus üzemi modellben a platform egy implicit terheléselosztót rendel minden felhőszolgáltatáshoz. A migrálás során egy új terheléselosztó erőforrás jön létre, és a terheléselosztó végpont csoport terheléselosztó szabályokká alakul. |
| Bejövő NAT-szabályok |Bejövő NAT-szabályok |A virtuális gépen meghatározott bemeneti végpontok a terheléselosztó hálózati címfordításra vonatkozó bejövő szabályaivá alakulnak a migrálás során. |
| Virtuális IP-cím |Nyilvános IP-cím DNS-névvel |A virtuális IP-cím nyilvános IP-cím lesz, és a terheléselosztóhoz társított. A virtuális IP-cím csak akkor migrálható, ha társítva van hozzá egy bemeneti végpont. |
| Virtuális hálózat |Virtuális hálózat |A virtuális hálózat az összes tulajdonságával a Resource Manager-alapú üzemi modellbe lesz migrálva. Létrejön egy új erőforráscsoport a következő névvel: `-migrated`. |
| Fenntartott IP-címek |Nyilvános IP-cím statikus kiosztási módszerrel |A terheléselosztóhoz társított fenntartott IP-címek a felhőszolgáltatással vagy a virtuális géppel együtt migrálódnak. A nem társított fenntartott IP-címek migrálása jelenleg nem támogatott. |
| Nyilvános IP-cím virtuális gépenként |Nyilvános IP-cím dinamikus kiosztási módszerrel |A virtuális géphez társított nyilvános IP-cím nyilvános IP-cím erőforrássá alakul, statikus kiosztási módszerrel beállítva. |
| NSG-k |NSG-k |Az alhálózatokhoz társított hálózati biztonsági csoportokat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A hálózati biztonsági csoportok a klasszikus üzemi modellben nem törlődnek a migráláskor. A hálózati biztonsági csoportok felügyeletisík-műveletei azonban zárolva vannak, amíg a migrálás folyamatban van. |
| DNS-kiszolgálók |DNS-kiszolgálók |A virtuális hálózathoz vagy virtuális géphez társított DNS-kiszolgálók a megfelelő erőforrás-migrálás részeként migrálódnak az összes tulajdonsággal együtt. |
| UDR-ek |UDR-ek |Az alhálózatokhoz társított felhasználó által megadott útvonalakat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A felhasználó által megadott útvonalak a klasszikus üzemi modellben nem törlődnek a migráláskor. A felhasználó által megadott útvonalak felügyeletisík-műveletei zárolva vannak, amíg a migrálás folyamatban van. |
| IP-továbbítási tulajdonság a virtuális gép hálózati konfigurációjában |IP-továbbítási tulajdonság a hálózati adapteren |A virtuális gép IP-továbbítási tulajdonsága a hálózati adapter tulajdonságává alakul a migrálás közben. |
| Terheléselosztó több IP-címmel |Terheléselosztó több nyilvános IP-erőforrással |Minden nyilvános IP-címet a terheléselosztóhoz társított nyilvános IP-erőforrást alakítani, és az áttelepítés után a terheléselosztóhoz társított. |
| Belső DNS-nevek a virtuális gépen |Belső DNS-nevek a hálózati adapteren |A migrálás során a virtuális gép belső DNS-utótagjai egy „InternalDomainNameSuffix” nevű csak olvasható tulajdonságba migrálódnak a hálózati adapteren. Az áttelepítés után az utótag változatlan marad, és a virtuális gép feloldása továbbra is működik, mint korábban. |
| Virtuális hálózati átjáró |Virtuális hálózati átjáró |Virtuális hálózati átjáró tulajdonságai a migrálás változatlan marad. Az átjáróhoz társított virtuális IP-cím sem változik. |
| Helyi hálózati hely |Helyi hálózati átjáró |Helyi hálózati hely tulajdonságai migrálódnak egy helyi hálózati átjáró nevű új erőforrásba. Ez jelöli, a helyszíni címelőtagokat és a távoli átjáró IP-cím. |
| Kapcsolati hivatkozások |Kapcsolat |Az átjáró és a hálózati konfigurációt a helyi hálózati hely közötti kapcsolati hivatkozásokat képviseli egy új erőforrást kapcsolatnak nevezik. Hálózati konfigurációs fájlokban található kapcsolati hivatkozások összes tulajdonságát másolja a kapcsolati erőforrás változatlan marad. A klasszikus üzemi modellben lévő virtuális hálózatok közötti kapcsolatot a virtuális hálózatot képviselő helyi hálózati helyre két IPsec-alagutak létrehozásával érhető el. Ezt átalakítják a virtuális hálózat –-virtuális-hálózat kapcsolat típusát a Resource Manager-modellben, anélkül, hogy a helyi hálózati átjárók. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Az automatizálás és az eszközök módosítása a migrálás után
Az erőforrások a klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe való migrálása részeként frissítenie kell a meglévő automatizálást vagy eszközöket annak érdekében, hogy ez a migrálás után továbbra is.
