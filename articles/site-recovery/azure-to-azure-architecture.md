---
title: Azure-bA replikációs architektúra az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk az Azure virtuális gépek, az Azure Site Recovery szolgáltatással az Azure-régiók között vész-helyreállítási beállításakor használt összetevőkről és architektúráról áttekintést nyújt.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 96873b5fdefc74893929f8150230118a162f195b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540720"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure-bA vész-helyreállítási architektúra


Ez a cikk ismerteti az architektúra, összetevők és vészhelyreállítás Azure virtuális gépek (VM-EK) használatával telepítéséhez használt folyamatok a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás. Állítsa be a vészhelyreállítás, az Azure virtuális gépek folyamatosan-bA replikálni egy másik célrégióban. Egy kimaradás során, virtuális gépek feladatainak átadása a másodlagos régióba, és ahonnan érheti el őket. Ha minden megfelelően fut újra, feladat-visszavételt, és továbbra is működik az elsődleges helyen.



## <a name="architectural-components"></a>Az architektúra összetevői

A következő összetevők kapnak szerepet Azure virtuális gépek vészhelyreállítása az alábbi táblázat foglalja össze.

**Összetevő** | **Követelmények**
--- | ---
**Virtuális gépek a forrásrégióban** | A több Azure virtuális gépek egyike egy [forrásrégió támogatott](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuális gépek futtathat bármely [támogatott operációs rendszert](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Forrás Virtuálisgép-tároló** | Azure virtuális gépek kezelhetők, vagy nem felügyelt lemezek terjednek rendelkezik tárfiókok között.<br/><br/>[Ismerje meg](azure-to-azure-support-matrix.md#replicated-machines---storage) támogatott az Azure storage.
**Forrás Virtuálisgép-hálózatok** | Virtuális gépek egy vagy több alhálózatot a virtuális hálózat (VNet) a forrásrégióban található. [További](azure-to-azure-support-matrix.md#replicated-machines---networking) érvényes hálózati követelmények.
**A gyorsítótár tárfiókja** | A gyorsítótárfiók a forrás-hálózatban van szüksége. A replikáció során virtuális gépek változásainak gyorsítótárában vannak tárolva a céloldali tárolóra való elküldése előtt.<br/><br/> Gyorsítótár használata biztosítja, hogy éles üzemi alkalmazások pedig a virtuális gép futó csak minimális hatással van.<br/><br/> [További](azure-to-azure-support-matrix.md#cache-storage) gyorsítótár tárolási követelményekkel kapcsolatos. 
**Célerőforrások** | Célerőforrások replikáció során, és a egy feladatátvétel esetén használt. Alapértelmezés szerint a Site Recovery állíthat be célerőforrás, vagy hozzon létre és testre szabhatja őket.<br/><br/> A célrégióban ellenőrizze a tudja létrehozni a virtuális gépeket, és hogy előfizetése rendelkezik-e elegendő erőforrás a célrégióban szükséges Virtuálisgép-méretek támogatják. 

![Forrás és cél replikációs](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Célerőforrások

Amikor engedélyezi egy virtuális gép replikációját, a Site Recovery segít célerőforrások automatikus létrehozására. 

**Célerőforrás** | **Alapértelmezett beállítás**
--- | ---
**Célként megadott előfizetés** | Ugyanaz, mint a forrás-előfizetés.
**Céloldali erőforráscsoport** | Az erőforráscsoport virtuális gépek tartozik, amelyhez a feladatátvételt követően.<br/><br/> A forrásrégió kivételével bármelyik Azure-régióban is lehet.<br/><br/> A Site Recovery létrehoz egy új erőforráscsoportot a célrégióban, egy "asr" utótaggal rendelkező.<br/><br/>
**Cél: virtuális hálózat** | A virtuális hálózat (VNet), amelyben a replikált virtuális gépek találhatók a feladatátvételt követően. A hálózatleképezés létrejön a forrás- és virtuális hálózatok között, és fordítva.<br/><br/> A Site Recovery létrehoz egy új virtuális hálózatot és alhálózatot, az "asr" utótaggal.
**Cél tárfiók** |  Ha a virtuális gép nem használ egy felügyelt lemezt, akkor a storage-fiókot, amelyhez adatokat a rendszer replikálja.<br/><br/> Site Recovery egy új tárfiókot hoz létre a célrégióban a forrás tárfiókban tükrözéséhez.
**Replikált felügyelt lemezeken** | Ha a virtuális gép egy felügyelt lemezt használ, akkor a felügyelt lemezek, amelyhez adatokat a rendszer replikálja.<br/><br/> Site Recovery replika felügyelt lemezeket hoz létre a tárolási terület a forrás tükrözéséhez.
**Cél rendelkezésre állási csoportok** |  Rendelkezésre állási csoport, amelyhez replikáló virtuális gépek a feladatátvételt követően találhatók.<br/><br/> A Site Recovery létrehoz egy rendelkezésre állási csoportot a célrégióban az "asr", utótaggal rendelkező virtuális gépek rendelkezésre állási csoport a forráshelyen található. Ha egy rendelkezésre állási csoport már létezik, szolgál, és a egy új nem jön létre.
**Cél rendelkezésre állási zónák** | Ha a célrégióban a rendelkezésre állási zónák használatát támogatja, a Site Recovery zóna száma azonos a forrásrégióban használt rendeli hozzá.

### <a name="managing-target-resources"></a>Tároló-erőforrások kezelése

Céloldali erőforrások is kezelhetők a következőképpen:

- Módosíthatja a célbeállítások módon engedélyezi a replikációt.
- Módosíthatja a célbeállítások után már a replikálás működik. A kivétel a rendelkezésre állási típusa (Egypéldányos, készlet vagy zóna). Ez a beállítás módosításához kell tiltsa le a replikációt, és módosítsa a beállítást, majd újraengedélyezni.



## <a name="replication-policy"></a>Replikációs szabályzat 

Ha engedélyezi az Azure virtuális gép replikációja, alapértelmezés szerint a Site Recovery létrehoz egy új replikációs házirendet az alapértelmezett beállításokkal, hogy a táblázat foglalja össze.

**Szabályzat-beállítás** | **Részletek** | **Alapértelmezett**
--- | --- | ---
**Helyreállítási pont megőrzése** | Itt adhatja meg, mennyi ideig tartja a Site Recovery a helyreállítási pontok | 24 óra
**Alkalmazáskonzisztens pillanatkép gyakorisága** | Milyen gyakran a Site Recovery egy alkalmazáskonzisztens pillanatképet vesz igénybe. | 60 percenként.

### <a name="managing-replication-policies"></a>Replikációs házirendek kezelése

Kezelheti és módosíthatja az alapértelmezett replikációs házirendek beállításait az alábbiak szerint:
- Módosíthatja a beállításokat, a replikáció engedélyezése.
- Bármikor létrehozhat egy replikációs házirendet, és majd azt alkalmazhatja, ha engedélyezi a replikációt.

### <a name="multi-vm-consistency"></a>Több virtuális gépre kiterjedő konzisztencia

Ha azt szeretné, virtuális gépek replikálása együtt, és megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontok a feladatátvétel során, ezeket együtt gyűjthet be egy replikációs csoportot. Több virtuális gépre kiterjedő konzisztencia hatással van a számítási feladatok teljesítményére, és csak a futó számítási feladatok, amelyek minden gép közötti konzisztenciára van szükség virtuális gépek esetében használható. 



## <a name="snapshots-and-recovery-points"></a>A pillanatképek és a helyreállítási pontok

Helyreállítási pontok pillanatképeiből venni egy adott időpontban időben Virtuálisgép-lemezek jönnek létre. Amikor feladatátvételt hajt végre egy virtuális Gépet, a helyreállítási pont használatával állítsa helyre a virtuális Gépet a célhelyen.

Amikor feladatátvétele, általában szeretnénk győződjön meg arról, hogy a virtuális gép elindul-e sérülés vagy adatvesztés nélkül, és hogy a virtuális gép adatai az operációs rendszerhez, és a virtuális gépen futtatott alkalmazások konzisztens. Ez a készített pillanatkép típusától függ.

A Site Recovery következő pillanatképeket készít:

1. A Site Recovery összeomlás-konzisztens pillanatképekkel az adatok alapértelmezés szerint, és alkalmazáskonzisztens pillanatképek akkor kerül, ha a gyakoriságot adja meg a számukra.
2. Helyreállítási pontok a pillanatképekből létrehozott, és tárolja a replikációs házirendben megőrzési beállításoknak.

### <a name="consistency"></a>Konzisztencia

A következő táblázat ismerteti a különböző típusú konzisztencia.

### <a name="crash-consistent"></a>Összeomlás-konzisztens

**Leírás** | **Részletek** | **Az ajánlás**
--- | --- | ---
Összeomlási alkalmazáskonzisztens pillanatkép készítésének volt a lemezen, ha a pillanatkép adatokat rögzíti. Ez nem foglalhatnak magukban a memóriában.<br/><br/> A lemezen lévő adatokat, amely jelen lehet, ha a virtuális gép leállt, vagy a tápkábel felhasználásával lett létrehozva a kiszolgálóról, a pillanatkép azonnali egyenértékű tartalmazza.<br/><br/> Egy összeomlás-konzisztens nem garantálja az adatkonzisztencia az operációs rendszerhez, vagy a virtuális gép található alkalmazásokhoz. | A Site Recovery összeomlás-konzisztens helyreállítási pontokat hoz létre öt percenként alapértelmezés szerint. Ez a beállítás nem módosítható.<br/><br/>  | A legtöbb alkalmazás még ma, összeomlás-konzisztens pontokról is helyreállíthatja.<br/><br/> Összeomlás-konzisztens helyreállítási pontok általában elegendő az operációs rendszerek és alkalmazások, például a DHCP-kiszolgáló és a nyomtatókiszolgálók a replikációhoz.

### <a name="app-consistent"></a>Alkalmazáskonzisztens

**Leírás** | **Részletek** | **Az ajánlás**
--- | --- | ---
Alkalmazáskonzisztens helyreállítási pontok az alkalmazáskonzisztens pillanatképek jönnek létre.<br/><br/> Az alkalmazáskonzisztens pillanatkép összeomlás-konzisztens pillanatkép található összes információt, valamint az adatokat a memóriában, és a folyamatban lévő tranzakciók tartalmaznak. | Alkalmazáskonzisztens pillanatképek a kötet árnyékmásolata szolgáltatás (VSS) használja:<br/><br/>   1.) Ha kezdeményezték egy pillanatkép, VSS a kötet módosításkori másolási (vonatkozó) műveletek végrehajtása.<br/><br/>   2.) előtt hajtja végre a mintavételt, VSS tájékoztatja a minden alkalmazás, amely a memória rezidens ürítése a lemezre kell a gépen.<br/><br/>   (3) a VSS majd lehetővé teszi a biztonsági mentési és vész-helyreállítási app (Ez esetben a Site Recovery) olvassa el a pillanatkép adatainak és a folytatáshoz. | Alkalmazáskonzisztens pillanatképek készülnek, a megadott gyakorisága beállítás szerint. Ez a gyakoriság mindig kisebbnek kell lennie, mint a beállított helyreállítási pontok megőrzése. Például ha megőrzi a helyreállítási pontok használata az alapértelmezett beállítás 24 órás, kell beállítania a gyakorisága kisebb, mint 24 órával.<br/><br/>Ezek összetettebb és hosszabb időt vesz igénybe, mint az összeomlás-konzisztens pillanatképekkel.<br/><br/> A replikáció engedélyezése virtuális gépeken futó alkalmazások teljesítményét befolyásolja. 

## <a name="replication-process"></a>Replikációs folyamat

Amikor engedélyezi egy Azure virtuális gép replikációját, a következők történnek:

1. A Site Recovery mobilitási szolgáltatás bővítmény automatikus telepítve van a virtuális gépen.
2. A bővítményt a virtuális gép regisztrálja a Site recoveryvel.
3. A folyamatos replikáció a virtuális gép kezdődik.  Lemezírások azonnal átkerülnek a gyorsítótárfiók a forráshelyen.
4. A Site Recovery feldolgozza az adatokat a gyorsítótárban, és elküldi azokat a célként megadott tárfiók, vagy a replika felügyelt lemezeket.
5. Az adatok feldolgozása után az összeomlás-konzisztens helyreállítási pontok öt percenként akkor jönnek létre. Alkalmazáskonzisztens helyreállítási pontokat a replikációs szabályzatban megadott beállítások alapján jönnek létre.

![Replikációs folyamat, 2. lépés engedélyezése](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Replikációs folyamat**

## <a name="connectivity-requirements"></a>Hálózati kapcsolati követelményeinek

 Az Azure virtuális gépek replikálása kimenő kapcsolat szükséges. A Site Recovery soha nem kell a virtuális gép bejövő kapcsolatot. 

### <a name="outbound-connectivity-urls"></a>Kimenő kapcsolatok (URL-címek)

Ha a virtuális gépek kimenő hozzáférésének vezérli az URL-címek, ezeket az URL-címek.

| **URL-cím** | **Részletek** |
| ------- | ----------- |
| *.blob.core.windows.net | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| login.microsoftonline.com | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| *.hypervrecoverymanager.windowsazure.com | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| *.servicebus.windows.net | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

IP-címeket használó virtuális gépek kimenő kapcsolat szabályozásához, hogy engedélyezze ezeket a címeket.

#### <a name="source-region-rules"></a>Forrás régió szabályok

**Rule** |  **Részletek** | **Szolgáltatáscímke**
--- | --- | --- 
Kimenő HTTPS-forgalom engedélyezése: 443-as port | Lehetővé teszi a tartományok, amelyek megfelelnek a forrásrégióban storage-fiókok | Storage. \<régió-neve >.
Kimenő HTTPS-forgalom engedélyezése: 443-as port | Lehetővé teszi a tartományok, amelyek megfelelnek az Azure Active Directory (Azure AD).<br/><br/> Ha a jövőben hozzáadja az Azure AD-címeket szeretne létrehozni új hálózati biztonsági csoport (NSG) szabályai.  | AzureActiveDirectory
Kimenő HTTPS-forgalom engedélyezése: 443-as port | Való hozzáférés engedélyezése [Site Recovery végpontok](https://aka.ms/site-recovery-public-ips) , amelyek megfelelnek a célhelyen. 

#### <a name="target-region-rules"></a>Cél régió szabályok

**Rule** |  **Részletek** | **Szolgáltatáscímke**
--- | --- | --- 
Kimenő HTTPS-forgalom engedélyezése: 443-as port | Engedélyezze, hogy a storage-fiókok a célrégióban megfelelnek tartományait. | Storage. \<régió-neve >.
Kimenő HTTPS-forgalom engedélyezése: 443-as port | Lehetővé teszi a tartományok, amelyek megfelelnek az Azure ad-hez.<br/><br/> Ha a jövőben hozzáadja az Azure AD-címeket szeretne létrehozni új NSG-szabályokat.  | AzureActiveDirectory
Kimenő HTTPS-forgalom engedélyezése: 443-as port | Való hozzáférés engedélyezése [Site Recovery végpontok](https://aka.ms/site-recovery-public-ips) , amelyek megfelelnek a helyét. 


#### <a name="control-access-with-nsg-rules"></a>Hozzáférés vezérlése az NSG-szabályok

Ha a virtuális gépek kapcsolatai szabályozhatja a hálózati forgalom és az Azure hálózatok vagy alhálózatok segítségével a szűréssel [NSG-szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview), vegye figyelembe az alábbi követelményeknek:

- NSG-szabályok a forrás az Azure-régióban kell engedélyezi a kimenő hozzáférést a replikációs forgalmat.
- Javasoljuk, hogy szabályokat hozzon létre egy tesztkörnyezetben, mielőtt éles környezetbe helyezi őket.
- Használat [szolgáltatáscímkéket](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) engedélyezése egyes IP-címek helyett.
    - Szolgáltatáscímkék IP-címelőtagok csoportjait össze minimálisra összetettségét, amikor a biztonsági szabályok létrehozása egy csoport képviseli.
    - A Microsoft automatikusan frissíti a szolgáltatáscímkéket idővel. 
 
Tudjon meg többet [kimenő kapcsolat](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) Site Recovery és [szabályozása az NSG-ket kapcsolat](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Kapcsolat a virtuális gépre kiterjedő konzisztencia

Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással.
- Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt.
- Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.




## <a name="failover-process"></a>Feladatátvételi folyamatot

Kezdeményezzen feladatátvételt, ha a virtuális gépek létrehozása a céloldali erőforráscsoport, célként megadott virtuális hálózat, alhálózat, és a cél rendelkezésre állási csoportban. A feladatátvétel során a bármelyik helyreállítási pontot is használhat.

![Feladatátvételi folyamatot](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>További lépések

[Gyorsan replikálja](azure-to-azure-quickstart.md) egy Azure virtuális Gépen egy másodlagos régióba.
