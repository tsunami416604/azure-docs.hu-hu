---
title: A nyelvi ismertetése (LUIS) - Azure végpont kvóta növeléséhez használja a Microsoft Azure Traffic Manager |} Microsoft Docs
description: A Microsoft Azure Traffic Manager segítségével végpont kvóta elosztva a nyelvi ismertetése (LUIS) végpont kvóta növeléséhez több előfizetések
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/07/2018
ms.author: v-geberr
ms.openlocfilehash: 513d4395b1d3e631855c2f6e132d54331b3ddf8d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266345"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Végpont kvóta közötti kulcsok kezelése a Microsoft Azure Traffic Manager segítségével
Nyelvi ismertetése (LUIS) lehetővé teszi egy kulcs kvótán túl a végpont kérelem kvóta növeléséhez. Hozzon létre egy további kulcsok LUIS, és vegye fel őket a LUIS alkalmazáshoz az ebben az esetben a **közzététel** lapját a **erőforrások és a kulcsok** szakasz. 

Az ügyfélalkalmazás rendelkezik, a kulcsok közötti forgalom kezelésére. LUIS, amely nem végez. 

Ez a cikk azt ismerteti, hogyan kulcsokat az Azure közötti forgalom kezelésére [Traffic Manager][traffic-manager-marketing]. Már rendelkeznie kell egy betanított és közzétett LUIS alkalmazást. Ha még nem rendelkezik ilyennel, kövesse az előre elkészített tartomány [gyors üzembe helyezés](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>PowerShell csatlakozni az Azure-portálon
Az a [Azure] [ azure-portal] portal, nyissa meg a PowerShell-ablakot. A PowerShell-ablakban ikonja van a **> _** a felső navigációs sávban. PowerShell használatával a portálról, a PowerShell legújabb kap, és Ön hitelesítve. A portál PowerShell szükséges egy [Azure Storage](https://azure.microsoft.com/services/storage/) fiók. 

![Képernyőfelvétel az Azure portál Powershell ablak megnyitása](./media/traffic-manager/azure-portal-powershell.png)

A következő szakaszok használata [Traffic Manager PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Azure-erőforráscsoport létrehozása a PowerShell használatával
Létrehozása az Azure-erőforrások, előtt hozzon létre egy erőforráscsoportot az összes erőforrást tartalmaz. Nevezze el az erőforráscsoportot `luis-traffic-manager` és használja a régió `West US`. Az erőforráscsoport régió a csoport metaadatokat tárol. Az erőforrások azt nem lelassul, ha azok egy másik régióban. 

Az erőforráscsoport létrehozása **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** parancsmagot:

```PowerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Teljes endpoint kvóta növeléséhez kulcsok LUIS létrehozása
1. Az Azure-portálon létrehoz két **nyelvi ismertetése** kulcsok, egyet-egyet a `West US` és egy-egy a `East US`. Használja a meglévő erőforráscsoportot, az előző szakaszban létrehozott nevű `luis-traffic-manager`. 

    ![Képernyőfelvétel az Azure portálon két LUIS kulcsokkal luis forgalomkezelő erőforráscsoportban található](./media/traffic-manager/luis-keys.png)

2. Az a [LUIS] [ LUIS] webhely, az a **közzététel** lapon kulcs hozzáadása az alkalmazáshoz, és közzé az alkalmazást. 

    ![Képernyőkép az LUIS portál két LUIS kulcsokkal közzététel az oldalon](./media/traffic-manager/luis-keys-in-luis.png)

    A példa URL-címet a **végpont** lekérdezési paraméterként oszlop használja az előfizetés kulccsal GET kérés. A két új kulcsokkal végponti URL-címek másolása. A cikk későbbi részében a Traffic Manager-konfiguráció részeként használhatók.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>A Traffic Managerrel kulcsok között LUIS végpont vonatkozó kérések kezelése
A TRAFFIC Manager létrehoz egy új DNS hozzáférési pont a végpontokat. Nem jár el átjáróként vagy proxyként megegyezik, azonban kizárólag a DNS-szinten. Ebben a példában a DNS-rekordokat nem változik. A DNS-szalagtár egyik a Traffic Managerrel megszerezni a helyes végpontját, hogy adott kérés kommunikációhoz használ. _Minden egyes_ kérelem szánt LUIS először határozza meg, melyik LUIS végpontot kell használni a Traffic Manager kérelem megadása szükséges. 

### <a name="polling-uses-luis-endpoint"></a>Lekérdezési használ LUIS végpont
A TRAFFIC Manager kérdezze le a végpontokat rendszeres időközönként ellenőrizze, hogy a végpont nem továbbra is elérhető. A Traffic Manager URL-címet kell egy GET kérelemhez érhető el, és térjen vissza a 200 kérdezi le. A végpont URL-cím a **közzététel** lap ezt végzi. Mivel minden egyes előfizetés kulcs egy másik útvonalat, és a lekérdezési karakterlánc-paraméterrel rendelkezik, a minden előfizetés kulcsot kell egy másik lekérdezés elérési utat. Minden alkalommal, amikor lekérdezi a Traffic Manager, költség kvóta kérelmet. A lekérdezési karakterlánc paraméter **q** a LUIS végpont az LUIS küldött utterance. Ez a paraméter egy utterance küldése helyett segítségével hozzáadása Traffic Manager lekérdezési a LUIS végpont napló, a hibakeresési technika konfigurálva a Traffic Manager lekérése közben.

Minden egyes LUIS végponthoz a saját elérési utat, mert a saját Traffic Manager-profil szükséges. Ahhoz, hogy a profilok kezeléséhez, hozzon létre egy [ _beágyazott_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architektúra. Egy szülő-profilt a gyermekek profilok mutat, és a forgalom kezeléséhez.

Miután beállította a Traffic Manager, akkor ne felejtse el módosítani az útvonalat a naplózási = false lekérdezési karakterlánc paraméter, a napló nem kitöltse a lekérdezés.

## <a name="configure-traffic-manager-with-nested-profiles"></a>A Traffic Manager beágyazott profilok konfigurálása
Az alábbi szakaszok a két gyermek profilok, a keleti LUIS kulcs és az egyikben a nyugati LUIS kulcs létrehozása. Ezután egy szülő-profil létrehozása, és a két gyermek profilok hozzáadódnak a szülő profil. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>USA keleti régiója Traffic Manager-profilt létrehozása a PowerShell használatával
USA keleti régiója Traffic Manager-profil több lépésből áll: profil létrehozása, adja hozzá a végpont és megadnia. Traffic Manager-profil sok végpont rendelkezhet, de minden egyes végpont rendelkezik ugyanazt az érvényesítési elérési utat. Mivel LUIS végpont URL-címéből az east és a west előfizetések különböző régióban és az Előfizetés kulcs miatt, minden LUIS végpont nem lehet a profil egy végpontot. 

1. A profil létrehozása **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** parancsmag

    A következő parancsmag segítségével hozzon létre a profilt. Ügyeljen arra, hogy módosítsa a `appIdLuis` és `subscriptionKeyLuis`. A subscriptionKey van keleti Velünk LUIS kulcshoz. Ha az elérési út nem megfelelő, a LUIS app ID és az Előfizetés kulcs, beleértve a Traffic Manager lekérdezési-e állapota `degraded` mert forgalom kezelése nem lehet sikeresen kérni a LUIS végpont. Győződjön meg arról, hogy értékének `q` van `traffic-manager-east` hívjuk fel a LUIS végpont logs ezt az értéket.

    ```PowerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Ebben a táblázatban a parancsmag minden változót:
    
    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-Név|Luis-profil-eastus|Az Azure portál traffic Manager neve|
    |-ResourceGroupName|Luis forgalomkezelő|Az előző szakaszban létrehozott|
    |-TrafficRoutingMethod|Teljesítmény|További információkért lásd: [Traffic Manager útválasztási módjai][routing-methods]. Használja a teljesítmény, ha az URL-kérelmet a Traffic Manager felhasználó régióban kell származnia. Ha egy chatbot vagy más alkalmazás keresztül haladó, feladata a chatbot a Traffic Manager hívásában régió utánozzák. |
    |-RelativeDnsName|Luis-dns-eastus|Ez a szolgáltatás altartomány: luis-dns-eastus.trafficmanager.net|
    |-Élettartam|30|Lekérdezési időköz, 30 másodperces|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port és LUIS protokollja HTTPS/443-as|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Cserélje le <appIdLuis> és <subscriptionKeyLuis> saját értékekkel.|
    
    A kérelem sikeres rendelkezik nincs válasz.

2. USA keleti régiója végpont hozzáadása **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** parancsmag

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Ebben a táblázatban a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-EndpointName|Kelet-végpont Luis|A profil alatt jelenik meg a végpont neve|
    |-TrafficManagerProfile|$eastprofile|1. lépésben létrehozott profil objektum használja|
    |-Típus|ExternalEndpoints|További információkért lásd: [Traffic Manager-végpontot][traffic-manager-endpoints] |
    |-A cél|eastus.API.cognitive.microsoft.com|Ez az a tartomány a LUIS végpont.|
    |-EndpointLocation|"eastus"|A végpont régiója|
    |-EndpointStatus|Engedélyezve|Engedélyezze a végpont létrehozásakor|

    A sikeres válasz néz ki:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. USA keleti régiója végpont beállítása **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** parancsmag

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    A sikeres válasz lesz az azonos választ, mint 2. lépés.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Az USA nyugati régiója Traffic Manager-profil létrehozása a PowerShell használatával
USA nyugati régiója Traffic Manager-profilt létrehozni, kövesse a lépéseket: profil létrehozása, adja hozzá a végpont és megadnia.

1. A profil létrehozása **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag

    A következő parancsmag segítségével hozzon létre a profilt. Ügyeljen arra, hogy módosítsa a `appIdLuis` és `subscriptionKeyLuis`. A subscriptionKey van keleti Velünk LUIS kulcshoz. Az elérési út nem megfelelő a LUIS app ID és az előfizetés kulcsot is beleértve, a Traffic Manager-lekérdezés akkor állapota `degraded` mert forgalom kezelése nem lehet sikeresen kérni a LUIS végpont. Győződjön meg arról, hogy értékének `q` van `traffic-manager-west` hívjuk fel a LUIS végpont logs ezt az értéket.

    ```PowerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Ebben a táblázatban a parancsmag minden változót:
    
    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-Név|Luis-profil-westus|Az Azure portál traffic Manager neve|
    |-ResourceGroupName|Luis forgalomkezelő|Az előző szakaszban létrehozott|
    |-TrafficRoutingMethod|Teljesítmény|További információkért lásd: [Traffic Manager útválasztási módjai][routing-methods]. Használja a teljesítmény, ha az URL-kérelmet a Traffic Manager felhasználó régióban kell származnia. Ha egy chatbot vagy más alkalmazás keresztül haladó, feladata a chatbot a Traffic Manager hívásában régió utánozzák. |
    |-RelativeDnsName|Luis-dns-westus|Ez a szolgáltatás altartomány: luis-dns-westus.trafficmanager.net|
    |-Élettartam|30|Lekérdezési időköz, 30 másodperces|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port és LUIS protokollja HTTPS/443-as|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Cserélje le <appId> és <subscriptionKey> saját értékekkel. Az Előfizetés kulcs nem egyezik a keleti előfizetés kulcs megjegyezhető|
    
    A kérelem sikeres rendelkezik nincs válasz.

2. USA nyugati régiója végpont hozzáadása **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** parancsmag

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Ebben a táblázatban a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-EndpointName|Nyugat-végpont Luis|A profil alatt jelenik meg a végpont neve|
    |-TrafficManagerProfile|$westprofile|1. lépésben létrehozott profil objektum használja|
    |-Típus|ExternalEndpoints|További információkért lásd: [Traffic Manager-végpontot][traffic-manager-endpoints] |
    |-A cél|westus.API.cognitive.microsoft.com|Ez az a tartomány a LUIS végpont.|
    |-EndpointLocation|"westus"|A végpont régiója|
    |-EndpointStatus|Engedélyezve|Engedélyezze a végpont létrehozásakor|

    A sikeres válasz néz ki:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. USA nyugati régiója végpont beállítása **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    A sikeres válasz az azonos választ, mint 2. lépés:.

### <a name="create-parent-traffic-manager-profile"></a>Szülő Traffic Manager-profil létrehozása
A szülő Traffic Manager-profil létrehozása, és a csatolás két gyermek Traffic Manager-profilokat a szülőhöz.

1. A szülő-profil létrehozása **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag

    ```PowerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Ebben a táblázatban a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-Név|szülő-Luis-profil|Az Azure portál traffic Manager neve|
    |-ResourceGroupName|Luis forgalomkezelő|Az előző szakaszban létrehozott|
    |-TrafficRoutingMethod|Teljesítmény|További információkért lásd: [Traffic Manager útválasztási módjai][routing-methods]. Használja a teljesítmény, ha az URL-kérelmet a Traffic Manager felhasználó régióban kell származnia. Ha egy chatbot vagy más alkalmazás keresztül haladó, feladata a chatbot a Traffic Manager hívásában régió utánozzák. |
    |-RelativeDnsName|szülő-dns-Luis|Ez a szolgáltatás altartomány: luis-dns-parent.trafficmanager.net|
    |-Élettartam|30|Lekérdezési időköz, 30 másodperces|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port és LUIS protokollja HTTPS/443-as|
    |-MonitorPath|`/`|Az elérési út nem számít, mivel a gyermek végpont elérési út helyett szerepel.|

    A kérelem sikeres rendelkezik nincs válasz.

2. USA keleti régiója gyermek profil hozzáadása a szülő **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** és **NestedEndpoints** típusa

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Ebben a táblázatban a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-EndpointName|gyermek-végpont-useast|Kelet-profil|
    |-TrafficManagerProfile|$parentprofile|Profil hozzárendelése ehhez a végponthoz való|
    |-Típus|NestedEndpoints|További információkért lásd: [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-Targetresourceid azonosítója|$eastprofile. Azonosítója|A gyermek profil azonosítója|
    |-EndpointStatus|Engedélyezve|Szülő való hozzáadását követően végpont állapota|
    |-EndpointLocation|"eastus"|[Az Azure-régió nevét](https://azure.microsoft.com/global-infrastructure/regions/) erőforrás|
    |-MinChildEndpoints|1|A gyermek végpontok minimális száma|

    A sikeres válasz megjelenését, például a következőt, és tartalmazza az új `child-endpoint-useast` végpont:    

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. USA nyugati régiója gyermek profil hozzáadása a szülő **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** parancsmag és **NestedEndpoints** típusa

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Ebben a táblázatban a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve|Cél|
    |--|--|--|
    |-EndpointName|gyermek-végpont-uswest|Nyugat-profil|
    |-TrafficManagerProfile|$parentprofile|Profil hozzárendelése ehhez a végponthoz való|
    |-Típus|NestedEndpoints|További információkért lásd: [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-Targetresourceid azonosítója|$westprofile. Azonosítója|A gyermek profil azonosítója|
    |-EndpointStatus|Engedélyezve|Szülő való hozzáadását követően végpont állapota|
    |-EndpointLocation|"westus"|[Az Azure-régió nevét](https://azure.microsoft.com/global-infrastructure/regions/) erőforrás|
    |-MinChildEndpoints|1|A gyermek végpontok minimális száma|

    A sikeres válasz megjelenését, például, és mindkét az előző is `child-endpoint-useast` végpont és az új `child-endpoint-uswest` végpont:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. A végpontokat **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag 

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    A sikeres válasz az azonos választ, mint 3. lépés:.

### <a name="powershell-variables"></a>PowerShell változók
A korábbi szakaszokban létrehozott három PowerShell változók: `$eastprofile`, `$westprofile`, `$parentprofile`. Ezeket a változókat szolgálnak a Traffic Manager konfigurációs vége felé. Ha úgy döntene, hogy nem hozza létre a változókat, vagy elfelejtette, vagy a PowerShell-ablakban túllépi az időkorlátot, használhatja a PowerShell-parancsmag  **[Get-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, hogy a profil újból beolvasni, és rendelje hozzá egy változóhoz. 

Cserélje le a csúcsos zárójelek elemek `<>`, a három profil van szüksége a megfelelő értékkel. 

```PowerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>A Traffic Manager works ellenőrzése
Győződjön meg arról, hogy a Traffic Manager-profilok munka, a profilok állapotának rendelkeznie kell a `Online` ezt az állapotot a végpont lekérdezési elérési alapul. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Új profilok megtekintése az Azure-portálon
A mindhárom profil létrehozása az erőforrások megtekintésével ellenőrizheti a `luis-traffic-manager` erőforráscsoportot.

![Képernyőfelvétel az Azure erőforrás csoport luis-adatforgalom-kezelő](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Győződjön meg arról a profil állapota Online
A Traffic Manager kérdezze le az elérési útját minden végpontot, győződjön meg arról, hogy online állapotban. Ha elérhető, a gyermek profilok állapotának vannak `Online`. Ez jelenik meg a **áttekintése** minden profil. 

![Képernyőfelvétel az Azure Traffic Manager-profil a figyelő állapota az Online megjelenítő áttekintés](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>A Traffic Manager works lekérdezési ellenőrzése
Egy másik érvényesítése a lekérdezés működését a traffic manager módja a LUIS végpont logs együtt. Az a [LUIS] [ LUIS] webhely alkalmazások listája lapon, a végpont napló, az alkalmazás exportálása. A Traffic Manager gyakran kérdezze le a két végpontok, mert nincsenek a naplókban akkor is, ha csak néhány perc múlva a le lett. Ne felejtse el, keresse meg a lekérdezés kezdődik ahol bejegyzéseket `traffic-manager-`.

```text
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>A Traffic Manager works DNS-válasz érvényesítése
Ellenőrizze, hogy a DNS-választ ad vissza egy LUIS végpont, kérjen a forgalom kezelése szülő DNS, a DNS-ügyfél szalagtárat használó profil. A DNS-beli név: a szülő profil `luis-dns-parent.trafficmanager.net`.

A következő Node.js-kódot egy kérést a szülő-profilhoz, és adja vissza egy LUIS végpont:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A sikeres válasz a LUIS végponthoz van:

```cmd
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>A Traffic Manager-szülő profil használata
Végpontok közötti forgalom kezelésére, szüksége lehet beszúrni a Traffic Manager DNS-hívás a LUIS végpont található. A hívás minden egyes LUIS végpont kérés történik, és LUIS ügyfélalkalmazás a felhasználó földrajzi helye szimulálása kell. A DNS-válaszkód Between az LUIS ügyfélalkalmazást és a kérelem hozzáadása LUIS végpont előrejelzését. 


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Távolítsa el a két LUIS előfizetés kulcsokat, a három Traffic Manager-profilokat és az erőforráscsoport, ezek az öt erőforrások találhatók. Ez az Azure-portálon történik. Törli a öt erőforrásokat, az erőforrások listájában. Ezután törölje az erőforráscsoportot. 

## <a name="next-steps"></a>További lépések

Felülvizsgálati [köztes](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) megértése, hogyan adható a forgalom felügyeleti kód BotFramework bot BotFramework v4 lehetőségeit. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
