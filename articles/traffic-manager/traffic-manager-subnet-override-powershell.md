---
title: Az Azure Traffic Manager alhálózati felülbírálása az Azure PowerShell használatával | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy a Traffic Manager alhálózati felülbírálása hogyan bírálja felül a Traffic Manager-profil útválasztási metódusát, hogy a végfelhasználói IP-cím alapján irányítsa a forgalmat egy végpontra az azure-beli végpont-hozzárendelések számára. Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938424"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>A Traffic Manager alhálózati felülbírálása az Azure Powershell használatával

A Traffic Manager alhálózati felülbírálás lehetővé teszi a profil útválasztási módjának módosítását.  A felülbírálás hozzáadása a végfelhasználó IP-címe alapján irányítja a forgalmat egy előre meghatározott IP-tartomány-végpont leképezéssel. 

## <a name="how-subnet-override-works"></a>Az alhálózat-felülbírálás működése

Amikor alhálózati felülbírálásokat ad hozzá egy forgalomkezelő profilhoz, a Traffic Manager először ellenőrzi, hogy van-e alhálózati felülbírálás a végfelhasználó IP-címéhez. Ha talál egyet, a felhasználó DNS-lekérdezése a megfelelő végpontra lesz irányítva.  Ha nem található leképezés, a Traffic Manager visszatér a profil eredeti útválasztási módszeréhez. 

Az IP-címtartományok cidr-tartományokként (például 1.2.3.0/24) vagy címtartományként (például 1.2.3.4-5.6.7.8) adhatók meg. Az egyes végpontokhoz társított IP-tartományoknak egyedinek kell lenniük az adott végponthoz képest. A különböző végpontok közötti IP-tartományok átfedése a Traffic Manager által elutasított profilt eredményezi.

Az útválasztási profiloknak két típusa van, amelyek támogatják az alhálózati felülbírálásokat:

* **Földrajzi** – Ha a Traffic Manager alhálózati felülbírálást talál a DNS-lekérdezés IP-címéhez, a lekérdezést a végpont állapotátől függetlenül a végponthoz irányítja.
* **Teljesítmény** – Ha a Traffic Manager alhálózati felülbírálása a DNS-lekérdezés IP-címét, akkor csak a forgalmat a végpontra, ha kifogástalan.  Traffic Manager visszaesik a teljesítmény-útválasztási heurisztikus, ha az alhálózati felülbírálási végpont nem kifogástalan.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager-alhálózat-felülbírálás létrehozása

Traffic Manager-alhálózat-felülbírálás létrehozásához az Azure PowerShell segítségével hozzáadhatja a felülbírálás alhálózatait a Traffic Manager-végponthoz.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/powershell)követett parancsokat, vagy a PowerShell t a számítógépről futtathatja. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShell t a számítógépről futtatja, szüksége van az Azure PowerShell-modulra, az 1.0.0-s vagy újabb verzióra. Futtathatja `Get-Module -ListAvailable Az` a telepített verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Login-AzAccount` az Azure-ba való bejelentkezéshez is futnia kell.


1. **A Traffic Manager végpontjának lekérése:**

    Az alhálózati felülbírálás engedélyezéséhez olvassa be azt a végpontot, amelyhez hozzá kívánja adni a felülírást, és a [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)használatával változóban tárolni szeretné.

    Cserélje le a Name, ProfileName és ResourceGroupName értéket a módosító végpont értékeire.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Adja hozzá az IP-címtartományt a végponthoz:**
    
    Az IP-címtartomány hozzáadása a végponthoz, az [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) segítségével adja hozzá a tartományt.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    A tartományok hozzáadása után használja a [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) ot a végpont frissítéséhez.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Az IP-címtartomány eltávolítása az [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)segítségével hajtható végre.

1.  **A Traffic Manager végpontjának lekérése:**

    Az alhálózati felülbírálás engedélyezéséhez olvassa be azt a végpontot, amelyhez hozzá kívánja adni a felülírást, és a [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)használatával változóban tárolni szeretné.

    Cserélje le a Name, ProfileName és ResourceGroupName értéket a módosító végpont értékeire.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Az IP-címtartomány eltávolítása a végpontból:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     A tartományok eltávolítása után használja a [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) ot a végpont frissítéséhez.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>További lépések
További információ a Traffic Manager [forgalomirányítási módszereiről.](traffic-manager-routing-methods.md)

További információ az [Alhálózati forgalom-útválasztási módszerről](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
