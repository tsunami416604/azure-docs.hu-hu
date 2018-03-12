---
title: "A Microsoft Azure verem szoftverfejlesztői készlet kibocsátási megjegyzései |} Microsoft Docs"
description: "Fejlesztések, javítások és Azure verem szoftverfejlesztői készlet kapcsolatos ismert problémák."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: brenduns
ms.reviewer: chjoy
ms.openlocfilehash: deef5d5383fcfd8e13c8088cb7901b07621f53a7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Az Azure verem szoftverfejlesztői készlet kibocsátási megjegyzései

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

A kibocsátási megjegyzések fejlesztései, javítások és Azure verem szoftverfejlesztői készlet ismert problémáira vonatkozó információkat tartalmazzák. Ha nem biztos abban, hogy melyik verzióját futtatja, akkor [a portál használatával ellenőrizze](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201803021"></a>Build 20180302.1

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat
Tekintse meg a [új szolgáltatásokat és javításokat](azure-stack-update-1802.md#new-features-and-fixes) az Azure verem 1802 frissítés kibocsátási megjegyzései Azure verem szakasza integrált rendszerek.

> [!IMPORTANT]    
> A elemek egy része szerepel a **új szolgáltatásokat és javításokat** szakasz csak integrált Azure verem rendszerek szempontjából.


### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- Lehetővé teszi [új támogatási kérést nyithat a legördülő menüből](azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portál nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Az Azure verem szoftverfejlesztői készlet https://aka.ms/azurestackforum használja.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Egy erőforrás vagy egy erőforráscsoport, tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok vagy előfizetések között erőforrások vagy erőforráscsoportok áthelyezése jelenleg nem támogatott.
 
- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.

- A felügyeleti portál irányítópultján a frissítés csempe nem jelenik meg a frissítésekkel kapcsolatos információk. A probléma megoldásához kattintson a csempére kattintva frissítse azt.

-   A felügyeleti portálon jelenhet meg a kritikus riasztások Microsoft.Update.Admin összetevő. A riasztás nevét, leírását és javítási összes jeleníti meg:  
    - *Hiba – hiányzik a sablon FaultType ResourceProviderTimeout.*

    Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 

#### <a name="health-and-monitoring"></a>Állapot- és figyelés
A verem Azure felügyeleti portálon, előfordulhat, hogy a kritikus riasztások nevű látható **függőben lévő külső tanúsítvány lejárta**.  Ezt a figyelmeztetést is nyugodtan figyelmen kívül hagyható, és az Azure verem szoftverfejlesztői készlet működésére hatással. 


#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Számítás
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- Az Azure verem támogatja a csak a rögzített típusúvá a VHD-k használatát. Néhány képet, a veremben Azure piactéren keresztül felajánlott használjon dinamikus virtuális merevlemezek, de amelyek el lettek távolítva. Dinamikus lemez nem csatlakoztatható a virtuális gép (VM) átméretezése hagyja el a virtuális gép hibás állapotban.

  A probléma orvoslása érdekében törölje a virtuális gép a virtuális lemez, a tárfiók VHD blob törlése nélkül. Majd a virtuális merevlemez átalakítása dinamikus lemezről rögzített méretű lemezt, és hozza létre a virtuális gép.

- Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a DS-ben több virtuális gép csatolhat adatlemezek száma helytelen. DS adatsorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

-  Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Hálózat
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.

- Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni.

- Belső Load Balancing (ILB) nem megfelelően kezeli a MAC-címek háttér virtuális gépekhez, emiatt ILB megszünteti a háttér-hálózat használata a Linux-példányok esetén.  ILB helyesen működik a Windows-példányok a háttér-hálózaton.

-   Az IP-továbbítást a szolgáltatás látható a portálon, nincs hatása azonban az IP-továbbítás engedélyezését. Ez a funkció jelenleg nem támogatott.

- Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.
 
- Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Az App Service megkerülő megoldás: a távoli asztal a vezérlő példányokra van szüksége, ha módosítja a biztonsági szabályok belül a hálózati biztonsági csoportok a PowerShell használatával.  Az alábbiakban példákat *engedélyezése*, majd állítsa vissza a konfiguráció *megtagadása*: 
    
    - *Engedélyezése:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
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
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
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


#### <a name="sql-and-mysql"></a>Az SQL és MySQL 
- Akár egy óraba felhasználók adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet.

- Az adatbázis-kiszolgálók üzemeltetési az erőforrás-szolgáltató és a felhasználó munkaterhelés használatra kell kijelölnie. Bármely más fogyasztó, beleértve az App Service szolgáltatások által használt példánya nem használható.


#### <a name="app-service"></a>App Service
- Ahhoz, hogy az első Azure-függvény létre előfizetést felhasználók regisztrálnia kell a storage erőforrás-szolgáltató.

- Ahhoz, hogy az infrastruktúra kiterjesztése olyan esetekben (munkavállalók, kezelési, előtér-szerepkörök), PowerShell számítás a kibocsátási megjegyzésekben leírt módon kell használnia.
 
#### <a name="usage-and-billing"></a>Használat és számlázás
- Nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>A Githubból Azure verem eszközök letöltése
- Használatakor a *meghívása webrequest* PowerShell parancsmag használatával töltheti le az Azure-verem eszközök a Githubból, hibaüzenet:     
    -  *Invoke-webrequest: A kérelmet megszakították: SSL/TLS biztonságos csatorna nem hozható létre.*     

  Ez a hiba oka a legutóbbi GitHub támogatási érvénytelenítése Tlsv1 és Tlsv1.1 kriptográfiai szabványok (a PowerShell alapértelmezett). További információkért lásd: [gyenge kriptográfiai szabványok eltávolítási figyelmeztetés](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához vegye fel `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a PowerShell-konzolon TLSv1.2 használandó, ha a letöltés a GitHub-adattárak kényszerítheti a parancsfájl elején.







## <a name="build-201801032"></a>Build 20180103.2

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

- Tekintse meg a [új szolgáltatásokat és javításokat](azure-stack-update-1712.md#new-features-and-fixes) az Azure verem 1712 frissítés kibocsátási megjegyzései Azure verem szakasza integrált rendszerek.

    > [!IMPORTANT]
    > A elemek egy része szerepel a **új szolgáltatásokat és javításokat** szakasz csak integrált Azure verem rendszerek szempontjából.

### <a name="known-issues"></a>Ismert problémák
 
#### <a name="deployment"></a>Környezet
- Adjon meg egy IP-cím szerint üzembe helyezése során.

#### <a name="infrastructure-management"></a>Infrastruktúra felügyelete
- Ne engedélyezze a infrastruktúra biztonsági mentés a **infrastruktúra biztonsági mentés** panelen.
- Az alaplapi felügyeleti vezérlővel (BMC) IP-cím és a modell nem láthatók a lényeges információk egy méretezési egység csomópont. Ez az elvárt viselkedés Azure verem csomagban.

#### <a name="portal"></a>Portál
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
-  Egyetlen olyan munkafolyamat, ahol ki kell választania egy előfizetés, a csoport vagy a hely egy legördülő listában, a legalább egy, az alábbi problémákat tapasztalhatja:

   - Megjelenik egy üres sort a lista tetején. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő listán szereplő elemek listája rövid, nem lehet a cikk bármelyike megtekintheti.
   - Ha több felhasználó-előfizetéssel rendelkezik, az erőforrás csoport legördülő lista üres is lehet. 

   Az utolsó két problémák megoldása érdekében adhatja meg az előfizetés vagy az erőforráscsoport (ha tudja) neve, vagy a PowerShell segítségével helyette.

- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.
- Ha a **összetevő** bármelyik hivatkozásra kattint **infrastruktúra-szerepkör** riasztást, a létrejövő **áttekintése** panel megpróbálja betölteni, és sikertelen lesz. Emellett a ** – áttekintés ** panel does nem túllépi az időkorlátot.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem áll az előfizetés engedéllyel megtekintheti a verem Azure portál használatával. A probléma megoldásához engedélyek PowerShell használatával ellenőrizheti.
 
#### <a name="marketplace"></a>Piactér
- Néhány Piactéri elemek el ebben a kiadásban kompatibilitási megfontolások miatt. Ezek lesznek ismét engedélyezni kell további ellenőrzése után.
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Számítás
- Felhasználók rendszer felajánlja a lehetőséget a virtuális gép létrehozása a georedundáns tárolást. E konfiguráció hatására a virtuális gép nem hozható létre. 
- Beállíthatja, hogy a virtuális gép rendelkezésre állási csoportban, csak az egyik tartalék tartomány, és egy frissítési tartomány.
- Nincs nincs Piactéri élmény virtuálisgép-méretezési csoportok létrehozásához. A skála beállítása egy sablon használatával hozhat létre.
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

#### <a name="networking"></a>Hálózat
- Nyilvános IP-címek a terheléselosztó nem létrehozása a portál használatával. A probléma megoldásához a PowerShell hozhat létre a terheléselosztó hasonló adataival.
- Hálózati terheléselosztó létrehozásakor létre kell hoznia a hálózati cím címfordítási (NAT) szabály. Ha ezt elmulasztja, kapunk hiba történt egy NAT-szabály hozzáadása a terheléselosztó létrehozása után.
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.
- Lehet, hogy az Azure verem operátorok nem lehet telepíteni, törlése, módosítása a Vnetek vagy a hálózati biztonsági csoportok. A probléma főként látható ugyanazon csomag későbbi frissítési kísérletek. Ennek oka egy frissítést, amely jelenleg vizsgált egy csomagolási kapcsolatos problémát.
- Belső Load Balancing (ILB) nem megfelelően kezeli a MAC-címek háttér virtuális gépekhez, amely Linux-példányra.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.

#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.
 
#### <a name="usage-and-billing"></a>Használat és számlázás
- Nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

#### <a name="identity"></a>Identitás

Az Azure Active Directory összevonási szolgáltatások (ADFS) környezetben telepített a **azurestack\azurestackadmin** fiók már nincs az alapértelmezett szolgáltató előfizetés tulajdonosának. Való bejelentkezés helyett a **felügyeleti portál / adminmanagement végpont** rendelkező a **azurestack\azurestackadmin**, használhatja a **azurestack\cloudadmin** fiókot, így hogy kezelése, és az alapértelmezett szolgáltató előfizetés használatára.

> [!IMPORTANT]
> Még a **azurestack\cloudadmin** fiók telepített AD FS-környezetben az alapértelmezett szolgáltató előfizetés tulajdonosának, nincs az állomás a távoli asztali engedélyek. Továbbra is használhatja a **azurestack\azurestackadmin** vagy a helyi rendszergazdai fiók bejelentkezési, eléréséhez és a gazdagép kezelése, igény szerint.

## <a name="build-201711221"></a>Build 20171122.1

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

- Tekintse meg a [új szolgáltatásokat és javításokat](azure-stack-update-1711.md#new-features-and-fixes) az Azure verem 1711 frissítés kibocsátási megjegyzései Azure verem szakasza integrált rendszerek.

    > [!IMPORTANT]
    > A elemek egy része szerepel a **új szolgáltatásokat és javításokat** szakasz csak integrált Azure verem rendszerek szempontjából.

### <a name="known-issues"></a>Ismert problémák
 
#### <a name="deployment"></a>Környezet
- Adjon meg egy IP-cím szerint üzembe helyezése során.

#### <a name="infrastructure-management"></a>Infrastruktúra felügyelete
- Ne engedélyezze a infrastruktúra biztonsági mentés a **infrastruktúra biztonsági mentés** panelen.
- Az alaplapi felügyeleti vezérlővel (BMC) IP-cím és a modell nem láthatók a lényeges információk egy méretezési egység csomópont. Ez az elvárt viselkedés Azure verem csomagban.

#### <a name="portal"></a>Portál
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
-  Egyetlen olyan munkafolyamat, ahol ki kell választania egy előfizetés, a csoport vagy a hely egy legördülő listában, a legalább egy, az alábbi problémákat tapasztalhatja:

   - Megjelenik egy üres sort a lista tetején. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő listán szereplő elemek listája rövid, nem lehet a cikk bármelyike megtekintheti.
   - Ha több felhasználó-előfizetéssel rendelkezik, az erőforrás csoport legördülő lista üres is lehet. 

   Az utolsó két problémák megoldása érdekében adhatja meg az előfizetés vagy az erőforráscsoport (ha tudja) neve, vagy a PowerShell segítségével helyette.

- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.
- Ha a **összetevő** bármelyik hivatkozásra kattint **infrastruktúra-szerepkör** riasztást, a létrejövő **áttekintése** panel megpróbálja betölteni, és sikertelen lesz. Emellett a **áttekintése** panel does nem túllépi az időkorlátot.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem áll az előfizetés engedéllyel megtekintheti a verem Azure portál használatával. A probléma megoldásához engedélyek PowerShell használatával ellenőrizheti.
 
#### <a name="marketplace"></a>Piactér
- Ha megpróbálja elemek hozzáadására a verem Azure piactér használatával a **hozzáadása az Azure-ból** beállítás, nem minden elem esetleg mások is láthatják letölthető.
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Számítás
- Felhasználók rendszer felajánlja a lehetőséget a virtuális gép létrehozása a georedundáns tárolást. E konfiguráció hatására a virtuális gép nem hozható létre. 
- Beállíthatja, hogy a virtuális gép rendelkezésre állási csoportban, csak az egyik tartalék tartomány, és egy frissítési tartomány.
- Nincs nincs Piactéri élmény virtuálisgép-méretezési csoportok létrehozásához. A skála beállítása egy sablon használatával hozhat létre.
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

#### <a name="networking"></a>Hálózat
- Nyilvános IP-címek a terheléselosztó nem létrehozása a portál használatával. A probléma megoldásához a PowerShell hozhat létre a terheléselosztó hasonló adataival.
- Hálózati terheléselosztó létrehozásakor létre kell hoznia a hálózati cím címfordítási (NAT) szabály. Ha ezt elmulasztja, kapunk hiba történt egy NAT-szabály hozzáadása a terheléselosztó létrehozása után.
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.
- Lehet, hogy az Azure verem operátorok nem lehet telepíteni, törlése, módosítása a Vnetek vagy a hálózati biztonsági csoportok. A probléma főként látható ugyanazon csomag későbbi frissítési kísérletek. Ennek oka egy frissítést, amely jelenleg vizsgált egy csomagolási kapcsolatos problémát.
- Belső Load Balancing (ILB) nem megfelelően kezeli a MAC-címek háttér virtuális gépekhez, amely Linux-példányra.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.

    > [!NOTE]
    > Tekintse meg az egyes [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) és [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) verzió kompatibilitási útmutatót cikkek beállítása.

#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.
 
#### <a name="usage-and-billing"></a>Használat és számlázás
- Nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

#### <a name="identity"></a>Identitás

Az Azure Active Directory összevonási szolgáltatások (ADFS) környezetben telepített a **azurestack\azurestackadmin** fiók már nincs az alapértelmezett szolgáltató előfizetés tulajdonosának. Való bejelentkezés helyett a **felügyeleti portál / adminmanagement végpont** rendelkező a **azurestack\azurestackadmin**, használhatja a **azurestack\cloudadmin** fiókot, így hogy kezelése, és az alapértelmezett szolgáltató előfizetés használatára.

> [!IMPORTANT]
> Még a **azurestack\cloudadmin** fiók telepített AD FS-környezetben az alapértelmezett szolgáltató előfizetés tulajdonosának, nincs az állomás a távoli asztali engedélyek. Továbbra is használhatja a **azurestack\azurestackadmin** vagy a helyi rendszergazdai fiók bejelentkezési, eléréséhez és a gazdagép kezelése, igény szerint.

