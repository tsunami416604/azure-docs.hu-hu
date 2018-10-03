---
title: Az Azure Stack 1809 frissítés |} A Microsoft Docs
description: További tudnivalók what's new in 1809 frissítés az Azure Stack integrált rendszerek, beleértve az ismert problémákat és hová töltse le a frissítést.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 4b83d3743f65a3ad1b757c2e6dee8416701b24f3
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243114"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a 1809 csomag tartalmát. A csomag magában foglalja a fejlesztések, javításokat és ismert problémái az Azure Stack jelen verziójában. Ez a cikk egy hivatkozást is tartalmaz, így a frissítés letölthető. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1809 frissítés buildszáma **1.1809.x.xx**.  

### <a name="new-features"></a>Új funkciók

Ez a frissítés az Azure Stack a következő fejlesztéseket tartalmazza:

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog-ügyfél (nyilvánosan elérhetők)** az ügyfél lehetővé teszi, hogy a naplózás, a riasztások és a syslog server vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti szoftverek az Azure Stack infrastruktúrájának biztonsági naplók továbbítását külső az Azure Stackhez. A syslog-ügyfél már támogatja a portot, amelyen a syslog-kiszolgáló figyel.

Ebben a kiadásban a syslog-ügyfél általánosan elérhető, és az éles környezetben is használható.

További információkért lásd: [syslog-továbbítás az Azure Stack](azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Hibák kijavítva:

- <!-- 2702741 -  IS ASDK --> Kijavítva a hiba melyik nyilvános IP-címek, amelyek a dinamikus kiosztási használatával lettek telepítve a metódus nem garantált, hogy egy állítsa le és vonja vissza kiadása után megőrzi. Most már megmaradnak.

- <!-- 3078022 - IS ASDK --> Ha egy virtuális gép felszabadítva 1808 előtt nem lehet újból lefoglalni a 1808 frissítés után.  Ezt a problémát megoldottuk a 1809. A javítás 1809 is indítható el, amelyek ebben az állapotban vannak, és nem indítható el. A javítás is megakadályozza, hogy a probléma került.

### <a name="changes"></a>Módosítások

- <!-- 1697698  | IS, ASDK --> *Gyorsútmutatók* az ide tartozó cikkekre az online az Azure Stack dokumentáció a felhasználói portál irányítópultján most hivatkozásra.

- <!-- 2515955   | IS ,ASDK--> *Az összes szolgáltatás* váltja fel *további szolgáltatások* az az Azure Stack rendszergazdai és felhasználói portálon. Most már használhatja *minden szolgáltatás* helyett az Azure Stack portálon lépjen a ugyanúgy, mint az az Azure Portalon teheti meg.

- <!--  TBD – IS, ASDK --> *Alapszintű A* virtuálisgép-méretek esetében kivezettük [virtuálisgép-méretezési csoportok létrehozása](azure-stack-compute-add-scalesets.md) (VMSS) a portálon keresztül. Ez a méret a VMSS létrehozásához, használja a PowerShell vagy a sablont.  

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Ez a frissítés a következő biztonsági frissítéseket telepíti:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE – 2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE – 2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE – 2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE – 2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE – 2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE – 2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE – 2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE – 2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE – 2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE – 2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE – 2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE – 2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE – 2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE – 2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE – 2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE – 2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE – 2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE – 2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE – 2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE – 2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE – 2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE – 2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE – 2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)

