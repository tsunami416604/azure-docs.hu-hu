## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Mit jelent az IaaS-erőforrások klasszikus környezetből Azure Resource Manager-alapú környezetbe migrálása?
A részletes elemzés előtt tekintsük át az adatsíkon és a felügyeleti síkon végzett műveletek közötti különbségeket az IaaS-erőforrásokon.

* A *felügyeleti/vezérlősík* a felügyeleti/vezérlősíkra vagy az erőforrások módosítását végző API-ra érkező hívásokat írja le. Például az olyan műveletek, mint a virtuális gépek létrehozása vagy újraindítása, vagy a virtuális hálózatok új alhálózattal való frissítése a futó erőforrások felügyeletét szolgálják. A példányokhoz való csatlakozásra közvetlenül nincsenek hatással.
* Az *adatsík* (alkalmazás) magának az alkalmazásnak a futtatókörnyezetét írja le, és olyan példányokkal való interakciót foglal magában, amely nem az Azure API-n keresztül történik. Egy webhely elérése vagy adatok lekérése egy futó SQL Server-példányból vagy egy MongoDB-kiszolgálóról adatsík- vagy alkalmazásinterakciónak minősül. Egy blob átmásolása egy tárfiókból vagy egy nyilvános IP-cím elérése RDP-n vagy SSH-n keresztül a virtuális gépről szintén adatsíkművelet. Ezek a műveletek működtetik az alkalmazást a számítási, hálózati és tárolási erőforrásokon.

