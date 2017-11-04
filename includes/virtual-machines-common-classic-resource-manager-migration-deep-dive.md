## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS-erőforrások áttelepítése a klasszikus telepítési modellből az Azure Resource Manager
Először fontos egy szolgáltatási (IaaS) erőforrásként infrastruktúrán adatok-vezérlősík és felügyeleti-vezérlősík műveletek közötti különbségek megértése.

* *Felügyeleti és szabályozási vezérlősík* ismerteti, amelyek a felügyeleti és szabályozási vezérlősík vagy az API erőforrások módosítása a hívást. Például az olyan műveletek, mint a virtuális gépek létrehozása vagy újraindítása, vagy a virtuális hálózatok új alhálózattal való frissítése a futó erőforrások felügyeletét szolgálják. Nincsenek közvetlenül hatással a Kapcsolódás a virtuális gépeket.
* *Adatok vezérlősík* (alkalmazás) magának az alkalmazásnak a futásidejű ismerteti, és magában foglalja a nem halad át az Azure API-példányok való együttműködéshez. Például a webhely eléréséhez, vagy húzza az adatokat egy futó SQL Server-példány vagy a MongoDB-kiszolgálóhoz, olyan adatok vezérlősík vagy alkalmazás interakciókat. Más például egy blobot másol a tárfiókból és egy nyilvános IP-cím a távoli asztal protokoll (RDP) vagy a Secure Shell (SSH) a virtuális géppé elérése. Ezek a műveletek működtetik az alkalmazást a számítási, hálózati és tárolási erőforrásokon.

Az adatok vezérlősík megegyezik a klasszikus üzembe helyezési modellel és erőforrás-kezelő verem között. A különbség, hogy az áttelepítés során a Microsoft az eszköz az erőforrásokat a klasszikus telepítési modellből, amely a Resource Manager-készletben ábrázolását. Ennek eredményeképpen kell új eszközöket, API-k és SDK-k segítségével kezelheti az erőforrásokat a Resource Manager-készletben.

