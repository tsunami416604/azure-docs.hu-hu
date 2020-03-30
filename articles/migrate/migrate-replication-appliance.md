---
title: Azure Migrate replikációs berendezés
description: Ismerje meg az Azure Migrate replikációs berendezés ügynökalapú VMWare áttelepítés.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4521fce6310b319d155a2f0c418cd934be7e2cb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245862"
---
# <a name="replication-appliance"></a>Replikációs berendezés

Ez a cikk ismerteti az [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz által használt replikációs készülék et vmware virtuális gépek, fizikai gépek és magán-/nyilvános felhőalapú virtuális gépek Azure-ba ügynökalapú áttelepítés használatával történő áttelepítésekor. 


## <a name="overview"></a>Áttekintés

A replikációs berendezés a VMware virtuális gépek vagy fizikai kiszolgálók ügynökalapú áttelepítésének beállításakor kerül üzembe helyezésre. Egyetlen helyszíni gépként van telepítve, akár VMware virtuális gépként, akár fizikai kiszolgálóként. Ez fut:

- **Replikációs berendezés**: A replikációs berendezés koordinálja a kommunikációt, és kezeli az adatok replikációját a helyszíni VMware virtuális gépek és az Azure-ba replikáló fizikai kiszolgálók esetében.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló, amely alapértelmezés szerint telepítve van a replikációs berendezésen, és a következő:
    - **Replikációs átjáró**: Replikációs átjáróként működik. Replikációs adatokat fogad a replikációra engedélyezett gépekről. Gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja a replikációs adatokat, és elküldi azokat az Azure-nak.
    - **Ügynöktelepítő**: A mobilitási szolgáltatás leküldéses telepítése. Ezt a szolgáltatást telepíteni és futtatni kell minden olyan helyszíni gépen, amelyet az áttelepítéshez replikálni szeretne.

## <a name="appliance-deployment"></a>A berendezés üzembe helyezése

**Alkalmazási cél** | **Részletek**
--- |  ---
VMware VM-ügynök alapú áttelepítés | Ova sablont az Azure Áttelepítési központból, és importálja a vCenter Server a készülék virtuális gép létrehozásához.
Fizikai gépügynök-alapú áttelepítés | Ha nem rendelkezik VMware-infrastruktúrával, vagy ha nem tud létrehozni egy VMware virtuális gépet ovasablon használatával, letölt egy szoftvertelepítőt az Azure Migrate hubról, és futtatja a készülék beállítása érdekében.

## <a name="appliance-requirements"></a>A készülékre vonatkozó követelmények

Ha a replikációs berendezést az Azure Migrate hubban található OVA sablon használatával állítja be, a készülék a Windows Server 2016 rendszert futtatja, és megfelel a támogatási követelményeknek. Ha a replikációs berendezést manuálisan állítja be egy fizikai kiszolgálón, győződjön meg arról, hogy az megfelel a követelményeknek.

**Összetevő** | **Követelmény**
--- | ---
 | **VMware VM készülék**
PowerCLI között | [A PowerCLI 6.0-s verzióját](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) telepíteni kell, ha a replikációs készülék VMware virtuális gépen fut.
Hálózati adapter típusa | VMXNET3 (ha a készülék VMware VM)
 | **Hardverbeállítások**
Processzormagok | 8
RAM | 16 GB
Lemezek száma | Három: Az operációs rendszer lemeze, a kiszolgáló gyorsítótárlemezének feldolgozása és az adatmegőrzési meghajtó.
Szabad lemezterület (gyorsítótár) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
**Szoftverbeállítások** |
Operációs rendszer | Windows Server 2016 vagy Windows Server 2012 R2
Licenc | A készülék windows Server 2016 kiértékelési licenccel rendelkezik, amely 180 napig érvényes.<br/><br/> Ha a kiértékelési időszak közel áll a lejárathoz, javasoljuk, hogy töltsön le és telepítsen egy új berendezést, vagy aktiválja a készülék virtuális gépének operációs rendszerlicencét.
Operációs rendszer területi beállítása | Angol (en-us)
TLS | A TLS 1.2-t engedélyezni kell.
.NET-keretrendszer | A .NET Framework 4.6-os vagy újabb rendszert telepíteni kell a számítógépre (erős titkosítás engedélyezve van.
MySQL | A MySQL-t fel kell szerelni a készülékre.<br/> MySQL kell telepíteni. Manuálisan is telepíthető, vagy a Site Recovery telepítheti a készülék üzembe helyezése során.
Egyéb alkalmazások | Ne futtasson más alkalmazásokat a replikációs készüléken.
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> - A parancssorhoz való hozzáférés megakadályozása. <br> - Hozzáférés megakadályozása a rendszerleíró adatbázis szerkesztő eszközök. <br> - Megbízhatósági logika a fájlmellékletekhez. <br> - Kapcsolja be a Script Execution. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nincs már létező alapértelmezett webhely <br> - Nincs már létező weboldal / alkalmazás hallgatása a 443-as porton <br>- Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás
**Hálózati beállítások** |
IP-cím típusa | Statikus
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)
Hálózati adapter típusa | VMXNET3

## <a name="mysql-installation"></a>MySQL telepítés 

A MySQL-t telepíteni kell a replikációs készülékgépre. Az alábbi módszerek egyikével telepíthető.

**Módszer** | **Részletek**
--- | ---
Manuális letöltés és telepítés | Töltse le a MySQL alkalmazást& helyezze el a C:\Temp\ASRSetup mappába, majd telepítse manuálisan.<br/> Amikor beállítja a készüléket MySQL jelenik meg, mint már telepítve van.
Online letöltés nélkül | Helyezze a MySQL telepítőalkalmazást a C:\Temp\ASRSetup mappába. Amikor telepíti a készüléket, és kattintson a mySQL letöltéséhez és telepítéséhez, a telepítő a hozzáadott telepítőt fogja használni.
Letöltés és telepítés az Azure Migrate szolgáltatásban | Amikor telepíti a készüléket, és a rendszer a MySQL-t kéri, válassza a **Letöltés és telepítés**lehetőséget.

## <a name="url-access"></a>URL-hozzáférés

A replikációs berendezésnek hozzá kell férnie ezekhez az URL-címekhez.

**Url** | **Részletek**
--- | ---
\*.backup.windowsazure.com | Replikált adatátvitelhez és koordinációhoz használatos
\*.store.core.windows.net | Replikált adatátvitelhez és koordinációhoz használatos
\*.blob.core.windows.net | Replikált adatokat tároló tárfiók eléréséhez
\*.hypervrecoverymanager.windowsazure.com | Replikációkezelési műveletekhez és koordinációhoz használatos
https:\//management.azure.com | Replikációkezelési műveletekhez és koordinációhoz használatos
*.services.visualstudio.com | Telemetriai célokra használva (Nem kötelező)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Az OVF beállításához hozzá kell férni ezekhez az URL-címekhez. Az Azure Active Directory hozzáférés-vezérlésre és identitáskezelésre használja őket
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | A MySQL letöltésének befejezése

## <a name="port-access"></a>Port-hozzáférés

**Eszköz** | **Kapcsolat**
--- | ---
Virtuális gépek | A virtuális gépeken futó Mobilitás szolgáltatás kommunikál a https 443-as porton lévő helyszíni replikációs eszközzel (konfigurációs kiszolgálóval) a replikáció kezeléséhez.<br/><br/> A virtuális gépek replikációs adatokat küldenek a folyamatkiszolgálónak (amely a konfigurációs kiszolgálógépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés vezényli a replikációt az Azure-ral HTTPS 443 kimenő porton keresztül.
Folyamatkiszolgáló | A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja azokat, és elküldi az Okat az Azure storage-ba a 443-as kimenő porton keresztül.<br/> Alapértelmezés szerint a folyamatkiszolgáló fut a replikációs berendezésen.


## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a virtuális gép replikációját, megkezdődik az Azure storage-ba történő kezdeti replikáció a megadott replikációs szabályzat használatával. 
2. A forgalom replikálódik az Azure storage nyilvános végpontjaira az interneten keresztül. A helyszíni helyről az Azure-ra irányuló, helyek közötti virtuális magánhálózaton (VPN) keresztül imitáló forgalom replikálása nem támogatott.
3. A kezdeti replikáció befejezése után megkezdődik a különbözeti replikáció. A rendszer naplózza a gép nyomon követett módosításait.
4. A kommunikáció a következőképpen történik:
    - A virtuális gépek a HTTPS 443-as porton lévő replikációs készülékkel kommunikálnak a replikáció kezeléséhez.
    - A replikációs berendezés vezényli a replikációt az Azure-ral HTTPS 443 kimenő porton keresztül.
    - A virtuális gépek replikációs adatokat küldenek a folyamatkiszolgálónak (a replikációs berendezésen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja azokat, és elküldi az Okat az Azure storage-ba a 443-as kimenő porton keresztül.
5. A replikációs adatok naplói először egy gyorsítótár-tárfiókban az Azure-ban. Ezeket a naplókat feldolgozzuk, és az adatokat egy Azure felügyelt lemez tárolja.

![Architektúra](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>A készülék frissítései

A készülék manuálisan frissül az Azure Áttelepítési központból. Azt javasoljuk, hogy mindig a legújabb verziót futtassa.

1. Az Azure Migrate > Servers > Az Azure Migrate: Server Assessment, Infrastructure servers alkalmazásban kattintson **a Konfigurációs kiszolgálók**elemre.
2. A **konfigurációs kiszolgálókban**egy hivatkozás jelenik meg az **Ügynök verzióban,** ha a replikációs eszköz új verziója elérhető. 
3. Töltse le a telepítőt a replikációs készülékgépre, és telepítse a frissítést. A telepítő észleli a készüléken futó verzióáramot.
 
## <a name="next-steps"></a>További lépések

- [Ismerje meg, hogyan](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) állíthatja be a replikációs berendezést az ügynökalapú VMware virtuális gépek áttelepítéséhez.
- [További információ](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) a replikációs berendezés fizikai kiszolgálókhoz való beállításáról.
