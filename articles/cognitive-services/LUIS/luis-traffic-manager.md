---
title: Végpont kvóta növeléséhez
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) lehetővé teszi, hogy a végpont kérelmi kvótát egy kulcs kvóta túl kínál. További kulcsokat hoz létre a LUIS, és hozzáadni azokat a LUIS-alkalmazás az ehhez a **közzététel** lapját a **erőforrások és a kulcsok** szakaszban.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: ba3ca363afe96c137a4a9eecdeda33e0f9129111
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868429"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>A Microsoft Azure Traffic Manager használatával kezelheti a végpont kvóta kulcsok
Language Understanding (LUIS) lehetővé teszi, hogy a végpont kérelmi kvótát egy kulcs kvóta túl kínál. További kulcsokat hoz létre a LUIS, és hozzáadni azokat a LUIS-alkalmazás az ehhez a **közzététel** lapját a **erőforrások és a kulcsok** szakaszban. 

Az ügyfélalkalmazás rendelkezik, a kulcsok közötti forgalom kezelésére. A LUIS, amely nem csinál. 

Ez a cikk azt ismerteti, hogyan kezelheti a forgalmat a kulcsok az Azure-ral való [Traffic Manager][traffic-manager-marketing]. Már rendelkeznie kell egy betanított és közzétett LUIS-alkalmazás. Ha nem rendelkezik egy, kövesse az előre összeállított tartomány [rövid](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>PowerShell csatlakozhat az Azure Portalon
Az a [Azure] [ azure-portal] portálon nyissa meg a PowerShell-ablakot. A PowerShell ablak esetében a ikon a **> _** a felső navigációs sávban. PowerShell használatával a portálról, a PowerShell legújabb kap, és a hitelesítés. A portálon PowerShell szükséges egy [Azure Storage](https://azure.microsoft.com/services/storage/) fiókot. 

![Képernyőkép az Azure portal, Powershell-ablak megnyitása](./media/traffic-manager/azure-portal-powershell.png)

Az alábbi szakaszok [Traffic Manager PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Azure-erőforráscsoport létrehozása a PowerShell használatával
Az Azure-erőforrások létrehozását, mielőtt hozzon létre egy erőforráscsoportot az összes erőforrást tartalmaz. Nevezze el az erőforráscsoport `luis-traffic-manager` és a régió használata `West US`. A régió, az erőforráscsoport a csoport metaadatait tárolja. Az erőforrások, nem fog lelassulnak, ha azok egy másik régióban. 

Hozzon létre erőforráscsoportot **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** parancsmagot:

```powerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>A LUIS teljes endpoint kvóta növeléséhez-kulcsok létrehozása
1. Az Azure Portalon hozzon létre két **Language Understanding** kulcsokat, egy a a `West US` , a másik pedig a `East US`. Használja a nevű meglévő erőforráscsoportot, az előző szakaszban létrehozott `luis-traffic-manager`. 

    ![Képernyőkép az Azure portal két LUIS kulcsokkal a luis-traffic-manager erőforráscsoportban](./media/traffic-manager/luis-keys.png)

2. Az a [LUIS] [ LUIS] webhelyén, a a **kezelés** részben, a a **kulcsokat és a végpontok** lapon kulcsok rendelni az alkalmazást, és tegye közzé újra az alkalmazás által Válassza a **közzététel** gombra a jobb felső menüben. 

    Példa URL-cím a **végpont** oszlop egy GET kéréssel használ a végpont kulcs lekérdezési paramétert. Másolja a két új kulcsok végponti URL-címek. A Traffic Manager konfigurálásakor a cikk későbbi részében használják.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>A Traffic Managerrel kulcsok között LUIS végpont vonatkozó kérések kezelése
A TRAFFIC Manager létrehoz egy új DNS hozzáférési pont a végpontokon. Nem jár el vagy proxyként működött, de szigorúan csak a DNS szintjén. Ebben a példában minden DNS-rekordokat nem változik. Egy DNS-kódtár használatával kommunikálni a Traffic Managerrel a megfelelő végpontra lekérni az adott kérelmet. _Minden egyes_ kérelem szánt LUIS először kell meghatározni, melyik LUIS használandó végpont, egy Traffic Manager kérelmet. 

### <a name="polling-uses-luis-endpoint"></a>Lekérdezési használja a LUIS-végpont
A TRAFFIC Manager lekérdezi a végpontok rendszeres időközönként ellenőrizze, hogy a végpont továbbra is elérhető. A Traffic Manager URL-címet kell egy GET kéréssel érhető el, és adja vissza a rendszer 200-as lekérdezve. A végponti URL-cím a **közzététel** ezt oldal végzi. Mivel minden egyes végpontkulcsának rendelkezik egy másik útvonalat, és a lekérdezési karakterlánc paraméterei, minden végponti kulcs kell egy másik lekérdezés elérési utat. Minden alkalommal, amikor lekérdezi a Traffic Manager, kerül a kvótakérelem. A lekérdezési karakterlánc paramétereként **q** a LUIS végpont az utterance (kifejezés), a LUIS küldött. Ez a paraméter helyett az utterance (kifejezés) segítségével hozzáadása Traffic Manager lekérdezési a LUIS-végpont naplót, a hibakeresési technikát konfigurált Traffic Manager beolvasása közben.

A LUIS végpontot a saját elérési útja kell, mert a saját Traffic Manager-profil szükséges. Annak érdekében, hogy a profilok kezelése, hozzon létre egy [ _beágyazott_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architektúra. Egy szülő profilt a gyermekek profilok mutat, és kezelheti az adatforgalmat azok között.

Ha a Traffic Manager konfigurálva van, ne felejtse el módosítani az útvonalat a naplózási = false (hamis) lekérdezési karakterlánc paramétereként, így a napló nem betelik-e a lekérdezés.

## <a name="configure-traffic-manager-with-nested-profiles"></a>A Traffic Manager konfigurálása a beágyazott profilok
Az alábbiakban két gyermek-profilok, egy, a keleti a LUIS-kulcs, és egy, a nyugati LUIS kulcs létrehozása. Ezután egy szülő-profil létrehozása, és a két alárendelt profilok kerülnek a fölérendelt profilt. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Az USA keleti Régiójában Traffic Manager-profil létrehozása a PowerShell használatával
Az USA keleti Régiójában Traffic Manager-profil létrehozásához több lépésből áll: profil létrehozása, végpont felvétele és beállítása a végpont. Traffic Manager-profil sok végpont tartozhat, de minden végpont rendelkezik ugyanazt az érvényesítési elérési utat. Mivel a LUIS végponti URL-címek esetében az USA keleti és nyugati előfizetések különböző régióban és a végpont kulcs miatt, LUIS végpontot nem lehet egyetlen végpontot a profiljában. 

1. A profil létrehozása **[New-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** parancsmag

    A profil létrehozásához használja a következő parancsmagot. Ügyeljen arra, hogy módosítsa a `appIdLuis` és `subscriptionKeyLuis`. A subscriptionKey az East US LUIS kulcs van. Ha az elérési út nem megfelelő, a LUIS alkalmazás azonosítója és a végpont kulcs, beleértve a Traffic Manager lekérdezés-e állapota `degraded` mert forgalom kezelése nem lehet sikeresen kérni a LUIS-végpontot. Győződjön meg arról, hogy értékét `q` van `traffic-manager-east` így láthatja, hogy ezt az értéket a LUIS-végpont naplókat.

    ```powerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:
    
    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Név|a Luis-profil – USA keleti régiója|A TRAFFIC Manager neve az Azure Portalon|
    |-ResourceGroupName|a Luis-traffic-manager|Az előző szakaszban létrehozott|
    |-TrafficRoutingMethod|Teljesítmény|További információkért lásd: [Traffic Manager útválasztási módszerei][routing-methods]. Használja a teljesítményt, ha az URL-cím kérésére a Traffic Manager a felhasználó a régióban kell származnia. Ha egy csevegőrobotot vagy más alkalmazás, feladata a csevegőrobot referenciaszámítógépnek a régió, a Traffic Manager hívásában. |
    |-RelativeDnsName|a Luis-dns-USA keleti régiója|Ez a szolgáltatás az altartomány: a luis-dns-eastus.trafficmanager.net|
    |-Élettartam|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port és a LUIS protokollja HTTPS/443-as|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Cserélje le <appIdLuis> és <subscriptionKeyLuis> a saját értékeire.|
    
    A kérelem sikeres válasz rendelkezik.

2. Adja hozzá az USA keleti Régiójában koncového bodu **[Add-azurermtrafficmanagerendpointconfig parancsmag esetében](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** parancsmag

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|a Luis-kelet-végpont|A profil alatt jelenik meg a végpont neve|
    |-TrafficManagerProfile|$eastprofile|1. lépésben létrehozott profil objektummal|
    |-Type|ExternalEndpoints|További információkért lásd: [Traffic Manager-végpont][traffic-manager-endpoints] |
    |-Cél|eastus.API.cognitive.microsoft.com|Ez az a tartomány a LUIS-végpont.|
    |-EndpointLocation|"eastus"|A végpont régió|
    |-EndpointStatus|Engedélyezve|Engedélyezze a végpont létrehozása|

    A sikeres válasz a következőhöz hasonló:

    ```console
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

3. Állítsa be az USA keleti Régiójában koncového bodu **[Set-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** parancsmag

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    A sikeres válasz ugyanazt a választ, 2. lépés lesz.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Az USA nyugati RÉGIÓJA Traffic Manager-profil létrehozása a PowerShell használatával
Az USA nyugati RÉGIÓJA Traffic Manager-profil létrehozásához kövesse az alábbi lépéseket:-profil létrehozása, végpont felvétele és beállítása a végpont.

1. A profil létrehozása **[New-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag

    A profil létrehozásához használja a következő parancsmagot. Ügyeljen arra, hogy módosítsa a `appIdLuis` és `subscriptionKeyLuis`. A subscriptionKey az East US LUIS kulcs van. Ha az elérési út nem megfelelő a LUIS-alkalmazás azonosítója és -végpont kulcsot is beleértve, a Traffic Manager-lekérdezés állapota-e `degraded` mert forgalom kezelése nem lehet sikeresen kérni a LUIS-végpontot. Győződjön meg arról, hogy értékét `q` van `traffic-manager-west` így láthatja, hogy ezt az értéket a LUIS-végpont naplókat.

    ```powerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:
    
    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Név|a Luis-profil-westus|A TRAFFIC Manager neve az Azure Portalon|
    |-ResourceGroupName|a Luis-traffic-manager|Az előző szakaszban létrehozott|
    |-TrafficRoutingMethod|Teljesítmény|További információkért lásd: [Traffic Manager útválasztási módszerei][routing-methods]. Használja a teljesítményt, ha az URL-cím kérésére a Traffic Manager a felhasználó a régióban kell származnia. Ha egy csevegőrobotot vagy más alkalmazás, feladata a csevegőrobot referenciaszámítógépnek a régió, a Traffic Manager hívásában. |
    |-RelativeDnsName|a Luis-dns-westus|Ez a szolgáltatás az altartomány: a luis-dns-westus.trafficmanager.net|
    |-Élettartam|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port és a LUIS protokollja HTTPS/443-as|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Cserélje le <appId> és <subscriptionKey> a saját értékeire. Ne feledje, ez a végpont kulcs nem egyezik a kelet-végpont kulcs|
    
    A kérelem sikeres válasz rendelkezik.

2. Adja hozzá az USA nyugati RÉGIÓJA koncového bodu **[Add-azurermtrafficmanagerendpointconfig parancsmag esetében](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** parancsmag

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|a Luis-Nyugat-végpont|A profil alatt jelenik meg a végpont neve|
    |-TrafficManagerProfile|$westprofile|1. lépésben létrehozott profil objektummal|
    |-Type|ExternalEndpoints|További információkért lásd: [Traffic Manager-végpont][traffic-manager-endpoints] |
    |-Cél|westus.API.cognitive.microsoft.com|Ez az a tartomány a LUIS-végpont.|
    |-EndpointLocation|"westus"|A végpont régió|
    |-EndpointStatus|Engedélyezve|Engedélyezze a végpont létrehozása|

    A sikeres válasz a következőhöz hasonló:

    ```console
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

3. Állítsa be az USA nyugati RÉGIÓJA koncového bodu **[Set-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Sikeres válasz a rendszer ugyanazt a választ, 2. lépés.

### <a name="create-parent-traffic-manager-profile"></a>Szülő Traffic Manager-profil létrehozása
A szülő Traffic Manager-profil létrehozása, és két gyermek Traffic Manager-profilok a szülő mutató hivatkozás.

1. A szülő-profil létrehozása **[New-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag

    ```powerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Név|szülő-Luis-profil|A TRAFFIC Manager neve az Azure Portalon|
    |-ResourceGroupName|a Luis-traffic-manager|Az előző szakaszban létrehozott|
    |-TrafficRoutingMethod|Teljesítmény|További információkért lásd: [Traffic Manager útválasztási módszerei][routing-methods]. Használja a teljesítményt, ha az URL-cím kérésére a Traffic Manager a felhasználó a régióban kell származnia. Ha egy csevegőrobotot vagy más alkalmazás, feladata a csevegőrobot referenciaszámítógépnek a régió, a Traffic Manager hívásában. |
    |-RelativeDnsName|a Luis-dns-szülőtartomány|Ez a szolgáltatás az altartomány: a luis-dns-parent.trafficmanager.net|
    |-Élettartam|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port és a LUIS protokollja HTTPS/443-as|
    |-MonitorPath|`/`|Az elérési út nem számít, mivel a gyermek végpont elérési út helyett szerepel.|

    A kérelem sikeres válasz rendelkezik.

2. USA keleti Régiójában gyermek profil hozzá a szülő **[Add-azurermtrafficmanagerendpointconfig parancsmag esetében](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** és **NestedEndpoints** típusa

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|gyermek-endpoint-useast|Kelet-profil|
    |-TrafficManagerProfile|$parentprofile|Ennek a végpontnak a hozzárendelt profil|
    |-Type|NestedEndpoints|További információkért lásd: [Add-azurermtrafficmanagerendpointconfig parancsmag esetében](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-Targetresourceid azonosítója|$eastprofile. Azonosító|A gyermek profil azonosítója|
    |-EndpointStatus|Engedélyezve|Végpont állapota a szülőhöz hozzáadása után|
    |-EndpointLocation|"eastus"|[Az Azure-régió neve](https://azure.microsoft.com/global-infrastructure/regions/) erőforrás|
    |-A minchildendpoints tulajdonság|1|A gyermekvégpontok minimális száma|

    A sikeres válasz tekintse meg az alábbiakhoz hasonló, és tartalmazza az új `child-endpoint-useast` végpont:    

    ```console
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

3. USA nyugati RÉGIÓJA gyermek profil hozzá a szülő **[Add-azurermtrafficmanagerendpointconfig parancsmag esetében](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** parancsmag és **NestedEndpoints** típusa

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Ez a táblázat azt ismerteti, hogy a parancsmag minden változót:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|gyermek-endpoint-uswest|Nyugat-profil|
    |-TrafficManagerProfile|$parentprofile|Ennek a végpontnak a hozzárendelt profil|
    |-Type|NestedEndpoints|További információkért lásd: [Add-azurermtrafficmanagerendpointconfig parancsmag esetében](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-Targetresourceid azonosítója|$westprofile. Azonosító|A gyermek profil azonosítója|
    |-EndpointStatus|Engedélyezve|Végpont állapota a szülőhöz hozzáadása után|
    |-EndpointLocation|"westus"|[Az Azure-régió neve](https://azure.microsoft.com/global-infrastructure/regions/) erőforrás|
    |-A minchildendpoints tulajdonság|1|A gyermekvégpontok minimális száma|

    A sikeres válasz tekintse meg hasonló, és tartalmazza a mindkét az előző `child-endpoint-useast` végpont és az új `child-endpoint-uswest` végpont:

    ```console
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

4. A végpontok beállítása **[Set-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** parancsmag 

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    A sikeres válasz a rendszer ugyanazt a választ, mint 3. lépés.

### <a name="powershell-variables"></a>PowerShell változók
A korábbi szakaszokban létrehozott három PowerShell változók: `$eastprofile`, `$westprofile`, `$parentprofile`. Ezeket a változókat használják a Traffic Manager konfigurációja a vége felé. Ha úgy döntött, nem hozza létre a változókat, vagy elfelejtette, vagy a PowerShell-ablakhoz túllépi az időkorlátot, a PowerShell-parancsmagot használhatja  **[Get-azurermtrafficmanagerprofile parancsmag](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, hogy a profil beolvasása újra, és rendelje hozzá egy változóhoz. 

Cserélje le a csúcsos zárójelpárban van, a cikkek `<>`, a három profil van szüksége a megfelelő értékekkel. 

```powerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Ellenőrizze a Traffic Manager működése
Győződjön meg arról, hogy a Traffic Manager-profilokkal, a profilok állapotát, rendelkeznie kell a `Online` Ez az állapot lekérdezési elérési útját a végpont alapul. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Új profilok megtekintése az Azure Portalon
Ellenőrizheti, hogy mindhárom profilhoz jönnek létre az erőforrások megnézzük a `luis-traffic-manager` erőforráscsoportot.

![Képernyőkép az Azure resource csoport luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Ellenőrizze, hogy a profil állapota egy Online
A Traffic Manager lekérdezi, hogy online állapotban minden végpont elérési útját. Ha online állapotú, a gyermek profilok állapotát vannak `Online`. Ez megjelenik a **áttekintése** az egyes profilokhoz. 

![Képernyőkép az Azure Traffic Manager-profil a figyelő állapota, Online megjelenítő áttekintés](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>A Traffic Manager működése lekérdezési ellenőrzése
Egy másik ellenőrzése a traffic manager működése lekérdezési módja a LUIS-végpont naplók. Az a [LUIS] [ LUIS] webhely alkalmazáslistában lapon, az alkalmazás a végpont-napló exportálása. A Traffic Manager milyen gyakran kérdezze le a két végpontok, mert nincsenek bejegyzések a naplókban akkor is, ha csak néhány percet a voltak. Ne felejtse el, és tekintse meg a bejegyzéseket, ahol a lekérdezés kezdete `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Ellenőrizze a DNS-választ a Traffic Manager működése
Ellenőrizze, hogy a DNS-választ adja vissza egy LUIS-végpontot, kérjen a forgalom kezelése szülő profil DNS a DNS ügyféloldali kódtár használatával. A DNS-név a szülő-profil van `luis-dns-parent.trafficmanager.net`.

A következő Node.js-kód kérést küld a szülő-profil, és adja vissza a LUIS-végpont:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A LUIS-végponttal a sikeres válasz a következő:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>A Traffic Manager szülő-profilja
Végpontok közötti forgalom kezelésére, kell beszúrni egy hívás az a Traffic Manager DNS-LUIS végpontjának megtalálása érdekében. Ez a hívás jön létre minden egyes LUIS végpont kérés esetében, és a felhasználó az intelligens HANGFELISMERÉSI ügyfélalkalmazás a földrajzi helyet szimulálása kell. A DNS-válasz kódja a LUIS-ügyfélalkalmazást és a kérelem között hozzá LUIS végpont előrejelzésére. 


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Távolítsa el a két LUIS végponthoz kulcsot, a három Traffic Manager-profilok és az öt ezeket az erőforrásokat tartalmazó erőforráscsoportot. Ez történik, az Azure Portalról. Az öt erőforrásokat törölnie az erőforrások listájában. Ezután törölje az erőforráscsoportot. 

## <a name="next-steps"></a>További lépések

Felülvizsgálat [közbenső](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) megérteni, hogyan a forgalom felügyeleti kódot is hozzáadhatók BotFramework robotprogramok BotFramework v4 beállítások. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