További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis [4457131](https://support.microsoft.com/help/4457131).

### <a name="prerequisites"></a>Előfeltételek

- Az Azure Stack telepítése [1808 frissítése](azure-stack-update-1808.md) az Azure Stack 1809 frissítés alkalmazása előtt. 
- Telepítse a legújabb elérhető [frissítést vagy gyorsjavítást verzió 1808](azure-stack-update-1808.md#post-update-steps).

  > [!TIP]  
  > Fizessen elő a következő *RRS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Az Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások *hiba – a sablon typu FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. A frissítés telepítésének befejezése után ezek a riasztások automatikusan bezáródik.

- <!-- 2489559 - IS --> Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. Frissítések kezelésével kapcsolatos további információkért lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).

- <!-- 2830461 - IS --> Bizonyos körülmények között, ha egy frissítéshez a figyelmet, a megfelelő riasztás lehetséges, hogy nem hozható létre. A pontos állapota továbbra is megjelennek a portálon, és nem változik.

### <a name="post-update-steps"></a>Frissítés utáni lépések

*Nincsenek nem frissítés 1809 frissítés utáni lépéseket.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

- Az Azure Stack technikai dokumentáció a legfrissebb kiadás összpontosít. Portál változások között, mert megjelenő használata az Azure Stack portálok kissé eltérhetnek a dokumentációban talál.

- <!-- 2930718 - IS ASDK --> A felügyeleti portálon, minden felhasználói előfizetés részleteinek elérésekor után bezárja a panelt, és kattintson a **legutóbbi**, a felhasználói előfizetés neve nem jelenik meg.

- <!-- 3060156 - IS ASDK --> A rendszergazda és a felhasználói portált, kattintson a portál beállításait, és válassza a **törli az összes beállítás és saját irányítópult** nem a várt módon működik. Egy hiba értesítés jelenik meg. 

- <!-- 2930799 - IS ASDK --> A rendszergazda és a felhasználói portálon a **minden szolgáltatás**, az eszköz **DDoS potection-tervek** nem megfelelően szerepel. Ez nem érhető valójában az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg arról, hogy a portál nem tudta létrehozni a Piactéri elemet. 

- <!-- 2930820 - IS ASDK --> A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem érhető valójában az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

- <!-- 2967387 – IS, ASDK --> A fiók használatával jelentkezzen be az Azure Stack rendszergazdai vagy a felhasználói portálon jelenik meg **azonosítatlan felhasználó**. Ez akkor fordul elő, ha a fiók nem rendelkezik vagy egy *első* vagy *utolsó* megadott név. A probléma megkerüléséhez az első vagy utolsó nevét adja meg a felhasználói fiók szerkesztésével. Meg kell majd jelentkezzen ki, majd majd jelentkezzen be újra a portálra.  

-  <!--  2873083 - IS ASDK --> Amikor a portal használatával hozzon létre egy virtuálisgép-méretezési csoport beállítása (VMSS), a *példány mérete* legördülő továbbra sem töltődik be megfelelően az Internet Explorer használata esetén. Ez a probléma megkerüléséhez használja egy másik böngészőben egy VMSS létrehozása a portál használata során.  

- <!-- TBD  ASDK --> Az alapértelmezett időzóna az összes Azure Stack üzemelő példányhoz most vannak beállítva az egyezményes világidő (UTC). Választhat egy időzóna telepítésekor az Azure Stack, azonban automatikusan visszaáll az UTC Időzóna alapértelmezés szerint a telepítés során.

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

- <!-- 2967447 - IS, ASDK --> A virtual machine scale set (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzemelő példány egy másik operációs rendszer válasszon vagy adjon meg egy másik CentOS képet az operátor telepítené a marketplace-ről letöltött Azure Resource Manager sablonnal.  

- <!-- 2724873 - IS --> A PowerShell-parancsmagok használata esetén **Start-AzsScaleUnitNode** vagy **Stop-AzsScaleunitNode** skálázási egységek kezelése, indítása vagy leállítása a skálázási egység az első kísérlet sikertelen lehet. A parancsmag futtatása sikertelen, az első, futtassa a parancsmagot egy második alkalommal. A második Futtatás sikeres legyen, a művelet végrehajtásához. 

- <!-- TBD - IS ASDK --> Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- TBD - IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  

- <!-- 2724961- IS ASDK --> Amikor regisztrál a **Microsoft.Insight** előfizetési beállítások, az erőforrás-szolgáltató egy Windows virtuális gép létrehozása és a vendég operációs rendszer diagnosztikai engedélyezve van, a Processzorhasználat (%) diagramon a virtuális gépek – Áttekintés lapon nem jelenik meg a mérőszámadatok.

   Keresse meg a mérőszámadatokat, például a processzor-diagram a virtuális gép, nyissa meg a metrikák ablakot, és a támogatott Windows virtuális gép vendég mérőszámok megjelenítése.



### <a name="networking"></a>Hálózat  

- <!-- 1766332 - IS ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

- <!-- 1902460 - IS ASDK --> Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- <!-- 16309153 - IS ASDK --> A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

- <!-- 2529607 - IS ASDK --> Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

-   <!-- 2664148 - IS ASDK --> Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró után átjárón már létre lett hozva. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

- <!-- 2352906 - IS ASDK --> Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.


### <a name="usage"></a>Használat  

- <!-- TBD - IS ASDK --> A nyilvános IP-használatának mérési adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* blokk, amely megjeleníti a rekord létrehozásának. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1809 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>További lépések

- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).  
