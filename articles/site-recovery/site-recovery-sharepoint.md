---
title: Állítsa be az Azure Site Recovery SharePoint Többrétegű alkalmazások vészhelyreállítása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állítható be az Azure Site Recovery képességeivel Többrétegű alkalmazások SharePoint vész-helyreállítása.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 5f477cf20b817d7a6c8be856636bf1e3755b5424
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443487"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Állítsa be az Azure Site Recovery használata vész-helyreállítási SharePoint Többrétegű alkalmazások vészhelyreállítása

Ez a cikk részletesen ismerteti, hogyan védheti meg a SharePoint alkalmazást a [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Áttekintés

A Microsoft SharePoint egy nagy teljesítményű alkalmazások, amelyek segítségével egy csoport vagy részleg rendezhet, együttműködés és megoszthatja az információkat. A SharePoint intranetes portálok, dokumentum és fájl felügyeleti, együttműködési, közösségi hálózatokkal, extranetek, webhelyek, nagyvállalati kereső és üzleti intelligencia biztosíthat. Rendszer-integrációs, a folyamatintegráció és a munkafolyamat-automatizálási képességeivel is rendelkezik. Általában szervezeteknek érdemes használni, 1. rétegbeli alkalmazás érzékeny az üzemkimaradás és az adatvesztés.

A Microsoft SharePoint jelenleg nem biztosít bármely-a-beépített vész-helyreállítási lehetőségei. A típusa és a méretezési csoport vészhelyzet függetlenül helyreállítási helyreállítható a farm készenléti adatközpontban használatát foglalja magában. Forgatókönyvek, ahol helyi redundáns rendszerek és a biztonsági mentések nem lehet helyreállítani az elsődleges adatközpont leállásakor készenléti adatközpontok szükségesek.

Egy jó vész-helyreállítási megoldás engedélyeznie kell a modellezést, a helyreállítási tervek körül az alkalmazás összetett architektúrák, például a SharePoint. Lehetővé teszi, hogy testre szabott lépések között különböző szinteken, és ezért így egykattintásos feladatátvétel egy alacsonyabb RTO egy esetleges vészhelyzet esetén az alkalmazás-hozzárendelések kezeléséhez is rendelkezhet.

Ez a cikk részletesen ismerteti, hogyan védheti meg a SharePoint alkalmazást a [Azure Site Recovery](site-recovery-overview.md). Ez a cikk azzal foglalkozik, ajánlott eljárások a replikál egy három réteg Azure, hogyan teheti egy vészhelyreállítási próba végrehajtása és feladatátvétel az Azure-ban az alkalmazás adhat a SharePoint-alkalmazás.

Megtekintheti az alábbi videó az Azure-bA Többrétegű alkalmazások helyreállításával kapcsolatban.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megértette a következő:

1. [A virtuális gépek replikálása Azure-bA](site-recovery-vmware-to-azure.md)
2. Hogyan [egy helyreállítási hálózat tervezése](site-recovery-network-design.md)
3. [Teszt feladatátvétel végrehajtása az Azure-bA](site-recovery-test-failover-to-azure.md)
4. [Feladatátvétel végrehajtása az Azure-bA](site-recovery-failover.md)
5. Hogyan [tartományvezérlő replikálása](site-recovery-active-directory.md)
6. Hogyan [SQL Server replikálása](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>A SharePoint-architektúra

A SharePoint egy vagy több kiszolgáló megfelel-e meghatározott célok és célkitűzések farm kialakítás megvalósítása rétegzett topológiákat és a kiszolgálói szerepkörök használatával telepíthető. Egy tipikus nagy, sokak által kért SharePoint támogató kiszolgálófarmot, nagyszámú egyidejű felhasználót és a tartalmak nagy számú szolgáltatás a csoportosítás használatához a méretezhetőség stratégiájuk részeként. Ez a megközelítés magában foglalja a szolgáltatásokat futtató dedikált kiszolgálókon, ezek a szolgáltatások csoportosítása, és ezután horizontális felskálázás a kiszolgálók egy csoportként. A következő topológiát mutatja be, a szolgáltatás és a egy SharePoint-kiszolgálófarm három réteg csoportosítási kiszolgáló. Tekintse meg a SharePoint-dokumentáció és a termék sor architektúrákat különböző SharePoint topológiák részletes útmutatást. További információt a SharePoint 2013 üzembe helyezése az annak [ebben a dokumentumban](https://technet.microsoft.com/library/cc303422.aspx).



![1. telepítési minta](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery támogatása

Ez a cikk létrehozására, VMware virtuális gépeknek a Windows Server 2012 R2 Enterprise használták. A SharePoint 2013 Enterprise edition és az SQL server 2014 Enterprise edition használták. A Site Recovery replikációs az alkalmazás független, a javaslatok, az itt elérhető kis türelmet, a következő szituációkat is várható.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen
**Azure** | NA | Igen

### <a name="sharepoint-versions"></a>SharePoint-verziók
A következő SharePoint server-verziók támogatottak.

* SharePoint server 2013 Standard
* SharePoint server 2013 Enterprise
* SharePoint server 2016 Standard
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Szem előtt tartani

Ha egy megosztott lemez alapú fürtöt használ, minden szinten az alkalmazás ezután nem ezeket a virtuális gépeket replikálhat a Site Recovery replikációs használhatják. Az alkalmazás által biztosított natív replikációval, és ezután egy [helyreállítási terv](site-recovery-create-recovery-plans.md) feladatátvétel minden szint esetében.

## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálásához

Hajtsa végre a [Ez az útmutató](site-recovery-vmware-to-azure.md) elindítani a virtuális gép replikálása az Azure-bA.

* A replikáció befejeződése után győződjön meg arról, nyissa meg az egyes csomagok minden virtuális gép, és válassza ki az azonos rendelkezésre állási csoport "replikált elem > Beállítások > Tulajdonságok > Számítás és hálózat". Például, ha a webes szint 3 virtuális gép, ellenőrizze a 3 virtuális gépeket egyazon rendelkezésre állási csoportban az Azure-ban részeként.

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Az Active Directory és DNS védelme útmutatásért tekintse meg [védelme az Active Directory és DNS-](site-recovery-active-directory.md) dokumentumot.

* SQL Serveren futó adatbázisszint védelméről útmutatásért tekintse meg [védelme SQL Server](site-recovery-active-directory.md) dokumentumot.

## <a name="networking-configuration"></a>Hálózati konfiguráció

### <a name="network-properties"></a>Hálózat tulajdonságai

* Az alkalmazás és a webes szintű virtuális gépek hálózati beállítások konfigurálása az Azure Portalon, hogy a virtuális gépek beolvasása a megfelelő DR hálózathoz csatlakozik a feladatátvételt követően.

    ![Hálózat kiválasztása](./media/site-recovery-sharepoint/select-network.png)


* Ha statikus IP-címet használ, majd adja meg az IP-Címmel azt szeretné, hogy a virtuális gép lépése a **cél IP-címet** mező

    ![Statikus IP-cím beállítása](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS- és a forgalom-Útválasztás

Internetkapcsolattal rendelkező helyek ["Priority" típusú Traffic Manager-profil létrehozása](../traffic-manager/traffic-manager-create-profile.md) az Azure-előfizetésében. Majd konfigurálja a DNS- és a Traffic Manager-profilt, az alábbi módon.


| **ahol** | **Forrás** | **Target**|
| --- | --- | --- |
| Public DNS | Nyilvános DNS-ben a SharePoint-webhelyek <br/><br/> Például: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| On-premises DNS | sharepointonprem.contoso.com | Nyilvános IP-címet a helyszíni farm |


A Traffic Manager-profilban [az elsődleges és helyreállítási végpontokat hozza létre](../traffic-manager/traffic-manager-configure-priority-routing-method.md). A külső végpont használata a helyi végpont és a nyilvános IP-cím az Azure-végpont. Győződjön meg arról, hogy a prioritás értéke magasabb a helyszíni végpontra.

A gazdagép egy adott portot (például 800) tesztoldalt a SharePoint webes rétegből ahhoz a Traffic Manager automatikusan észleli a rendelkezésre állási feladatátvétel után. Ez a megoldás, abban az esetben, ha bármely, a SharePoint-webhelyek nem engedélyezhető a névtelen hitelesítést.

[A Traffic Manager-profil konfigurálása](../traffic-manager/traffic-manager-configure-priority-routing-method.md) az az alábbi beállításokat.

* Útválasztási módszer – "Priority"
* Az élettartam (TTL) – "30 másodperc' DNS-idő
* Végpontfigyelő beállításai – Ha engedélyezi a névtelen hitelesítés biztosíthat egy adott webhely végpontja. Vagy használhatja egy teszt lap egy adott portot (például 800).

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv lehetővé teszi, hogy a műveleti sorrend, a feladatátvétel különböző rétegek a többrétegű alkalmazásokban, ezért application konzisztencia megőrzése érdekében. Kövesse az alábbi lépéseket a helyreállítási terv egy többrétegű webes alkalmazás létrehozása során. [További tudnivalók a helyreállítási terv létrehozása](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok felvétele

1. A helyreállítási terv létrehozásához adja meg az alkalmazás és a webes szintű virtuális gépeket.
2. Kattintson a "Testreszabás" gombra a virtuális gépek csoportosítása. Alapértelmezés szerint minden virtuális gép a csoport "% 1" részét képezik.

    ![Helyreállítási pont Védettként testreszabása](./media/site-recovery-sharepoint/rp-groups.png)

3. Hozzon létre egy másik csoportot, (2. csoport) és a webes szintű virtuális gépek áthelyezése az új csoportba. Az alkalmazás az adatszintű virtuális gépeket (1. csoport) kell tartozniuk, és a webes szintű virtuális gépek kell lennie a csoport "% 2" részét. Ez a annak érdekében, hogy fel az alkalmazás szintű virtuális gépek rendszerindítási először a webes szintű virtuális gépek követ.


### <a name="adding-scripts-to-the-recovery-plan"></a>A helyreállítási tervbe szkriptek hozzáadása

A leggyakrabban használt Azure Site Recovery-szkriptek üzembe helyezhető az Automation-fiókját, ha a "Üzembe helyezés az Azure" gombra kattint. Bármely közzétett parancsfájl használatakor győződjön meg arról, hogy kövessék a parancsfájl.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Előzetes művelet parancsfájl hozzáadása (1. csoport) a feladatátvétel SQL rendelkezésre állási csoporthoz. Az "ASR-SQL-FailoverAG" parancsfájl használata a minta parancsprogramok közzé. Győződjön meg arról, kövesse a parancsfájl található útmutatást, és megfelelően végezze el a szükséges módosításokat a parancsfájl.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Csatlakoztassa a feladatait egy terheléselosztó post művelet parancsfájl hozzáadása a feladatátviteli virtuális gépeket a webes szint (2. csoport). Az "ASR-AddSingleLoadBalancer" parancsfájl használata a minta parancsprogramok közzé. Győződjön meg arról, kövesse a parancsfájl található útmutatást, és megfelelően végezze el a szükséges módosításokat a parancsfájl.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adjon hozzá egy manuális lépés, hogy az Azure-ban a farmban mutasson a DNS-rekordok frissítéséhez.

    * Az internetkapcsolattal helyek nem DNS el frissítés feladatátvétel után szükséges. A Traffic Manager konfigurálása "Hálózatkezelési útmutató" szakaszban ismertetett lépéseket követve. A Traffic Manager-profil be lett állítva az előző szakaszban leírtak szerint, ha az Azure virtuális gépen (a példában 800) helyőrző port megnyitásához parancsfájl hozzáadása.

    * Belső hálózati helyek adjon hozzá valamilyen manuális lépés frissítse a DNS-rekord az új webes szintű virtuális gép load balancer IP-címre mutasson.

4. Adjon hozzá egy manuális lépés search-alkalmazás visszaállítása biztonsági másolatból, vagy egy új keresési szolgáltatás elindításához.

5. Egy biztonsági másolatból történő visszaállítását a Search szolgáltatásalkalmazást, kövesse az alábbi lépéseket.

    * Ez a módszer azt feltételezi, hogy a Search Service-alkalmazás biztonsági másolatának hajtottak végre a katasztrofális esemény előtt és, hogy a biztonsági mentés érhető el a DR-helyen.
    * Ezzel könnyedén biztosíthatja a biztonsági mentés ütemezése (például naponta egyszer) és a egy másolási eljárás használatával helyezi el a biztonsági mentés a DR-helyen. Másolás eljárások magukban foglalhatják parancsfájlalapú programok például AzCopy (Azure példány) vagy az elosztott fájlrendszer replikációs szolgáltatása (elosztott szolgáltatások fájlreplikáció) beállítását.
    * Most, hogy a SharePoint-farm fut, nyissa meg a központi adminisztrációs, biztonsági mentését és visszaállítását, és válassza ki, állítsa vissza. A visszaállításhoz megadott biztonsági mentési hely interrogates (Előfordulhat, hogy frissíteni szeretné a értéket). Válassza ki a Search szolgáltatásalkalmazást biztonsági mentés, visszaállítás szeretné.
    * Keresési migráláskor áll vissza. Ne feledje, hogy a visszaállítás vár található azonos topológia (azonos számú kiszolgálók), és azonos rögzített meghajtóbetűjelei ezeket a kiszolgálókhoz legyenek hozzárendelve. További információkért lásd: ["Visszaállítás Search szolgáltatásalkalmazást SharePoint 2013-hoz"](https://technet.microsoft.com/library/ee748654.aspx) dokumentumot.


6. Az új keresési szolgáltatás alkalmazás kezdve az alábbi lépéseket követve.

    * Ez a módszer azt feltételezi, hogy a "Keresés felügyelete" adatbázis biztonsági másolatának a DR helyén érhető el.
    * A Search szolgáltatásalkalmazást adatbázisok nem lesznek replikálva, mivel azok kell lenniük, hozza létre újra. Ehhez navigáljon a központi adminisztrációs, és törli a Search szolgáltatásalkalmazást. A Search-Index futtató minden olyan kiszolgálók törölje az indexfájlok.
    * Hozza létre újból a Search szolgáltatásalkalmazást, és újból létrehozza az adatbázisokat. Azt javasoljuk, hogy egy előkészített parancsfájlt, amely ehhez a szolgáltatásalkalmazáshoz újból létrehozza, mivel nem minden műveletet a grafikus felhasználói felületen keresztül végezhető el. Például, a keresési topológiát telepítési és beállítási index meghajtóhelyét lesznek csak akkor lehetséges SharePoint PowerShell-parancsmagok használatával. A Windows PowerShell-parancsmaggal Restore-SPEnterpriseSearchServiceApplication, és adja meg a naplóküldés és a replikált keresési felügyeleti adatbázist, Search_Service__DB. Ez a parancsmag a search konfigurálása, séma, felügyelt tulajdonságok, szabályok és források biztosít, és létrehozza az alapértelmezett készletét, a többi összetevő.
    * A Search Service-alkalmazás rendelkezik kell hozza létre újra, miután minden tartalomforrásként visszaállítani a keresési szolgáltatás teljes bejárást kell elindítani. A helyszíni farmból, például a Keresés a javaslatokban néhány elemzési információk elveszhetnek.

7. A helyreállítási terv mentése összes lépésének elvégzése, és az utolsó helyreállítási terv következő fog kinézni.

    ![Mentett RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Teszt feladatátvétel végrehajtása
Hajtsa végre a [Ez az útmutató](site-recovery-test-failover-to-azure.md) feladatátvételi teszt végrehajtásához.

1.  Nyissa meg az Azure Portalra, és válassza ki a Recovery Services-tárolót.
2.  Kattintson a helyreállítási terv létrehozása SharePoint-alkalmazás.
3.  Kattintson a "Feladatátvételi teszt".
4.  Válassza ki a helyreállítási pont és az Azure virtual network, a teszt feladatátvételi folyamat elindításához.
5.  A másodlagos környezetben működik, ha az ellenőrzés hajthat végre.
6.  Ha az ellenőrzés befejeződött, a helyreállítási terv "Feladatátvételi teszt utáni karbantartás" kattintva, és a teszt feladatátvételi környezetet karbantartása.

Feladatátvételi teszt során ad útmutatást és DNS-beli [feladatátvételi szempontokat részletező cikkben, az Active Directory és DNS-](site-recovery-active-directory.md#test-failover-considerations) dokumentum.

Feladatátvételi teszt végezni az SQL Always ON rendelkezésre állási csoportok útmutatásért tekintse meg [feladatátvételi teszt során az SQL Server Always On](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentumot.

## <a name="doing-a-failover"></a>Feladatátvétel végrehajtása
Hajtsa végre a [Ez az útmutató](site-recovery-failover.md) a feladatátvétel végrehajtása.

1.  Nyissa meg az Azure Portalra, és válassza ki a helyreállítási tárban.
2.  Kattintson a helyreállítási terv létrehozása SharePoint-alkalmazás.
3.  Kattintson a "Feladatátvételi".
4.  Válassza ki a helyreállítási pontot a feladatátvételi folyamat elindításához.

## <a name="next-steps"></a>További lépések
További információ [más alkalmazások replikálása](site-recovery-workload.md) Site Recovery használatával.
