---
title: App Service az Azure-verem update 1 kibocsátási megjegyzései |} Microsoft Docs
description: A frissítés a veremben Azure App Service megismerése az ismert problémákról, valamint a frissítés letöltése helyét.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 80bd865b7a08d9488c0fb6a1a5b60445b9c6eaaa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service az Azure-verem update 1 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A kibocsátási megjegyzések a fejlesztései és a javításokat az Azure App Service Azure verem Update 1 és olyan ismert problémákat ismertetik. Ismert problémák közvetlenül a telepítés, a frissítési folyamat és a problémákat a build (telepítés utáni) kapcsolatos problémák vannak felosztva.

> [!IMPORTANT]
> A 1802 frissítés alkalmazásához a integrált Azure verem rendszerre, vagy telepítheti a legújabb Azure verem szoftverfejlesztői készlet Azure App Service üzembe helyezése előtt.
>
>

## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure verem Update 1 buildszáma **69.0.13698.9**

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az Azure App Service Azure veremben új telepítés most igényel a [három-tulajdonos helyettesítő tanúsítvány](azure-stack-app-service-before-you-get-started.md#get-certificates) , amelyben az Azure App Service-ben mostantól kezelése a Kudu SSO fejlesztései miatt. Az új tulajdonos  **\*. sso.appservice.\< a régióban\>.\< tartománynév\>.\< bővítmény\>**
>
>

Tekintse meg a [mielőtt hozzáfogna dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

Az Azure App Service Azure verem Update 1 tartalmazza a következő fejlesztéseket és javításokat:

- **Magas rendelkezésre állás az Azure App Service** – az Azure-verem 1802 update szolgáltatást munkaterhelések történő központi fault tartományok. Ezért App Service-infrastruktúrát, képes lehet a hibatűrő, mivel a tartalék tartományok lesznek telepítve. Alapértelmezés szerint a minden új központi telepítéséhez, az Azure App Service ezzel a képességgel rendelkezik, azonban a központi telepítés előtt Azure verem 1802 befejeződött a frissítés alkalmazva tekintse meg a [App Service tartalék tartomány dokumentáció](azure-stack-app-service-fault-domain-update.md)

- **Meglévő virtuális hálózat üzembe** -ügyfelek most már telepítheti a verem Azure App Service egy meglévő virtuális hálózaton belül. A meglévő virtuális hálózat telepítése lehetővé teszi az ügyfelek csatlakozni az SQL Server és a fájlkiszolgáló személyes portokon keresztül az Azure App Service-ben szükséges. A telepítés során az ügyfelek választhatja azonban központi telepítése egy meglévő virtuális hálózatban [kell létrehozni, használja az App Service-alhálózatok](azure-stack-app-service-before-you-get-started.md#virtual-network) telepítését megelőzően.

- Szoftverfrissítések **App Services-bérlő, a rendszergazda, Funkciók portálok és a Kudu eszközök**. Konzisztens Azure verem portál SDK-verzió.

- **A következő alkalmazás-keretrendszerbeli és eszközök frissítések**:
    - Hozzáadott **.Net 2.0 alapvető** támogatja
    - Hozzáadott **Node.JS** verziók:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Hozzáadott **NPM** verziók:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Hozzáadott **PHP** frissítések:
        - 5.6.32
        - 7.0.26 (x86 és x64)
        - 7.1.12 (x86 és x64)
    - Frissített **Git for Windows** v 2.14.1
    - Frissített **Mercurial** v4.5.0 számára

  - Támogatása az **csak HTTPS** egyéni tartományi szolgáltatásban a bérlői portálon App Service szolgáltatást. 

  - Az egyéni tárolási választó tárolási kapcsolat az Azure Functions hozzáadott érvényesítése 

#### <a name="fixes"></a>Javítások

- Amikor egy offline központi telepítési csomagot hoz létre, az ügyfelek többé nem kapja meg hozzáférés megtagadásáról szóló hibaüzenetet, amikor megnyitja a mappa az App Service-telepítő

- Az alkalmazás bérlői portál az egyéni tartományok szolgáltatás használata során meg a problémát.

- Rendszergazda fenntartott nevek használatával a telepítés során az ügyfelek megakadályozása

- Engedélyezve van az App Service telepítési **tartományhoz csatlakoztatott** fájlkiszolgáló

- Azure verem legfelső szintű továbbfejlesztett beolvasása a parancsfájl-tanúsítványt, és most ellenőrzi a legfelső szintű tanúsítvány az App Service telepítőjét a.

- Az Azure Resource Manager alatt adja vissza, ha egy előfizetés rögzített helytelen állapot Microsoft.Web névtér a benne lévő erőforrások törlése.

### <a name="known-issues-with-the-deployment-process"></a>A telepítési folyamat szolgáltatással kapcsolatos ismert problémák

- Tanúsítvány érvényesítési hibák

Egyes ügyfelek rendelkezik közben a problémák miatt túl szigorú ellenőrzés a telepítő az integrált rendszeren való telepítésekor tanúsítványok biztosítása az App Service telepítőjét. Az App Service telepítőjét új kiadása, az ügyfelek kell [töltse le a frissített telepítő](https://aka.ms/appsvconmasinstaller). Ha folytatja, a frissített telepítővel tanúsítványok érvényesítése közben hibákat tapasztal, forduljon a támogatási szolgálathoz.

- A probléma integrált rendszer Azure verem legfelső szintű tanúsítvány lekérése.

A Get-AzureStackRootCert.ps1 hibát okozott az ügyfelek számára nem sikerült beolvasni az Azure-verem legfelső szintű tanúsítvány olyan gépen, amely nem rendelkezik a legfelső szintű tanúsítvány telepítése a parancsfájl végrehajtása közben. A parancsfájl is most újra megjelent, ezt a problémát, és a kérelem ügyfelek feloldása [töltse le a frissített segítő parancsfájlok](https://aka.ms/appsvconmashelpers). Ha folytatja, a főtanúsítványt a frissített parancsfájllal beolvasása hibákat tapasztal, forduljon a támogatási szolgálathoz.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák

- Nincsenek ismert problémák a frissítés az Azure App Service Azure verem frissítése 1.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- A Tárolóhelycsere nem működik.

Hely tárolóhelycsere ebben a kiadásban megszakad. Működésének visszaállításához végezze el az alábbi lépéseket:

1. A ControllersNSG hálózati biztonsági csoportot **engedélyezése** az App controller szolgáltatáspéldány távoli asztali kapcsolatokat. AppService.local cserélje le az App Service telepítette az erőforráscsoport nevét.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

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

2. Keresse meg a **CN0-VM** alatt az Azure verem felügyeleti portálon található virtuális gépek és **kattintson a kapcsolódás** megnyitása a távoli asztali munkamenetgazda, a vezérlő példánya. Az App Service központi telepítése során megadott hitelesítő adatokat használja.
3. Start **rendszergazdaként PowerShell** és hajtsa végre a következő parancsfájlt

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Zárja be a távoli asztali munkamenetben.
5. Állítsa vissza a ControllersNSG hálózati biztonsági csoport **Megtagadás** az App controller szolgáltatáspéldány távoli asztali kapcsolatokat. AppService.local cserélje le az App Service telepítette az erőforráscsoport nevét.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

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
- Munkavállalók nem érhető el a fájlkiszolgáló, ha az App Service egy meglévő virtuális hálózat és a fájlkiszolgáló csak érhető el a privát hálózaton.
 
Ha úgy dönt, hogy az üzembe helyezés meglévő virtuális hálózat és a fájl kiszolgálóhoz való csatlakozás belső IP-címet, hozzá kell adnia egy kimenő biztonsági szabály engedélyezése az SMB adatforgalmát. a munkavégző és a fájlkiszolgáló között. Ehhez nyissa meg a felügyeleti portál WorkersNsg, és a következő tulajdonságokkal kimenő biztonsági szabály felvétele:
 * Forrás: bármely
 * Forrás-porttartomány: *
 * Cél: IP-címek
 * Cél IP-címtartomány: IP-címtartományra a fájlkiszolgáló
 * Célporttartomány: 445-ös
 * Protokoll: TCP
 * Művelet: engedélyezése
 * Prioritás: 700
 * Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service Azure veremben működő felhő rendszergazdák kapcsolatos ismert problémák

A dokumentációban találja a [Azure verem 1802 kibocsátási megjegyzései](azure-stack-update-1802.md)

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintéséért lásd: [Azure verem – áttekintés az Azure App Service](azure-stack-app-service-overview.md).
- App Service Azure veremben telepítendő előkészítése további információkért lásd: [az App Service Azure veremben megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
