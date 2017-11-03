---
title: "Egy Azure Site Recovery segítségével többrétegű SharePoint alkalmazás replikálása |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan replikáljon egy többrétegű SharePoint-alkalmazást, az Azure Site Recovery képességeit."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: sutalasi
ms.openlocfilehash: 55323df68715c80d5e8535199cd739921a3baad9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Azure Site Recovery segítségével vész-helyreállítási egy többrétegű SharePoint alkalmazás replikálása

Ez a cikk részletesen ismerteti, hogyan védi meg a SharePoint használó [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Áttekintés

A Microsoft SharePoint egy hatékony alkalmazás, amelynek segítségével egy csoport vagy részleg rendezéséhez, együttműködés és az információkat. SharePoint intranetes portálok, dokumentum és fájlkezelés, együttműködés, közösségi hálózatokkal, extranetek, webhelyek, vállalati keresési és üzleti intelligencia biztosít. Azt is, rendszerintegráció integrálása és munkafolyamat automatizálási lehetőségek körét. Általában a szervezetek esetekben 1. szintű alkalmazásként bizalmas állásidő és az adatok veszhetnek el.

A Microsoft SharePoint jelenleg nem biztosít bármely out-of-az-box vész-helyreállítási funkciók. A típust és egy olyan vészhelyzet esetén a méretezési, függetlenül helyreállítási magában foglalja a farm helyreállítható készenléti adatközpont használatát. Készenléti adatközpontok forgatókönyvek, ahol helyi redundáns rendszerek és a biztonsági mentések nem állíthat helyre, az elsődleges adatközpont kimaradás szükségesek.

Egy jó vész-helyreállítási megoldást körül az összetett alkalmazásarchitektúráknak, például a SharePoint helyreállítási tervek modellezési engedélyezni kell. Ez lehet alkalmazástársítások között különböző szinteket, és így biztosítható a egy kattintással indítható feladatátvételt egy alacsonyabb RTO katasztrófa esetén kezelése testreszabott lépések hozzáadása lehetőséget.

Ez a cikk részletesen ismerteti, hogyan védi meg a SharePoint használó [Azure Site Recovery](site-recovery-overview.md). Ez a cikk gyakorlati tanácsok a végez replikációt egy három réteg SharePoint-alkalmazások Azure, a vész-helyreállítási részletezési honnan és hogyan zajlik a feladatátvétel az Azure alkalmazás.

Figyelemmel követheti az alábbi videó egy Azure multi-rétegbeli alkalmazás helyreállításával kapcsolatban.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Előfeltételek

Megkezdése előtt győződjön meg arról, hogy a következő:

1. [A virtuális gépek replikálása Azure-bA](site-recovery-vmware-to-azure.md)
2. Hogyan [egy helyreállítási hálózathoz. terv](site-recovery-network-design.md)
3. [A teszt feladatátvétel az Azure-bA](site-recovery-test-failover-to-azure.md)
4. [A feladatátvétel az Azure-bA](site-recovery-failover.md)
5. Hogyan [tartományvezérlő replikálása](site-recovery-active-directory.md)
6. Hogyan [SQL-kiszolgáló replikálása](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-architektúra

SharePoint rétegzett topológiákat és a kiszolgálói szerepkörök használatával, amely megfelel a konkrét célokat és célok farm-kialakításának megvalósítása egy vagy több kiszolgálón is telepíthető. Egy tipikus nagy, nagy igény SharePoint-kiszolgálófarm nagyszámú egyidejű felhasználót és nagyszámú tartalomelem támogató szolgáltatás csoportosítás használata a méretezhetőség stratégia részeként. Ez a megközelítés szolgáltatásokat futtató található dedikált kiszolgálókra, ezek a szolgáltatások csoportosítása, és majd kiterjesztése a kiszolgálókra csoportként. A következő topológiát mutatja be, a szolgáltatás és a kiszolgáló SharePoint-kiszolgálófarm három rétegekhez csoportosítása. További információ a SharePoint dokumentációjának és a termék sor architektúrák különböző SharePoint topológiák részletes útmutatást. A SharePoint 2013 fejlesztésével kapcsolatos további részletei [Ez a dokumentum](https://technet.microsoft.com/en-us/library/cc303422.aspx).



![Telepítési minta 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás

Ez a cikk létrehozásához, a VMware virtuális gépek, a Windows Server 2012 R2 Enterprise használták. A SharePoint 2013 Enterprise edition és az SQL server 2014 Enterprise edition használták. A Site Recovery replikációs alkalmazás független, a ajánlás itt várt tartsa lenyomva az alábbi forgatókönyvek esetén is.

### <a name="source-and-target"></a>Forrása és célja

**A forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-bA**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen

### <a name="sharepoint-versions"></a>SharePoint-verziók
A SharePoint server következő verziói támogatottak.

* A SharePoint server 2013 Standard
* SharePoint server 2013 Enterprise
* A SharePoint server 2016 Standard
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Vegye figyelembe a következőkre

Ha használ egy megosztott lemez alapú fürt bármely réteg az alkalmazás majd csak akkor használhatják a Site Recovery replikációs azon virtuális gépek replikálásához. Az alkalmazás által biztosított natív replikációt használ, és ezután egy [helyreállítási terv](site-recovery-create-recovery-plans.md) összes tiers feladatátvételre.

## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálása

Hajtsa végre a [Ez az útmutató](site-recovery-vmware-to-azure.md) elindítani a virtuális gép replikálása Azure-bA.

* Ha a replikáció befejeződött, győződjön meg arról, nyissa meg az egyes rétegek minden virtuális gép, és válassza ki az azonos rendelkezésre állási csoport "replikált elemek > Beállítások > Tulajdonságok > Számítás és hálózat". Például ha a webes réteg 3 virtuális gép, ellenőrizze a 3 virtuális gép beállítása az Azure-ban ugyanabban a rendelkezésre állási részeként.

    ![Set-rendelkezésre állási-csoport](./media/site-recovery-sharepoint/select-av-set.png)

* Active Directory és a DNS védelméről útmutatásért tekintse meg [Active Directory védelmére és a DNS-](site-recovery-active-directory.md) dokumentum.

* Működő SQL server adatbázis-rétegből ellátásáról útmutatásért tekintse meg [SQL Server védelme](site-recovery-active-directory.md) dokumentum.

## <a name="networking-configuration"></a>Hálózati konfigurációja

### <a name="network-properties"></a>Hálózat tulajdonságai

* Az alkalmazás és a webes réteg virtuális gépek hálózati beállítások konfigurálása a Azure-portálon, hogy a virtuális gépek a feladatátvételt követően a megfelelő vész-Helyreállítási hálózathoz beolvasása csatlakoztatva.

    ![Válassza ki a hálózati](./media/site-recovery-sharepoint/select-network.png)


* Ha statikus IP-címet használ, majd adja meg az IP-cím azt szeretné, hogy a virtuális gépet a hálózatról a **cél IP-címet** mező

    ![Statikus IP-cím beállítása](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS- és a forgalom-Útválasztás

Az internetre irányuló helyek ["Priority" típusú Traffic Manager-profil létrehozása](../traffic-manager/traffic-manager-create-profile.md) az Azure-előfizetésben. Majd konfigurálja a DNS és a Traffic Manager-profilt a következő módon.


| **Ha** | **Forrás** | **Cél**|
| --- | --- | --- |
| Nyilvános DNS-ben | Nyilvános DNS-ben, a SharePoint-webhelyekhez <br/><br/> Például: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.NET |
| A helyi DNS | sharepointonprem.contoso.com | A helyszíni farm nyilvános IP-cím |


A Traffic Manager-profilt [az elsődleges és a helyreállítási végpontokat hoz létre](../traffic-manager/traffic-manager-configure-priority-routing-method.md). A külső végpont a helyszíni végpont és az Azure-végpont nyilvános IP-címet használja. Győződjön meg arról, hogy a prioritás értéke magasabb a helyszíni végpont számára.

Egy adott portot (például 800) tesztoldalt működteti a SharePoint webes réteg ahhoz a Traffic Manager felismeri a rendelkezésre állási post feladatátvétele. Ez a megoldás, abban az esetben nem engedélyezhető a névtelen hitelesítés bármely, a SharePoint-webhelyek.

[A Traffic Manager-profil konfigurálása](../traffic-manager/traffic-manager-configure-priority-routing-method.md) az az alábbi beállításokat.

* Útválasztási módszer - "Priority"
* Élettartam (TTL) - "30 másodperc' DNS-ideje
* Végpont-figyelő beállításai – Ha engedélyezi a névtelen hitelesítés adhat egy adott webhelyre végpont. Vagy egy adott portot (például 800) tesztoldalt is használhatja.

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv lehetővé teszi, hogy a különböző rétegek egy többrétegű alkalmazást, ezért az alkalmazás konzisztencia fenntartása a feladatátvételi sorrendje. Kövesse a következő lépések a helyreállítási terv többrétegű webalkalmazás létrehozása során. [További információ a helyreállítási terv létrehozása](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok hozzáadása

1. A helyreállítási terv létrehozásához adja meg az alkalmazás és a webes réteg virtuális gépeket.
2. Kattintson a "Testreszabás" a virtuális gépek csoportosításához. Alapértelmezés szerint az összes virtuális gép "Csoport 1" részét képezik.

    ![Függő Entitás testreszabása](./media/site-recovery-sharepoint/rp-groups.png)

3. Hozzon létre egy másik csoportot (csoport % 2), és helyezze át a webes réteg virtuális gépeket az új csoportba. Az alkalmazás réteg virtuális gépek csoport "% 1" részének kell lennie, és webes réteg virtuális gépek csoport "% 2" részének kell lennie. Ez az annak érdekében, hogy be az alkalmazás réteg virtuális gépek rendszerindító webes réteg virtuális gépeket először követ.


### <a name="adding-scripts-to-the-recovery-plan"></a>A helyreállítási terv parancsprogramokat hozzáadása

A leggyakrabban használt Azure Site Recovery-parancsfájlok telepítheti be az alábbi "Az Azure telepítéséhez" gombra kattintva Automation-fiók. Ha bármely közzétett parancsfájl használ, győződjön meg arról, akkor kövesse az útmutató a parancsfájlt.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Előtti parancsfájlművelet hozzáadása csoporthoz "% 1" SQL rendelkezésre állási csoport feladatai. A parancsfájllal "ASR-SQL-FailoverAG" a minta parancsfájlokat tesznek közzé. Győződjön meg arról, kövesse az útmutató a parancsfájlt, és végrehajtják a szükséges módosításokat a parancsfájl megfelelően.

    ![Adja hozzá-AG-parancsfájl--1. lépés](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Adja hozzá-AG-parancsfájl-– 2. lépés](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Post művelet parancsfájl csatolni a feladatait egy terheléselosztó hozzáadása a virtuális gépeket webes réteg (csoport 2). A parancsfájllal "ASR-AddSingleLoadBalancer" a minta parancsfájlokat tesznek közzé. Győződjön meg arról, kövesse az útmutató a parancsfájlt, és végrehajtják a szükséges módosításokat a parancsfájl megfelelően.

    ![Adja hozzá-LB-parancsfájl--1. lépés](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Adja hozzá-LB-parancsfájl-– 2. lépés](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adjon hozzá egy manuális lépés, hogy a farmban az Azure-ban mutasson a DNS-rekordok frissítéséhez.

    * Az internetes webhelyek nincs DNS-frissítések szükséges post feladatátvételi. A Traffic Manager konfigurálása "Hálózat útmutatást" szakaszban ismertetett lépéseket követve. Ha be van állítva a Traffic Manager-profilt, az előző szakaszban leírtak szerint, az Azure virtuális gépen (a példában szereplő 800) típusú port megnyitására parancsfájl hozzáadása.

    * Belső webhelyet vegye fel az új webes réteg VM load balancer IP mutasson a DNS-rekord frissítéséhez egy manuális lépés.

4. Adjon hozzá egy manuális lépés keresőalkalmazás visszaállítása biztonsági másolatból, vagy egy új keresési szolgáltatás elindításához.

5. Keresési szolgáltatásalkalmazás visszaállítása egy biztonsági másolatból, hajtsa végre az alábbi lépéseket.

    * Ez a módszer feltételezi, hogy a keresési szolgáltatásalkalmazás egy biztonsági másolat a katasztrofális esemény előtt, és hogy a biztonsági mentés érhető el a vész-Helyreállítási helyen.
    * Ez könnyen elérhető a biztonsági mentés ütemezése (például naponta egyszer), és egy másolás eljárással helyezhető el a biztonsági mentés a vész-Helyreállítási helyen. Másolás eljárások tartalmazhatnak parancsfájlalapú programok például AzCopy (Azure másolás) vagy az elosztott fájlrendszer replikációs szolgáltatása (elosztott szolgáltatások fájlreplikáció) beállítása.
    * Most, hogy a SharePoint-farm fut, nyissa meg a központi adminisztrációs, biztonsági mentése és visszaállítása, és válassza ki a visszaállítani. A visszaállítási interrogates megadott biztonsági mentési helyre (esetleg frissítse az értéket). Válassza ki a visszaállítani kívánt keresési szolgáltatásalkalmazás biztonsági mentés.
    * Keresési helyreáll. Ne feledje, hogy a visszaállítás vár található azonos topológia (azonos számú kiszolgálók) és azonos rögzített meghajtóbetűjelei rendelt ezeken a kiszolgálókon. További információkért lásd: ["Visszaállítása keresési szolgáltatásalkalmazás a SharePoint 2013"](https://technet.microsoft.com/library/ee748654.aspx) dokumentum.


6. Egy új szolgáltatás keresőalkalmazás kezdve, hajtsa végre az alábbi lépéseket.

    * Ez a módszer azt feltételezi, hogy a "Keresés felügyelete" adatbázis biztonsági másolatának érhető el a vész-Helyreállítási helyen.
    * A keresési szolgáltatásalkalmazás adatbázisok nem replikálja, mivel azok kell lenniük, hozza létre újra. Ehhez navigáljon a központi adminisztrációs, és a keresési szolgáltatásalkalmazás törlése. Bármely futtató kiszolgálók szerinti a Search-Index törölje az indexfájlok.
    * Hozza létre újból a keresési szolgáltatásalkalmazás, és újra létrehozza az adatbázisokat. Javasoljuk, hogy rendelkezik egy előkészített parancsfájlt, amely ehhez a szolgáltatásalkalmazáshoz újra létrehozza, mivel nincs lehetőség a grafikus felhasználói felületen keresztül minden művelet végrehajtására. Például az index meghajtó helyének és a keresési topológia beállításával csak akkor lehetséges SharePoint PowerShell-parancsmagok használatával. A Windows PowerShell-parancsmag visszaállítási-SPEnterpriseSearchServiceApplication, és adja meg a naplóküldés és replikált Keresés felügyelete adatbázist, Search_Service__DB. Ez a parancsmag a keresési konfigurációt, a séma, a felügyelt tulajdonságok, a szabályok és a források biztosít, és létrehoz egy alapértelmezett készletét a többi összetevő.
    * Keresési rendelkezik kell hozza létre újra, miután minden tartalomforrásának visszaállítása a keresési szolgáltatás teljes bejárást kell elindítani. A helyszíni kiszolgálófarmon, például a keresési javaslatok tárolt analytics adatok elvesznek.

7. Amennyiben az összes lépésének elvégzése, mentse a helyreállítási tervet, és az utolsó helyreállítási terv a következőképpen néznek.

    ![Mentett RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>A teszt feladatátvétel
Hajtsa végre a [Ez az útmutató](site-recovery-test-failover-to-azure.md) feladatátvételi teszt végrehajtásához.

1.  Nyissa meg az Azure-portálhoz, és válassza a helyreállítási szolgáltatás tárolójából.
2.  Kattintson a helyreállítási terv SharePoint-alkalmazás létrehozása.
3.  Kattintson a "Test Failover".
4.  Válassza ki a helyreállítási pont és a teszt feladatátvételi megkezdéséhez Azure virtuális hálózat.
5.  A másodlagos környezetben működik, ha az érvényesítést végezheti el.
6.  Ha az ellenőrzés befejeződött, kattintson a helyreállítási terv "Karbantartása a feladatátvételi teszt", és a teszt feladatátvételi környezet karbantartása.

A teszt feladatátvétel ad útmutatást, majd tekintse át a DNS, [feladatátvételi szempontokat részletező cikket az Active Directory és a DNS-](site-recovery-active-directory.md#test-failover-considerations) dokumentum.

A feladatátvétel tesztelése az SQL mindig a rendelkezésre állási csoportok útmutatásért tekintse meg [feladatátvételi teszt során az SQL Server Always On](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentum.

## <a name="doing-a-failover"></a>A feladatátvétel
Hajtsa végre a [Ez az útmutató](site-recovery-failover.md) a a feladatátvétel.

1.  Nyissa meg az Azure-portálhoz, és válassza a Recovery Services-tároló.
2.  Kattintson a helyreállítási terv SharePoint-alkalmazás létrehozása.
3.  Kattintson a "Failover".
4.  Válassza ki a helyreállítási pontot a feladatátvételi folyamat elindításához.

## <a name="next-steps"></a>Következő lépések
További tudnivalók [replikál a többi alkalmazás](site-recovery-workload.md) Site Recovery segítségével.
