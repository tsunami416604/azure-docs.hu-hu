---
title: Az Azure Stack 1808 frissítés |} A Microsoft Docs
description: További tudnivalók what's new in 1808 frissítés az Azure Stack integrált rendszerek, beleértve az ismert problémákat és hová töltse le a frissítést.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 1e2a3221b6db5becbea49aa6ec5fee7b145e8e03
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295394"
---
# <a name="azure-stack-1808-update"></a>Azure Stack 1808 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a 1808 csomag tartalmát. A csomag magában foglalja a fejlesztések, javításokat és ismert problémái az Azure Stack jelen verziójában. Ez a cikk egy hivatkozást is tartalmaz, így a frissítés letölthető. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1808 frissítés buildszáma **1.1808.0.97**.  

### <a name="new-features"></a>Új funkciók

Ez a frissítés az Azure Stack a következő fejlesztéseket tartalmazza.

- <!--  2682594   | IS  -->  **Minden Azure Stack-környezet már használja az egyezményes világidő (UTC) időzóna formátumot.**  Összes adatok naplózása, és most már a kapcsolódó információkat UTC formátumban jeleníti meg. Ha egy előző verziójáról, amely nem lett telepítve az UTC frissíti, a környezet frissül (UTC) használata. 

- <!-- 2437250  | IS  ASDK --> **Felügyelt lemezek használata támogatott.** Az Azure Stack virtuális gépek és virtuálisgép-méretezési csoportok most már felügyelt lemez is használható. További információkért lásd: [Azure Stack-Managed Disks: különbségek és szempontok](/azure/azure-stack/user/azure-stack-managed-disk-considerations).

- <!-- 2563799  | IS  ASDK -->  **Az Azure Monitor**. Azure Monitor az Azure-ban, mint például az Azure Monitor az Azure Stacken biztosít a legtöbb szolgáltatás alapszinten – infrastruktúra-metrikák és naplók. További információkért lásd: [Azure Monitor az Azure Stacken](/azure/azure-stack/user/azure-stack-metrics-azure-data).

- <!-- 2487932| IS -->  **A bővítmény gazdagép előkészítése**. A bővítmény gazdagép segítségével biztonságos Azure Stack segítségével a szükséges TCP/IP-portok számának csökkentésével. A 1808 a frissítés előkészítése, bővítmény gazdagép Felkészülés az Azure Stack. További információkért lásd: [előkészítése az Azure stack-bővítmény gazdagép](/azure/azure-stack/azure-stack-extension-host-prepare).

- <!-- IS --> **A Virtual Machine Scale Sets katalóguselemek immár beépített**.  A Virtual Machine Scale Set gyűjteményelem elérhetővé válik, a felhasználói és rendszergazdai portálokon nélkül, ahonnan letöltheti azt.  Ha frissít, 1808 érhető el frissítés befejezése után.  

