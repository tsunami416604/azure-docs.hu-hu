
Ha az Azure nem problémával az ebben a cikkben, látogasson el a [MSDN és a Stack Overflow Azure fórumok](https://azure.microsoft.com/support/forums/). A probléma, beküldheti, ezek fórumokban, vagy @AzureSupport a Twitteren. Emellett fájlt a az Azure támogatási kérelmet kiválasztásával **segítségre van szüksége** a a [az Azure támogatási](https://azure.microsoft.com/support/options/) hely.

## <a name="general-troubleshooting-steps"></a>Általános hibaelhárítási lépéseket
### <a name="troubleshoot-common-allocation-failures-in-the-classic-deployment-model"></a>A klasszikus üzembe helyezési modellel közös helyfoglalási hibák elhárítása
Ezeket a lépéseket megoldhatók a virtuális gépek sok pufferallokációs hibák:

* Egy másik Virtuálisgép-méretet a virtuális gép átméretezésével.<br>
    Kattintson a **összes tallózása** > **virtuális gépek (klasszikus)** > a virtuális gép > **beállítások** > **mérete**. Részletes útmutató: [méretezze át a virtuális gép](https://msdn.microsoft.com/library/dn168976.aspx).
* Minden virtuális gép törlése a felhőalapú szolgáltatás, és hozza létre a virtuális gépek.<br>
    Kattintson a **összes tallózása** > **virtuális gépek (klasszikus)** > a virtuális gép > **törlése**. Kattintson a **új** > **számítási** > [virtuálisgép-lemezkép].

### <a name="troubleshoot-common-allocation-failures-in-the-azure-resource-manager-deployment-model"></a>Az Azure Resource Manager üzembe helyezési modellel közös helyfoglalási hibák elhárítása
Ezeket a lépéseket megoldhatók a virtuális gépek sok pufferallokációs hibák:

* Állítsa le (felszabadítása) minden virtuális gép ugyanabban a rendelkezésre állási állítsa be, majd indítsa újra a minden egyes.<br>
    Leállítása: kattintson a **erőforráscsoportok** > az erőforráscsoport > **erőforrások** > a rendelkezésre állási csoport > **virtuális gépek** > a virtuális gép >  **Állítsa le**.
  
    Után minden virtuális gép leállításához válassza ki az első virtuális gép, kattintson a **Start**.

## <a name="background-information"></a>Háttér-információkat
### <a name="how-allocation-works"></a>Foglalási működése
Az Azure adatközpontjaiban van particionálva csoportokba. Általában egy memóriafoglalási kérelem több fürt kísérlet történik, de előfordulhat, hogy a foglalási kérelem bizonyos korlátozások kényszerítése sikertelen bejelentkezési kísérletet a kérelem csak egy fürt Azure platformon. Ebben a cikkben kifejezés ez szerint "fürtre van rögzítve." 1. ábra alatt a kis-és a normál foglalási több fürt megkísérlő mutatja be. 2. ábrán látható, a kis-és memóriakiosztást, mert, ahol a felhőalapú szolgáltatás CS_1 vagy rendelkezésre állási készlet tárolása fürt 2 van rögzítve.
![Foglalási diagramja](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Miért fordulhat elő, pufferallokációs hibák
Egy memóriafoglalási kérelem fürtre van rögzítve, nincs szabad erőforrások keresésére, mivel a rendelkezésre álló erőforráskészlet kisebb sikertelen magasabb esélyét. Továbbá ha a memóriafoglalási kérelem fürtre van rögzítve, de az adott fürt nem támogatja a kért erőforrás típusát, a kérelem sikertelen lesz akkor is, ha a fürt szabad erőforrást. 3. ábra alatt az esetben, ha egy rögzített foglalás sikertelen lesz, mivel az egyetlen jelölt fürtnek nincs szabad erőforrást mutatja be. 4. ábrán látható, az esetet, ahol egy rögzített foglalás sikertelen lesz, mivel az egyetlen jelölt fürt nem támogatja a kért Virtuálisgép-méret, annak ellenére, hogy a fürt ingyenes erőforrással rendelkezik.

![Rögzített memóriafoglalási hiba](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## <a name="detailed-troubleshoot-steps-specific-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Részletes lépéseket adott foglalási hiba forgatókönyvek a klasszikus üzembe helyezési modellel hibaelhárítása
Az alábbiakban gyakori foglalási forgatókönyvek, amelyek egy foglalási kérelem rögzítve. Igazolnia kell alaposabban egyes forgatókönyvek a cikk későbbi részében.

* Méretezze át a virtuális gépek vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
* Indítsa újra a részlegesen leállított (felszabadított) virtuális gépek
* Indítsa újra a teljes leállított (felszabadított) virtuális gépek
* Átmeneti vagy üzemi környezetek (csak szolgáltatásként platform)
* Az affinitáscsoport (virtuális gép vagy szolgáltatás beléptetőkártyával)
* Kapcsolat-csoport-alapú virtuális hálózat

Foglalási hiba megjelenésekor tekintse meg, ha bemutatott esetekben a hiba a érvényes. Az Azure platform által visszaadott foglalási hiba segítségével azonosíthatja a megfelelő forgatókönyv. Ha a kérés van rögzítve, távolítson el néhányat a kitűzési korlátozást nyissa meg a további fürtök, növelve a eséllyel sikerül, felosztási vonatkozó kérelmét.

Általában mindaddig, amíg a hiba nem jelzi, "a kért Virtuálisgép-méret nem támogatott", mindig újból megpróbálkozhat egy későbbi időpontban erőforrásokkal előfordulhat, hogy rendelkezik felszabadult a fürt a kérelem olyan módon. Ha a probléma, hogy a kért Virtuálisgép-méret nem támogatott, próbálkozzon egy másik Virtuálisgép-méretet. Ellenkező esetben az egyetlen lehetősége a kitűzési korlátozás távolítható el.

Két gyakori hiba forgatókönyvek affinitáscsoportok kapcsolódnak. A múltban affinitáscsoport használatával adja meg a virtuális gépek/szolgáltatáspéldány közel vagy való engedélyezése a virtuális hálózat létrehozása. Regionális virtuális hálózatok bevezetésével affinitáscsoportok szükségesek már nem hozhat létre virtuális hálózatot. Azure-infrastruktúra a hálózati késés csökkentésére az ajánlás affinitáscsoportok használandó virtuális gép vagy szolgáltatás közelségi kapcsolat megváltozott.

5. ábra alatt a besorolás (rögzített) foglalás forgatókönyv mutatja be.
![Rögzített foglalás besorolás](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [!NOTE]
> Egy rövid formája, amely minden foglalási forgatókönyvben hiba. Tekintse meg a [hiba karakterlánc keresési](#Error string lookup) a részletes hibaüzeneteket tartalmazza.
> 
> 

## <a name="allocation-scenario-resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Foglalási forgatókönyv: méretezze át a virtuális gépek vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
**Hiba történt**

Upgrade_VMSizeNotSupported vagy GeneralError

**A fürt rögzítési OK**

Méretezze át egy virtuális Gépet, vagy egy virtuális gép vagy egy szerepkörpéldányt hozzáadása egy meglévő felhőszolgáltatáshoz kérelem ki lehet megkísérelni, az eredeti fürt, amelyen a meglévő felhőalapú szolgáltatást. Új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Ha a hiba Upgrade_VMSizeNotSupported *, próbálkozzon egy másik Virtuálisgép-méretet. Ha egy másik Virtuálisgép-méretet használó lehetőség nem érhető el, de a másik virtuális IP-cím (VIP) elfogadható, az új virtuális gép és az új felhőalapú szolgáltatás hozzáadása a regionális virtuális hálózatot, amelyen a meglévő virtuális gépek futnak a új felhőalapú szolgáltatás létrehozása. Ha a meglévő felhőalapú szolgáltatást használja a regionális virtuális hálózatot, továbbra is létrehozhat egy új virtuális hálózat az új felhőalapú szolgáltatás, és csatlakoztassa a [az új virtuális hálózat már meglévő virtuális hálózatot](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Ha a hiba GeneralError *, valószínű, hogy típusú erőforrások (például egy adott virtuális gép méretét) támogatja a fürt, de a fürt nem rendelkezik szabad erőforrást jelenleg. Hasonló a fenti esetben vegye fel a kívánt számítási erőforrással keresztül új felhőalapú szolgáltatás (vegye figyelembe, hogy rendelkezik-e az új felhőalapú szolgáltatás használatára egy különböző VIP) létrehozásához, és a felhőszolgáltatások eléréséhez regionális virtuális hálózatot használja.

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Foglalási forgatókönyv: újraindítás részlegesen leállítva (felszabadított) virtuális gépek
**Hiba történt**

GeneralError *

**A fürt rögzítési OK**

Részleges felszabadítás azt jelenti, hogy a felhőszolgáltatás (felszabadított) egy vagy több, de nem minden virtuális gépe leállt. Amikor leállít (felszabadítása) a virtuális gép, a kapcsolódó erőforrások kiadásakor. A leállított (felszabadított) virtuális gép újraindítása ezért egy új memóriafoglalási kérelem. Virtuális gépek újraindításával részben felszabadított felhőszolgáltatásban megegyezik virtuális gépek hozzáadása egy meglévő felhőszolgáltatáshoz. A memóriafoglalási kérelem ki lehet megkísérelni, az eredeti fürt, amelyen a meglévő felhőalapú szolgáltatást. Másik felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Elfogadható használja másik virtuális IP-címhez, törölje a leállított (felszabadított) virtuális gépek (de tartsa a kapcsolódó lemezek), és adja hozzá a virtuális gépek biztonsági keresztül másik felhőalapú szolgáltatást. Regionális virtuális hálózatot használja a felhőszolgáltatások eléréséhez:

* Ha a meglévő felhőalapú szolgáltatást használ egy regionális virtuális hálózatot, vegyük fel az új felhőalapú szolgáltatás ugyanazt a virtuális hálózatot.
* Ha a meglévő felhőalapú szolgáltatást használja a regionális virtuális hálózat, az új felhőszolgáltatás, új virtuális hálózat létrehozása, majd [csatlakozzon a meglévő virtuális hálózatot az új virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="allocation-scenario-restart-fully-stopped-deallocated-vms"></a>Foglalási forgatókönyv: újraindítás teljesen leállt (felszabadított) virtuális gépek
**Hiba történt**

GeneralError *

**A fürt rögzítési OK**

Teljes felszabadítás azt jelenti, hogy leállította (felszabadítva) összes virtuális gépet egy felhőalapú szolgáltatás. Indítsa újra a virtuális gépeken foglalási kérelmeket kell kísérli meg a következő az eredeti fürt, amelyen a felhőalapú szolgáltatás. Új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Ha az elfogadható használja másik virtuális IP-címhez, törli az eredeti leállított (felszabadított) virtuális gépek (de tartsa a kapcsolódó lemezek), és törölje a megfelelő felhőszolgáltatást (a tartozó számítási erőforrásokat már kiadott, (felszabadított) leállításakor a virtuális gépek). Hozzon létre egy új felhőalapú szolgáltatás hozzáadása a virtuális gépek vissza.

## <a name="allocation-scenario-stagingproduction-deployments-platform-as-a-service-only"></a>Foglalási forgatókönyv: átmeneti vagy üzemi környezetek (csak szolgáltatásként platform)
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**A fürt rögzítési OK**

Az átmeneti üzembe helyezési és az éles környezet egy felhőalapú szolgáltatás ugyanazon fürt üzemelteti. Amikor a második telepítést, akkor a megfelelő memóriafoglalási kérelem ugyanazon fürt, amelyen az első telepített történt kísérlet.

**Megkerülő megoldás**

Törli a első és az eredeti felhőalapú szolgáltatás, és telepítse újra a felhőalapú szolgáltatáshoz. Ez a művelet lehet, hogy az első telepített megnyílik, elég szabad erőforrást mindkét központi telepítés módosításokkal rendelkező fürtben, vagy a fürt, amely támogatja a kért Virtuálisgép-méretek.

## <a name="allocation-scenario-affinity-group-vmservice-proximity"></a>Foglalási forgatókönyv: affinitáscsoport (virtuális gép vagy szolgáltatás beléptetőkártyával)
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**A fürt rögzítési OK**

A számítási erőforrás egy affinitáscsoporthoz van kötve egy fürt. Új számítási erőforrás-kérelmek abban a affinitáscsoport ugyanabban a fürtben, a meglévő erőforrásokat a rendszer hol tárolja a rendszer megkísérelte. Ez érvényét veszti, hogy az új erőforrások jönnek létre egy új felhőalapú szolgáltatás vagy egy meglévő felhőszolgáltatáshoz keresztül.

**Megkerülő megoldás**

Az affinitáscsoportokban nincs szükség, ha nem használható affinitáscsoport, vagy csoportosíthatja a számítási erőforrásokat több affinitáscsoportok.

## <a name="allocation-scenario-affinity-group-based-virtual-network"></a>Foglalási forgatókönyv: affinitás csoport-alapú virtuális hálózat
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**A fürt rögzítési OK**

Regionális virtuális hálózatokba jelentek meg, mielőtt rendelje hozzá a virtuális hálózat affinitáscsoporttal kellett. Ennek eredményeképpen az affinitáscsoport csoportosítson erőforrásokat kötik azonos korlátait leírtak számítási a "foglalási forgatókönyv: affinitáscsoport (virtuális gép vagy szolgáltatás beléptetőkártyával)" szakaszban. A számítási erőforrásokat egy fürt vannak társítva.

**Megkerülő megoldás**

Az affinitáscsoportokban nem szükséges, ha az új erőforrások hozzáadása esetén új regionális virtuális hálózat létrehozása, majd [csatlakozzon a meglévő virtuális hálózatot az új virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternatív megoldásként, [a kapcsolat-csoport-alapú virtuális hálózat regionális virtuális hálózat áttelepítése](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), majd újra adja hozzá a kívánt erőforrást.

## <a name="detailed-troubleshooting-steps-specific-allocation-failure-scenarios-in-the-azure-resource-manager-deployment-model"></a>Részletes hibaelhárítási lépéseket adott foglalási hiba forgatókönyvek az Azure Resource Manager üzembe helyezési modellel
Az alábbiakban gyakori foglalási forgatókönyvek, amelyek egy foglalási kérelem rögzítve. Igazolnia kell alaposabban egyes forgatókönyvek a cikk későbbi részében.

* Méretezze át a virtuális gépek vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
* Indítsa újra a részlegesen leállított (felszabadított) virtuális gépek
* Indítsa újra a teljes leállított (felszabadított) virtuális gépek

Foglalási hiba megjelenésekor tekintse meg, ha bemutatott esetekben a hiba a érvényes. Az Azure platform által visszaadott foglalási hiba segítségével azonosíthatja a megfelelő forgatókönyv. Ha a kérés egy meglévő fürthöz van rögzítve, távolítson el néhányat a kitűzési korlátozást nyissa meg a további fürtök, növelve a eséllyel sikerül, felosztási vonatkozó kérelmét.

Általában mindaddig, amíg a hiba nem jelzi, "a kért Virtuálisgép-méret nem támogatott", mindig újból megpróbálkozhat egy későbbi időpontban erőforrásokkal előfordulhat, hogy rendelkezik felszabadult a fürt a kérelem olyan módon. Ha a probléma, hogy a kért Virtuálisgép-méret nem támogatott, lásd az alábbi a lehetséges megoldások.

## <a name="allocation-scenario-resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Foglalási forgatókönyv: méretezze át a virtuális gépek vagy virtuális gépek felvétele a rendelkezésre állási készlet
**Hiba történt**

Upgrade_VMSizeNotSupported * vagy GeneralError *

**A fürt rögzítési OK**

Méretezze át egy virtuális Gépet, vagy adja hozzá a virtuális gépek rendelkezésre állási készlet kérelem ki lehet megkísérelni, az eredeti fürt, amelyen a meglévő rendelkezésre állási csoportot. Egy új rendelkezésre állási készlet létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Ha a hiba Upgrade_VMSizeNotSupported *, próbálkozzon egy másik Virtuálisgép-méretet. Ha egy másik Virtuálisgép-méretet használó lehetőség nem érhető el, állítsa le a rendelkezésre állási csoport virtuális gépeinek. Módosíthatja a virtuális gépen futó foglal le a virtuális gépek a fürt, amely támogatja a kívánt Virtuálisgép-méretet a méretét.

Ha a hiba GeneralError *, valószínű, hogy típusú erőforrások (például egy adott virtuális gép méretét) támogatja a fürt, de a fürt nem rendelkezik szabad erőforrást jelenleg. Ha a virtuális gép egy másik rendelkezésre állási készlet része lehet, hozzon létre egy új virtuális gép egy másik rendelkezésre állási csoportban, (ugyanabban a régióban). Új virtuális gép felvehető ugyanahhoz a virtuális hálózathoz.  

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Foglalási forgatókönyv: újraindítás részlegesen leállítva (felszabadított) virtuális gépek
**Hiba történt**

GeneralError *

**A fürt rögzítési OK**

Részleges felszabadítás azt jelenti, hogy (felszabadított) egy vagy több leállt, de nem minden, a rendelkezésre állási virtuális gépeket. Amikor leállít (felszabadítása) a virtuális gép, a kapcsolódó erőforrások kiadásakor. A leállított (felszabadított) virtuális gép újraindítása ezért egy új memóriafoglalási kérelem. Részlegesen felszabadított rendelkezésre állási csoportban lévő virtuális gépek újraindításával megegyezik virtuális gépek hozzáadása egy meglévő rendelkezésre állási csoportot. A memóriafoglalási kérelem ki lehet megkísérelni, az eredeti fürt, amelyen a meglévő rendelkezésre állási csoportot.

**Megkerülő megoldás**

Állítsa le az összes virtuális gép rendelkezésre állási csoportban, az újraindítás előtt. Ezzel biztosíthatja, hogy fut-e egy új foglalási kísérlet és, hogy egy új fürt választhat ki, amely rendelkezik a rendelkezésre álló kapacitásból.

## <a name="allocation-scenario-restart-fully-stopped-deallocated"></a>Foglalási forgatókönyv: újraindítás teljesen leállt (felszabadított)
**Hiba történt**

GeneralError *

**A fürt rögzítési OK**

Teljes felszabadítás azt jelenti, hogy leállította (felszabadítva) minden virtuális gép rendelkezésre állási csoportba. A virtuális gépek újraindítására a memóriafoglalási kérelem által megcélzott összes fürt, amely támogatja a kívánt méretet.

**Megkerülő megoldás**

Jelölje be a lefoglalni egy új Virtuálisgép-méretet. Ha ez nem működik, próbálkozzon újra később.

<a name="Error string lookup"></a>

## <a name="error-string-lookup"></a>Hibák karakterlánc keresése
**New_VMSizeNotSupported***

"A virtuális gép mérete (vagy a Virtuálisgép-méretek kombinációja) szükséges a központi telepítési nem lehet kiépíteni telepítési kérelem korlátok miatt. Ha lehetséges próbálja meg lazítani a megszorításokon (például virtuális hálózati kötéseken), helyezze üzembe olyan üzemeltett szolgáltatásban, nincs másik üzemelő példány, illetve másik affinitáscsoportban vagy affinitáscsoport nélkül, vagy próbálja más régióban üzembe helyezni."

**New_General***

"Kiosztása nem sikerült; nem felel meg a kérelem megkötések. A kért új szolgáltatástelepítés egy affinitáscsoporthoz van kötve, vagy azt a virtuális hálózat célozza, van egy meglévő üzemelő példányt az üzemeltetett szolgáltatásban. Az alábbi feltételek valamelyikének korlátozza az új központi telepítést az adott Azure-erőforrások. Próbálkozzon újra később, vagy csökkentse a virtuális gép méretét vagy a szerepkörpéldányok számát. Azt is megteheti, ha lehetséges, távolítsa el a fentebb említett korlátozások, vagy próbálja más régióban üzembe helyezni."

**Upgrade_VMSizeNotSupported***

"Nem lehet frissíteni az üzemelő példányt. A kért Virtuálisgép-méret XXX nem lehet elérhető a meglévő telepítés támogató erőforrásokban. Próbálkozzon újra később, próbálkozzon más Virtuálisgép-méretet vagy kevesebb szerepkörpéldányt beállítani, vagy hozzon létre egy üzemelő példányt egy üres üzemeltetett szolgáltatásban új affinitáscsoporttal vagy affinitáscsoport nélkül az kötés."

**GeneralError***

"A kiszolgáló belső hibát észlelt. Próbálja megismételni a kérelmet." Vagy "A feladatvégrehajtás nem memóriakiosztást a szolgáltatáshoz."

