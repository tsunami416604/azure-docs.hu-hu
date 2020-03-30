---
title: Helyek közötti (S2S) VPN konfigurálása az Azure Files szolgáltatáshoz | Microsoft dokumentumok
description: A helyek közötti (S2S) VPN konfigurálása az Azure Files használatához
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae3d38d92990d7a1af4146c25b017286ebd29352
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061042"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Helyek közötti VPN konfigurálása az Azure Files szolgáltatáshoz
A helyek közötti (S2S) VPN-kapcsolat segítségével csatlakoztathatja az Azure-fájlmegosztásokat az SMB-n keresztül a helyszíni hálózatról, a 445-ös port megnyitása nélkül. Beállíthat egy helyek közötti VPN-t [az Azure VPN-átjáró](../../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával, amely egy Azure-erőforrás, amely VPN-szolgáltatásokat kínál, és a tárfiókok vagy más Azure-erőforrások mellett egy erőforráscsoportban van telepítve.

![Egy Azure VPN-átjáró topológiáját bemutató topológia, amely Egy Azure-fájlmegosztást csatlakoztat egy helyszíni webhelyhez S2S VPN használatával](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Azt javasoljuk, hogy olvassa el [az Azure Files hálózati áttekintést,](storage-files-networking-overview.md) mielőtt folytatná ezt a cikket az Azure Files elérhető hálózati lehetőségek teljes körű megvitatására.

A cikk ismerteti a lépéseket, hogy konfigurálja a helyek közötti VPN-t az Azure-fájlmegosztások közvetlenül a helyszínen csatlakoztatásához. Ha az Azure File Sync szinkronizálási forgalmát webhelyről webhelyre vpn-en keresztül szeretné irányítani, olvassa el [az Azure File Sync proxy és a tűzfal beállításainak konfigurálása című témakört.](storage-sync-files-firewall-and-proxy.md)

## <a name="prerequisites"></a>Előfeltételek
- Egy Azure-fájlmegosztást szeretne csatlakoztatni a helyszíni. Az Azure-fájlmegosztások a tárfiókokon belül vannak üzembe helyezve, amelyek olyan felügyeleti konstrukciók, amelyek egy megosztott tárolókészletet képviselnek, amelyben több fájlmegosztást, valamint más tárolási erőforrásokat, például blobtárolókat vagy várólistákat helyezhet üzembe. Az Azure-fájlmegosztások és tárfiókok üzembe helyezéséről az [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című részben olvashat bővebben.

- A helyszíni csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók privát végpontja. Ha többet szeretne tudni a rról, hogyan hozhat létre privát [végpontot, olvassa el az Azure Files hálózati végpontok konfigurálása című témakört.](storage-files-networking-endpoints.md?tabs=azure-portal) 

- Hálózati berendezés vagy kiszolgáló a helyszíni adatközpontban, amely kompatibilis az Azure VPN-átjáróval. Az Azure Files a helyszíni hálózati berendezés választott, de az Azure VPN Gateway a [tesztelt eszközök listáját](../../vpn-gateway/vpn-gateway-about-vpn-devices.md)vezeti. A különböző hálózati készülékek különböző funkciókat, teljesítményjellemzőket és felügyeleti funkciókat kínálnak, ezért vegye figyelembe ezeket a hálózati készülék kiválasztásakor.

    Ha nem rendelkezik meglévő hálózati berendezéssel, a Windows Server beépített kiszolgálói szerepkört, útválasztást és távelérést (RRAS) tartalmaz, amely helyszíni hálózati eszközként is használható. Az Útválasztás és távelérés Windows Server rendszerben történő konfigurálásáról a [RAS-átjáró című témakörben](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway)olvashat bővebben.

## <a name="add-storage-account-to-vnet"></a>Tárfiók hozzáadása a virtuális hálózathoz
Az Azure Portalon keresse meg a helyszíni csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiókot. A tárfiók tartalomjegyzékében jelölje ki a **Tűzfalak és** a virtuális hálózatok bejegyzést. Ha nem adott hozzá virtuális hálózatot a tárfiókhoz a létrehozásakor, az eredményül kapott ablaktáblán ki kell jelölni a **Hozzáférés engedélyezése** a választóból gombot a **Minden hálózathoz.**

Ha hozzá szeretné adni a tárfiókot a kívánt virtuális hálózathoz, válassza a **Kijelölt hálózatok**lehetőséget. A **Virtuális hálózatok** alcím alatt kattintson a **+ Meglévő virtuális hálózat hozzáadása** vagy a **+Új virtuális hálózat hozzáadása** a kívánt állapottól függően elemre. Új virtuális hálózat létrehozása egy új Azure-erőforrás létrehozását eredményezi. Az új vagy meglévő virtuális hálózat-erőforrásnak nem kell ugyanabban az erőforráscsoportban vagy előfizetésben lennie, mint a tárfióknak, azonban ugyanabban a régióban kell lennie, mint a tárfiók, és az erőforráscsoport és az erőforráscsoport és az előfizetés, amelyben a virtuális hálózatüzembe helyezése meg kell egyeznie azzal, amit meg fog a VPN-átjáró üzembe helyezését. 

![Képernyőkép az Azure Portalról, amely lehetővé teszi egy meglévő vagy új virtuális hálózat hozzáadását a tárfiókhoz](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Ha meglévő virtuális hálózatot ad hozzá, a rendszer megkéri, hogy válasszon ki egy vagy több alhálózatot annak a virtuális hálózatnak, amelyhez a tárfiókot hozzá kell adni. Ha új virtuális hálózatot választ, a virtuális hálózat létrehozásának részeként alhálózatot hoz létre, és később további lehetőségeket adhat hozzá a virtuális hálózathoz létrejövő Azure-erőforráson keresztül.

Ha korábban még nem adott hozzá tárfiókot az előfizetéshez, a Microsoft.Storage szolgáltatás végpontját hozzá kell adni a virtuális hálózathoz. Ez eltarthat egy ideig, és amíg ez a művelet befejeződik, nem fogja tudni elérni az Azure-fájlmegosztások az adott tárfiókon belül, beleértve a VPN-kapcsolaton keresztül. 

## <a name="deploy-an-azure-vpn-gateway"></a>Azure VPN-átjáró üzembe helyezése
Az Azure Portal tartalomjegyzékében válassza az **Új erőforrás létrehozása lehetőséget,** és keressen *a Virtuális hálózati átjáró*t. A virtuális hálózati átjárónak ugyanabban az előfizetésben, Az Azure-régióban és az erőforráscsoportban kell lennie, mint az előző lépésben üzembe helyezett virtuális hálózatnak (vegye figyelembe, hogy az erőforráscsoport automatikusan kiválasztásra kerül a virtuális hálózat kiválasztásakor). 

Az Azure VPN-átjáró üzembe helyezése céljából a következő mezőket kell feltöltenie:

- **Név**: A VPN-átjáró Azure-erőforrásának neve. Ez a név bármilyen név lehet, amelyet hasznosnak talál a vezetőség számára.
- **Régió**: Az a régió, amelyre a VPN-átjáró telepítve lesz.
- **Átjáró típusa**: A helyek közötti VPN telepítése céljából ki kell választania a **VPN-t.**
- **VPN-típus:** A VPN-eszköztől függően *választhat útvonalalapú** vagy **házirend-alapú.** Az útvonalalapú VPN-ek támogatják az IKEv2-t, míg a házirendalapú VPN-ek csak az IKEv1-et. A VPN-átjárók két típusáról a [Házirend-alapú és útvonalalapú VPN-átjárók – ismertető című témakörben olvashat bővebben.](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **Termékváltozat**: A termékváltozat szabályozza az engedélyezett helyek közötti alagutak számát és a VPN kívánt teljesítményét. A használati esetnek megfelelő termékváltozat kiválasztásához tekintse meg az [átjáró termékváltozatának](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) listáját. A VPN-átjáró termékváltozata szükség esetén később is módosítható.
- **Virtuális hálózat**: Az előző lépésben létrehozott virtuális hálózat.
- **Nyilvános IP-cím**: A VPN-átjáró IP-címe, amely elérhető lesz az interneten. Valószínűleg új IP-címet kell létrehoznia, de ha ez helyénvaló, használhat egy meglévő, nem használt IP-címet is. Ha az új létrehozása lehetőséget **választja,** egy új IP-cím Azure-erőforrás jön létre ugyanabban az erőforráscsoportban, mint a VPN-átjáró, és a **nyilvános IP-cím neve** lesz az újonnan létrehozott IP-cím neve. Ha a **Meglévő használata**lehetőséget választja, ki kell jelölnie a meglévő nem használt IP-címet.
- **Aktív-aktív mód engedélyezése**: Csak akkor válassza **az Engedélyezve** lehetőséget, ha aktív-aktív átjárókonfigurációt hoz létre, ellenkező esetben hagyja **a Letiltva lehetőséget.** Az aktív-aktív módról a [Magas rendelkezésre állású létesítmények közötti és a virtuális hálózat](../../vpn-gateway/vpn-gateway-highlyavailable.md)közötti kapcsolat .
- **A BGP ASN konfigurálása:** Csak akkor válassza **az Engedélyezve lehetőséget,** ha a konfigurációnak kifejezetten szüksége van erre a beállításra. A beállításról a [BGP az Azure VPN-átjáróval kapcsolatban.](../../vpn-gateway/vpn-gateway-bgp-overview.md)

A VPN-átjáró létrehozásához válassza a **Véleményezés + létrehozás** lehetőséget. A VPN-átjáró teljes létrehozása és üzembe helyezése akár 45 percet is igénybe vehet.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Helyi hálózati átjáró létrehozása a helyszíni átjáróhoz 
A helyi hálózati átjáró egy Azure-erőforrás, amely a helyszíni hálózati berendezést képviseli. Az Azure Portal tartalomjegyzékében válassza az **Új erőforrás létrehozása lehetőséget,** és keressen *helyi hálózati átjárót.* A helyi hálózati átjáró egy Azure-erőforrás, amely a tárfiók, a virtuális hálózat és a VPN-átjáró mellett lesz telepítve, de nem kell ugyanabban az erőforráscsoportban vagy előfizetésben lennie, mint a tárfiók. 

A helyi hálózati átjáró erőforrás központi telepítése céljából a következő mezőket kell felnagyítania:

- **Név**: A helyi hálózati átjáró Azure-erőforrásának neve. Ez a név bármilyen név lehet, amelyet hasznosnak talál a vezetőség számára.
- **IP-cím:** A helyi átjáró nyilvános IP-címe a helyszínen.
- **Címterület**: A helyi hálózati átjáró által képviselt hálózat címtartományai. Több címterület-tartományt is hozzáadhat, de győződjön meg arról, hogy az itt megadott tartományok nem fedik át azokat a tartományokat, amelyekhez csatlakozni szeretne. 
- **BGP-beállítások konfigurálása:** Csak akkor adja meg a BGP-beállításokat, ha a konfigurációmegköveteli ezt a beállítást. A beállításról a [BGP az Azure VPN-átjáróval kapcsolatban.](../../vpn-gateway/vpn-gateway-bgp-overview.md)
- **Előfizetés**: A kívánt előfizetés. Ennek nem kell egyeznie a VPN-átjáróhoz vagy a tárfiókhoz használt előfizetéssel.
- **Erőforráscsoport**: A kívánt erőforráscsoport. Ennek nem kell egyeznie a VPN-átjáróhoz vagy a tárfiókhoz használt erőforráscsoporttal.
- **Hely:** Az Azure-régió a helyi hálózati átjáró erőforrás t kell létrehozni. Ennek meg kell egyeznie a VPN-átjáróhoz és a tárfiókhoz kiválasztott régióval.

A **Létrehozás gombra** a helyi hálózati átjáró-erőforrás létrehozásához válassza a Létrehozás lehetőséget.  

## <a name="configure-on-premises-network-appliance"></a>Helyszíni hálózati készülék konfigurálása
A helyszíni hálózati berendezés konfigurálásának konkrét lépései a szervezet által kiválasztott hálózati berendezéstől függenek. Attól függően, hogy a szervezet által választott [eszköz,](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) a tesztelt eszközök listája lehet egy linket az eszköz gyártójának az Azure VPN Gateway konfigurálására vonatkozó utasításokat.

## <a name="create-the-site-to-site-connection"></a>A helyek közötti kapcsolat létrehozása
Az S2S VPN telepítésének befejezéséhez létre kell hoznia egy kapcsolatot a helyszíni hálózati készülék (amelyet a helyi hálózati átjáró erőforrás képvisel) és a VPN-átjáró között. Ehhez keresse meg a fent létrehozott VPN-átjárót. A VPN-átjáró tartalomtáblájában válassza a **Kapcsolatok**lehetőséget, majd kattintson a **Hozzáadás**gombra. Az eredményül kapott **Kapcsolat hozzáadása** ablaktábla a következő mezőket igényli:

- **Név**: A kapcsolat neve. A VPN-átjáró több kapcsolatot is üzemeltethet, ezért válasszon egy olyan nevet, amely hasznos a vezetőség számára, és megkülönbözteti az adott kapcsolatot.
- **Kapcsolat típusa**: S2S-kapcsolat óta válassza a **helyek közötti (IPSec)** lehetőséget a legördülő listában.
- **Virtuális hálózati átjáró**: Ez a mező automatikusan be van jelölve arra a VPN-átjáróra, amelyhez a kapcsolatot létesíti, és nem módosítható.
- **Helyi hálózati átjáró**: Ez az a helyi hálózati átjáró, amelyet a VPN-átjáróhoz szeretne csatlakoztatni. Az eredményül kapott kijelölési ablaktáblának a fent létrehozott helyi hálózati átjáró nevét kell feljegyeznie.
- **Megosztott kulcs (PSK):** Betűk és számok keveréke, amely a kapcsolat titkosításának létrehozásához használatos. Ugyanazt a megosztott kulcsot kell használni mind a virtuális hálózat, mind a helyi hálózati átjárókban. Ha az átjáróeszköz nem biztosít ilyet, itt létrehozhat egyet, és biztosíthatja azt az eszközszámára.

A kapcsolat létrehozásához válassza az **OK gombot.** Ellenőrizheti, hogy a kapcsolat sikeresen létrejött-e a **Kapcsolatok** lapon.

## <a name="mount-azure-file-share"></a>Az Azure fájlmegosztásának csatlakoztatása 
Az S2S VPN konfigurálásának utolsó lépése annak ellenőrzése, hogy működik-e az Azure Files esetében. Ezt úgy teheti meg, hogy az Azure-fájlmegosztást a kívánt operációs rendszerrel a helyszínen csatlakoztatja. Lásd az utasításokat, hogy felmászik az operációs rendszer itt:

- [Windows](storage-how-to-use-files-windows.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Lásd még
- [Az Azure Files hálózati áttekintése](storage-files-networking-overview.md)
- [Pont-hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files használatával](storage-files-configure-p2s-vpn-windows.md)
- [Pont-hely (P2S) VPN konfigurálása Linuxon az Azure Files-szal való használatra](storage-files-configure-p2s-vpn-linux.md)