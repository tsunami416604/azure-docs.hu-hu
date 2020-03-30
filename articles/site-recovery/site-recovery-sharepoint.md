---
title: Többrétegű SharePoint-alkalmazás vészhelyreállítása az Azure Site Recovery használatával
description: Ez a cikk bemutatja, hogyan állíthat be vészhelyreállítást egy többrétegű SharePoint-alkalmazáshoz az Azure Site Recovery-képességek használatával.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: d74e28ce470c23bbc8ee2081532a198c260ccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74706367"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Vészhelyreállítás beállítása többrétegű SharePoint-alkalmazásokhoz vészhelyreállításhoz az Azure Site Recovery használatával

Ez a cikk részletesen ismerteti, hogyan védheti meg a SharePoint-alkalmazásokat az [Azure Site Recovery](site-recovery-overview.md)használatával.


## <a name="overview"></a>Áttekintés

A Microsoft SharePoint egy hatékony alkalmazás, amely segítséget nyújt egy csoport vagy részleg számára az információk rendszerezéséhez, együttműködéséhez és megosztásához. A SharePoint intranetes portálokat, dokumentum- és fájlkezelést, együttműködést, közösségi hálózatokat, extraneteket, webhelyeket, vállalati keresést és üzleti intelligenciát biztosít. Rendszerintegrációs, folyamatintegrációs és munkafolyamat-automatizálási képességekkel is rendelkezik. A szervezetek általában úgy vélik, hogy az állásidő és az adatvesztés érzékeny tier-1 alkalmazás.

A Microsoft SharePoint ma nem biztosít beépített vész-helyreállítási lehetőségeket. A katasztrófa típusától és méretétől függetlenül a helyreállítás magában foglalja egy készenléti adatközpont használatát, amelybe a farm helyreállíthatja. Készenléti adatközpontok szükségesek olyan esetekben, amikor a helyi redundáns rendszerek és biztonsági mentések nem tudnak helyreállni az elsődleges adatközpontban a szolgáltatáskimaradásból.

Egy jó vész-helyreállítási megoldás lehetővé kell tennie a helyreállítási tervek modellezése körül az összetett alkalmazásarchitektúrák, például a SharePoint. Azt is lehetővé kell tenni, hogy személyre szabott lépéseket az alkalmazás leképezések kezelésére különböző szintek között, és így egy kattintásos feladatátvétel alacsonyabb RTO katasztrófa esetén.

Ez a cikk részletesen ismerteti, hogyan védheti meg a SharePoint-alkalmazásokat az [Azure Site Recovery](site-recovery-overview.md)használatával. Ez a cikk ismerteti a háromrétegű SharePoint-alkalmazások Azure-ba replikálására vonatkozó gyakorlati tanácsokat, hogyan teheti meg a vész-helyreállítási gyakorlatot, és hogyan veheti át feladatátvételt az alkalmazás azure-ba.

Megtekintheti az alábbi videót egy többrétegű alkalmazás Azure-ba való helyreállításáról.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy megértette a következőket:

