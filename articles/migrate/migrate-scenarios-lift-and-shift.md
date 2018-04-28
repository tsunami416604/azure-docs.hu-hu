---
title: A helyszíni munkaterhelések áttelepítése az Azure Site Recovery és az Azure DMS |} Microsoft Docs
description: Ismerje meg, hogyan készíti elő az Azure a helyszíni gépeket az Azure-adatbázis áttelepítési szolgáltatás, és az Azure Site Recovery szolgáltatás áttelepítéséhez.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>2. forgatókönyv: Növekedési és shift áttelepítése az Azure-bA

A Contoso vállalat készül áttelepítése az Azure-bA. Próbálja ki ez, azok szeretne mérheti fel, és telepítse át egy kis helyszíni utazás alkalmazást az Azure-bA. A kétrétegű utazás alkalmazás, egy virtuális gép, és egy SQL Server-adatbázis, a második virtuális Gépen futó webes alkalmazás is. Az alkalmazás a VMware-ben üzemel, a környezetet pedig a vCenter Server kezeli. 

A [1. forgatókönyv: az Azure-bA áttelepítési értékeléséhez](migrate-scenarios-assessment.md), ezek az adatok áttelepítési Segéd (DMA-) segítségével mérheti fel az alkalmazáshoz tartozó SQL Server-adatbázis. Ezenkívül azokat az Azure áttelepítése szolgáltatás segítségével mérheti fel az alkalmazás virtuális gépeket. Most ebben a forgatókönyvben, ha sikeresen elvégezte az értékelést szeretnék telepítse át az adatbázist egy Azure SQL felügyelt példányon Azure adatbázis áttelepítési szolgáltatás (DMS), és a helyszíni gépeket Azure virtuális gépek az Azure Site Recovery szolgáltatással.