![A felügyeleti és szabályozási vezérlősík és adatok vezérlősík közötti különbség bemutató diagram, amelyek](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Egyes migrálási forgatókönyvekben az Azure platform leállítja, felszabadítja és újraindítja a virtuális gépeket. Ennek hatására az adatok-vezérlősík rövid idejű leállást.
>

## <a name="the-migration-experience"></a>A migrálási folyamat
Az áttelepítés előtt:

* Győződjön meg róla, hogy a migrálni kívánt erőforrások nem használnak nem támogatott szolgáltatásokat vagy konfigurációkat. A platform általában észleli ezeket a problémákat, és hibát jelez.
* Ha virtuális gépeket, amelyek nem része virtuális hálózatnak, ezeket leállítása és felszabadítása. lehetséges az előkészítési művelet részeként. Ha meg szeretne tartani a nyilvános IP-cím, fontolja meg az IP-cím foglalása ahhoz, hogy kiváltsa az előkészítési művelet. Ha a virtuális gépek virtuális hálózatban, ezeket nem leállítása és felszabadítása.
* A migrálást ütemezze munkaidőn kívülre, hogy a migrálás során esetlegesen felmerülő nem várt hibák kezelhetőek legyenek.
* Töltse le a virtuális gépek aktuális konfigurációját a PowerShell vagy a parancssori felület (CLI) parancsai, illetve REST API-k segítségével, hogy könnyebb legyen az érvényesítés az előkészítési lépés után.
* Az automatizálás és operationalization parancsfájlok a Resource Manager üzembe helyezési modellben kezelésére, az áttelepítés megkezdése előtt frissítse. GET műveleteket is használhat, ha az erőforrások már előkészített állapotban vannak.
* Értékelje ki a szerepköralapú hozzáférés-vezérlést (RBAC) házirendekben, amelyek az IaaS-erőforrásokra, a klasszikus üzembe helyezési modellben van konfigurálva, és megtervezheti, hogy az áttelepítés befejezése után.

Az áttelepítési munkafolyamat a következőképpen történik:

![Az áttelepítési munkafolyamat bemutató diagram, amelyek](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Az alábbi szakaszokban ismertetett olyan összes idempotent. Ha még nem támogatott szolgáltatása vagy konfigurációs hiba problémát, próbálja meg újra a prepare, vagy megszakításra műveletet. Azure próbálja meg újra a műveletet.
>
>

### <a name="validate"></a>Érvényesítés
Az érvényesítés művelet a migrálási folyamat első lépése. Ez a lépés célja a klasszikus üzembe helyezési modellel áttelepíteni kívánt erőforrások állapotát. A művelet értékeli ki, hogy az erőforrások képesek áttelepítési (sikeres vagy sikertelen).

Válassza a virtuális hálózat vagy a felhőszolgáltatás (Ha nincs a virtuális hálózat), amelyet az áttelepítés ellenőrzése. Ha az erőforrás nem kompatibilis az áttelepítés, a Azure miért sorolja fel a okok miatt.

#### <a name="checks-not-done-in-the-validate-operation"></a>Ellenőrzi, hogy nem végezheti el a validate művelet

A validate művelet csak elemzi a klasszikus üzembe helyezési modellel erőforrások állapotát. Ellenőrizheti, hogy az összes hiba és a klasszikus üzembe helyezési modellel különböző konfigurációkban miatt nem támogatott forgatókönyvek. Nincs lehetőség minden problémákat kell keresnie az Azure Resource Manager-készletben előfordulhat, hogy határoznak meg az erőforrások az áttelepítés során. Ezek a problémák csak a rendszer ellenőrzi, ha az erőforrások áttelepítési (az előkészítési művelet) átalakítása a következő lépésben változni. A következő táblázat felsorolja a nincs megadva a validate műveletet a hibák:


|A validate műveletben nem hálózat ellenőrzése|
|-|
|ER és a VPN-átjárók rendelkező virtuális hálózat.|
|A virtuális hálózati átjáró kapcsolat megszakad.|
|Összes ER áramkör előre amelyek áttelepítése az Azure Resource Manager-készletben.|
|Az Azure erőforrás-kezelő kvótája ellenőrzi a hálózati erőforrásokhoz. Például: statikus nyilvános IP-címet, dinamikus nyilvános IP-címek, betöltése a terheléselosztó hálózati biztonsági csoportok, útvonaltábláit és hálózati adapterek. |
| Minden terheléselosztási szabály központi telepítési és a virtuális hálózati érvényesek. |
| Ütköző privát IP-címek virtuális gépek leállítási felszabadítása az azonos virtuális hálózatban lévő között. |

### <a name="prepare"></a>Előkészítés
Az előkészítés művelet a migrálási folyamat második lépése. Ebben a lépésben célja az infrastruktúra-szolgáltatási erőforrásokat a klasszikus telepítési modellből a Resource Managerhez tartozó erőforrások átalakítása szimulálásához. További az előkészítési művelet megadja e-párhuzamos jelenítheti meg.

> [!NOTE] 
> Ezzel a lépéssel nem módosulnak az erőforrások a klasszikus üzembe helyezési modellben. Ez biztonságos lépése kimenő áttelepítési próbált üzemeltetéséhez. 

Kiválasztása a virtuális hálózat vagy a felhőszolgáltatás (Ha a virtuális hálózat nincs), amelyet az áttelepítés előkészítése

* Ha az erőforrás nem kompatibilis az áttelepítés, Azure leállítja az áttelepítési folyamatot, és felsorolja az előkészítési művelet sikertelenségének okát.
* Ha az erőforrás képes áttelepítési, Azure zárolja az erőforrások az áttelepítés alatt a felügyeleti-vezérlősík műveleteket. Például nem csatolhat adatlemezt egy migrálás alatt álló virtuális géphez.

Azure ezután elindítja az áttelepítést, a metaadatok a klasszikus telepítési modellből az erőforrás-kezelő áttelepítése erőforrásokhoz.

Az előkészítési művelet befejezése után lehetősége van az erőforrásokat a klasszikus üzembe helyezési modellben és a Resource Manager alkalmazott. Az Azure platform a klasszikus üzemi modellben minden egyes felhőszolgáltatáshoz létrehoz egy erőforráscsoport-nevet a következő mintának megfelelően: `cloud-service-name>-Migrated`.

> [!NOTE]
> Nincs lehetőség, és válassza ki az áttelepített erőforrások létrehozott erőforráscsoport nevét (például "-áttelepített"). Áttelepítés befejezése után, azonban használhatja az áthelyezés szolgáltatást az Azure Resource Manager bármely erőforráscsoport kívánt erőforrások áthelyezése. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../articles/resource-group-move-resources.md).

A következő két képernyőképeket az eredményeket mutatja, a sikeres művelet előkészítése után. Az első címtárra jeleníti meg egy erőforráscsoportot, amely tartalmazza az eredeti felhőalapú szolgáltatás. A másodikban az új "-át" a azonos Azure Resource Manager erőforrásokat tartalmazó erőforráscsoportot.

![Képernyőkép a eredeti felhőalapú szolgáltatás](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Az előkészítési művelet az Azure Resource Manager erőforrások képernyőkép](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Íme egy háttérben nézze meg az erőforrások az előkészítési fázis befejezése után. Vegye figyelembe, hogy az erőforrás a adatok vezérlősík azonos. A felügyeleti vezérlősík (klasszikus üzembe helyezési modellel), mind a vezérlő vezérlősík (erőforrás-kezelő) a jelzi.

![Az előkészítési fázis ábrája](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuális gépek, amelyek nincsenek rajta egy virtuális hálózatot a klasszikus üzembe helyezési modellel leállítása és felszabadítása az áttelepítés ezen szakaszában.
>

### <a name="check-manual-or-scripted"></a>Ellenőrzés (manuális vagy szkriptalapú)
A jelölőnégyzet a lépésben lehetősége van a korábban letöltött konfigurációt használja, hogy helyes-e jelenik-e az áttelepítés ellenőrzése. Azt is megteheti bejelentkezhet a portálon, és a helyszíni ellenőrzése a tulajdonságok és az erőforrások ellenőrzése, hogy a metaadatok áttelepítési megfelelőnek tűnik.

Ha virtuális hálózatot migrál, a virtuális gépek legtöbb konfigurációja nem indul újra. A virtuális gépek alkalmazásokhoz ellenőrizheti, hogy továbbra is fut-e az alkalmazás.

Tesztelheti a figyelési és működési parancsfájlokat, ha a virtuális gépek vannak a várt módon működik, és a frissített parancsfájlok megfelelő működéséhez. Csak a GET műveletek támogatottak, amikor az erőforrások az előkészített állapotban vannak.

Nincs beállítva ablak, amely előtt véglegesítése az áttelepítés szükséges időt. Bármennyi időt eltölthet ebben az állapotban. A felügyeleti sík azonban zárolva van ezekhez az erőforrásokhoz, amíg meg nem szakítja vagy véglegesíti a műveletet.

Ha bármilyen problémát észlel, mindig megszakíthatja a migrálást, és visszatérhet a klasszikus üzemi modellhez. Után visszaléphet, az Azure megnyitja az erőforrások, a felügyeleti-vezérlősík műveleteket, így folytathatja a virtuális gépek a klasszikus üzembe helyezési modellben a normál működést.

### <a name="abort"></a>Megszakítás
Ez az egy opcionális lépés, ha azt szeretné, állítsa vissza a módosítást, a klasszikus üzembe helyezési modellre, és állítsa le az áttelepítés. Ez a művelet törli a Resource Manager metaadatokat (a prepare lépésben létrehozott) erőforrások. 

![Megszakítási lépés ábrája](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Ez a művelet nem hajtható végre, miután léptetnie a véglegesítési művelet.     
>

### <a name="commit"></a>Véglegesítés
Az ellenőrzés befejezése után véglegesítheti a migrálást. Erőforrások nem jelenik meg többé a klasszikus üzembe helyezési modellel, és csak a Resource Manager üzembe helyezési modellben érhető el. A migrált erőforrások csak az új portálon kezelhetők.

> [!NOTE]
> Ez egy idempotens művelet. Ha nem sikerül, akkor próbálja megismételni a műveletet. Ha továbbra is fennáll, sikertelen, hozzon létre egy támogatási jegy, vagy hozzon létre egy fórumbejegyzést a "ClassicIaaSMigration" címke a [VM fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Véglegesítési lépés ábrája](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Áttelepítés folyamatábrája

Ez a folyamatábra bemutatja, hogyan áttelepítés folytatásához:

![Képernyőkép a migrálási lépésekről](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>A klasszikus üzembe helyezési modellt Resource Managerhez tartozó erőforrások fordítása
A klasszikus üzembe helyezési modellel és erőforrás-kezelő ábrázolásai az erőforrásokat a következő táblázatban található. Az egyéb szolgáltatások és erőforrások jelenleg nem támogatottak.

| Klasszikus ábrázolás | Resource Manager-ábrázolás | Megjegyzések |
| --- | --- | --- |
| Felhőszolgáltatás neve |DNS-név |A migrálás során minden felhőszolgáltatáshoz egy új erőforráscsoport jön létre a következő elnevezési mintának megfelelően: `<cloudservicename>-migrated`. Ez az erőforráscsoport tartalmazza az összes erőforrást. A felhőszolgáltatás egy DNS-névvé alakul, amely a nyilvános IP-címhez van társítva. |
| Virtuális gép |Virtuális gép |A virtuális gépre jellemző tulajdonságok a migrálás során nem változnak. Bizonyos osProfile adatok, például a számítógép nevét, a klasszikus üzembe helyezési modellben nem tárolja, és az áttelepítés után üres marad. |
| A virtuális géphez csatolt lemezerőforrások |A virtuális géphez csatolt implicit lemezek |A lemezek nem legfelső szintű erőforrásként vannak modellezve a Resource Manager-alapú üzemi modellben. A virtuális gép implicit lemezeiként lesznek migrálva. Jelenleg csak a virtuális géphez csatolt lemezek támogatottak. Erőforrás-kezelő virtuális gépek storage-fiókok most már használhatja a klasszikus üzembe helyezési modellel, amelyek lehetővé teszik a lemezek számára könnyen valamelyik frissítés nélkül telepíthetők át. |
| Virtuálisgép-bővítmények |Virtuálisgép-bővítmények |Az összes erőforrás-bővítmény, az XML-bővítmények kivételével, migrálva lett a klasszikus üzemi modellből. |
| A virtuális gép tanúsítványai |Tanúsítványok az Azure Key Vaultban |Ha egy felhőalapú szolgáltatás szolgáltatás tanúsítványokat tartalmazza, az áttelepítés hoz létre egy új, az Azure key vault egy felhőalapú szolgáltatás, és a tanúsítványok áthelyezi a key vault. A virtuális gépek frissülnek, hogy a kulcstartóban található tanúsítványokra hivatkozzanak. <br><br> Ne törölje a key vault. Ennek hatására a virtuális Gépen nyissa meg a sikertelen állapot. |
| WinRM-konfiguráció |WinRM-konfiguráció osProfile alatt |A Rendszerfelügyeleti webszolgáltatások konfiguráció az áthelyezésekor változatlan marad a migrálás során. |
| Rendelkezésre állási csoport tulajdonsága |Rendelkezésre állási csoport erőforrás | Rendelkezésre állási-csoport megadását a virtuális Gépen a klasszikus üzembe helyezési modellel tulajdonság. A rendelkezésre állási csoportok legfelső szintű erőforrásokká alakulnak a migrálás részeként. A következő konfigurációk nem támogatottak: több rendelkezésre állási csoport felhőszolgáltatásonként, illetve egy vagy több rendelkezésre állási csoport olyan virtuális gépekkel, amelyek nem tartoznak egy rendelkezésre állási csoporthoz sem egy felhőszolgáltatásban. |
| Hálózati konfiguráció egy virtuális gépen |Elsődleges hálózati adapter |A hálózati konfiguráció egy virtuális gépen az elsődleges hálózati adapter erőforrásként jelenik meg a migrálás után. Azoknál a virtuális gépeknél, amelyek nem tagjai virtuális hálózatnak, a belső IP-cím módosul a migrálás során. |
| Több hálózati adapter egy virtuális gépen |Hálózati illesztők |A virtuális gépek több hálózati csatolóval van társítva, ha mindegyik hálózati interfész válik a legfelső szintű erőforrás található összes tulajdonság együtt az áttelepítés részeként. |
| Elosztott terhelésű végpont csoport |Terheléselosztó |A klasszikus üzemi modellben a platform egy implicit terheléselosztót rendel minden felhőszolgáltatáshoz. A migrálás során egy új terheléselosztó erőforrás jön létre, és a terheléselosztó végpont csoport terheléselosztó szabályokká alakul. |
| Bejövő NAT-szabályok |Bejövő NAT-szabályok |A virtuális gépen meghatározott bemeneti végpontok a terheléselosztó hálózati címfordításra vonatkozó bejövő szabályaivá alakulnak a migrálás során. |
| Virtuális IP-cím |Nyilvános IP-cím DNS-névvel |A virtuális IP-cím egy nyilvános IP-cím lesz, és a terheléselosztó társítva. A virtuális IP-cím csak akkor migrálható, ha társítva van hozzá egy bemeneti végpont. |
| Virtuális hálózat |Virtuális hálózat |A virtuális hálózat az összes tulajdonságával a Resource Manager-alapú üzemi modellbe lesz migrálva. Létrejön egy új erőforráscsoport a következő névvel: `-migrated`. |
| Fenntartott IP-címek |Nyilvános IP-cím statikus kiosztási módszerrel |A terheléselosztóhoz társított fenntartott IP-címek a felhőszolgáltatással vagy a virtuális géppel együtt migrálódnak. A nem társított fenntartott IP-címek migrálása jelenleg nem támogatott. |
| Nyilvános IP-cím virtuális gépenként |Nyilvános IP-cím dinamikus kiosztási módszerrel |A virtuális géphez társított nyilvános IP-cím nyilvános IP-cím erőforrássá alakul, statikus kiosztási módszerrel beállítva. |
| NSG-k |NSG-k |Az alhálózatokhoz társított hálózati biztonsági csoportokat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A hálózati biztonsági csoportok a klasszikus üzemi modellben nem törlődnek a migráláskor. A hálózati biztonsági csoportok felügyeletisík-műveletei azonban zárolva vannak, amíg a migrálás folyamatban van. |
| DNS-kiszolgálók |DNS-kiszolgálók |A virtuális hálózathoz vagy virtuális géphez társított DNS-kiszolgálók a megfelelő erőforrás-migrálás részeként migrálódnak az összes tulajdonsággal együtt. |
| UDR-ek |UDR-ek |Az alhálózatokhoz társított felhasználó által megadott útvonalakat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A felhasználó által megadott útvonalak a klasszikus üzemi modellben nem törlődnek a migráláskor. A felhasználó által megadott útvonalak felügyeletisík-műveletei zárolva vannak, amíg a migrálás folyamatban van. |
| IP-továbbítási tulajdonság a virtuális gép hálózati konfigurációjában |IP-továbbítási tulajdonság a hálózati adapteren |A virtuális gép IP-továbbítási tulajdonsága a hálózati adapter tulajdonságává alakul a migrálás közben. |
| Terheléselosztó több IP-címmel |Terheléselosztó több nyilvános IP-erőforrással |Minden nyilvános IP-címet a terheléselosztóhoz társított alakítja át a nyilvános IP-erőforrás, és a terheléselosztóhoz társított áttelepítés után. |
| Belső DNS-nevek a virtuális gépen |Belső DNS-nevek a hálózati adapteren |A migrálás során a virtuális gép belső DNS-utótagjai egy „InternalDomainNameSuffix” nevű csak olvasható tulajdonságba migrálódnak a hálózati adapteren. Az áttelepítés után a utótag változatlan marad, és VM megoldás is működnek, mint korábban. |
| Virtuális hálózati átjáró |Virtuális hálózati átjáró |Virtuális hálózati átjáró tulajdonságai változatlan települnek át. Az átjáróhoz társított virtuális IP-cím sem változik. |
| Helyi hálózati hely |Helyi hálózati átjáró |Helyi hálózati hely tulajdonságai a helyi hálózati átjáró nevű új erőforrás változatlan települnek át. Ez címelőtagokat a helyszíni és a távoli átjáró IP jelöli. |
| Kapcsolati hivatkozások |Kapcsolat |Az átjáró és a hálózati konfigurációt a helyi hálózati telephely közötti kapcsolat hivatkozásokat egy új erőforrás neve kapcsolat által képviselt. Kapcsolat hivatkozást a hálózati konfigurációs fájlok tulajdonságainak változatlan lesz másolva a kapcsolati erőforrást. A klasszikus üzembe helyezési modellel virtuális hálózatok közötti kapcsolatot hoz létre a virtuális hálózatot képviselő helyi hálózati helyekhez két IPsec-alagutak érhető el. A rendszer átalakítja a virtuális hálózati--virtuális-hálózat kapcsolat típusa az erőforrás-kezelő modellben való anélkül, hogy a helyi hálózati átjáró. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Az automatizálás és az eszközök módosítása a migrálás után
Az erőforrások a klasszikus telepítési modellből a Resource Manager üzembe helyezési modellel áttelepítés részeként kell frissíteni a meglévő automatizációk vagy tooling annak érdekében, hogy az használható folyamatosan, az áttelepítés után.
