---
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tagore
ms.openlocfilehash: b86e0d784d26e9e483dd12e20d45189ae8bfb9bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866195"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS-erőforrások áttelepítése a klasszikus üzembe helyezési modellből az Azure Resource Managerbe
Először is fontos megérteni a különbséget az adatsík és a felügyeleti sík műveletek az infrastruktúra szolgáltatásként (IaaS) erőforrások.

* *A Felügyeleti/vezérlési sík* a felügyeleti/vezérlősíkba vagy az erőforrások módosítására szolgáló API-ba érkező hívásokat írja le. Például az olyan műveletek, mint a virtuális gépek létrehozása vagy újraindítása, vagy a virtuális hálózatok új alhálózattal való frissítése a futó erőforrások felügyeletét szolgálják. Nem befolyásolják közvetlenül a virtuális gépekhez való csatlakozást.
* *Adatsík* (alkalmazás) ismerteti az alkalmazás futásidejű magát, és magában foglalja a kölcsönhatás olyan példányokkal, amelyek nem megy keresztül az Azure API-t. Például a webhely elérése, vagy adatok lehívása egy futó SQL Server-példányból vagy egy MongoDB-kiszolgálóról adatsík vagy alkalmazásinterakció. További példák közé tartozik egy blob másolása egy tárfiókból, és egy nyilvános IP-cím elérése a Távoli asztali protokoll (RDP) vagy a Biztonságos rendszerhéj (SSH) használatához a virtuális gépre. Ezek a műveletek működtetik az alkalmazást a számítási, hálózati és tárolási erőforrásokon.

Az adatsík megegyezik a klasszikus üzembe helyezési modell és az Erőforrás-kezelő halmok között. A különbség az, hogy az áttelepítési folyamat során a Microsoft lefordítja az erőforrások megjelenítését a klasszikus telepítési modellből az Erőforrás-kezelő veremben lévőre. Ennek eredményeképpen új eszközöket, API-kat és SDK-kat kell használnia az Erőforrás-kezelő veremben lévő erőforrások kezeléséhez.

