---
title: Az Azure és az Azure vész-helyreállítási architektúrája az Azure Site Recovery szolgáltatásban
description: Az Azure Site Recovery szolgáltatás használatával az Azure-régiók közötti vész-helyreállítás beállításakor használt architektúra áttekintése.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 94da1639b5398a03b36fba3ff88877468a97ec36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294116"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Az Azure-ból Azure-ba történő vészhelyreállítás architektúrája


Ez a cikk ismerteti az architektúra, összetevők és folyamatok, amikor az Azure [Site Recovery](site-recovery-overview.md) szolgáltatás használatával üzembe helyezi a vész-helyreállítási az Azure virtuális gépek (VM-ek) üzembe helyezésekor használt. Vész-helyreállítási beállítással az Azure-beli virtuális gépek folyamatosan replikálódik egy másik célrégióba. Ha kimaradás történik, a másodlagos régióba való virtuális gépek et átveheti, és onnan elérheti őket. Ha minden normálisan fut újra, visszaléphet, és folytathatja a munkát az elsődleges helyen.



## <a name="architectural-components"></a>Az architektúra összetevői

Az Azure-beli virtuális gépek vész-helyreállítási összetevői az alábbi táblázatban kerülnek összegzésre.

**Összetevő** | **Követelmények**
--- | ---
**Virtuális gépek a forrásrégióban** | Egy további Azure-beli virtuális gépek egy [támogatott forrásrégióban.](azure-to-azure-support-matrix.md#region-support)<br/><br/> A virtuális gépek bármilyen [támogatott operációs rendszert](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)futtathatnak.
**Forrás virtuálisgép-tároló** | Az Azure virtuális gépek kezelhetők, vagy nem felügyelt lemezek a tárfiókok között elosztva.<br/><br/>[További információ a](azure-to-azure-support-matrix.md#replicated-machines---storage) támogatott Azure-tárhelyről.
**Forrás virtuálisgép-hálózatok** | A virtuális gépek egy vagy több alhálózatban, a forrásrégióban lévő virtuális hálózatban (VNet) találhatók. [További információ](azure-to-azure-support-matrix.md#replicated-machines---networking) a hálózati követelményekről.
**Gyorsítótártár-fiók** | Szüksége van egy gyorsítótár-tárfióka a forráshálózatban. A replikáció során a virtuális gép módosításai a gyorsítótárban tárolódnak, mielőtt a céltárolóba kerülne.  A gyorsítótár-tárfiókoknak szabványosnak kell lenniük.<br/><br/> A gyorsítótár használata minimális hatást biztosít a virtuális gépen futó éles alkalmazásokra.<br/><br/> [További információ](azure-to-azure-support-matrix.md#cache-storage) a gyorsítótár-tárolási követelményekről. 
**Célzott erőforrások** | A célerőforrások a replikáció során és feladatátvételkor használatosak. A Site Recovery alapértelmezés szerint beállíthat célerőforrást, vagy létrehozhatja/testreszabhatja őket.<br/><br/> A célrégióban ellenőrizze, hogy képes-e virtuális gépeket létrehozni, és hogy az előfizetés elegendő erőforrással rendelkezik-e a virtuális gép méretének támogatásához, amelyre a célrégióban lesz szükség. 

![Forrás- és célreplikáció](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Célzott erőforrások

Ha engedélyezi a virtuális gép replikációját, a Site Recovery lehetővé teszi a célerőforrások automatikus létrehozását. 

**Célerőforrás** | **Alapértelmezett beállítás**
--- | ---
**Cél-előfizetés** | Ugyanaz, mint a forrás-előfizetés.
**Cél erőforráscsoport** | Az erőforráscsoport, amelyhez a virtuális gépek a feladatátvétel után tartoznak.<br/><br/> A forrásrégió kivételével bármely Azure-régióban lehet.<br/><br/> A Site Recovery új erőforráscsoportot hoz létre a célrégióban, "asr" utótaggal.<br/><br/>
**Cél virtuális hálózat** | A virtuális hálózat (VNet), amelyben replikált virtuális gépek találhatók feladatátvétel után. A forrás- és a célvirtuális hálózatok között hálózati leképezés jön létre, és fordítva.<br/><br/> A Site Recovery új virtuális hálózatot és alhálózatot hoz létre az "asr" utótaggal.
**Céltárfiók** |  Ha a virtuális gép nem használ felügyelt lemezt, ez az a tárfiók, amelyre az adatok replikálódnak.<br/><br/> A Site Recovery új tárfiókot hoz létre a célrégióban, hogy tükrözze a forrástárfiókot.
**Replikáni kezelt lemezek** | Ha a virtuális gép felügyelt lemezt használ, akkor ez az a felügyelt lemez, amelyre az adatok replikálódnak.<br/><br/> A Site Recovery replikáni kezelt lemezeket hoz létre a tárolási régióban a forrás tükrözéséhez.
**Cél elérhetőségi csoportjai** |  Rendelkezésre állási készlet, amelyben replikáló virtuális gépek találhatók a feladatátvétel után.<br/><br/> A Site Recovery létrehoz egy rendelkezésre állási készletet a célrégióban az "asr" utótaggal, a forráshelyen egy rendelkezésre állási készletben található virtuális gépekhez. Ha létezik egy rendelkezésre állási csoport, a használatban van, és egy új nem jön létre.
**Célelérhetőségi zónák** | Ha a célrégió támogatja a rendelkezésre állási zónákat, a Site Recovery ugyanazt a zónaszámot rendeli hozzá, mint a forrásrégióban.

### <a name="managing-target-resources"></a>Célerőforrások kezelése

A célerőforrásokat a következőképpen kezelheti:

- A replikáció engedélyezésekor módosíthatja a célbeállításokat.
- A replikáció már működik működése után módosíthatja a célbeállításokat. A kivétel a rendelkezésre állás típusa (egyetlen példány, készlet vagy zóna). A beállítás módosításához le kell tiltania a replikációt, módosítania kell a beállítást, majd újra engedélyeznie kell.



## <a name="replication-policy"></a>Replikációs szabályzat 

Ha engedélyezi az Azure VM replikációját, alapértelmezés szerint a Site Recovery új replikációs házirendet hoz létre a táblázatban összegzett alapértelmezett beállításokkal.

**Házirend beállítása** | **Részletek** | **Alapértelmezett**
--- | --- | ---
**Helyreállítási pont megőrzése** | Megadja, hogy a Site Recovery mennyi ideig tartsa meg a helyreállítási pontokat | 24 óra
**Alkalmazáskonzisztens pillanatfelvétel gyakorisága** | Milyen gyakran site recovery vesz egy alkalmazás-konzisztens pillanatképet. | Négyóránként

### <a name="managing-replication-policies"></a>Replikációs házirendek kezelése

Az alapértelmezett replikációs házirendek beállításait a következőképpen kezelheti és módosíthatja:
- A replikáció engedélyezésekor módosíthatja a beállításokat.
- A replikációs házirendet bármikor létrehozhatja, majd alkalmazhatja, amikor engedélyezi a replikációt.

### <a name="multi-vm-consistency"></a>Több vm-konzisztencia

Ha azt szeretné, hogy a virtuális gépek együtt replikáljanak, és megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokat a feladatátvétel, összegyűjtheti őket egy replikációs csoportba. A több virtuális gép konzisztenciája hatással van a számítási feladatok teljesítményére, és csak olyan számítási feladatokat futtató virtuális gépekhez használható, amelyek az összes gépen konzisztenciát igényelnek. 



## <a name="snapshots-and-recovery-points"></a>Pillanatképek és helyreállítási pontok

Helyreállítási pontok jönnek létre a virtuális gép lemezek pillanatképei egy adott időpontban készített pillanatképek. Ha feladatátvételt egy virtuális gép, egy helyreállítási pontot használ a virtuális gép visszaállításához a célhelyen.

A rendszer- és a felértékelések során általában biztosítani szeretnénk, hogy a virtuális gép sérülés vagy adatvesztés nélkül kezdődjön, és hogy a virtuális gép adatai konzisztensek legyenek az operációs rendszer és a virtuális számítógépen futó alkalmazások számára. Ez a készített pillanatképek típusától függ.

A Site Recovery a következőképpen készít pillanatképeket:

1. A Site Recovery alapértelmezés szerint összeomlás-konzisztens pillanatképeket készít az adatokról, és alkalmazáskonzisztens pillanatképeket, ha megad számukra gyakoriságot.
2. A helyreállítási pontok a pillanatképekből jönnek létre, és a replikációs házirend ben megadott megőrzési beállításoknak megfelelően tárolódnak.

### <a name="consistency"></a>Konzisztencia

Az alábbi táblázat a konzisztencia különböző típusait ismerteti.

### <a name="crash-consistent"></a>Összeomlás-konzisztens

**Leírás** | **Részletek** | **Ajánlás**
--- | --- | ---
Az összeomlás konzisztens pillanatkép rögzíti az adatokat, amelyek a lemezen, amikor a pillanatkép készült. Nem tartalmaz semmit a memóriában.<br/><br/> A lemezen lévő adatok megfelelőjét tartalmazza, amelyek akkor lennének jelen, ha a virtuális gép összeomlik, vagy a tápkábelt a pillanatkép készítése pillanatában lekérik a kiszolgálóról.<br/><br/> Az összeomlás-konzisztens nem garantálja az adatok konzisztenciáját az operációs rendszer, vagy a virtuális gép alkalmazások. | A Site Recovery alapértelmezés szerint ötpercenként hozza létre az összeomlás-konzisztens helyreállítási pontokat. Ez a beállítás nem módosítható.<br/><br/>  | Ma a legtöbb alkalmazás jól helyretudja állítani az összeomlás-konzisztens pontokat.<br/><br/> Az összeomlás-konzisztens helyreállítási pontok általában elegendőek az operációs rendszerek, valamint az olyan alkalmazások, mint a DHCP-kiszolgálók és a nyomtatókiszolgálók replikációjához.

### <a name="app-consistent"></a>Alkalmazáskonzisztens

**Leírás** | **Részletek** | **Ajánlás**
--- | --- | ---
Az alkalmazáskonzisztens helyreállítási pontok alkalmazáskonzisztens pillanatképekből jönnek létre.<br/><br/> Az alkalmazáskonzisztens pillanatkép tartalmazza az összes információt egy összeomlás-konzisztens pillanatkép, valamint az összes adatot a memóriában és a folyamatban lévő tranzakciók. | Az alkalmazáskonzisztens pillanatképek a Kötet árnyékmásolata szolgáltatást (VSS) használják:<br/><br/>   1) Pillanatkép indításakor a VSS írásra másolási (COW) műveletet hajt végre a köteten.<br/><br/>   2) Mielőtt végrehajtaná a COW-t, a VSS tájékoztatja a gépen lévő összes alkalmazást, hogy lemezre kell ürítenie a memóriarezidens adatait.<br/><br/>   3) A VSS ezután lehetővé teszi, hogy a biztonsági mentési/vész-helyreállítási alkalmazás (ebben az esetben a Site Recovery) olvassa el a pillanatkép adatait, és folytassa. | Az alkalmazáskonzisztens pillanatképek a megadott gyakoriságnak megfelelően készülnek. Ennek a gyakoriságnak mindig kisebbnek kell lennie, mint a helyreállítási pontok megtartása. Ha például a helyreállítási pontokat az alapértelmezett 24 órás beállítással tartja meg, a gyakoriságot 24 óránál rövidebbre kell beállítania.<br/><br/>Összetettebbek, és hosszabb időt vesz igénybe, mint az összeomlás-konzisztens pillanatképek.<br/><br/> Ezek befolyásolják a replikációra engedélyezett virtuális számítógépen futó alkalmazások teljesítményét. 

## <a name="replication-process"></a>Replikációs folyamat

Ha engedélyezi a replikációt egy Azure-beli virtuális géphez, a következő történik:

1. A Site Recovery Mobility szolgáltatás bővítmény automatikusan telepítve van a virtuális gépre.
2. The extension registers the VM with Site Recovery.
3. A virtuális gép folyamatos replikációja megkezdődik.  A lemezírások azonnal átkerülnek a forráshelyen lévő gyorsítótártárfiókba.
4. A Site Recovery feldolgozza a gyorsítótárban lévő adatokat, és elküldi azokat a céltárfióknak vagy a replikán lévő felügyelt lemezeknek.
5. Az adatok feldolgozása után összeomlás-konzisztens helyreállítási pontok jönnek létre öt percenként. Az alkalmazáskonzisztens helyreállítási pontok a replikációs házirendben megadott beállításnak megfelelően jönnek létre.

![Replikációs folyamat engedélyezése, 2.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Replikációs folyamat**

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények

 Az Azure virtuális gépek replikálása kimenő kapcsolatszükséges. A Site Recovery-nek soha nem kell bejövő kapcsolatot a virtuális géphez. 

### <a name="outbound-connectivity-urls"></a>Kimenő kapcsolat (URL-címek)

Ha a virtuális gépek kimenő hozzáférése URL-címekkel van vezérelve, engedélyezze ezeket az URL-címeket.

| **Url** | **Részletek** |
| ------- | ----------- |
| *.blob.core.windows.net | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| login.microsoftonline.com | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| *.hypervrecoverymanager.windowsazure.com | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| *.servicebus.windows.net | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |
| *.vault.azure.net | Lehetővé teszi az ADE-kompatibilis virtuális gépek replikációjának engedélyezését a portálon keresztül
| *.automation.ext.azure.com | Lehetővé teszi a mobilügynök automatikus frissítésének engedélyezését egy replikált elemhez a portálon keresztül

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ip-címeket használó virtuális gépek kimenő kapcsolatának szabályozásához engedélyezze ezeket a címeket.
Kérjük, vegye figyelembe, hogy a hálózati kapcsolati követelmények részletei a [hálózati tanulmányban találhatók.](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Forrásrégió szabályai

**Szabály** |  **Részletek** | **Szolgáltatáscímke**
--- | --- | --- 
HTTPS kimenő engedélyezése: 443-as port | A forrásrégióban lévő tárfiókoknak megfelelő tartományok engedélyezése | Tároló. \<régiónév>
HTTPS kimenő engedélyezése: 443-as port | Az Azure Active Directorynak (Azure AD) megfelelő tartományok engedélyezése  | AzureActiveDirectory
HTTPS kimenő engedélyezése: 443-as port | A célrégióban az Eseményközpontnak megfelelő tartományok engedélyezése. | EventsHub. \<régiónév>
HTTPS kimenő engedélyezése: 443-as port | Az Azure Site Recovery szolgáltatásnak megfelelő tartományok engedélyezése  | AzureSiteRecovery
HTTPS kimenő engedélyezése: 443-as port | Az Azure Key Vaultnak megfelelő tartományok engedélyezése (ez csak az ADE-kompatibilis virtuális gépek portálon keresztüli replikációjának engedélyezéséhez szükséges) | AzureKeyVault
HTTPS kimenő engedélyezése: 443-as port | Az Azure Automation Controllernek megfelelő tartományok engedélyezése (Ez csak a mobilügynök automatikus frissítésének engedélyezéséhez szükséges egy replikált elem hez a portálon keresztül) | GuestandHybridManagement

#### <a name="target-region-rules"></a>Célrégióra vonatkozó szabályok

**Szabály** |  **Részletek** | **Szolgáltatáscímke**
--- | --- | --- 
HTTPS kimenő engedélyezése: 443-as port | A célrégióban lévő tárfiókoknak megfelelő tartományok engedélyezése | Tároló. \<régiónév>
HTTPS kimenő engedélyezése: 443-as port | Az Azure AD-nek megfelelő tartományok engedélyezése  | AzureActiveDirectory
HTTPS kimenő engedélyezése: 443-as port | A forrásrégióban az Eseményközpontnak megfelelő tartományok engedélyezése. | EventsHub. \<régiónév>
HTTPS kimenő engedélyezése: 443-as port | Az Azure Site Recovery szolgáltatásnak megfelelő tartományok engedélyezése  | AzureSiteRecovery
HTTPS kimenő engedélyezése: 443-as port | Az Azure Key Vaultnak megfelelő tartományok engedélyezése (ez csak az ADE-kompatibilis virtuális gépek portálon keresztüli replikációjának engedélyezéséhez szükséges) | AzureKeyVault
HTTPS kimenő engedélyezése: 443-as port | Az Azure Automation Controllernek megfelelő tartományok engedélyezése (Ez csak a mobilügynök automatikus frissítésének engedélyezéséhez szükséges egy replikált elem hez a portálon keresztül) | GuestandHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Hozzáférés szabályozása Az NSG-szabályokkal

Ha a virtuális gép kapcsolatát az Azure-hálózatokba/alhálózatokból az [NSG-szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview)használatával érkező és onnan érkező hálózati forgalom szűrésével vezérli, vegye figyelembe az alábbi követelményeket:

- NSG-szabályok a forrás Azure-régióban lehetővé kell tennie a kimenő hozzáférést a replikációs forgalom.
- Azt javasoljuk, hogy hozzon létre szabályokat egy tesztkörnyezetben, mielőtt azokat éles környezetben.
- Az egyes IP-címek engedélyezése helyett [használjon szolgáltatáscímkéket.](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)
    - A szolgáltatáscímkék az IP-címelőtagok egy csoportját jelölik össze, hogy a biztonsági szabályok létrehozásakor a lehető legkisebb legyen az összetettség.
    - A Microsoft idővel automatikusan frissíti a szolgáltatáscímkéket. 
 
További információ a site recovery [kimenő kapcsolatáról](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) és [az NSG-kkel való kapcsolat szabályozásáról.](concepts-network-security-group-with-site-recovery.md)


### <a name="connectivity-for-multi-vm-consistency"></a>Csatlakoztathatóság a több vm-konzisztenciához

Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással.
- Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt.
- Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.




## <a name="failover-process"></a>Feladatátvételi folyamat

Feladatátvétel indításakor a virtuális gépek a célerőforrás-csoportban, a célvirtuális hálózaton, a célalhálózatban és a célrendelkezésre állási csoportban jönnek létre. Feladatátvétel során bármilyen helyreállítási pontot használhat.

![Feladatátvételi folyamat](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>További lépések

[Gyorsan replikálja](azure-to-azure-quickstart.md) az Azure virtuális gép egy másodlagos régióban.
