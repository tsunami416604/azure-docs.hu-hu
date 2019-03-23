---
title: Azure Stack 1902 frissítése |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1902 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: fbf9f4aa79af32cf0e73f4e383130c565de16f53
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372369"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1902 csomag tartalmát. A frissítési fejlesztései, javításokat és új funkciók az Azure Stack ezen verziója tartalmazza. Ez a cikk is ebben a kiadásban az ismert problémákat, és tartalmaz egy hivatkozást a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat, és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1902 frissítés buildszáma **1.1902.0.69**.

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Ne felejtse el telepíteni a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) az Azure Stack 1902 frissítése előtt 1901.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

> [!TIP]  
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Az Azure Stack-gyorsjavítások

- **1809**: [KB 4481548 – az Azure Stack gyorsjavítás 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nincs elérhető aktuális gyorsjavítást.
- **1901**: [KB 4495662 – az Azure Stack gyorsjavítás 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**: [KB 4494719 – az Azure Stack gyorsjavítás 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> - Telepítse a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) esetében (ha vannak) 1901 1902 frissítése előtt.

- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Ha az Azure Stack a System Center Operations Manager (SCOM) által felügyelt, ügyeljen arra, hogy frissítse a [felügyeleti csomag a Microsoft Azure Stackhez készült](https://www.microsoft.com/download/details.aspx?id=55184) verzióra 1.0.3.11 1902 alkalmazása előtt.

- Az Azure Stack-frissítési csomag formátuma megváltozott **.bin/.exe/.xml** való **.zip/.xml** a 1902 kiadástól kezdve. A csatlakoztatott Azure Stack skálázási egységek ügyfelek fogják látni a **frissítés érhető el** üzenet a portálon. Nem csatlakozó ügyfelek mostantól egyszerűen töltse le, és importálja a .zip-fájlt a megfelelő .xml.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Fejlesztései

- A 1902 build vezet be az Azure Stack rendszergazdai portál csomagok, ajánlatok, kvóták és kiegészítő csomagok létrehozására szolgáló új felhasználói felületet. További információk, többek között a képernyőképek: [csomagok, ajánlatok és kvóták létrehozása](azure-stack-create-plan.md).

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Csomag integritás és a biztonsági és offline támogatunk könnyebb kezelhetőség javítása érdekében a Microsoft megváltozott a csomag formátuma .exe és .bin fájlt egy .zip-fájlt. Az új formátum további megbízhatóság hozzáadása a kicsomagolása folyamat, amely időnként okozhat a frissítés előkészítése leáll. A csomag formátuma is vonatkozik, az OEM-csomagok frissítése.

- Az Azure Stack-kezelői élmény javításához futtatásakor **Test-AzureStack**, operátorok segítségével mostantól egyszerűen `Test-AzureStack -Group UpdateReadiness` után tíz további paraméterek átadása helyett egy `include` utasítást. Példa:

  ```powershell
  Test-AzureStack -Group UpdateReadiness  
  ```

- A frissítési folyamat során a teljes megbízhatóságot és rendelkezésre állást az alapvető infrastruktúra-szolgáltatások javításához a natív erőforrás-szolgáltató frissítése, a frissítési művelet terv részeként lesz észlelése és automatikus globális szervizelések meghívása, igény szerint. Globális szervizelési "javítás" a munkafolyamatok a következők:

  - Ellenőrizze, hogy a virtuális gépeket, amelyek nem optimális állapotban van, és próbálja meg őket igény szerint.
  - SQL-szolgáltatási problémák keresése a vezérlő terv részeként, és próbálja meg őket igény szerint.
  - A szoftveres terheléselosztó (SLB) szolgáltatás állapotának ellenőrzése során, a hálózati vezérlő (NC), és próbálja meg őket igény szerint.
  - Ellenőrizze a hálózati vezérlő (NC) szolgáltatás állapotát, és próbálja meg helyreállítani igény szerint.
  - Ellenőrizze a válságkezelési helyreállítási konzol szolgáltatás (ERCS) service fabric csomópontok állapotát, és javítsa ki őket igény szerint.
  - Ellenőrizze a XRP service fabric-csomópont állapotát, és szükség esetén javítsa ki őket.
  - Ellenőrizze az Azure-konzisztens tároló (ACS) service fabric csomópontok állapotát, és javítsa ki őket igény szerint.

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Fejlesztések a megbízhatóságot, egyszerű kapacitásbővítést téve lehetővé során a csomópont hozzáadása a skálázási egység állapot "Expanding tároló" futó állapotban történő váltáskor.    

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Fejlesztések az Azure stack diagnosztikai eszközök napló gyűjtése megbízhatóságának és teljesítményének javítása érdekében. További naplózás a hálózati és identitáskezelési szolgáltatásokat. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Megbízhatóságát fejlesztései **Test-AzureStack** titkos Elforgatás készenléti teszt.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Továbbfejlesztett AD Graph megbízhatóságának növelése, az ügyfél Active Directory-környezetbe való kommunikáció során.

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Hardver fejlesztései készlet gyűjtemény **Get-AzureStackStampInformation**.

- ERCS infrastruktúrán futó műveletek megbízhatóságának javítása érdekében a memória ERCS példányonként növeli a 8 GB-tól 12 GB. Az Azure Stackkel integrált rendszerek telepítés esetén az eredmény egy 12 GB-os növekedést teljes.

> [!IMPORTANT]
> Ahhoz, hogy a javítási és frissítési folyamat a lehető legkevesebb bérlői állásidőt eredményez, ellenőrizze, hogy az Azure Stack-blokk 12 GB-nál több szabad terület a rendelkezik a **kapacitás** panelen. Láthatja, hogy ez a memória növelése megjelennek a **kapacitás** panelen a frissítés sikeres telepítése után.

## <a name="common-vulnerabilities-and-exposures"></a>Gyakori biztonsági rések és besorolási

Ez a frissítés a következő biztonsági frissítéseket telepíti:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis cikkei [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), megjelenik egy figyelmeztető üzenet az alaplapi felügyeleti vezérlő (BMC). Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások `Error – Template for FaultType UserAccounts.New is missing.` biztonságosan figyelmen kívül hagyhatja ezeket a riasztásokat. A riasztás automatikusan bezáródik a frissítés a telepítés befejezése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: [gyorsjavítások](#hotfixes), valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).  

- Az adatok rest titkosítási kulcsok lekéréséhez és tárolja azokat biztonságosan kívül az Azure Stack üzemelő példányához. Kövesse a [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

<!-- 2931230 – IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- 3557860 - IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és ezután törölje a felhasználói előfizetések.

<!-- 1663805 - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként használja [engedélyek ellenőrzése érdekében PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése. A probléma megkerüléséhez hozza létre újból a tárfiókot, a korábban használt azonos néven.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A virtual machine scale set (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzembe helyezéshez használt egy másik operációs rendszert, vagy egy Azure Resource Manager-sablonnal, adjon meg egy másik CentOS rendszerképet, amely szerint a marketplace-ről üzembe helyezés előtt lett letöltve a operátor.  

<!-- TBD - IS ASDK --> 
- Frissítse a 1902 alkalmazása után, a következő problémák léphetnek a felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezésekor:

   - Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
      1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Válassza ki **erőforrás-szolgáltatók**, majd **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
      2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)**, és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
   - Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.

- Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem teszi lehetővé, hogy jelentkezzen be az SSH-kulcsok használata. Áthidaló megoldásként használja a Linux-bővítményt a Virtuálisgép-hozzáférés SSH-kulcsok megvalósításához a kiépítés után, vagy jelszóalapú hitelesítés használatára.

- Ha nem rendelkezik egy életciklus állomás (HLH): Build 1902, mielőtt kellett állítani a csoportházirend **Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Biztonsági beállítások** való **LM- és NTLM – használja NTLMv2 munkamenet, haegyeztetése**. Build 1902, mivel azt hagyja **nincs definiálva** vagy - **csak a válasz küldése NTLMv2** (azaz az alapértelmezett érték). Ellenkező esetben nem tudott létrehozni egy távoli PowerShell-munkamenetet, és kapni fog egy **a hozzáférés megtagadva** hiba:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Hálózat  

<!-- 3239127 - IS, ASDK -->
- Az Azure Stack portálon Ha módosít egy statikus IP-címet az IP-konfiguráció a Virtuálisgép-példányhoz csatolt hálózati adapterhez kötött látni fogja egy figyelmeztető üzenet arról, hogy 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Biztonságosan figyelmen kívül hagyhatja ezt az üzenetet; az IP-cím megváltozik, akkor is, ha a Virtuálisgép-példány nem indítja újra.

<!-- 3632798 - IS, ASDK -->
- A portálon, ha egy bejövő biztonsági szabály felvétele, és válassza a **Szolgáltatáscímke** forrásaként, több lehetőség megjelennek a **forráscímke** listája, amelyek nem érhetők el az Azure Stackhez. Érvényes, az Azure Stack csak lehetőségek a következők:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  A többi beállítást az Azure Stackben forrás nem támogatottak. Hasonlóképpen ha adjon hozzá egy kimenő biztonsági szabályt, és válassza **Szolgáltatáscímke** célhelyeként, ugyanezt a listát lehetőségei **forráscímke** jelenik meg. A csak érvényes beállítások ugyanazok, mint a **forráscímke**, amint az az előző listában.

- Hálózati biztonsági csoportok (NSG) nem működik az Azure Stackhez ugyanolyan módon globális Azure-ban. Az Azure-ban, több port adhatók meg egy NSG-szabály (a portal, PowerShell, használatával és a Resource Manager-sablonok). Az Azure Stackben azonban nem állíthatja be több portot egy NSG-szabályt a portálon keresztül. A probléma megkerüléséhez használja egy Resource Manager-sablon vagy a PowerShell segítségével ezek további szabályokat állíthat be.

<!-- 3203799 - IS, ASDK -->
- Az Azure Stack nem támogatja a több mint 4 hálózati adapter (NIC) való csatlakoztatás egy Virtuálisgép-példánnyal, a példány méretétől függetlenül.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A storage erőforrás-szolgáltatót regisztrálnia kell, az előfizetés az első Azure-függvény létrehozása előtt.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1902 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload). 

Csak csatlakoztatott forgatókönyvekben Azure Stack üzemelő példányok rendszeres időnként ellenőrzik egy biztonságos végpontot, és automatikusan értesíti, ha egy frissítés érhető el a felhőben. További információkért lásd: [frissítések kezelése az Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