- <!-- IS, ASDK --> **Skálázás virtuálisgép-méretezési**.  A portálon való [méretezése egy virtuálisgép-méretezési csoportban](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

- <!-- 2489570 | IS ASDK--> **Egyéni IPSec/IKE-házirend konfigurációk támogatása** a [VPN-átjárók az Azure Stackben](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Kubernetes Piactéri elem**. Kubernetes-fürtök használatával most már telepítheti a [Kubernetes Piactéri elem](azure-stack-solution-template-kubernetes-cluster-add.md). Felhasználók válassza ki a Kubernetes-elemet, és töltse ki a Kubernetes-fürt üzembe helyezése az Azure Stackhez néhány paramétert. A sablonok az a célja, hogy egyszerűsítsen felhasználóknak, hogy a telepítő fejlesztési-tesztelési Kubernetes-üzembehelyezés néhány lépésben.

- <!-- | IS ASDK--> **Blockchain-sablonok**. Most már futtathat [Ethereum consortium központi telepítések](azure-stack-ethereum.md) az Azure Stacken. A három új sablonokat is megtalálhatja a [Azure Stack gyors üzembe helyezési sablonokat](https://github.com/Azure/AzureStack-QuickStart-Templates). A felhasználó telepítheti és konfigurálhatja egy többtagú consortium Ethereum hálózati minimális Azure-ban és az Ethereum ismeretekkel lehetővé teszik. A sablonok az a célja, hogy egyszerűsítsen felhasználóknak, hogy a telepítő fejlesztési-tesztelési Blockchain telepítések néhány lépésben.

- <!-- | IS ASDK--> **Az API verzió profil 2017-03-09-profile frissítve lett, hogy a 2018-03-01-hibrid**. API-profilok adja meg, az Azure erőforrás-szolgáltató és az Azure REST-végpontokat az API-verzió. Profilokkal kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](/azure/azure-stack/user/azure-stack-version-profiles).

 ### <a name="fixed-issues"></a>Hibák kijavítva:
- <!-- IS ASDK--> Azt javítva a rendelkezésre állási csoportot a portálon, amely eredményezett a egy tartalék tartomány és a frissítési tartomány 1 csoport létrehozásához. 

- <!-- IS ASDK --> Beállítás virtuálisgép-méretezési csoportok méretezése a portálon érhető el.  

- <!-- 2494144- IS, ASDK --> Most, hogy néhány F-sorozatú virtuális gépek méretei nem jelenik meg, amikor a központi telepítés a virtuális gépek méretének probléma megoldódik. 

- <!-- IS, ASDK --> A teljesítmény, amikor létrehozza a virtuális gépek és egyéb optimalizált fejlesztések az alapul szolgáló tárolóról használja.

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer.


### <a name="changes"></a>Módosítások
- <!-- 1697698  | IS, ASDK --> *Gyorsútmutatók* az ide tartozó cikkekre az online az Azure Stack dokumentáció a felhasználói portál irányítópultján most hivatkozásra.

- <!-- 2515955   | IS ,ASDK--> *Az összes szolgáltatás* váltja fel *további szolgáltatások* az az Azure Stack rendszergazdai és felhasználói portálon. Most már használhatja *minden szolgáltatás* helyett az Azure Stack portálon lépjen a ugyanúgy, mint az az Azure Portalon teheti meg.

- <!-- TBD | IS, ASDK --> *+ Erőforrás létrehozása* váltja fel *+ új* az az Azure Stack rendszergazdai és felhasználói portálon.  Most már használhatja *+ erőforrás létrehozása* helyett az Azure Stack portálon lépjen a ugyanúgy, mint az az Azure Portalon teheti meg.  

- <!--  TBD – IS, ASDK --> *Alapszintű A* virtuálisgép-méretek esetében kivezettük [virtuálisgép-méretezési csoportok létrehozása](azure-stack-compute-add-scalesets.md) (VMSS) a portálon keresztül. Ez a méret a VMSS létrehozásához, használja a PowerShell vagy a sablont.  

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Ez a frissítés telepítése a következő frissítéseket:  

- [CVE – 2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE – 2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE – 2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE – 2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE – 2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE – 2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE – 2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE – 2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE – 2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE – 2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE – 2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE – 2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE – 2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE – 2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE – 2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE – 2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE – 2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis [4343887](https://support.microsoft.com/help/4343887).

Emellett tartalmazza a kockázatcsökkentési spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna biztonsági rés ismertetett terminálon L1 tartalék (L1TF), más néven a [Microsoft biztonsági tanácsadó ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Előfeltételek

- Az Azure Stack telepítése [1807 frissítése](azure-stack-update-1807.md) az Azure Stack 1808 frissítés alkalmazása előtt. 

- Telepítse a legújabb elérhető [frissítést vagy gyorsjavítást verzió 1807](azure-stack-update-1807.md#post-update-steps).  
  > [!TIP]  
  > Fizessen elő a következő *RRS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Az Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) ellenőrzése az Azure Stack állapotát, és hárítson el minden működési problémát talál, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.  

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md) a 1808 frissítés után az alaplapi felügyeleti vezérlő (BMC) figyelmeztető üzenet jelenik meg. Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások *hiba – a sablon typu FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. A frissítés telepítésének befejezése után ezek a riasztások automatikusan bezáródik.

- <!-- 2489559 - IS --> Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. Frissítések kezelésével kapcsolatos további információkért lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).

- <!-- 2830461 - IS --> Bizonyos körülmények között, ha egy frissítéshez a figyelmet, a megfelelő riasztás lehetséges, hogy nem hozható létre. A pontos állapota továbbra is megjelennek a portálon, és nem változik.

### <a name="post-update-steps"></a>Frissítés utáni lépések
Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információt a következő cikkeket, megtekintése, valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md). 
- [KB 4464226 – az Azure Stack gyorsjavítás az Azure Stack gyorsjavítás 1.1808.1.99]( https://support.microsoft.com/help/4464226)


## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

- <!-- TBD - IS ASDK --> Előfordulhat, hogy megjelenik egy üres irányítópult, a portálon. Az irányítópult helyreállítani, kattintson **irányítópult szerkesztése**, majd kattintson a jobb gombbal, és válassza ki **visszaállítja az alapértelmezett állapotba**.

- <!-- 2930718 - IS ASDK --> A felügyeleti portálon, minden felhasználói előfizetés részleteinek elérésekor után bezárja a panelt, és kattintson a **legutóbbi**, a felhasználói előfizetés neve nem jelenik meg.

- <!-- 3060156 - IS ASDK --> A rendszergazda és a felhasználói portált, kattintson a portál beállításait, és válassza a **törli az összes beállítás és saját irányítópult** nem a várt módon működik. Egy hiba értesítés jelenik meg. 

- <!-- 2930799 - IS ASDK --> A rendszergazda és a felhasználói portálon a **minden szolgáltatás**, az eszköz **DDoS potection-tervek** nem megfelelően szerepel. Ez nem érhető valójában az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg arról, hogy a portál nem tudta létrehozni a Piactéri elemet. 

- <!-- 2930820 - IS ASDK --> A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem érhető valójában az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

- <!-- 2967387 – IS, ASDK --> A fiók használatával jelentkezzen be az Azure Stack rendszergazdai vagy a felhasználói portálon jelenik meg **azonosítatlan felhasználó**. Ez akkor fordul elő, ha a fiók nem rendelkezik vagy egy *első* vagy *utolsó* megadott név. A probléma megkerüléséhez az első vagy utolsó nevét adja meg a felhasználói fiók szerkesztésével. Meg kell majd jelentkezzen ki, majd majd jelentkezzen be újra a portálra. 

-  <!--  2873083 - IS ASDK --> Amikor a portal használatával hozzon létre egy virtuálisgép-méretezési csoport beállítása (VMSS), a *példány mérete* legördülő továbbra sem töltődik be megfelelően az Internet Explorer használata esetén. Ez a probléma megkerüléséhez használja egy másik böngészőben egy VMSS létrehozása a portál használata során.  

- <!-- 2931230 – IS  ASDK --> Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

- <!--2760466 – IS  ASDK --> Amikor telepít egy új Azure Stack-környezet, amely ebben a verzióban fut, a riasztás azt jelzi, *aktiválás szükséges* nem jelenítik meg. [Az aktiválás](azure-stack-registration.md) marketplace szindikálási használatához szükség.  

- <!-- TBD - IS ASDK --> A két felügyeleti előfizetés adattípusokat [verziójú 1804-es verzióban bevezetett](azure-stack-update-1804.md#new-features) nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

- <!-- TBD - IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- <!-- TBD - IS ASDK --> Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.


### <a name="health-and-monitoring"></a>Állapot és figyelés
- <!-- 1264761 - IS ASDK -->  A riasztásokat a **egészségügyi vezérlő** összetevő, amely rendelkezik a következő adatokat:  

   #1 riasztás:
   - NAME: Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Állapotfigyelő vezérlő
   - Leírás: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

  Riasztási #2:
   - NAME: Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Állapotfigyelő vezérlő
   - Leírás: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és automatikusan fog bezárása idővel.  


- <!-- 2812138 | IS --> Láthatja, hogy egy riasztás **tárolási** összetevő, amely rendelkezik a következő adatokat:

   - NAME: Storage szolgáltatás belső kommunikációs hiba  
   - SÚLYOSSÁG: kritikus  
   - ÖSSZETEVŐ: Storage  
   - Leírás: A Storage szolgáltatás belső kommunikációs hiba történt, amikor kéréseket küld az alábbi csomópontok.  

    A riasztás biztonságosan figyelmen kívül hagyható, de a riasztás lezárása manuálisan kell.

- <!-- 2368581 - IS. ASDK --> Az Azure Stack operátorait, ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés egy **Fabric Virtuálisgép-létrehozási hiba**, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz.


### <a name="compute"></a>Compute
- <!-- 2869209 – IS, ASDK --> Használatakor a [ **Add-AzsPlatformImage** parancsmag](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), kell használnia a **- OsUri** paramétert, a tárfiók URI, ahol fel a rendszer a lemezen. Ha helyi elérési útját a lemezt használ, a parancsmag a következő hibával meghiúsul: *hosszú ideig futó művelet sikertelen volt, "Sikertelen" állapotú*. 

- <!--  2966665 – IS, ASDK --> A prémium szintű méretre SSD adatlemezek csatolására felügyelt lemez (DS, DSv2, Fs, Fs_V2) virtuális gépek sikertelen lesz, és hiba: *nem sikerült frissíteni a lemezt a virtuális gép "vmname" hiba: a kért művelet nem hajtható végre, mert a tárfiók típusa " Virtuálisgép-méret esetében nem támogatott a Premium_LRS' "Fs_v2 Standard_DS/Ds_V2 és FS)*

   A probléma megkerüléséhez használja *Standard_LRS* adatlemez, hanem *Premium_LRS lemezek*. Felhasználása *Standard_LRS* adatlemezek nem változik, iops-t vagy a számlázási költségeket. 

- <!--  2795678 – IS, ASDK --> Ha a portálon hozhat létre virtuális gépeket (VM) a prémium szintű virtuális gép méretét (DS, Ds_v2, FS, FSv2) használ, a virtuális gép létrejött, a standard szintű storage-fiókban. A standard szintű tárfiók-létrehozás nem befolyásolja, funkcionálisan IOPs, vagy számlázási. 

   Biztonságosan figyelmen kívül hagyhatja a figyelmeztetést, amely szerint: *, olyan méretnél, amely támogatja a prémium szintű lemezek használatát egy standard lemez használatát választotta. Ez ronthatja az operációs rendszer teljesítményét, és nem ajánlott. Fontolja meg inkább a prémium szintű tárolást (SSD) használatát.*

- <!-- 2967447 - IS, ASDK --> A virtuálisgép-méretezési csoportot (VMSS) hozzon létre felhasználói élményt 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzemelő példány egy másik operációs rendszer válasszon vagy adjon meg egy másik CentOS képet az operátor telepítené a marketplace-ről letöltött ARM-sablonokkal.  

- <!-- 2724873 - IS --> A PowerShell-parancsmagok használata esetén **Start-AzsScaleUnitNode** vagy **Stop-AzsScaleunitNode** skálázási egységek kezelése, indítása vagy leállítása a skálázási egység az első kísérlet sikertelen lehet. A parancsmag futtatása sikertelen, az első, futtassa a parancsmagot egy második alkalommal. A második Futtatás sikeres legyen, a művelet végrehajtásához. 

- <!-- TBD - IS ASDK --> Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- TBD - IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  

- <!-- 2724961- IS ASDK --> Amikor regisztrál a **Microsoft.Insight** előfizetési beállítások, az erőforrás-szolgáltató és a egy Windows virtuális gép létrehozása a vendég operációs rendszer diagnosztikai engedélyezve van, a Processzorhasználat (%) diagramon a virtuális gépek – Áttekintés lapon nem fogja tudni metrikai adatok megjelenítése.

   A virtuális gép számára a processzor diagram megkereséséhez nyissa meg a **metrikák** panel és a támogatott Windows VM show Vendég mérőszámok.



### <a name="networking"></a>Hálózat  

- <!-- 1766332 - IS ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

- <!-- 1902460 - IS ASDK --> Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- <!-- 16309153 - IS ASDK --> A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

- <!-- 2702741 -  IS ASDK --> Nyilvános IP-címek, a dinamikus kiosztási módszer használatával üzembe helyezett nem biztos, hogy egy állítsa le és vonja vissza kiadása után megőrzi.

- <!-- 2529607 - IS ASDK --> Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

-   <!-- 2664148 - IS ASDK --> Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró után átjárón már létre lett hozva. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

- <!-- 2352906 - IS ASDK --> Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- <!-- 2489178 - IS ASDK --> Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.



### <a name="usage"></a>Használat  
- <!-- TBD - IS ASDK --> Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure Stack 1808 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>További lépések
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).  
