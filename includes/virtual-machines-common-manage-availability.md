## <a name="understand-planned-vs-unplanned-maintenance"></a>A tervezett és a nem tervezett karbantartás közti különbségek ismertetése
Kétféle esemény létezik a Microsoft Azure platformon, amely befolyásolhatja a virtuális gépek rendelkezésre állását: a tervezett karbantartás és a nem tervezett karbantartás.

* A **tervezett karbantartási események** a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések a virtuális gépeket futtató platforminfrastruktúra általános megbízhatóságának, teljesítményének és biztonságának növelése érdekében. A frissítések a legtöbb esetben semmilyen hatással nincsenek a virtuális gépek vagy a felhő működésére. Egyes esetekben azonban a frissítések megkövetelik a virtuális gép újraindítását a szükséges frissítések a platforminfrastruktúrára való telepítése érdekében.
* A **nem tervezett karbantartási események** akkor következnek be, ha a virtuális gép mögöttes hardveres vagy fizikai infrastruktúrája valamiképp meghibásodik. Ez lehet helyi hálózati hiba, a helyi lemezek meghibásodása, vagy egyéb állványszintű meghibásodások. Ha a rendszer ilyen hibát észlel, az Azure platform automatikusan átmenti a virtuális gépet az azt futtató rendellenes fizikai gépről egy másik, rendesen működő fizikai gépre. Ilyen esetek ritkán lépnek fel, de ezek is okozhatják a virtuális gép újraindítását.

Az ilyen események okozta állásidő hatásainak csökkentése érdekében javasoljuk, hogy kövesse az alábbi bevált gyakorlatokat a virtuális gépek magas rendelkezésre állásának biztosításához:

* [Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]
* [Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]
* [Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása]
* [Több tárfiók használata az egyes rendelkezésre állási csoportokhoz]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében
Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Így legalább egy virtuális gép mindig elérhető lesz a tervezett vagy nem tervezett karbantartási események esetében is, és így teljesíthető a 99,95%-os Azure SLA. További információkért lásd [a virtuális gépek esetében érvényes SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) részleteit.

> [!IMPORTANT]
> Ha lehet, önmagában ne helyezzen egyetlen különálló virtuális gépet egy rendelkezésre állási csoportba. Az így konfigurált virtuális gépek nem jogosultak az SLA-garanciára, és leállhatnak az Azure tervezett karbantartási eseményei során, hacsak az önálló virtuális gép nem [Azure Premium Storage](../articles/storage/storage-premium-storage.md) csomagot használ. A Prémium szintű tárfiókot használó virtuális gépekre az Azure SLA minden esetben vonatkozik.

A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy **frissítési tartományt** és egy **tartalék tartományt**. Minden eges rendelkezésre állási csoportnak öt, felhasználó által nem konfigurálható frissítési tartományt oszt ki a rendszer alapértelmezés szerint (a Resource Manager-környezetek ezután akár 20 frissítési tartományig növelhetőek), és így kijelöli azokat a virtuális gépeket és mögöttes fizikai hardvereszközöket magukba foglaló csoportokat, amelyek egy időben újraindíthatóak. Ha egy rendelkezésre állási csoportba ötnél több virtuális gép van konfigurálva, a hatodik virtuális gép az elsővel azonos frissítési tartományba kerül, a hetedik a másodikkal és így tovább. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre.

A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve a Resource Manager-környezetben (két tartalék tartományba a klasszikus környezetben). Bár a virtuális gépek rendelkezésre állási csoportokba rendezése nem védi az alkalmazást az operációs rendszerre vezethető vagy az alkalmazásspecifikus hibáktól, korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy tápellátás-megszakadások hatását.

