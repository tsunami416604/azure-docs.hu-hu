---
title: Az Azure Stack 1803 frissítés |} A Microsoft Docs
description: 'További tudnivalók: Mi az az Azure Stack 1803 frissítés integrált rendszerek, az ismert problémák és hová töltse le a frissítést.'
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989571"
---
# <a name="azure-stack-1803-update"></a>Azure Stack-1803 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a fejlesztést, és javítja a 1803 frissítési csomag, ismert problémák az ebben a kiadásban, valamint a helyét, a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure Stack 1803 frissítés buildszáma **20180329.1**.


## <a name="before-you-begin"></a>Előkészületek    
> [!IMPORTANT]    
> Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. Frissítések kezelésével kapcsolatos további információkért lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Előfeltételek
- Az Azure Stack telepítése [1802-es frissítés](azure-stack-update-1802.md) az Azure Stack 1803 frissítés alkalmazása előtt.   

- Telepítés **AzS gyorsjavítás – 1.0.180312.1-összeállítása 20180222.2** az Azure Stack 1803 frissítés alkalmazása előtt. Ez a gyorsjavítás frissíti a Windows Defender, és az Azure Stackhez készült frissítések letöltése esetén érhető el.

  Telepítse a gyorsjavítást, kövesse a normál eljárásai [frissítések telepítése az Azure Stack](azure-stack-apply-updates.md). A frissítés neve jelenik meg **AzS gyorsjavítás – 1.0.180312.1**, és a következő fájlokat tartalmazza: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.XML

  Feltöltése után ezeket a fájlokat egy tárfiókot és tárolót, futtassa a telepítést a frissítés csempe a felügyeleti portálon. 
  
  Ellentétben az Azure Stackhez frissítéseket a frissítés telepítése nem módosítja az Azure Stack verzióját. Ellenőrizze a frissítés telepítve van-e, tekintse meg a listáját **telepített frissítések**.



### <a name="new-features"></a>Új funkciók 
A frissítés tartalmazza a következő fejlesztések és javítások az Azure Stackhez.