![A felügyeleti/vezérlősík és az adatsík közötti különbséget bemutató diagram](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Egyes migrálási forgatókönyvekben az Azure platform leállítja, felszabadítja és újraindítja a virtuális gépeket. Ez egy rövid adatsík-leállást okoz.
>

## <a name="the-migration-experience"></a>A migrálási folyamat
Az áttelepítés megkezdése előtt:

* Győződjön meg róla, hogy a migrálni kívánt erőforrások nem használnak nem támogatott szolgáltatásokat vagy konfigurációkat. A platform általában észleli ezeket a problémákat, és hibát jelez.
* Ha olyan virtuális gépekkel rendelkezik, amelyek nem virtuális hálózatban vannak, a felkészülési művelet részeként leállnak és feloldódnak. Ha nem szeretné elveszíteni a nyilvános IP-címet, fontolja meg az IP-cím lefoglalását az előkészítési művelet elrendelése előtt. Ha a virtuális gépek egy virtuális hálózatban vannak, nem állnak le, és feloldják őket.
* A migrálást ütemezze munkaidőn kívülre, hogy a migrálás során esetlegesen felmerülő nem várt hibák kezelhetőek legyenek.
* Töltse le a virtuális gépek aktuális konfigurációját a PowerShell vagy a parancssori felület (CLI) parancsai, illetve REST API-k segítségével, hogy könnyebb legyen az érvényesítés az előkészítési lépés után.
* Frissítse az automatizálási és üzembe helyezési parancsfájlokat az Erőforrás-kezelő telepítési modelljének kezeléséhez az áttelepítés megkezdése előtt. GET műveleteket is használhat, ha az erőforrások már előkészített állapotban vannak.
* Értékelje ki a szerepköralapú hozzáférés-vezérlési (RBAC) szabályzatokat, amelyek a klasszikus központi telepítési modell IaaS-erőforrásain vannak konfigurálva, és tervezze meg az áttelepítés befejezése után.

Az áttelepítési munkafolyamat a következő:

![Az áttelepítési munkafolyamatot bemutató diagram](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> A következő szakaszokban leírt műveletek mind idempotens. Ha nem támogatott szolgáltatásvagy konfigurációs hiba miatt nem probléma, próbálkozzon újra az előkészítési, megszakítási vagy véglegesítési művelettel. Az Azure újra próbálkozik a művelettel.
>
>

### <a name="validate"></a>Érvényesítés
Az érvényesítés művelet a migrálási folyamat első lépése. A lépés célja, hogy elemezze a klasszikus központi telepítési modellben áttelepíteni kívánt erőforrások állapotát. A művelet kiértékeli, hogy az erőforrások képesek-e az áttelepítésre (sikeres vagy sikertelen).

Kiválaszthatja a virtuális hálózat vagy a felhőszolgáltatás (ha nem egy virtuális hálózat), amely ellenőrizni szeretné az áttelepítés. Ha az erőforrás nem képes a migrálásra, az Azure felsorolja az okokat.

#### <a name="checks-not-done-in-the-validate-operation"></a>Az érvényesítési műveletben nem végzett ellenőrzések

Az érvényesítési művelet csak a klasszikus üzembe helyezési modell erőforrásainak állapotát elemzi. Ellenőrizheti az összes hiba és nem támogatott forgatókönyvek miatt a klasszikus telepítési modell különböző konfigurációk miatt. Nem lehet ellenőrizni az összes olyan problémát, amelyet az Azure Resource Manager verem az áttelepítés során az erőforrásokra kényszeríthet. Ezeket a problémákat csak akkor ellenőrzi a rendszer, ha az erőforrások az áttelepítés következő lépésében (az előkészítési művelet) átalakításon mennek keresztül. Az alábbi táblázat az összes olyan problémát sorolja fel, amelyet az érvényesítési művelet nem ellenőrzött:


|A hálózati ellenőrzések nem az érvényesítési műveletben|
|-|
|ER- és VPN-átjárókkal rendelkező virtuális hálózat.|
|Virtuális hálózati átjárókapcsolat leválasztott állapotban.|
|Az összes ER-kapcsolat előzetes áttelepítése az Azure Resource Manager verembe.|
|Az Azure Resource Manager kvóta ellenőrzi a hálózati erőforrásokat. Például: statikus nyilvános IP, dinamikus nyilvános IP-k, terheléselosztó, hálózati biztonsági csoportok, útvonaltáblák és hálózati adapterek. |
| Minden terheléselosztó szabály érvényes a központi telepítés és a virtuális hálózat. |
| Ütköző privát IP-k a leállított virtuális gépek között ugyanazon a virtuális hálózaton. |

### <a name="prepare"></a>Előkészítés
Az előkészítés művelet a migrálási folyamat második lépése. A lépés célja, hogy szimulálja az IaaS-erőforrások átalakítását a klasszikus üzembe helyezési modellről az Erőforrás-kezelő erőforrásokra. Továbbá, a felkészülési művelet bemutatja ezt egymás mellett, hogy vizualizáljon.

> [!NOTE] 
> A klasszikus üzembe helyezési modellben lévő erőforrások nem módosulnak ebben a lépésben. Ez egy biztonságos lépés a futáshoz, ha kipróbálod az áttelepítést. 

Kiválaszthatja a virtuális hálózat vagy a felhőszolgáltatás (ha nem egy virtuális hálózat), amely et szeretne felkészülni az áttelepítésre.

* Ha az erőforrás nem képes az áttelepítésre, az Azure leállítja az áttelepítési folyamatot, és felsorolja az előkészítési művelet sikertelen okát.
* Ha az erőforrás képes a migrálásra, az Azure zárolja az áttelepítés alatt álló erőforrások felügyeleti síkműveleteit. Például nem csatolhat adatlemezt egy migrálás alatt álló virtuális géphez.

Az Azure ezután elindítja a metaadatok migrálását a klasszikus üzembe helyezési modellről az erőforrások erőforrás-kezelőjébe.

Az előkészítési művelet befejezése után lehetősége van az erőforrások megjelenítésére a klasszikus telepítési modellben és az Erőforrás-kezelőben is. Az Azure platform a klasszikus üzemi modellben minden egyes felhőszolgáltatáshoz létrehoz egy erőforráscsoport-nevet a következő mintának megfelelően: `cloud-service-name>-Migrated`.

> [!NOTE]
> Az áttelepített erőforrásokhoz (azaz "-Áttelepítés) létrehozott erőforráscsoport nevét nem lehet kiválasztani. Az áttelepítés befejezése után azonban az Azure Resource Manager áthelyezési funkciójával erőforrásokat helyezhet át a kívánt erőforráscsoportokba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../articles/resource-group-move-resources.md).

A következő két képernyőkép a sikeres előkészítési művelet után mutatja be az eredményt. Az első egy erőforráscsoportot mutat, amely az eredeti felhőszolgáltatást tartalmazza. A második az új "-Migrated" erőforráscsoportot mutatja be, amely az egyenértékű Azure Resource Manager-erőforrásokat tartalmazza.

![Az eredeti felhőszolgáltatást bemutató képernyőkép](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Képernyőkép, amely en az Azure Resource Manager erőforrásai láthatók az előkészítési műveletben](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Itt van egy kulisszák mögöttnézd meg a források befejezése után a felkészülési fázisban. Vegye figyelembe, hogy az adatsíkban lévő erőforrás megegyezik. A felügyeleti síkban (klasszikus üzembe helyezési modell) és a vezérlősíkban (Erőforrás-kezelő) is megjelenik.

![Az előkészítési fázis ábrája](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuális gépek, amelyek nem a klasszikus telepítési modell virtuális hálózatleállíttatása és felszabadított ebben a fázisban az áttelepítés leáll.
>

### <a name="check-manual-or-scripted"></a>Ellenőrzés (manuális vagy szkriptalapú)
Az ellenőrzési lépésben lehetősége van arra, hogy a korábban letöltött konfigurációt használja annak ellenőrzésére, hogy az áttelepítés helyesnek tűnik-e. Azt is megteheti, hogy bejelentkezik a portálra, és ellenőrizze a tulajdonságokat és az erőforrásokat, hogy ellenőrizze, hogy a metaadat-áttelepítés jól néz ki.

Ha virtuális hálózatot migrál, a virtuális gépek legtöbb konfigurációja nem indul újra. A virtuális gépeken futó alkalmazások esetében ellenőrizheti, hogy az alkalmazás továbbra is fut-e.

Tesztelheti a figyelési és a működési parancsfájlok, hogy ha a virtuális gépek a várt módon működik, és ha a frissített parancsfájlok megfelelően működik. Csak a GET műveletek támogatottak, amikor az erőforrások az előkészített állapotban vannak.

Nincs olyan beállított időablak, amely előtt el kell követnie az áttelepítést. Bármennyi időt eltölthet ebben az állapotban. A felügyeleti sík azonban zárolva van ezekhez az erőforrásokhoz, amíg meg nem szakítja vagy véglegesíti a műveletet.

Ha bármilyen problémát észlel, mindig megszakíthatja a migrálást, és visszatérhet a klasszikus üzemi modellhez. Miután visszalép, az Azure megnyitja a felügyeleti sík on the resources, így folytathatja a normál műveleteket a virtuális gépeken a klasszikus üzembe helyezési modellben.

### <a name="abort"></a>Megszakítás
Ez egy nem kötelező lépés, ha vissza szeretné állítani a módosításokat a klasszikus telepítési modellre, és le szeretné állítani az áttelepítést. Ez a művelet törli az erőforrás-kezelő metaadatait (az előkészítési lépésben létrehozott) az erőforrásokhoz. 

![A megszakítási lépés diagramja](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Ez a művelet nem végezhető el a véglegesítési művelet aktiválása után.     
>

### <a name="commit"></a>Véglegesítés
Az ellenőrzés befejezése után véglegesítheti a migrálást. Az erőforrások már nem jelennek meg a klasszikus központi telepítési modellben, és csak az Erőforrás-kezelő telepítési modelljében érhetők el. A migrált erőforrások csak az új portálon kezelhetők.

> [!NOTE]
> Ez egy idempotens művelet. Ha nem sikerül, próbálkozzon újra a művelettel. Ha továbbra is sikertelen, hozzon létre egy támogatási jegyet, vagy hozzon létre egy fórumot a [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)
>
>

![Véglegesítési lépés diagramja](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Áttelepítési folyamatábra

Itt van egy folyamatábra, amely bemutatja, hogyan kell eljárni az áttelepítés:

![Képernyőkép a migrálási lépésekről](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>A klasszikus üzembe helyezési modell fordítása erőforrás-kezelői erőforrásokba
Az alábbi táblázatban megtalálhatja az erőforrások klasszikus központi telepítési modelljét és erőforrás-kezelői ábrázolásait. Az egyéb szolgáltatások és erőforrások jelenleg nem támogatottak.

| Klasszikus ábrázolás | Resource Manager-ábrázolás | Megjegyzések |
| --- | --- | --- |
| Felhőszolgáltatás neve |DNS-név |A migrálás során minden felhőszolgáltatáshoz egy új erőforráscsoport jön létre a következő elnevezési mintának megfelelően: `<cloudservicename>-migrated`. Ez az erőforráscsoport tartalmazza az összes erőforrást. A felhőszolgáltatás egy DNS-névvé alakul, amely a nyilvános IP-címhez van társítva. |
| Virtuális gép |Virtuális gép |A virtuális gépre jellemző tulajdonságok a migrálás során nem változnak. Bizonyos osProfile-adatok, például a számítógépnév, nem tárolódnak a klasszikus telepítési modellben, és az áttelepítés után üresek maradnak. |
| A virtuális géphez csatolt lemezerőforrások |A virtuális géphez csatolt implicit lemezek |A lemezek nem legfelső szintű erőforrásként vannak modellezve a Resource Manager-alapú üzemi modellben. A virtuális gép implicit lemezeiként lesznek migrálva. Jelenleg csak a virtuális géphez csatolt lemezek támogatottak. Az erőforrás-kezelő virtuális gépei mostantól a klasszikus központi telepítési modelltárfiókokat is használhatják, ami lehetővé teszi a lemezek frissítés nélküli egyszerű áttelepítését. |
| Virtuálisgép-bővítmények |Virtuálisgép-bővítmények |Az összes erőforrás-bővítmény, az XML-bővítmények kivételével, migrálva lett a klasszikus üzemi modellből. |
| A virtuális gép tanúsítványai |Tanúsítványok az Azure Key Vaultban |Ha egy felhőszolgáltatás szolgáltatástanúsítványokat tartalmaz, az áttelepítés egy új Azure-kulcstartót hoz létre felhőalapú szolgáltatásonként, és áthelyezi a tanúsítványokat a key vaultba. A virtuális gépek frissülnek, hogy a kulcstartóban található tanúsítványokra hivatkozzanak. <br><br> Ne törölje a key vault. Ez azt eredményezheti, hogy a virtuális gép sikertelen állapotba kerül. |
| WinRM-konfiguráció |WinRM-konfiguráció osProfile alatt |A Rendszerfelügyeleti webszolgáltatások konfiguráció az áthelyezésekor változatlan marad a migrálás során. |
| Rendelkezésre állási csoport tulajdonsága |Rendelkezésre állási csoport erőforrás | Rendelkezésre állási készlet specifikáció egy tulajdonság a virtuális gép a klasszikus üzembe helyezési modellben. A rendelkezésre állási csoportok legfelső szintű erőforrásokká alakulnak a migrálás részeként. A következő konfigurációk nem támogatottak: több rendelkezésre állási csoport felhőszolgáltatásonként, illetve egy vagy több rendelkezésre állási csoport olyan virtuális gépekkel, amelyek nem tartoznak egy rendelkezésre állási csoporthoz sem egy felhőszolgáltatásban. |
| Hálózati konfiguráció egy virtuális gépen |Elsődleges hálózati adapter |A hálózati konfiguráció egy virtuális gépen az elsődleges hálózati adapter erőforrásként jelenik meg a migrálás után. Azoknál a virtuális gépeknél, amelyek nem tagjai virtuális hálózatnak, a belső IP-cím módosul a migrálás során. |
| Több hálózati adapter egy virtuális gépen |Hálózati illesztők |Ha egy virtuális gép hez több hálózati adapter van társítva, minden hálózati adapter az áttelepítés részeként legfelső szintű erőforrássá válik, az összes tulajdonsággal együtt. |
| Elosztott terhelésű végpont csoport |Terheléselosztó |A klasszikus üzemi modellben a platform egy implicit terheléselosztót rendel minden felhőszolgáltatáshoz. A migrálás során egy új terheléselosztó erőforrás jön létre, és a terheléselosztó végpont csoport terheléselosztó szabályokká alakul. |
| Bejövő NAT-szabályok |Bejövő NAT-szabályok |A virtuális gépen meghatározott bemeneti végpontok a terheléselosztó hálózati címfordításra vonatkozó bejövő szabályaivá alakulnak a migrálás során. |
| Virtuális IP-cím |Nyilvános IP-cím DNS-névvel |A virtuális IP-cím nyilvános IP-címlesz, és a terheléselosztóhoz van társítva. A virtuális IP-cím csak akkor migrálható, ha társítva van hozzá egy bemeneti végpont. |
| Virtuális hálózat |Virtuális hálózat |A virtuális hálózat az összes tulajdonságával a Resource Manager-alapú üzemi modellbe lesz migrálva. Létrejön egy új erőforráscsoport a következő névvel: `-migrated`. |
| Fenntartott IP-címek |Nyilvános IP-cím statikus kiosztási módszerrel |A terheléselosztóhoz társított fenntartott IP-címek a felhőszolgáltatással vagy a virtuális géppel együtt migrálódnak. A nem társított fenntartott IP-címek migrálása jelenleg nem támogatott. |
| Nyilvános IP-cím virtuális gépenként |Nyilvános IP-cím dinamikus kiosztási módszerrel |A virtuális géphez társított nyilvános IP-cím nyilvános IP-cím erőforrássá alakul, statikus kiosztási módszerrel beállítva. |
| NSG-k |NSG-k |Az alhálózatokhoz társított hálózati biztonsági csoportokat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A hálózati biztonsági csoportok a klasszikus üzemi modellben nem törlődnek a migráláskor. A hálózati biztonsági csoportok felügyeletisík-műveletei azonban zárolva vannak, amíg a migrálás folyamatban van. |
| DNS-kiszolgálók |DNS-kiszolgálók |A virtuális hálózathoz vagy virtuális géphez társított DNS-kiszolgálók a megfelelő erőforrás-migrálás részeként migrálódnak az összes tulajdonsággal együtt. |
| UDR-ek |UDR-ek |Az alhálózatokhoz társított felhasználó által megadott útvonalakat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A felhasználó által megadott útvonalak a klasszikus üzemi modellben nem törlődnek a migráláskor. A felhasználó által megadott útvonalak felügyeletisík-műveletei zárolva vannak, amíg a migrálás folyamatban van. |
| IP-továbbítási tulajdonság a virtuális gép hálózati konfigurációjában |IP-továbbítási tulajdonság a hálózati adapteren |A virtuális gép IP-továbbítási tulajdonsága a hálózati adapter tulajdonságává alakul a migrálás közben. |
| Terheléselosztó több IP-címmel |Terheléselosztó több nyilvános IP-erőforrással |A terheléselosztóhoz társított minden nyilvános IP-cím nyilvános IP-erőforrássá alakul át, és az áttelepítés után a terheléselosztóhoz lesz társítva. |
| Belső DNS-nevek a virtuális gépen |Belső DNS-nevek a hálózati adapteren |A migrálás során a virtuális gép belső DNS-utótagjai egy „InternalDomainNameSuffix” nevű csak olvasható tulajdonságba migrálódnak a hálózati adapteren. Az utótag változatlan marad az áttelepítés után, és a virtuális gép megoldásának a korábbiaknak köszönhetően továbbra is működnie kell. |
| Virtuális hálózati átjáró |Virtuális hálózati átjáró |A virtuális hálózati átjáró tulajdonságai változatlanul áttelepülnek. Az átjáróhoz társított virtuális IP-cím sem változik. |
| Helyi hálózati hely |Helyi hálózati átjáró |A helyi hálózati hely tulajdonságai változatlanul átkerülnek egy helyi hálózati átjárónak nevezett új erőforrásba. Ez a helyszíni címelőtagokat és a távoli átjáró IP-címét jelöli. |
| Kapcsolati hivatkozások |Kapcsolat |Az átjáró és a helyi hálózati hely közötti kapcsolódási hivatkozásokat a hálózati konfigurációban egy új, Kapcsolat nevű erőforrás képviseli. A hálózati konfigurációs fájlokban a kapcsolathivatkozás összes tulajdonsága változatlanul átmásolva lesz a Kapcsolat erőforrásba. A klasszikus telepítési modellben a virtuális hálózatok közötti kapcsolat két IPsec-alagút létrehozásával érhető el a virtuális hálózatokat képviselő helyi hálózati helyekhez. Ez az Erőforrás-kezelő modellben a virtuális hálózat és a virtuális hálózat típusú kapcsolattípussá alakul át, anélkül, hogy helyi hálózati átjárókra lenne szükség. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Az automatizálás és az eszközök módosítása a migrálás után
Az erőforrások klasszikus központi telepítési modellből az Erőforrás-kezelő telepítési modellbe való áttelepítésének részeként frissítenie kell a meglévő automatizálást vagy eszközt annak érdekében, hogy az áttelepítés után is működjön.