<!--Image reference-->
   ![A frissítési és tartalék tartományokat tartalmazó konfiguráció elméleti rajza](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains-and-availability-sets"></a>Tartalék tartományok és rendelkezésre állási csoportok felügyelt lemezeken
Az [Azure Managed Disks](../articles/storage/storage-faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet.


## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása
Ha a virtuális gépek majdnem azonosak, és ugyanaz a rendeltetésük az alkalmazásban, javasoljuk, hogy konfiguráljon egy rendelkezésre állási csoportot az alkalmazás mindegyik rétegéhez.  Ha két különböző réteget ugyanabba a rendelkezésre állási csoportba helyez, az ugyanabban az alkalmazásrétegben lévő mindegyik virtuális gép egyszerre indítható újra. Ha legalább két virtuális gépet konfigurál az egyes rendelkezésre állási csoportokba minden egyes réteghez, ezzel garantálja, hogy minden egyes rétegben legalább egy virtuális gép elérhető lesz.

Például az IIS-t, Apache-ot és Nginxet futtató alkalmazás előterében lévő mindegyik virtuális gépet elhelyezheti egyetlen rendelkezésre állási csoportban. Ugyanebbe a rendelkezésre állási csoportba mindenképp csak előtérben futó virtuális gépeket helyezzen. Ugyanígy gondoskodjon róla, hogy csak az adatrétegben futó virtuális gépek kerüljenek a nekik dedikált rendelkezésre állási csoportba, például replikált SQL Server-virtuálisgépek vagy MySQL-virtuálisgépek.

<!--Image reference-->
   ![Alkalmazásrétegek](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása
Az [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) a rendelkezésre állási csoportokkal kombinálva a legmagasabb fokú rugalmasságot biztosítja az alkalmazásoknak. Az Azure Load Balancer több virtuális gép között osztja el a forgalmat. A Standard csomagban elérhető virtuális gépek esetében az Azure Load Balancer a csomag részét képezi. Nem mindegyik virtuális gép csomagja tartalmazza az Azure Load Balancert. A virtuális gépek terheléselosztásáról további információkért lásd a [virtuális gépek terheléselosztását](../articles/virtual-machines/virtual-machines-linux-load-balance.md) ismertető témakört.

Ha a terheléselosztó nem úgy van konfigurálva, hogy a terhelést elossza több virtuális gép között, a tervezett karbantartás hatással lesz az egyetlen forgalomkiszolgáló virtuális gépre, ami kimaradást okoz az alkalmazásrétegben. Ha több egy rétegbe tartozó virtuális gépet helyez egyazon terheléselosztó és rendelkezésre állási csoport alá, a forgalmat mindig legalább egy példány képes lesz kiszolgálni.

## <a name="use-multiple-storage-accounts-for-each-availability-set"></a>Több tárfiók használata az egyes rendelkezésre állási csoportokhoz
Az Azure Managed Disks használata esetén az alábbi útmutatót átugorhatja. Az Azure Managed Disks eredendően magas rendelkezésre állást és redundanciát biztosít, mivel a lemezek a virtuális gépek rendelkezésre állási csoportjaihoz igazodó tartalék tartományokban vannak tárolva. További információ: [Azure Managed Disks – áttekintés](../articles/storage/storage-managed-disks-overview.md).

Nem felügyelt lemezek használata esetén érdemes bizonyos ajánlott eljárásokat követni a virtuális merevlemezek (VHD-k) által a virtuális gépben használt tárfiókokat illetően. Mindegyik lemez (VHD) egy lapblob egy Azure Storage-fiókban. Fontos gondoskodni a megfelelő redundanciáról és elkülönítésről a tárfiókokban, hogy biztosítható legyen a rendelkezésre állási csoportban lévő virtuális gépek magas rendelkezésre állása.

1. **Tárolja az egyazon virtuális géppel társított összes lemezt (operációsrendszer- és adatlemezt) ugyanabban a tárfiókban.**
2. **A tárfiókok [korlátait](../articles/storage/storage-scalability-targets.md) érdemes figyelemmel kísérni,** amikor további virtuális merevlemezeket ad egy tárfiókhoz.
3. **Használjon külön tárfiókot minden egyes virtuális géphez a rendelkezésre állási csoportban.** Az egyazon rendelkezésre állási csoportban lévő virtuális gépeket NEM szabad ugyanabban a tárfiókban tárolni. A különböző rendelkezésre állási csoportokban lévő virtuális gépek tárolhatók egy tárfiókban, ha attól még a fenti ajánlott eljárások feltételei teljesülnek.

<!-- Link references -->
[Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]: #configure-each-application-tier-into-separate-availability-sets
[Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Több tárfiók használata az egyes rendelkezésre állási csoportokhoz]: #use-multiple-storage-accounts-for-each-availability-set



<!--HONumber=Feb17_HO2-->