1. [Virtuális gép replikálása az Azure-ba](site-recovery-vmware-to-azure.md)
2. Helyreállítási [hálózat tervezése](site-recovery-network-design.md)
3. [Tesztfeladat-átvétel az Azure-ba](site-recovery-test-failover-to-azure.md)
4. [Feladat-átvétel az Azure-ba](site-recovery-failover.md)
5. Tartományvezérlő [replikálása](site-recovery-active-directory.md)
6. Az [SQL Server replikálása](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-architektúra

A SharePoint egy vagy több kiszolgálóra is telepíthető rétegzett topológiák és kiszolgálói szerepkörök használatával olyan farmterv megvalósításához, amely megfelel bizonyos céloknak és céloknak. Egy tipikus nagy, nagy igényű SharePoint-kiszolgálófarm, amely nagyszámú egyidejű felhasználót és nagyszámú tartalomelemet támogat, a méretezhetőségi stratégia részeként szolgáltatáscsoportosítást használ. Ez a megközelítés magában foglalja a szolgáltatások dedikált kiszolgálókon történő futtatását, a szolgáltatások csoportosítását, majd a kiszolgálók csoportként történő horizontális felskálázását. Az alábbi topológia egy háromrétegű SharePoint-kiszolgálófarm szolgáltatás- és kiszolgálócsoportosítását mutatja be. A SharePoint dokumentációjában és termékcsalád-architektúráiban részletes útmutatást talál a különböző SharePoint-topológiákról. A SharePoint 2013 központi telepítéséről ebben a dokumentumban talál további [részleteket.](https://technet.microsoft.com/library/cc303422.aspx)



![1. telepítési minta](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery támogatása

A Site Recovery alkalmazásfüggetlen, és a Támogatott gépen futó SharePoint bármely verziójával együtt kell működnie. A cikk létrehozásához a VMware virtuális gépeket a Windows Server 2012 R2 Enterprise rendszerrel hozták létre. A SharePoint 2013 Enterprise edition és az SQL Server 2014 Enterprise edition programot használt.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Igen
**Vmware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen
**Azure** | NA | Igen


### <a name="things-to-keep-in-mind"></a>Ügyeljen az alábbiakra

Ha az alkalmazás bármely rétegeként megosztott lemezalapú fürtöt használ, akkor a Site Recovery replikációs replikációja nem fogja tudni használni a virtuális gépek replikálásához. Használhatja az alkalmazás által biztosított natív replikációt, majd egy [helyreállítási terv](site-recovery-create-recovery-plans.md) használatával minden réteg feladatátvételt.

## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálása

Kövesse [ezt az útmutatást](site-recovery-vmware-to-azure.md) a virtuális gép Azure-ba történő replikálásának megkezdéséhez.

* Miután a replikáció befejeződött, győződjön meg arról, hogy minden egyes réteg minden virtuális gépére megy, és ugyanazt a rendelkezésre állási készletet választja a "Replikált elem > beállítások > tulajdonságok > számítási és hálózati" mezőben. Ha például a webes szint 3 virtuális géptel rendelkezik, győződjön meg arról, hogy az összes 3 virtuális gép úgy van konfigurálva, hogy az Azure-ban azonos rendelkezésre állási készlet része legyen.

    ![Készlet-rendelkezésre állás-készlet](./media/site-recovery-sharepoint/select-av-set.png)

* Az Active Directory és a DNS védelmével kapcsolatos útmutatásért olvassa el az [Active Directory és a DNS védelme](site-recovery-active-directory.md) című dokumentumcímű dokumentumban.

* Az SQL-kiszolgálón futó adatbázisréteg védelmével kapcsolatos útmutatásért tekintse meg [az SQL Server-dokumentum védelme](site-recovery-sql.md) című dokumentumot.

## <a name="networking-configuration"></a>Hálózati konfiguráció

### <a name="network-properties"></a>Hálózati tulajdonságok

* Az alkalmazás- és webréteg virtuális gépei esetében konfigurálja a hálózati beállításokat az Azure Portalon, hogy a virtuális gépek a feladatátvétel után a megfelelő DR-hálózathoz kapcsolódjanak.

    ![Hálózat kiválasztása](./media/site-recovery-sharepoint/select-network.png)


* Ha statikus IP-címet használ, adja meg azt az IP-címet, amelyet a virtuális gépnek a **Cél IP** mezőben el kell látnia

    ![Statikus IP beállítása](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS és forgalomátirányítás

Az internetes webhelyek hez [hozzon létre egy "Prioritás" típusú Traffic Manager-profilt](../traffic-manager/traffic-manager-create-profile.md) az Azure-előfizetésben. Ezután konfigurálja a DNS- és Traffic Manager-profilját a következő módon.


| **Ahol** | **Forrás** | **Cél**|
| --- | --- | --- |
| Nyilvános DNS | Nyilvános DNS SharePoint-webhelyekhez <br/><br/> Pl.: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Helyszíni DNS | sharepointonprem.contoso.com | Nyilvános IP a helyszíni farmon |


A Traffic Manager-profilban [hozza létre az elsődleges és helyreállítási végpontokat.](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Használja a külső végpontot a helyszíni végponthoz és az Azure-végpont nyilvános IP-címéhez. Győződjön meg arról, hogy a prioritás magasabb a helyszíni végpontra van állítva.

Egy tesztlap megadása egy adott porton (például 800) a SharePoint webes rétegében annak érdekében, hogy a Traffic Manager automatikusan észlelhesse a rendelkezésre állás utáni feladatátvételt. Ez egy kerülő megoldás arra az esetre, ha egyik SharePoint-webhelyen sem tudja engedélyezni a névtelen hitelesítést.

[Konfigurálja a Traffic Manager-profilt](../traffic-manager/traffic-manager-configure-priority-routing-method.md) az alábbi beállításokkal.

* Útválasztási módszer - "Prioritás"
* DNS-idő az élő (TTL) - "30 másodperc"
* Végpontfigyelő beállításai – Ha engedélyezheti a névtelen hitelesítést, adott webhelyvégpontot adhat meg. Vagy használhat tesztoldalt egy adott porton (például 800).

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv lehetővé teszi a különböző szintek feladatátvételének szekvenálását egy többrétegű alkalmazásban, így az alkalmazás konzisztenciájának fenntartása. Kövesse az alábbi lépéseket, miközben helyreállítási tervet hoz létre egy többrétegű webalkalmazáshoz. [További információ a helyreállítási terv létrehozásáról.](site-recovery-runbook-automation.md#customize-the-recovery-plan)

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása feladatátvételi csoportokhoz

1. Hozzon létre egy helyreállítási tervet az alkalmazás- és webesréteg virtuális gépei hozzáadásával.
2. A virtuális gépek csoportosításához kattintson a "Testreszabás" gombra. Alapértelmezés szerint minden virtuális gép az "1.

    ![RP testreszabása](./media/site-recovery-sharepoint/rp-groups.png)

3. Hozzon létre egy másik csoportot (2. csoport), és helyezze át a webes réteg virtuális gépeit az új csoportba. Az alkalmazásszintű virtuális gépeknek az "1. Ez biztosítja, hogy az alkalmazásszintű virtuális gépek először elindulnak, majd a webes szintű virtuális gépek.


### <a name="adding-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez

A leggyakrabban használt Azure Site Recovery-parancsfájlokat üzembe helyezheti az Automation-fiókba, az alábbi "Üzembe helyezés az Azure-ba" gombra kattintva. Ha bármely közzétett parancsfájlt használ, győződjön meg arról, hogy követi a parancsfájlban található útmutatást.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adjon hozzá egy művelet előtti parancsfájlt az "1. Használja a mintaparancsfájlokban közzétett "ASR-SQL-FailoverAG" parancsfájlt. Győződjön meg arról, hogy kövesse a parancsfájlban található útmutatást, és megfelelően hajtsa végre a szükséges módosításokat a parancsfájlban.

    ![Add-AG-Script-Lépés-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Lépés-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adjon hozzá egy műveletutáni parancsfájlt, amelyhez egy terheléselosztót csatolhat a webes réteg (2. csoport) virtuális gépein. Használja a mintaparancsfájlokban közzétett "ASR-AddSingleLoadBalancer" parancsfájlt. Győződjön meg arról, hogy kövesse a parancsfájlban található útmutatást, és megfelelően hajtsa végre a szükséges módosításokat a parancsfájlban.

    ![-LB-Script-Step-1 hozzáadása](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![-LB-Script-Step-2 hozzáadása](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Manuális lépés hozzáadása a DNS-rekordok frissítéséhez, hogy az Azure új farmjára mutasson.

    * Az internettel szemben álló webhelyek esetében nincs szükség DNS-frissítésre a feladatátvétel után. A Traffic Manager konfigurálásához kövesse a "Hálózati útmutató" című szakaszban ismertetett lépéseket. Ha a Traffic Manager-profil az előző szakaszban leírtak szerint van beállítva, adjon hozzá egy parancsfájlt a próbabábu-port megnyitásához (800 a példában) az Azure virtuális gépen.

    * Belső elérésű helyek esetén adjon hozzá egy manuális lépést a DNS-rekord frissítéséhez, hogy az új webszintű virtuális gép terheléselosztó IP-címére mutasson.

4. Manuális lépés hozzáadása a keresési alkalmazás biztonsági másolatból való visszaállításához vagy egy új keresési szolgáltatás indításához.

5. A Keresési szolgáltatás alkalmazás biztonsági másolatból történő visszaállításához kövesse az alábbi lépéseket.

    * Ez a módszer feltételezi, hogy a keresési szolgáltatás alkalmazás biztonsági mentése a katasztrofális esemény előtt történt, és hogy a biztonsági mentés elérhető a VÉSZ-helyen.
    * Ez könnyen elérhető a biztonsági mentés ütemezésével (például naponta egyszer), és egy másolási eljárás segítségével helyezze el a biztonsági másolatot a VÉSZ-helyen. Másolási eljárások lehetnek parancsfájlalapú programok, például az AzCopy (Azure Copy) vagy a DFSR (Elosztott fájlszolgáltatások replikációs szolgáltatás) beállítása.
    * Most, hogy a SharePoint-farm fut, keresse meg a Központi felügyelet "Biztonsági mentés és visszaállítás" című részét, és válassza a Visszaállítás lehetőséget. A visszaállítás lekérdezi a megadott biztonsági mentési helyet (szükség lehet az érték frissítésére). Válassza ki a visszaállítani kívánt keresési szolgáltatásalkalmazás biztonsági másolatát.
    * A keresés visszaáll. Ne feledje, hogy a visszaállítás azt várja, hogy ugyanazt a topológiát (azonos számú kiszolgálót) és a kiszolgálókhoz rendelt merevlemez-betűjeleket találja. További információt a ["Search service application in SharePoint 2013" című dokumentumban talál.](https://technet.microsoft.com/library/ee748654.aspx)


6. Ha új keresési szolgáltatásalkalmazással kezdi, kövesse az alábbi lépéseket.

    * Ez a módszer feltételezi, hogy a "Search Administration" adatbázis biztonsági másolata elérhető a VÉSZ-helyen.
    * Mivel a többi keresési szolgáltatásalkalmazás-adatbázis nem replikálódik, újra létre kell hozni őket. Ehhez keresse meg a Központi felügyelet lapot, és törölje a keresési szolgáltatásalkalmazást. A keresési indexet tartalmazó kiszolgálókon törölje az indexfájlokat.
    * Hozza létre újra a keresési szolgáltatásalkalmazást, és ez hozza létre újra az adatbázisokat. Javasoljuk, hogy egy előkészített parancsfájlt, amely újra létrehozza ezt a szolgáltatásalkalmazást, mivel nem lehet végrehajtani az összes műveletet a grafikus felhasználói felületen keresztül. Például az indexmeghajtó helyének beállítása és a keresési topológia konfigurálása csak SharePoint PowerShell-parancsmagok használatával lehetséges. Használja a Windows PowerShell-parancsmag visszaállítás-SPEnterpriseSearchServiceApplication és adja meg a naplószállított és replikált Search Administration adatbázis, Search_Service__DB. Ez a parancsmag adja meg a keresési konfigurációt, sémát, felügyelt tulajdonságokat, szabályokat és forrásokat, és létrehoz egy alapértelmezett készletet a többi összetevőből.
    * A keresési szolgáltatás alkalmazás újbóli létrehozása után minden tartalomforráshoz el kell indítania egy teljes bejárást a keresési szolgáltatás visszaállításához. A helyszíni farmból bizonyos elemzési adatok elvesznek, például a keresési javaslatok.

7. Miután az összes lépést elvégezte, mentse a helyreállítási terv és a végső helyreállítási terv fog kinézni a következő.

    ![Mentett RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Tesztfeladat-átvétel e
Kövesse [ezt az útmutatást](site-recovery-test-failover-to-azure.md) a teszt feladatátvételhez.

1.  Nyissa meg az Azure Portalt, és válassza ki a helyreállítási szolgáltatás tárolóját.
2.  Kattintson a SharePoint-alkalmazáshoz létrehozott helyreállítási tervre.
3.  Kattintson a "Teszt feladatátvétel".
4.  Válassza ki a helyreállítási pontot és az Azure virtuális hálózatot a teszt feladatátvételi folyamat elindításához.
5.  Ha a másodlagos környezet ben, elvégezheti az ellenőrzéseket.
6.  Miután az ellenőrzések befejeződtek, a "Karbantartás teszt feladatátvétel" elemre kattinthat a helyreállítási terven, és a tesztfeladat-átvételi környezet törlődik.

Az AD és a DNS tesztfeladat-átvételével kapcsolatos útmutatásért tekintse meg az [AD és a DNS-dokumentum feladatátvételi szempontjainak tesztelése](site-recovery-active-directory.md#test-failover-considerations) című dokumentum.

Az SQL Always ON rendelkezésre állási csoportok tesztfeladat-átvételének elvégzéséhez az [Alkalmazás DR végrehajtása az Azure Site Recovery szolgáltatással című és a Teszt feladatátvételi](site-recovery-sql.md#disaster-recovery-of-an-application) dokumentum című útmutató.

## <a name="doing-a-failover"></a>Feladatátvétel
Kövesse [ezt az útmutatást](site-recovery-failover.md) a feladatátvételhez.

1.  Nyissa meg az Azure Portalt, és válassza ki a Recovery Services-tárolót.
2.  Kattintson a SharePoint-alkalmazáshoz létrehozott helyreállítási tervre.
3.  Kattintson a "Feladatátvétel" gombra.
4.  Válassza ki a helyreállítási pontot a feladatátvételi folyamat elindításához.

## <a name="next-steps"></a>További lépések
A Site Recovery segítségével további információval tud [hatni más alkalmazások replikálásáról.](site-recovery-workload.md)