Ha azt szeretné, hogy próbálja ki [1. forgatókönyv](migrate-scenarios-assessment.md), majd ebben az esetben a szemléltető utazás alkalmazással töltheti le, és [github](https://github.com/Microsoft/SmartHotel360).



**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Adatbázis-kezelési szolgáltatás](https://docs.microsoft.com/azure/dms/dms-overview) | DMS lehetővé teszi, hogy az Azure data platformok, minimális állásidővel több adatbázis forrásból zökkenőmentes áttelepítés. | A szolgáltatás jelenleg nyilvános előzetes verziójában (április 2018), és ingyenesen használható az előzetes ingyenesen elérhető.<br/><br/> Vegye figyelembe, hogy a nyilvános előzetes verzióhoz DMS legfeljebb egy [régiók száma](https://docs.microsoft.com/azure/dms/dms-overview).
[Az Azure SQL felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Felügyelt példány egy felügyelt adatbázis-szolgáltatás, amely egy teljes körűen felügyelt SQL Server-példány Azure felhőben jelöli. Osztja meg ugyanazt a kódot az SQL Server adatbázismotor legújabb verzióját, és a legújabb funkciókat, a teljesítménnyel kapcsolatos fejlesztések és a biztonsági javítások. | Azure SQL adatbázis felügyelt példányok használatával az Azure-ban futó függő díj terheli kapacitása alapján. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a kezeli a áttelepítési és vészhelyreállítás Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-bA replikálás során Azure Storage díjak sem merülnek fel.  Azure virtuális gépek jönnek létre, és a függő díj terheli, feladatátvétel esetén. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és az árképzés terén.

Ebben a forgatókönyvben be lesznek állítva a telephelyek közötti VPN, hogy a DMS a helyi adatbázis csatlakozhat, Azure SQL felügyelt példányt létrehozni az Azure-ban, és telepítse át az adatbázist. A Site Recovery azt fogja készítse elő a helyszíni VMware-környezetben, a replikáció beállítása és a virtuális gépek áttelepítése az Azure-bA.  


> [!IMPORTANT]
> Módosítania kell az SQL által felügyelt példány korlátozott nyilvános előzetes verzióban igényelt. Annak érdekében, hogy az Azure-előfizetésre van szüksége [regisztráljon](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Előfizetési is igénybe vehet néhány napon belül végezze el, győződjön meg arról, hogy lehetővé teszi a forgatókönyv alkalmazása előtt.

## <a name="architecture"></a>Architektúra

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

Ebben a forgatókönyvben:

- Contoso egy tipikus vállalaton belüli képviselő többéves nevét. A Contoso biztosítani segítségével mérheti fel, és telepítse át a helyszíni kétrétegű utazás alkalmazást.
- Contoso rendelkezik egy olyan helyszíni adatközpontban (a contoso-adatközpontban), egy a helyi tartományvezérlővel (**contosodc1**).
- A belső utazás alkalmazás két virtuális gépeket, WEBVM és SQLVM rétegzett és VMware ESXi-állomáson található (**contosohost1.contoso.com**).
- A VMware-környezetet egy virtuális gépen futó vCenter Server (**vcenter.contoso.com**) felügyeli.

## <a name="prerequisites"></a>Előfeltételek

Ha azt szeretné, ebben a forgatókönyvben futtatásához, Ez mit kell rendelkeznie.

Követelmények | Részletek
--- | ---
**Azure-előfizetés** | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha még nem a rendszergazda egy meglévő előfizetéshez használ, meg kell dolgoznia a rendszergazdától, hogy rendeljen Önhöz tulajdonosi vagy közreműködői engedélyekkel kell rendelkeznie.<br/><br/> Ha részletesebb engedélyekre van szükség, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**A Site recovery (helyszíni)** | Egy helyszíni vCenter kiszolgáló és az 5.5, 6.0 vagy 6.5<br/><br/> Az ESXi-állomáson, 5.5, 6.0 vagy 6.5-ös verzióját<br/><br/> Egy vagy több VMware virtuális gépek az ESXi-állomáson futó.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Támogatott [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurációs.
**DMS** | A DMS kell egy [kompatibilis helyszíni VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Be kell tudnia a helyszíni VPN-eszköz konfigurálásához. Külsőleg irányuló nyilvános IPv4-cím kell rendelkeznie, és a cím nem található a NAT-eszköz mögött.<br/><br/> Ellenőrizze, hogy a helyszíni SQL Server-adatbázis elérhető lesz.<br/><br/> A Windows tűzfal a forrás adatbázismotor eléréséhez képesnek kell lennie. [További információk](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Ha a tűzfal előtt az adatbázis-számítógép, vegye fel a szabályokat, hogy engedélyezi a hozzáférést az adatbázishoz, és a fájlok SMB 445-ös porton keresztül.<br/><br/> SQL Server adatforráshoz kapcsolódnak, és felügyelt célpéldányának használt hitelesítő adatokat a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie.<br/><br/> Kell egy hálózati megosztás DMS használatával a forrás-adatbázis biztonsági mentése az a helyi adatbázisban.<br/><br/> Győződjön meg arról, hogy a szolgáltatás az adatforrás SQL Server-példányt futtató fióknak írási jogosultságokkal a hálózati megosztáson.<br/><br/> Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes hozzáférési jogosultsággal rendelkezik a hálózati megosztáson. Az Azure-adatbázis áttelepítési szolgáltatás megszemélyesít ezeket a biztonsági mentési fájlok feltöltése az Azure storage-tároló felhasználói hitelesítő adatokat.<br/><br/> Az SQL Server Express telepítési folyamat beállítja a TCP/IP-protokoll **letiltott** alapértelmezés szerint. Győződjön meg arról, hogy engedélyezve van.


# <a name="scenario-overview"></a>Forgatókönyv áttekintése

A következő lépéseket hajtjuk végre:

> [!div class="checklist"]
> * **1. lépés: Egy pont-pont VPN-kapcsolat állítható be**: DMS VPN webhelyek kapcsolaton keresztül csatlakozik a helyi adatbázis. A VPN-kapcsolat és a későbbi virtuális hálózat létrehozása a Microsoft, azt újra felhasználhatja ezt a hálózatot, a Site Recovery. A hálózati hoz létre a régióban, amelyben a Recovery Services-tároló létrehozása kell lennie.
> * **2. lépés: Állítson be egy SQL Azure felügyelt példány**: egy előre létrehozott felügyelt példány, amelyre áttelepíti a helyszíni SQL Server-adatbázis szükséges.
> * **3. lépés: Állítsa be a DMS SA URI-JÁNAK**: A közös hozzáférésű jogosultságkód (SAS) egységes erőforrás-azonosító (URI) delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz, hogy a tárolási objektum korlátozott engedélyek megadásához. Állíthat be egy SAS URI-t, így a DMS férhetnek hozzá a fiókot a tároló, amelyhez a szolgáltatás feltölti az SQL Server biztonsági másolat fájljait.
> * **4. lépés: Felkészülés a DMS**: az adatbázis áttelepítési szolgáltató regisztrálása, hozzon létre egy példányt, és majd a DMS-projekt létrehozása.
> * **5. lépés: Felkészülés Azure Site Recovery**: a replikált adatok tárolásához Azure storage-fiók létrehozása.
> * **6. lépés: A helyszíni VMware előkészítése a Site Recovery**: készítse elő a fiókok a virtuális gép felderítés és az ügynök telepítése, és készítse elő a feladatátvételt követően Azure virtuális gépeken való kapcsolódáshoz. 
> * **7. lépés: Replikálása virtuális gépek**: a Site Recovery forrása és célja a környezet beállításához, állítson be egy replikációs házirendet, és indítsa el a virtuális gépek replikálása az Azure storage.
> * **8. lépés: Telepítse át a DMS adatbázis**: egy adatbázis-áttelepítés futtatása.
> * **9. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése**: feladatátvételt a virtuális gépek áttelepítése az Azure-bA.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>1. lépés: Webhelyek VPN-kapcsolat beállítása

Az a pont-pont VPN-kapcsolat előkészítéséhez egy virtuális hálózatot és alhálózatok beállítása, hozzon létre a virtuális hálózat VPN-átjáró, és állítsa be a helyi hálózati átjáró, adott Azure tudja, hogyan csatlakozhat a helyszíni VPN. Ezután hozzon létre, és a pont-pont kapcsolat ellenőrzéséhez.

### <a name="set-up-a-virtual-network"></a>Virtuális hálózat létrehozása

DMS biztosít webhelyek kapcsolatot a helyszíni SQL Server Azure virtuális hálózat létrehozása.


1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **hozzon létre egy erőforrást**. Válassza ki **hálózati** > **virtuális hálózati**.
2. A **virtuális hálózati** > **telepítési modell kiválasztása**, jelölje be **erőforrás-kezelő** > **létrehozása**
3. A **virtuális hálózat létrehozása** > **neve**, adjon meg egy egyedi hálózati nevet. A mi esetünkben **ContosoVNET**.
4. A **Címtéren**, az IP-címtartomány hozzáadása. A tartomány nem lehet átfedésben a helyszíni címterület a.
5. A **erőforráscsoport**, hozzon létre egy új csoportot, vagy válasszon egy meglévőt. Ebben a forgatókönyvben használunk **ContosoRG**.
6. A **hely**, válassza ki azt a régiót, amelyben a virtuális hálózat létrehozásához. Használunk **USA keleti régiója 2**.
7. A **alhálózati**, adja hozzá az első alhálózat nevét, és -címtartományt. Létrehoztunk Önnek a **alkalmazások** alhálózat.
8. Tiltsa le a végpontok.

    ![Virtuális hálózat létrehozása](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Válassza ki **rögzítés az irányítópulton** így könnyebben megtalálhatja a jövőben a hálózaton, és kattintson **létrehozása**.
10. A virtuális hálózat néhány másodperc alatt létrejön. Miután létrejön, ellenőrizze az Azure portál Irányítópultjára.
11. Győződjön meg arról, hogy a kommunikációs portok engedélyezettek-e a virtuális hálózat: 443-as, 53-as és 9354-es, 445-ös, 12000.


### <a name="set-up-subnets"></a>Alhálózatok beállítása

Az Azure-hálózatot fogja tudunk négy alhálózatok:

![Alhálózati listája](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

A hálózat létrehozásakor beállítása az első alhálózat (alkalmazások). Ezután hozzon létre a többi alhálózatok. Az átjáró alhálózatának a VPN gateway-kapcsolatot, az Azure-forgalmat küldhetnének a VPN-kapcsolat a helyszíni hely használja.

1. A virtuális hálózat alatt **beállítások**, kattintson a **alhálózati** > **+ alhálózati**.
2. Állítson be egy alhálózati **Datamigration**, ennek-címtartományt, és kattintson a **OK**.

    ![Adatok alhálózati](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. A **+ alhálózati**, állítson be egy alhálózati **identitás**, ennek-címtartományt, és kattintson a **OK**.
    ![Alhálózati azonosító](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. A **alhálózati**, kattintson a **átjáróalhálózatot**, és kattintson a **OK**.
    - Ez az alhálózat tartalmaz az IP-címek, a VPN-átjáró által használt VPN-kapcsolatokhoz.
    - Úgy, hogy Azure felismerje azt a rendszer automatikusan beállítja az alhálózat neve.
    - Állítsa be a címet automatikusan kitölti a helyszíni alhálózati kereséséhez. 

    ![Átjáró-alhálózatot](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>A virtuális hálózati átjáró beállítása

1. A portáloldal bal oldalán kattintson a **+** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. A **eredmények**, kattintson a **virtuális hálózati átjáró**.
2. A „Virtuális hálózati átjáró” lap alján kattintson a **Létrehozás** gombra.
3. A **virtuális hálózati átjáró létrehozása** >  **neve**, adja meg az átjáró objektum nevét.
4. A **átjáró típusa**, jelölje be **VPN**.
5. A **VPN-típus**, jelölje be **útválasztó-alapú**.
6. A **SKU**, jelölje be az átjáró-Termékváltozat a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Nem engedélyezi az aktív-aktív mód. [További](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) termékváltozatok kapcsolatban.
7. A **virtuális hálózati** > **virtuális hálózatot választ**, vegye fel a létrehozott hálózati. Az átjáró megkapja a hálózathoz.
8. A **első IP-konfiguráció**, adjon meg egy nyilvános IP-címet.  VPN-átjáró nyilvános IP-címnek kell rendelkeznie. Ez a cím dinamikusan van rendelve az erőforrás, a VPN-átjáró létrehozásakor. Csak dinamikus címek jelenleg támogatott, de az IP-cím nem változik, miután van hozzárendelve.
    - Kattintson a **létrehozás átjáró IP-konfiguráció**. A **nyilvános IP-cím kiválasztása**, kattintson a **+ új létrehozása**.
    - A **nyilvános IP-cím létrehozása**, adja meg a nyilvános IP-cím (a Contoso-átjáró) nevét. A Termékváltozat, hagyja **alapvető**, és kattintson a **OK** menti a módosításokat.
        
    ![Virtuális hálózati átjáró](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. A **Létrehozás** gombra kattintva hozzákezdhet a VPN-átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az irányítópulton megjelenik "A virtuális hálózati átjáró".

    ![Ellenőrizze a virtuális hálózati átjáró](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy akkor kell a portáloldalt a kész állapot megjelenítéséhez frissítenie.

### <a name="set-up-a-local-network-gateway"></a>Állítsa be a helyi hálózati átjáró

Az Azure helyi hálózati átjáró beállítása a helyszíni hely képviseli.

1. A portálon kattintson az **+Erőforrás létrehozása** gombra.
2. A keresőmezőbe írja be a **Helyi hálózati átjáró** kifejezést, majd nyomja le az **Enter** billentyűt a kereséshez. Az eredmények között kattintson **helyi hálózati átjáró** > **létrehozása**.
3. A **helyi hálózati átjáró létrehozása** > **neve**, adjon meg egy nevet, amely a helyi hálózati átjáróobjektumnak Azure használ.
4. **IP-cím**, adja meg a helyszíni VPN-eszközön, amelyhez csatlakozni fognak az Azure nyilvános IP-címét. Az IP-cím nem lehet NAT'ed, és lehet, hogy Azure képes elérni.
5. A **Címtéren**, írja be a helyi hálózati-címtartományokat, amelyek a VPN-átjáró a helyi eszközön keresztül történik. Győződjön meg arról, hogy azok az Azure virtuális hálózat címtartományok nem fednek át.
6. **BGP-beállítások konfigurálása** nem releváns ebben a forgatókönyvben.
7. A **előfizetés**, győződjön meg arról, hogy az előfizetés megfelelő.
8. A **erőforráscsoport**: válassza ki az erőforráscsoportot, mint amellyel a hálózati (ContosoRG).
9. A **hely**, válassza ki azt a régiót, amelyben a virtuális hálózat létrehozása.

    ![Helyi átjáró](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Kattintson a **létrehozása** a helyi átjáró létrehozásához.

### <a name="configure-your-on-premises-vpn-device"></a>Konfigurálja a helyszíni VPN-eszköz

Ez a helyszíni VPN-eszköz konfigurálásához szüksége:

- Az újonnan létrehozott Azure virtuális hálózati átjáró nyilvános IPv4-címe.
- Az előmegosztott kulcs (az a pont-pont VPN-kapcsolat létrehozásakor használt kulcs).  

Segítséget a helyszíni VPN-eszköz beállításához:

- Attól függően, hogy a helyszíni VPN-eszköz, amely rendelkezik valószínűleg tudni letölteni egy VPN-eszköz konfigurációs parancsprogram. [További információk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Felülvizsgálati [további hasznos erőforrások](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>VPN-kapcsolat létrehozása

Most hozzon létre a pont-pont VPN-kapcsolat a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.

1. Nyissa meg a virtuális hálózat lap **virtuális hálózatok** > **áttekintése** > **csatlakoztatott eszközök**. 
2. Kattintson a **kapcsolatok** > **+ Hozzáadás**.
3. A **kapcsolat hozzáadása a** > **neve**, adjon meg egy nevet a kapcsolathoz.
4. A **kapcsolattípus**, jelölje be **pont-pont (IPSec)**.
5. A **virtuális hálózati átjáró** értéke fix, mivel, amelyből az átjáró csatlakozik.
6. A **helyi hálózati átjáró**, adja meg a létrehozott helyi hálózati átjáró.
7. A **megosztott kulcsos**, adja meg a kulcsot, amely megegyezik-e a helyi helyszíni VPN-eszköz használata. A példában az abc123 értéket használtuk, de lehetséges (és javasolt) ennél összetettebb értéket használni. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.
8. Az értékek **előfizetés**, **erőforráscsoport**, és **hely** rögzítettek.

    ![VPN-kapcsolat](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Kattintson a **OK** a VPN-kapcsolat létrehozásához. Látni fogja, a képernyőn megjelenő "Kapcsolat létrehozása" flash.
5. A kapcsolat létrehozása után megtekintheti a a **kapcsolatok** a virtuális hálózati átjáró oldalán. Az állapot ismeretlen a kerül > Csatlakozás > sikeres.

### <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése

Az Azure Portalon a kapcsolatra lépve tekintheti meg a Resource Manager VPN-átjárók kapcsolati állapotát. 

1. Kattintson a **összes erőforrás**, és keresse meg a virtuális hálózati átjáró.
2. A virtuális hálózati átjáró oldalon kattintson **kapcsolatok**. Láthatja az egyes kapcsolatok állapotát.
3. Kattintson a kapcsolat nevére, és a további információk megtekintéséhez **Essentials**. Állapota "Sikeres" és "Csatlakoztatott" Ha a sikeres kapcsolat hajtott végre.

Most győződjön meg arról, hogy tud kapcsolódni az SQL Server virtuális gép VPN-kapcsolaton keresztül. A távoli asztali kapcsolat létrehozása és a virtuális gép IP-cím kapcsolódjanak.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>2. lépés: Egy felügyelt Azure SQL-példány előkészítése

### <a name="set-up-a-virtual-network"></a>Virtuális hálózat létrehozása

Ebben a forgatókönyvben akkor hozzon létre egy másik virtuális hálózatot, amely az Azure SQL-felügyelt példány van kijelölve.  Vegye figyelembe az alábbi követelményeknek:

- A felügyelt példány létrehozásához szükséges dedikált alhálózat.
- Az alhálózat kell megadni, és nem más felhőalapú szolgáltatás, és nem lehet egy átjáró-alhálózatot. A felügyelt példány létrehozása után az alhálózat nem hozzáadhat erőforrásokat.
- Az alhálózat nem lehet vele társított NSG-t.
- Az alhálózat rendelkeznie kell egy felhasználó útvonal tábla (UDR) 0.0.0.0/0 következő ugrásaként internetes rendelve csak útvonalként. 
- Nem kötelező egyéni DNS: Ha egyéni DNS meg van adva a virtuális hálózaton, Azure rekurzív feloldókat IP-cím (például 168.63.129.16) hozzá kell adni a listához. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Az alhálózat nem lehet egy végpontot (tároló vagy SQL) társítva. A virtuális hálózaton Szolgáltatásvégpontok le kell tiltani.
- Az alhálózat rendelkeznie kell legalább 16 IP-címet. [További](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) a felügyelt példány alhálózati méretezésével kapcsolatban.

Az alábbiak szerint állíthatja be a virtuális hálózatot:

1.  Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra. 
2. Válassza ki **hálózati** > **virtuális hálózati**.
3. A **virtuális hálózati** > **telepítési modell kiválasztása**, jelölje be **erőforrás-kezelő** > **létrehozása**.
4. A **virtuális hálózat létrehozása** > **neve**, adjon meg egy egyedi hálózati nevet. A mi esetünkben **ContosoVNETSQLMI**.
5. A **Címtéren**, az alábbi IP-címtartomány hozzáadása. A tartomány a helyszíni és ContosoVNET címterület nem lehet átfedésben.
6. A **erőforráscsoport**, hozzon létre egy új csoportot, vagy válasszon egy meglévőt. Ebben a forgatókönyvben használunk **ContosoRG**.
7. A **hely**, válassza ki azt a régiót, amelyben a virtuális hálózat létrehozásához. Használunk **USA keleti régiója 2**.
8. A **alhálózati**, adja hozzá az első alhálózat nevét, és -címtartományt. Létrehoztunk a **ManagedInstances** alhálózat.
9. Tiltsa le a végpontok.

    ![Felügyelt példány hálózati](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. A hálózati telepítése után a DNS-beállításait módosítani szeretné. Ebben a forgatókönyvben a DNS-ben először mutat az identitás alhálózatot egy statikus magánhálózati IP-cím (172.16.3.4), és az Azure DNS-feloldási 168.63.129.16 tartományvezérlője.

    ![Felügyelt példány hálózati](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Útválasztás beállítása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra. Kattintson a **útvonaltábla** > **létrehozása az útvonaltáblán** lap.
2. A **útvonaltábla létrehozása** > **neve**, adja meg a táblázat nevét.
3. Válassza ki az előfizetést, erőforráscsoportot és helyet. Hagyja a BGP le van tiltva, és kattintson a **létrehozása**.
    ![Útvonaltábla](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Az útvonaltábla létrehozása után nyissa meg azt, és kattintson **útvonalak** > **+ Hozzáadás**.
5. A **útvonal hozzáadása** > **neve**, adjon meg egy útvonal-nevet.
6. A **címelőtag**, adja meg a 0.0.0.0/0.
7. A **a következő ugrás típusa**, jelölje be **Internet**. Ezután kattintson az **OK** gombra.

     ![Útválasztási táblázat](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Az útvonal vonatkozik a felügyelt példány alhálózat

1. Nyissa meg a létrehozott virtuális hálózatot. Kattintson a **alhálózatok** > saját-alhálózat-neve.
2. Kattintson a **útvonaltábla** > saját tábla neve. Ezután kattintson a **Save** (Mentés) gombra.

     ![Útválasztási táblázat](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Egy felügyelt példányának létrehozása

1. Kattintson a **hozzon létre egy erőforrást**, keresse meg **felügyelt példány**, és válassza ki **Azure SQL Database felügyelt-példányt (előzetes verzió)**.
2. Kattintson a **Create** (Létrehozás) gombra.
3. A **SQL-példány felügyelt**, jelölje ki az előfizetését, és ellenőrizze, hogy **feltételek előzetes** megjelenítése **elfogadott**.
4. A **felügyelt példánynév**, adjon meg egy nevet. 
5. Adja meg a példányhoz tartozó rendszergazdai felhasználónevet és jelszót.
6. Válassza ki az erőforráscsoportot, helyét és a virtuális hálózat példány.
7. Kattintson a **tarifacsomag** mérete számítási és tárolási. Alapértelmezés szerint a példány 32 GB tárhely ingyenesen (április 2018) lekérése.
8. Adja meg a tároló és a virtuális magok száma.
9. Kattintson a **alkalmaz** menti a beállításokat, és **létrehozása** központi telepítése a felügyelt példány. Központi telepítési állapotának megtekintéséhez kattintson **értesítések**, és **folyamatban lévő telepítés**.

    ![Felügyelt példány](media/migrate-scenarios-lift-and-shift/mi-1.png)

Központi telepítés befejezése után a felügyelt példány, két új erőforrások megjelennek az ContosoRG erőforráscsoport: virtuális fürtöt a felügyelt példányok, és felügyelt SQL-példány. USA keleti régiója 2 helyen mindkettő.

![Felügyelt példány](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>3. lépés: A SAS URI-JÁNAK beolvasása DMS

Első egy SAS URI érdekében, hogy a DMS a fiók a tároló, a biztonsági másolat használt fájlokat tartalmazó adatbázisok Azure SQL adatbázis felügyelt példány áttelepítése feltöltéséhez. 

1. Nyissa meg a Tártallózót (előzetes verzió).
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob-tároló, amelynek le szeretné kérdezni az Aláírást tartalmazó. Bontsa ki a tárfiók **Blobtárolók**.
3. Kattintson a jobb gombbal a tárolóra, és válassza ki **közös hozzáférésű Jogosultságkód beolvasása**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. A **közös hozzáférésű Jogosultságkód**, adja meg a házirend, a kezdő és záró dátumát, az időzóna, és a hozzáférési szintet szeretné az erőforrás. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Egy második párbeszédpanel a blob-tároló URL-cím és a tárolási erőforrások eléréséhez használhatja QueryStrings jeleníti meg. Válassza ki **másolási** értékeinek másolására. Tartsa biztonságos helyen. Már szükség DMS beállításakor.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>4. lépés: A DMS előkészítése

Regisztrálja a adatbázis áttelepítési szolgáltatót, és hozzon létre egy példányt.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Válassza ki az előfizetés **erőforrás-szolgáltató**. 
2. Keresse meg a "áttelepítési", és jobb oldalán Microsoft.DataMigration, válassza **regisztrálása**. 


### <a name="create-an-instance"></a>Hozzon létre egy példányt

1. Válassza ki **+ hozzon létre egy erőforrást**, keresse meg az "Azure adatbázis áttelepítési Service", és válassza **Azure adatbázis áttelepítési szolgáltatás** a legördülő listából.
2. Az Azure adatbázis áttelepítési szolgáltatás (előzetes verzió) képernyőn válassza ki a **létrehozása**.
3. Adja meg a szolgáltatás, az előfizetés, erőforráscsoportban található, virtuális hálózat és az árképzési szint nevét.
4. Válassza ki **létrehozása** létrehozni a szolgáltatást.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>5. lépés: A Site Recovery szolgáltatás Azure előkészítése

### <a name="set-up-a-virtual-network"></a>Virtuális hálózat létrehozása

Az Azure-hálózatot, amelyben az Azure virtuális gépeken található ha feladatátvételt követően jönnek létre, és tárolja a replikált adatok Azure storage-fiók szükséges.

- Ebben a forgatókönyvben céljából az Azure-hálózatot, amely korábban, a DMS létrehozott fogjuk használni.
- Vegye figyelembe, hogy a hálózat használata és a Site Recovery-tárolónak ugyanabban a régióban kell lennie.


### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

A Site Recovery az Azure storage replikálja a virtuális gép adatait. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során.

1. Az [Azure Portal](https://portal.azure.com) menüjében válassza az **Új** > **Tárolás** > **Tárfiók** lehetőséget.
2. A **Tárfiók létrehozása** területen írja be a fiók nevét. Ezekben az oktatóanyagokban használja a **contosovmsacct1910171607** nevet. A névnek egyedinek kell lennie az Azure-ban, 3–24 karakter közé kell esnie, továbbá csak számokat és kisbetűket tartalmazhat.
3. Az **Üzemi modell** mezőben válassza a **Resource Manager** lehetőséget.
4. A **Fiók típusa** mezőben válassza az **Általános célú** lehetőséget. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget. Ne válasszon blob-tárolót.
5. A **Replikáció** mezőben válassza az alapértelmezett **Írásvédett georedundáns tárolás** értéket a tárhely-redundanciához.
6. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.
7. Az **Erőforráscsoport** mezőben adjon meg egy új erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ezekben az oktatóanyagokban használja a **ContosoRG** nevet.
8. A **Hely** mezőben válassza ki a tárfiók földrajzi helyét. A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Ebben a forgatókönyvben használunk a **USA keleti régiója 2** régióban.

   ![Create a storage account](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

### <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Monitoring és felügyelet** > **Backup és Site Recovery** lehetőséget.
2. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. Ehhez az oktatóanyaghoz használja a **ContosoVMVault** nevet.
3. Az **Erőforráscsoport** mezőben válassza ki a **contosoRG** nevű meglévő erőforráscsoportot.
4. A **hely**, adja meg az Azure-régió **USA keleti régiója 2**.
5. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.
Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>6. lépés: A Site Recovery a helyszíni VMware előkészítése

VMware előkészítése a Site Recovery, itt mit kell tennie:

- Fiók előkészítése a vCenter-kiszolgálón vagy vSphere ESXi-gazdagépen a virtuális gépek felderítésének automatizálásához
- Fiók előkészítése a mobilitási szolgáltatás VMware virtuális gépekre való automatikus telepítéséhez
- Készítse elő a helyszíni virtuális gépek, ha az Azure virtuális gépen a feladatátvételt követően csatlakozni szeretne.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Virtuális gépek automatikus észlelését. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Olyan műveletek, például a létrehozása és lemezek eltávolítása, és bekapcsolja a virtuális gépeket futtató fiók szükséges.

Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adjon meg egy nevet, például: **Azure_Site_Recovery**.
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

**Tevékenység** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).
**Teljes replikáció, feladatátvétel, feladat-visszavétel** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt virtuális gépen.

- A Site Recovery hajthatja végre az automatikus ügyfélleküldéses telepítést, az összetevő, ha engedélyezi a virtuális gép replikációs.
- Az automatikus ügyfélleküldéses telepítést elő kell készíteni egy Site Recovery eléréséhez a virtuális gép által használt fiók.
- Ezt a fiókot az Azure-konzolon a replikációs beállításához adja meg.
- Tartomány vagy helyi fiók telepítése a virtuális Gépre van szüksége.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Az Azure-bA feladatátvétel után érdemes az Azure-ban a replikált virtuális gépek csatlakozni a helyi hálózatról.

Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni a Windows virtuális gépekhez, tegye a következőket:

1. Ha internetes hozzáférést kíván használni, engedélyezze az RDP-t a helyszíni virtuális gépen a feladatátvétel előtt. Győződjön meg arról, hogy a TCP és UDP-szabályok hozzáadása történik meg a a **nyilvános** profilt, és, hogy engedélyezi-e az RDP a **Windows tűzfal** > **engedélyezett alkalmazások** minden profil esetében.
2. Ha helyek közötti VPN-kapcsolatot kíván használni, engedélyezze az RDP-t a helyszíni gépen. Engedélyezze az RDP-t a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén a **Tartomány és Privát** hálózatok számára.
3. Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információk](https://support.microsoft.com/kb/3031135).
4. A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, a frissítés befejeződéséig nem fog tudni bejelentkezni a virtuális gépre.
5. A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>7. lépés: A Site Recovery virtuális gépek replikálása

### <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Recovery Services-tárolók** listából válassza ki a **ContosoVMVault** nevű tárat.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

    ![OVF-sablon](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Győződjön meg róla, központi telepítésének megtervezése

A **üzembe helyezésének tervezése**, kattintson a **Igen, elvégeztem**, a legördülő listából.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása


A forráskörnyezet telepítéséhez egyetlen magas rendelkezésre állású helyszíni gépre van szükség a helyszíni Site Recovery-összetevők futtatásához. Összetevők közé tartoznak a konfigurációs kiszolgáló és a folyamatkiszolgáló.

- A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.


A konfigurációs kiszolgáló beállítása a magas rendelkezésre állású VMware virtuális gépként:

- Előkészített nyitott virtualizációs formátum (OVF) sablon letöltése.
- Importálja a sablont a VMware virtuális gép létrehozása.
- Állítsa be a konfigurációs kiszolgáló, és regisztrálja őket a tárolóban lévő állapottal. 

A regisztrációt követően a Site Recovery felderíti a helyszíni VMware virtuális gépeket.



#### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.

    ![Sablon letöltése](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
2. Töltse le a konfigurációs kiszolgáló OVF-sablonját.

    ![OVF letöltése](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  A konfigurációs kiszolgálói sablon legújabb verzióját közvetlenül letöltheti a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter server, a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához. 

     ![OVF-sablon](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

    * Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** (Bekapcsolás a telepítést követően) beállítás jelölését. Ezután kattintson a **Befejezés** gombra. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.



#### <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.

    ![OVF-sablon](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.

    ![OVF-sablon](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Beállítások konfigurálása és a VMware-kiszolgáló hozzáadása

1. A konfigurációs kiszolgáló felügyeleti varázslójában válassza a **Setup connectivity** (Kapcsolat beállítása) gombot, majd válassza ki a hálózati adaptert, amely a replikációs forgalmat fogadja. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás a konfigurálás után nem módosítható.
2. A **Helyreállítási tár kiválasztása** területen válassza ki az Azure-előfizetést, valamint a megfelelő erőforráscsoportot és tárolót.

    ![Tároló](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
4. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
5. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
6. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás** elemet.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. A **Virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a mobilitási szolgáltatás gépekre való automatikus telepítéséhez használt felhasználónevet és jelszót, ha a replikáció engedélyezve van. A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez. 
8. A regisztráció befejezését követően ellenőrizze az Azure Portalon, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepelnek-e a tároló **Forrás** lapján. Ezután válassza az **OK** gombot a célbeállítások konfigurálásához.
9. A Site Recovery a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemet.

### <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. A **infrastruktúra előkészítése** > **cél**, válassza ki a használni kívánt Azure-előfizetést.
2. A cél telepítési modell kiválasztása **Azure Resource Manager**.

3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend**, kattintson a **hozhat létre és társíthat** .
1. A **Replikációs szabályzat létrehozása** területen adja meg a szabályzat nevét: **VMwareRepPolicy**.
2. A **Helyreállítási időkorlát küszöbértéke** beállításnál használja az alapértelmezett 60 percet. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
3. A **Helyreállítási pont megőrzése** beállításnál használja az alapértelmezett 24 óra értéket, ami azt mutatja, hogy a rendszer milyen hosszan őrzi meg az egyes helyreállítási pontokat. A jelen oktatóanyag elvégzéséhez érdemes 72 órát megadnia. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
4. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításnál használja az alapértelmezett 60 perc értéket, az alkalmazáskonzisztens pillanatképek létrehozásának gyakoriságára. A szabályzat létrehozásához válassza az **OK** gombot.

    ![Replikációs házirend létrehozása](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs szabályzat társítása](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>A replikáció engedélyezése

Most indítsa el a virtuális gépek replikálása. A Site Recovery telepíti a mobilitási szolgáltatás egyes virtuális gépek replikációját engedélyezésekor. 


1. Válassza az **Alkalmazás replikálása** > **Forrás** elemet.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet.
5. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezután kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. Kattintson a feladatátvételi virtuális gépekre használni kívánt Azure üzembehelyezési modellre (klasszikus vagy Resource Manager).
2. Válassza ki az adatok replikálásához használni kívánt Azure Storage-fiókot.
3. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
4. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre.
5. Jelölje ki az alhálózatot a virtuális hálózat. Ezután kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. A **virtuális gépek** > **válassza ki a virtuális gépek**, válassza ki a virtuális gép (WEBVM), amelyet. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. 

    ![A replikáció engedélyezése](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. A hozzáadott virtuális gépek monitorozásához ellenőrizze a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók** > **Legutóbbi kapcsolat** területen. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet. 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
8. A **Tulajdonságok** > **Tulajdonságok konfigurálása** mezőben válassza ki a fiókot, amelynek használatával a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen. 

    ![A replikáció engedélyezése](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva.
10. Válassza ki a **Replikáció engedélyezése** elemet.

    ![A replikáció engedélyezése](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Előrehaladását úgy követheti nyomon a **Védelemengedélyezési** feladat **beállítások** > **feladatok** > **Site Recovery-feladatok**. 
12. Miután a **Védelemvéglegesítési** feladat fut, a gép készen áll a feladatátvételt, és megjelenik **áttekintése** > **Essentials**.

    ![Alapvető erőforrások](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>8. lépés: A DMS adatbázis áttelepítése

A DMS-projekt létrehozása, és futtassa az áttelepítés.


### <a name="create-a-database-migration-project"></a>Az adatbázis áttelepítése-projekt létrehozása

1. Keresse meg a DMS erőforrást az Azure portálon, és nyissa meg.
2. Válassza ki **+ új áttelepítési projekt**.
3. A **új áttelepítési projekt**, adja meg a projekt nevét.
4. A **Forráskiszolgáló típusa** mezőben válassza az **SQL Server** lehetőséget. A **server Céltípust**, jelölje be **Azure SQL adatbázis felügyelt példány**.
5. Kattintson a **létrehozása** a projekt létrehozásához.
6. A **részletek forrás**, adja meg a kapcsolódási adatait, a helyszíni adatforrás SQL Server. Kattintson a **Save** (Mentés) gombra.
7. A **céloz részletek**, adja meg a célként, ami a előtti ponton aktívvá vált Azure SQL adatbázis felügyelt példány, amelyre át lesz telepítve a helyi adatbázis a kapcsolódási adatait. Ezután kattintson a **Save** (Mentés) gombra.
8. A **projekt összefoglalása**, tekintse át, és ellenőrizze az áttelepítési projekttel kapcsolatos részleteket.

    ![DMS projekt](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Az adatbázis áttelepítése

1. Miután létrehozta a projektet, a varázsló jelenik meg.
2. Adja meg a hitelesítő adatokat a forrás és cél kiszolgálókat, és kattintson a **mentése**. A varázsló megpróbál bejelentkezni a helyszíni SQL Server.

    ![DMS varázsló](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. A **céladatbázisokhoz térkép**, válassza ki a forrás adatbázist szeretne áttelepíteni. Ezután kattintson a **Save** (Mentés) gombra.

    ![DMS varázsló](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. A **céloz részletek**, jelölje be **tudható, hogy a célként megadott részletek**. Adja meg az SQL felügyelt példányok, és a hitelesítő adatok a DNS-nevét. Ezután kattintson a **Save** (Mentés) gombra.

    ![DMS varázsló](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. Válassza ki a mentett projekt **+ új tevékenység**. Válassza ki **áttelepítés futtatása**.
6. Amikor a rendszer kéri, adja meg a hitelesítő adatait a forrás és cél kiszolgálókat. Ezután kattintson a **Save** (Mentés) gombra.
7. A **céladatbázisokhoz térkép**, válassza ki a forrás-adatbázist, amelyet át szeretne. Kattintson a **mentése**
8. A **konfigurálni az áttelepítési beállításokat** > **biztonsági mentési helye**, adja meg a hálózati megosztáshoz a helyi számítógépen létrehozott. DMS forrás biztonsági mentések erre a megosztásra vesz igénybe.  Ne feledje, hogy az adatforrás SQL Server-példányt futtató szolgáltatásfiókot a megosztás írási jogosultsággal kell rendelkeznie.
9. Adja meg a felhasználónevet és jelszót, amely a DMS megszemélyesíthet-e a biztonsági mentési fájlok feltöltése az Azure storage-tárolóhoz, a visszaállítás.
10. Adja meg a SAS URI-t, amely a DMS hozzáférést biztosít számára a fiók a tároló, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat, és az Azure SQL adatbázis felügyelt példányra áttelepítéshez.  Ezután kattintson a **Save** (Mentés) gombra.
11. A **áttelepítési összegzés**, adja meg az áttelepítési tevékenység nevét > **az áttelepítés**.
12. A Projekt > **áttekintése**, az áttelepítési állapotának figyelésére. Az áttelepítés befejezése után győződjön meg arról, hogy létezik-e a céladatbázisokhoz felügyelt-példányon.


## <a name="step-9-migrate-vms-with-site-recovery"></a>9. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése

Javasoljuk, hogy győződjön meg arról, hogy minden a várt módon működik, mielőtt elindítaná a teljes áttelepítés feladatátvételi teszt futtatása. Feladatátvételi teszt futtatásakor a következő történik:

1. Egy Előfeltételek ellenőrzése során, hogy az áttelepítéshez szükséges feltételek vannak érvényben.
2. A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
3. A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A virtuális gép tulajdonságlapján kattintson **+ feladatátvételi teszt**.

    ![A feladatátvételi teszt](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Válassza ki **legújabb feldolgozott**, hogy áthelyezze a feladatokat a virtuális gép a legutóbbi pontnak időben. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
2. A **Feladatátvételi teszt** területen válassza ki, hogy az Azure virtuális gépek mely cél Azure-hálózathoz csatlakozzanak majd a feladatátvételt követően.
3. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Folyamatban van a tárolóban nyomon követheti > **beállítások** > **feladatok** > **feladatátvételi teszt**.
4. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban.
5. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.

### <a name="migrate-the-machines"></a>A gépek áttelepítése

Miután ellenőrizte, hogy, hogy a feladatátvételi teszt megfelelően működik-e, hozzon létre egy helyreállítási terv áttelepítése az Azure-bA.

### <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. Válassza ki a tárolóval, **helyreállítási tervek (helyreállítás)** > **+ a helyreállítási terv**.
2. A **helyreállítási terv létrehozása**, adja meg a csomag nevét.
3. Válassza ki a forrás konfigurációs kiszolgáló, és az Azure és a cél. Válassza ki **erőforrás-kezelő** telepítési modell. A forráshely kell rendelkeznie a gépek, amelyek engedélyezve vannak feladatátvételét és helyreállítását. 
4. A **elemek kijelölése**, vegye fel a gépek (WEBVM) a tervbe. Ezután kattintson az **OK** gombra.
5. Kattintson a **OK** a terv létrehozásához.

    ![Helyreállítási terv](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Feladatátvétel futtatása

1. A **helyreállítási tervek**, kattintson a terv > **feladatátvételi**.
2. A **feladatátvételi** > **helyreállítási pont**, válassza ki a legutóbbi helyreállítási pontot.
3. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási kulcs beállítását.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a forrás virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

    ![Feladatátvétel](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.

    ![Feladatátvétel](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Migrálás befejezése** parancsra. Ez befejezi a migrálási folyamatot, valamint leállítja a virtuális gép replikálását és a virtuális gép Site Recovery-számlázását.

    ![Feladatátvétel](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Frissítse a kapcsolati karakterlánc

Az áttelepítési folyamat utolsó lépésként frissítése érdekében szükség, hogy az áttelepített adatbázis az SQL MI futó mutasson az alkalmazás a kapcsolati karakterláncot.

1. Keresse meg az új kapcsolati karakterláncot az Azure portálon kattintva **beállítások** > **kapcsolati karakterláncok**.

    ![Feladatátvétel](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. Ez a karakterlánc a felhasználónév és jelszó frissítése az SQL MI kell.
3. A karakterlánc konfigurálása után szükség az alkalmazás web.config fájljában aktuális kapcsolati karakterlánc.
4. A fájl frissítése, és mentse, után indítsa újra az IIS a WEBVM a. Ezt megteheti a parancssort az IISRESET /RESTART használatával.
5. IIS újraindítása után, az alkalmazás fogja most a adatbázist használ a SQL MI futó.
6. Ezen a ponton a SQLVM gép helyszíni is le kell állítani, és az áttelepítés akkor fejeződött be.



## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben:

> [!div class="checklist"]
> * A helyi adatbázis áttelepítése az Azure SQL-felügyelt példányba DMS.
> * A helyszíni virtuális gépek áttelepítése az Azure virtuális gépen, az Azure Site Recovery szolgáltatással.