![A felügyeleti/vezérlősík és az adatsík közötti különbségeket bemutató képernyőkép](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> Egyes migrálási forgatókönyvekben az Azure platform leállítja, felszabadítja és újraindítja a virtuális gépeket. Ez rövid állásidőt eredményez az adatsíkon.
>
>

## <a name="the-migration-experience"></a>A migrálási folyamat
Mielőtt belekezdene a migrálási folyamatba, érdemes elvégeznie a következőket:

* Győződjön meg róla, hogy a migrálni kívánt erőforrások nem használnak nem támogatott szolgáltatásokat vagy konfigurációkat. A platform általában észleli ezeket a problémákat, és hibát jelez.
* Ha rendelkezik olyan virtuális gépekkel, amelyek nem részei virtuális hálózatnak, azok az előkészítési művelet keretében le lesznek állítva és fel lesznek szabadítva. Ha nem szeretné elveszteni a nyilvános IP-címet, gondoskodjon annak megőrzéséről az előkészítési művelet elindítása előtt. Ha azonban a virtuális gépek egy virtuális hálózaton találhatóak, nem lesznek leállítva és felszabadítva.
* A migrálást ütemezze munkaidőn kívülre, hogy a migrálás során esetlegesen felmerülő nem várt hibák kezelhetőek legyenek.
* Töltse le a virtuális gépek aktuális konfigurációját a PowerShell vagy a parancssori felület (CLI) parancsai, illetve REST API-k segítségével, hogy könnyebb legyen az érvényesítés az előkészítési lépés után.
* Frissítse az automatizálási/működőképesség tételi szkripteket a Resource Manager-alapú üzemi modell kezelésére a migrálás megkezdése előtt. GET műveleteket is használhat, ha az erőforrások már előkészített állapotban vannak.
* Értékelje ki a klasszikus IaaS-erőforrásokon konfigurált RBAC-házirendeket, és tervezze meg, mi történjen a migrálás után.

A migrálási munkafolyamat a következő:

![A migrálási munkafolyamatot bemutató képernyőkép](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> A következő szakaszokban ismertetett összes művelet idempotens. Ha valamely előkészítési, megszakítási vagy véglegesítési művelet során a olyan probléma lép fel, amely nem a támogatás hiányának vagy konfigurációs hibának köszönhető, próbálja újra végrehajtani az adott műveletet. Az Azure platform újrapróbálkozik a művelettel.
>
>

### <a name="validate"></a>Érvényesítés
Az érvényesítés művelet a migrálási folyamat első lépése. A lépés célja, hogy a háttérben elemezze a migrálás alatt álló erőforrások adatait, valamint sikeres/sikertelen eredményt adjon vissza, ha az erőforrások migrálhatók.

Ki kell választania a virtuális hálózatot vagy üzemeltetett szolgáltatást (ha az nem virtuális hálózat), amelyet érvényesíteni kíván a migrálásra.

* Ha az erőforrás nem migrálható, az Azure platform felsorolja a támogatás hiányának okait.

A tárolási szolgáltatások érvényesítésekor a migrált fiókot egy olyan erőforráscsoportban fogja találni, amelynek a neve megegyezik a tárfiókkal, a „-Migrated” karakterlánccal kiegészítve.  Például, ha a tárfiókja neve „mystorage”, az Azure Resource Manager-kompatibilis erőforrást a „mystorage-Migrated” nevű erőforráscsoportban fogja találni, amely egy „mystorage” nevű tárfiókot tartalmaz.

### <a name="prepare"></a>Előkészítés
Az előkészítés művelet a migrálási folyamat második lépése. A lépés célja, hogy szimulálja az IaaS-erőforrások átalakítását klasszikusból Resource Manager-alapú erőforrásokká, valamint ezt egymás mellett vizuálisan megjelenítse.

Ki kell választania a virtuális hálózatot vagy üzemeltetett szolgáltatást (ha az nem virtuális hálózat), amelyet elő kíván készíteni a migrálásra.

* Ha az erőforrás nem migrálható, az Azure platform leállítja a migrálási folyamatot, és felsorolja az előkészítés művelet sikertelenségének okait.
* Ha az erőforrás migrálható, az Azure platform először lezárja a felügyeleti sík műveleteit a migrálás alatt álló erőforrásokon. Például nem csatolhat adatlemezt egy migrálás alatt álló virtuális géphez.

Az Azure platform ezután elindítja a migrálandó erőforrások metaadatainak migrálását a klasszikus környezetből Resource Manager-alapúba.

Az előkészítési művelet befejezése után lehetősége van az erőforrások megjelenítésére a klasszikus és a Resource Manager-alapú környezetben is. Az Azure platform a klasszikus üzemi modellben minden egyes felhőszolgáltatáshoz létrehoz egy erőforráscsoport-nevet a következő mintának megfelelően: `cloud-service-name>-Migrated`.

> [!NOTE]
> A migrált erőforrásokhoz létrehozott (azaz a „-Migrated”) erőforráscsoport nevét nem választhatja ki, azonban a migrálás befejezése után az Azure Resource Manager áthelyezés szolgáltatásával az erőforrásokat bármely erőforráscsoportban elhelyezheti. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../articles/resource-group-move-resources.md)

Itt két képernyőkép látható, amelyek egy sikeres előkészítési művelet eredményét mutatják. Az első képernyőképen egy erőforráscsoport látható, amely az eredeti felhőszolgáltatást tartalmazza. A második képernyőképen az új „-Migrated” erőforráscsoport látható, amely a fentieknek megfelelő Azure Resource Manager-erőforrásokat tartalmazza.

![Képernyőkép a Portal klasszikus felhőszolgáltatásáról](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Képernyőkép a Portal Azure Resource Manager-erőforrásairól előkészítés közben](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

> [!NOTE]
> A nem klasszikus virtuális hálózatban lévő virtuális gépek felszabadítva leállnak a migrálás ezen lépésében.
>
>

### <a name="check-manual-or-scripted"></a>Ellenőrzés (manuális vagy szkriptalapú)
Az ellenőrzés lépésben dönthet úgy, hogy a korábban letöltött konfigurációval megerősíti, hogy a migrálás megfelelő. Alternatív megoldásként bejelentkezhet a portálra, és a tulajdonságok és az erőforrások szúrópróbaszerű ellenőrzésével megerősítheti, hogy a metaadatok migrálása megfelelő.

Ha virtuális hálózatot migrál, a virtuális gépek legtöbb konfigurációja nem indul újra. Az ezeken a virtuális gépeken található alkalmazások esetén ellenőrizheti, hogy továbbra is megfelelően működnek és futnak-e.

A megfigyelési/automatizálási és működési szkriptek tesztelésével ellenőrizheti, hogy a virtuális gépek és a frissített parancsfájlok a várakozásoknak megfelelően működnek-e. Csak a GET műveletek támogatottak, amikor az erőforrások az előkészített állapotban vannak.

Nincs megadott időtartam, miután véglegesítenie kell a migrálást. Bármennyi időt eltölthet ebben az állapotban. A felügyeleti sík azonban zárolva van ezekhez az erőforrásokhoz, amíg meg nem szakítja vagy véglegesíti a műveletet.

Ha bármilyen problémát észlel, mindig megszakíthatja a migrálást, és visszatérhet a klasszikus üzemi modellhez. Miután visszatér, az Azure platform megnyitja a felügyeleti sík műveleteket az erőforráson, hogy folytathassa a normál üzemeltetést a virtuális gépeken a klasszikus üzemi modellben.

### <a name="abort"></a>Megszakítás
A megszakítás egy választható lépés, amellyel visszaállíthatja a klasszikus üzemi modell módosításait, és leállíthatja a migrálást.

> [!NOTE]
> Ezt a műveletet nem lehet végrehajtani, miután elindította a véglegesítés műveletet.     
>
>

### <a name="commit"></a>Véglegesítés
Az ellenőrzés befejezése után véglegesítheti a migrálást. Az erőforrások ezután nem jelennek meg a klasszikus modellben, és csak a Resource Manager-alapú üzemi modellben érhetők el. A migrált erőforrások csak az új portálon kezelhetők.

> [!NOTE]
> Ez egy idempotens művelet. Ha sikertelen, javasoljuk, hogy próbálja újra a műveletet. Ha továbbra is sikertelen, hozzon létre egy támogatási jegyet, vagy egy fórumbejegyzést ClassicIaaSMigration címkével a [VM fórumunkon](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>
<br>
Folyamatábra a migrálási folyamat lépéseiről

![Képernyőkép a migrálási lépésekről](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>Erőforrások fordítása klasszikusból Azure Resource Manager-alapúba
Az erőforrások klasszikus és Resource Manager-alapú ábrázolását a következő táblázatban találja. Az egyéb szolgáltatások és erőforrások jelenleg nem támogatottak.

| Klasszikus ábrázolás | Resource Manager-ábrázolás | Részletes megjegyzések |
| --- | --- | --- |
| Felhőszolgáltatás neve |DNS-név |A migrálás során minden felhőszolgáltatáshoz egy új erőforráscsoport jön létre a következő elnevezési mintának megfelelően: `<cloudservicename>-migrated`. Ez az erőforráscsoport tartalmazza az összes erőforrást. A felhőszolgáltatás egy DNS-névvé alakul, amely a nyilvános IP-címhez van társítva. |
| Virtuális gép |Virtuális gép |A virtuális gépre jellemző tulajdonságok a migrálás során nem változnak. Bizonyos osProfile-adatok, például a számítógép neve, nincsenek a klasszikus üzemi modellben tárolva, ezért üresek maradnak a migrálás után. |
| A virtuális géphez csatolt lemezerőforrások |A virtuális géphez csatolt implicit lemezek |A lemezek nem legfelső szintű erőforrásként vannak modellezve a Resource Manager-alapú üzemi modellben. A virtuális gép implicit lemezeiként lesznek migrálva. Jelenleg csak a virtuális géphez csatolt lemezek támogatottak. A Resource Manager virtuális gépek mostantól használhatnak klasszikus tárfiókokat, amelyek lehetővé teszik a lemezek egyszerű migrálását frissítések nélkül. |
| Virtuálisgép-bővítmények |Virtuálisgép-bővítmények |Az összes erőforrás-bővítmény, az XML-bővítmények kivételével, migrálva lett a klasszikus üzemi modellből. |
| A virtuális gép tanúsítványai |Tanúsítványok az Azure Key Vaultban |Ha egy felhőszolgáltatás szolgáltatási tanúsítványokat tartalmaz, a rendszer felhőszolgáltatásonként létrehoz egy új Azure-kulcstartót, amelybe áthelyezi a tanúsítványokat. A virtuális gépek frissülnek, hogy a kulcstartóban található tanúsítványokra hivatkozzanak. <br><br> **MEGJEGYZÉS:** Ne törölje a kulcstartót, mivel ez sikertelen állapotba helyezheti a virtuális gépet. Jelenleg a háttérrendszer fejlesztésén dolgozunk, hogy a kulcstartók biztonságosan törölhetők, illetve a virtuális géppel együtt egy új előfizetésbe áthelyezhetők legyenek. |
| WinRM-konfiguráció |WinRM-konfiguráció osProfile alatt |A Rendszerfelügyeleti webszolgáltatások konfiguráció az áthelyezésekor változatlan marad a migrálás során. |
| Rendelkezésre állási csoport tulajdonsága |Rendelkezésre állási csoport erőforrás | A rendelkezésre állási csoport specifikációja egy tulajdonság volt a virtuális gépen a klasszikus üzemi modellben. A rendelkezésre állási csoportok legfelső szintű erőforrásokká alakulnak a migrálás részeként. A következő konfigurációk nem támogatottak: több rendelkezésre állási csoport felhőszolgáltatásonként, illetve egy vagy több rendelkezésre állási csoport olyan virtuális gépekkel, amelyek nem tartoznak egy rendelkezésre állási csoporthoz sem egy felhőszolgáltatásban. |
| Hálózati konfiguráció egy virtuális gépen |Elsődleges hálózati adapter |A hálózati konfiguráció egy virtuális gépen az elsődleges hálózati adapter erőforrásként jelenik meg a migrálás után. Azoknál a virtuális gépeknél, amelyek nem tagjai virtuális hálózatnak, a belső IP-cím módosul a migrálás során. |
| Több hálózati adapter egy virtuális gépen |Hálózati illesztők |Ha egy virtuális géphez több hálózati adapter is van társítva, mindegyik hálózati adapter legfelső szintű erőforrássá alakul a migrálás részeként a Resource Manager-alapú üzemi modellben az összes tulajdonsággal együtt. |
| Elosztott terhelésű végpont csoport |Terheléselosztó |A klasszikus üzemi modellben a platform egy implicit terheléselosztót rendel minden felhőszolgáltatáshoz. A migrálás során egy új terheléselosztó erőforrás jön létre, és a terheléselosztó végpont csoport terheléselosztó szabályokká alakul. |
| Bejövő NAT-szabályok |Bejövő NAT-szabályok |A virtuális gépen meghatározott bemeneti végpontok a terheléselosztó hálózati címfordításra vonatkozó bejövő szabályaivá alakulnak a migrálás során. |
| Virtuális IP-cím |Nyilvános IP-cím DNS-névvel |A virtuális IP-cím nyilvános IP-címmé alakul, és a terheléselosztóhoz lesz társítva. A virtuális IP-cím csak akkor migrálható, ha társítva van hozzá egy bemeneti végpont. |
| Virtuális hálózat |Virtuális hálózat |A virtuális hálózat az összes tulajdonságával a Resource Manager-alapú üzemi modellbe lesz migrálva. Létrejön egy új erőforráscsoport a következő névvel: `-migrated`. |
| Fenntartott IP-címek |Nyilvános IP-cím statikus kiosztási módszerrel |A terheléselosztóhoz társított fenntartott IP-címek a felhőszolgáltatással vagy a virtuális géppel együtt migrálódnak. A nem társított fenntartott IP-címek migrálása jelenleg nem támogatott. |
| Nyilvános IP-cím virtuális gépenként |Nyilvános IP-cím dinamikus kiosztási módszerrel |A virtuális géphez társított nyilvános IP-cím nyilvános IP-cím erőforrássá alakul, statikus kiosztási módszerrel beállítva. |
| NSG-k |NSG-k |Az alhálózatokhoz társított hálózati biztonsági csoportokat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A hálózati biztonsági csoportok a klasszikus üzemi modellben nem törlődnek a migráláskor. A hálózati biztonsági csoportok felügyeletisík-műveletei azonban zárolva vannak, amíg a migrálás folyamatban van. |
| DNS-kiszolgálók |DNS-kiszolgálók |A virtuális hálózathoz vagy virtuális géphez társított DNS-kiszolgálók a megfelelő erőforrás-migrálás részeként migrálódnak az összes tulajdonsággal együtt. |
| UDR-ek |UDR-ek |Az alhálózatokhoz társított felhasználó által megadott útvonalakat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A felhasználó által megadott útvonalak a klasszikus üzemi modellben nem törlődnek a migráláskor. A felhasználó által megadott útvonalak felügyeletisík-műveletei zárolva vannak, amíg a migrálás folyamatban van. |
| IP-továbbítási tulajdonság a virtuális gép hálózati konfigurációjában |IP-továbbítási tulajdonság a hálózati adapteren |A virtuális gép IP-továbbítási tulajdonsága a hálózati adapter tulajdonságává alakul a migrálás közben. |
| Terheléselosztó több IP-címmel |Terheléselosztó több nyilvános IP-erőforrással |A terheléselosztóhoz társított összes nyilvános IP-cím a terheléselosztóhoz társított nyilvános IP-erőforrássá alakul a migrálás után. |
| Belső DNS-nevek a virtuális gépen |Belső DNS-nevek a hálózati adapteren |A migrálás során a virtuális gép belső DNS-utótagjai egy „InternalDomainNameSuffix” nevű csak olvasható tulajdonságba migrálódnak a hálózati adapteren. Az utótag változatlan marad a migrálás után, és a virtuális gép feloldása továbbra is a korábbiaknak megfelelően kell, hogy működjön. |
| Virtuális hálózati átjáró |Virtuális hálózati átjáró |A virtuális hálózati átjáró tulajdonságai a migrálás során nem változnak. Az átjáróhoz társított virtuális IP-cím sem változik. |
| Helyi hálózati hely |Helyi hálózati átjáró |A helyi hálózati hely tulajdonságai módosítás nélkül migrálódnak egy Helyi hálózati átjáró nevű új erőforrásba. Ez a helyszíni címelőtagokat és a távoli átjárók IP-címét képviseli. |
| Kapcsolati hivatkozások |Kapcsolat |Az átjáró és a helyi hálózati hely közötti kapcsolati hivatkozásokat a hálózati konfigurációban egy Connection nevű újonnan létrehozott erőforrás képviseli a Resource Managerben a migrálás után. A rendszer a hálózati konfigurációs fájlokban található kapcsolati hivatkozások összes tulajdonságát módosítás nélkül másolja át az újonnan létrehozott Connection erőforrásba. VNetek közötti kapcsolatot klasszikus modellben a VNeteket képviselő helyi hálózati helyre vezető két IPsec-alagút létrehozásával létesíthet. Ez a Resource Manager-modellben Vnet2Vnet kapcsolati típusra alakul anélkül, hogy szükség lenne helyi hálózati átjárókra. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Az automatizálás és az eszközök módosítása a migrálás után
Az erőforrásoknak a klasszikus üzemi modellből Resource Manager-alapú üzemi modellbe való migrálása részeként frissítenie kell a meglévő automatizálást vagy eszközöket, hogy biztosítsa a megfelelő működésüket a migrálás után.