- **Azure Stack titkos kódok frissítése** - (fiókok és -tanúsítványok). Titkos kódok kezelésével kapcsolatos további információkért lásd: [elforgatása a titkos kulcsok az Azure Stackben](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Az automatikus átirányítás a HTTPS** HTTP használhatja a rendszergazda és a felhasználói portál eléréséhez. Ennek a fejlesztésnek alapján történt [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) visszajelzést az Azure Stackhez. 

- <!-- 2202621  --> **A piactér elérésére** – használatával most megnyithatja az Azure Stack piactéren a [+ új](https://ms.portal.azure.com/#create/hub) belül a rendszergazda és a felhasználói portálon parancsát, ugyanúgy az az Azure Portalon teheti meg.
 
- <!-- 2202621 --> **Az Azure Monitor** -hozzáadja az Azure Stack [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) , a rendszergazda és a felhasználói portálon. Ez magában foglalja a mérőszámok és Tevékenységnaplók az új tallózók. Az Azure Monitor a külső hálózatok eléréséhez port **13012** Tűzfalkonfiguráció nyitva kell lennie. Azure Stack által igényelt portokat kapcsolatos további információkért lásd: [adatközpont integrációja az Azure Stack - végpontok közzététele](azure-stack-integrate-endpoints.md).

   Ennek részeként is módosíthatja, a **további szolgáltatások**, *Auditnaplók* jelenik meg, mint *tevékenységeket tartalmazó naplók*. A funkció mostantól az Azure Portallal konzisztens. 

- <!-- 1664791 --> **Ritka fájlok** – Ha egy új kép hozzáadása az Azure Stackhez, vagy keresztül marketplace tartalomtípus-gyűjtési, képet vehet fel a kép egy ritka fájlok alakítja át. Nem lehet konvertálni a lemezképek, amelyek az Azure Stack-verziók 1803 használata előtt adtak hozzá. Ehelyett ez a funkció kihasználásához képek újraküldeni marketplace tartalomtípus-gyűjtési kell használnia. 
 
   Ritka fájlok egy hatékony fájlformátum tárolási lemezterület-használat csökkentésére, és i/o javítására használjuk.  További információkért lásd: [ritka Fsutil](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) a Windows Server. 

### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

- <!-- 1739988 --> Belső Load Balancing (ILB) most már megfelelően kezeli a MAC-címek a háttérbeli virtuális géppel, amely hatására az ILB csomagok a háttérbeli hálózati eldobni a háttér-hálózat Linux-példány használata esetén. ILB jól működik, a Windows-példányok a háttér-hálózat. 

- <!-- 1805496 --> Egy problémát, ahol az Azure Stack közötti VPN-kapcsolatok lenne lecsatlakoznak különböző beállítások, mint az Azure az IKE-házirend használata az Azure Stack miatt. SALifetime (idő), és SALiftetime (bájt) értékeit a rendszer nem kompatibilis az Azure-ral, és, hogy megváltoztak az 1803-as verzióban az Azure-beállításoknak megfelelően. Előtt 1803 SALifetime (másodperc) értéke volt 1803 14400 és 27,000 most módosításait. A SALifetime (bájt) értéke 1803 előtt volt 819,200 és az 1803 33,553,408 módosításait.

- <!-- 2209262 --> A ahol VPN-kapcsolatok keretezte a portálon; IP-probléma azonban engedélyezése vagy IP-továbbítás vizualizációtól nem befolyásolja. A funkció alapértelmezés szerint és megváltoztathatja ezt még nem támogatott be van kapcsolva.  A vezérlő el lett távolítva a portálról. 

- <!-- 1766332 --> Az Azure Stack nem támogatja a házirend-alapú VPN-átjárók, annak ellenére, hogy a beállítás megjelenik a portálon.  A beállítás a portálról el lett távolítva. 

- <!-- 1868283 --> Az Azure Stack most megakadályozza a dinamikus lemezek segítségével létrehozott virtuális gép átméretezése. 

- <!-- 1756324 --> Virtuális gépek használati adatainak most választja el egymástól, óránként történik. Ez az egységes Azure-ral. 

- <!--  2253274 --> A probléma, a rendszergazdai és felhasználói portálon, a beállítások panelen alhálózatok a virtuális hálózathoz nem sikerül betölteni. Áthidaló megoldásként a powershellel és a [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) parancsmag használatával tekintheti meg és kezelheti ezeket az információkat.

- Mikor hozzon létre egy virtuális gépet, az üzenet *nem jeleníthető meg az árképzés* már nem jelenik meg, ha válasszon méretet a virtuális gép méretét.

- Különböző javítja a teljesítményt, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer.


### <a name="changes"></a>Módosítások
- A módszer egy újonnan létrehozott ajánlat az állapotváltozáshoz *privát* való *nyilvános* vagy *leszerelt* megváltozott. További információkért lásd: [hozzon létre egy ajánlatot](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái    
<!-- 2328416 --> Az 1803-frissítés telepítése során a blob szolgáltatás és a blob szolgáltatás használó belső szolgáltatásokba állásidő is lehet. Ez néhány virtuális gép műveleteit tartalmazza. Állásidő emiatt bérlő hibák műveletek vagy riasztások származó szolgáltatásokat, amelyek nem férhetnek hozzá adataihoz. Ez a probléma megoldását magát a frissítés telepítésének befejezésekor. 



### <a name="post-update-steps"></a>Frissítés utáni lépések
- 1803 a telepítés után bármely alkalmazandó gyorsjavítások telepítéséről. További információt a következő cikkeket, megtekintése, valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).

  - [KB 4344115 – az Azure Stack gyorsjavítás 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Ez a frissítés telepítése után tekintse át a tűzfal-konfigurációjában, hogy [szükséges portokat](azure-stack-integrate-endpoints.md) nyitva. Például a frissítés vezet be *Azure Monitor* mely tevékenységeket tartalmazó naplók-naplók módosítását tartalmazza. Ezzel a port 13012 szolgál, és a is nyitva kell lennie.  


### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban a telepítés utáni ismert problémái build **20180323.2**.

#### <a name="portal"></a>Portál
- <!-- 2332636 - IS -->  Az AD FS használata az Azure Stack identitásrendszer és a frissítés erre a verzióra, az Azure Stack, az alapértelmezett szolgáltatója előfizetés alapértelmezett tulajdonosa áll vissza a beépített **CloudAdmin** felhasználói.  
  Megkerülő megoldás: A frissítés telepítése után a probléma megoldásához használja a 3. lépés a [eseményindító automation konfigurálása jogcím-szolgáltatói megbízhatósági az Azure Stackben](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) eljárás alaphelyzetbe állítani az alapértelmezett szolgáltatója előfizetés tulajdonosa.   

- Lehetővé teszi [, nyisson egy új támogatási kérelmet a legördülő listából](azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portálon nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Az Azure Stack integrált rendszerek használata https://aka.ms/newsupportrequest.

- <!-- 2050709 --> A felügyeleti portálon nincs lehetőség a Blob service, Table service vagy Queue szolgáltatás tármetrikák szerkesztése. Nyissa meg a Storage, és válassza ki a blob, tábla vagy üzenetsor-szolgáltatás csempéjét, amikor egy új panel nyílik meg, amely megjeleníti az adott szolgáltatásnak a teljesítménymetrikák diagramja. Majd válassza a felső a metrikák diagram csempe szerkesztése, ha a diagram szerkesztése panel megnyílik, de nem jeleníti meg a beállítások szerkesztéséhez metrikák.

- Ez nem lehetséges számítási és tárolási erőforrások megtekintése a felügyeleti portálon. A probléma oka hiba történt a frissítés sikeres helytelenül jelenti a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon segítségért a Microsoft ügyfél-támogatási szolgálathoz.

- Előfordulhat, hogy megjelenik egy üres irányítópult, a portálon. Szeretné használni az irányítópultot, kattintson a fogaskerék ikonra a portál jobb felső sarokban, és válassza **alapértelmezett beállítások visszaállítása**.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.

- A frissítés csempét az irányítópulton, a felügyeleti portálon, nem jelennek meg a frissítésekkel kapcsolatos információk. A probléma megoldásához kattintson a csempére, és frissítse azt.

- A felügyeleti portálon, megjelenhet egy kritikus riasztása a következőhöz a *Microsoft.Update.Admin* összetevő. A riasztás nevét, leírását és szervizelési összes állapotúként jelennek meg:  
    - *Hiba – hiányzik a sablon typu FaultType ResourceProviderTimeout.*

  Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 


#### <a name="health-and-monitoring"></a>Állapot és figyelés
- <!-- 1264761 - IS ASDK -->  A riasztásokat a *egészségügyi vezérlő* összetevő, amely rendelkezik a következő adatokat:  

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

  Mindkét riasztás biztonságosan figyelmen kívül hagyhatja. Ezek automatikusan megszűnik idővel.  


#### <a name="marketplace"></a>Piactér
- A felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és megtekintheti a felügyeleti elemek, például a csomagokat és ajánlatokat. Ezek az elemek nem működik a felhasználók számára is.



#### <a name="compute"></a>Compute
- A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- Amikor létrehoz egy rendelkezésre állási csoportot a portálon a **új** > **számítási** > **rendelkezésre állási csoport**, csak hozhat létre egy rendelkezésre állási csoport egy tartalék tartomány és a frissítési tartomány 1. Áthidaló megoldásként egy új virtuális gép létrehozásakor, a rendelkezésre állási csoportot használja a Powershellt, CLI-t, vagy létrehozása a portálon.

- Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, amelyek a D sorozatú virtuális gépek csatlakoztathat adatlemezek száma helytelen. Az összes támogatott D sorozatú virtuális gépek, az Azure konfigurációs lehetővé teszi tetszőleges számú adatlemezeket.

- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  Ezután próbálkozzon töltse a Virtuálisgép-rendszerképet, amely korábban nem sikerült.

-  Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  


#### <a name="networking"></a>Hálózat
- Miután egy virtuális Gépet létrehozni, és társított nyilvános IP-címet, IP-címet a virtuális gép nem szüntesse meg. Társításának úgy tűnik, hogy működik, de az előzőleg hozzárendelt nyilvános IP-cím marad az eredeti virtuális gép társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia az új virtuális gépek létrehozása.

  Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli próbál meg ezt az IP cím eredményt kapcsolaton keresztül a eredetileg kapcsolódó virtuális gép, és a egy újra csatlakozhat.



- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

- Az Azure Stack nem támogatja a virtuális gép üzembe helyezése után további hálózati adapterek hozzáadása egy Virtuálisgép-példánnyal. Ha a virtuális gép több hálózati adaptert igényel, akkor meg kell határozni központi telepítéskor.

- <!-- 2096388 --> A felügyeleti portálon egy hálózati biztonsági csoportra vonatkozó szabályainak frissítése nem használhat. 

    Megkerülő megoldás az App Service: a tartományvezérlő-példányok távoli asztal kell, ha módosítja a biztonsági szabályok belül a hálózati biztonsági csoportok, a PowerShell használatával.  Az alábbiakban példákat *engedélyezése*, majd állítsa vissza a konfigurációt a *megtagadása*:  
    
    - *Engedélyezése:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Megtagadása:*

        ```powershell
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```


#### <a name="sql-and-mysql"></a>SQL- és MySQL
- A folytatás előtt tekintse át a fontos megjegyzést a [megkezdése előtt](#before-you-begin) közel kezdete ezek a kibocsátási megjegyzéseket.

- Akár egy óra elteltével a felhasználó adatbázist is létrehozhat egy új SQL vagy MySQL típusú központi telepítés is igénybe vehet.

- Konfigurációelemek létrehozása a kiszolgálókon, a fogadó SQL vagy MySQL csak az erőforrás-szolgáltató támogatott. A gazdagép-kiszolgálón létrehozott elemek nem az erőforrás-szolgáltató által létrehozott egy nem megfelelő állapot eredményezhet.  

- <!-- IS, ASDK --> Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** nevezze a Termékváltozat létrehozott SQL- és MySQL erőforrás-szolgáltatókat.

> [!NOTE]  
> Miután frissítette az Azure Stack 1803, továbbra is használhatja az SQL- és MySQL erőforrás-szolgáltatókat korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és a MySQL amikor új kiadása elérhetővé válik. Azure Stack, például frissítések alkalmazása az SQL- és MySQL erőforrás-szolgáltatók egymás után.  Például 1711 verzióját használja, ha először a alkalmazni 1712, majd az 1802-es verzióját, és frissítse az 1803.      
>   
> A telepítés, frissítés 1803 nem befolyásolja a felhasználók által SQL vagy MySQL típusú erőforrás-szolgáltató jelenlegi felhasználása.
> Az erőforrás-szolgáltatókat használ verziójától függetlenül adatbázisaikat a felhasználók adatait nem megszáradásukig, és elérhető marad.    



#### <a name="app-service"></a>App Service
- Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.


#### <a name="usage"></a>Használat  
- Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Az Azure Stack-eszközök letöltése a Githubról
- Használatakor a *invoke-webrequest* PowerShell-parancsmag használatával töltse le az Azure Stack-eszközök a Githubról, hibaüzenetet kap:     
    -  *Invoke-webrequest: A kérés megszakadt: nem sikerült létrehozni az SSL/TLS biztonságos csatornát.*     

  Ez a hiba oka egy legutóbbi GitHub támogatás elavulásának Tlsv1 és Tlsv1.1 titkosítási szabványok (a PowerShell-alapértelmezett). További információkért lásd: [gyenge titkosítási szabványok eltávolítási értesítés](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához adja hozzá `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` kényszerítése a PowerShell-konzol használatához TLSv1.2 GitHub-adattárak való letöltése esetén a parancsfájl elejére.


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure Stack 1803 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
