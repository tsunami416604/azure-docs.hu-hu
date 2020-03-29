---
title: Végpontkvóta növelése – LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) lehetővé teszi a végpontkérelem-kvóta növelését egyetlen kulcs kvótán túl. Ez úgy történik, hogy további kulcsokat hoz létre a LUIS számára, és hozzáadja őket a LUIS alkalmazáshoz az **Erőforrások és kulcsok** szakasz **Közzététel** lapján.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70256607"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Végpontkvóta kezelése a Kulcsok között a Microsoft Azure Traffic Manager segítségével
A Language Understanding (LUIS) lehetővé teszi a végpontkérelem-kvóta növelését egyetlen kulcs kvótán túl. Ez úgy történik, hogy további kulcsokat hoz létre a LUIS számára, és hozzáadja őket a LUIS alkalmazáshoz az **Erőforrások és kulcsok** szakasz **Közzététel** lapján. 

Az ügyfél-alkalmazás nak kell kezelnie a forgalmat a kulcsok között. A LUIS nem csinál ilyet. 

Ez a cikk bemutatja, hogyan kezelheti a forgalmat a kulcsok között az Azure [Traffic Manager][traffic-manager-marketing]segítségével. Már rendelkeznie kell egy betanított és közzétett LUIS-alkalmazással. Ha nem rendelkezik ilyen, kövesse az Előre összeállított tartomány [rövid útmutatóját.](luis-get-started-create-app.md) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Csatlakozás a PowerShellhez az Azure Portalon
Az [Azure Portalon][azure-portal] nyissa meg a PowerShell-ablakot. A PowerShell ablakikon a **>_** a felső navigációs sávon. A PowerShell használatával a portálról, a legújabb PowerShell-verziót kapja, és hitelesítve van. A portálon a PowerShell hez [egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/) szükséges. 

![Képernyőkép az Azure Portalról, amelyen a Powershell ablaka meg van nyitva](./media/traffic-manager/azure-portal-powershell.png)

A következő szakaszok [a Traffic Manager PowerShell-parancsmagjait](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager)használják.

## <a name="create-azure-resource-group-with-powershell"></a>Azure-erőforráscsoport létrehozása a PowerShellsegítségével
Az Azure-erőforrások létrehozása előtt hozzon létre egy erőforráscsoportot, amely tartalmazza az összes erőforrást. Nevezze el `luis-traffic-manager` az erőforráscsoportot, `West US`és használja a régiót. Az erőforráscsoport régiója metaadatokat tárol a csoportról. Nem fogja lelassítani az erőforrásokat, ha egy másik régióban vannak. 

Erőforráscsoport létrehozása **[az Új-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** parancsmaggal:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS-kulcsok létrehozása a végponti kvóta növeléséhez
1. Az Azure Portalon hozzon létre két `West US` **nyelvi megértési** kulcsot, egyet a és egyet a. `East US` Használja az előző szakaszban létrehozott, a `luis-traffic-manager`név szerint létrehozott meglévő erőforráscsoportot. 

    ![Képernyőkép az Azure Portalkét LUIS-traffic-manager erőforráscsoportban lévő KÉT LUIS-kulcsból](./media/traffic-manager/luis-keys.png)

2. A [LUIS-webhely][LUIS] **Kezelése** szakaszában, az **Azure Resources** lapon rendeljen kulcsokat az alkalmazáshoz, és tegye közzé újra az alkalmazást a jobb felső menü **Közzététel** gombjával. 

    A **végpontoszlopban** lévő példa URL-címe egy GET-kérelmet használ a végpontkulcakul lekérdezési paraméterként. Másolja a két új kulcs végpontURL-címeit. Ezek a cikk későbbi részében a Traffic Manager konfigurációrészeként használatosak.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Luis-végpontkérelmek kezelése kulcsok között a Traffic Manager segítségével
A Traffic Manager új DNS-hozzáférési pontot hoz létre a végpontokhoz. Nem átjáróként vagy proxyként működik, hanem szigorúan a DNS szintjén. Ez a példa nem módosítja a DNS-rekordokat. Egy DNS-kódtár segítségével kommunikál a Traffic Manager az adott kérés hez a megfelelő végpont lekéréséhez. _A_ LUIS-nak szánt minden egyes kérelemhez először egy Traffic Manager-kérelemre van szükség annak meghatározásához, hogy melyik LUIS-végpontot kell használni. 

### <a name="polling-uses-luis-endpoint"></a>A lekérdezés LUIS-végpontot használ
A Traffic Manager rendszeres időközönként lekérdezi a végpontokat, hogy a végpont továbbra is elérhető legyen. A Traffic Manager lekérdezett URL-címkell elérhető a GET kérelmet, és vissza a 200. A **Közzététel** lap végponturl-címe ezt teszi. Mivel minden végpontkulcs más útvonal- és lekérdezési karakterlánc-paramétereket rendelkezik, minden végpontkulcsnak más lekérdezési útvonalra van szüksége. Minden alkalommal, amikor a Traffic Manager lekérdezi, a kvótakérelem költsége. A lekérdezési karakterlánc paraméter **q** a LUIS-végpont a LUIS-nak küldött utterance (kifejezés). Ez a paraméter, ahelyett, hogy egy utterance (kifejezés) küldése, a Traffic Manager lekérdezés hozzáadása a LUIS-végpont napló hibakeresési technikaként, miközben a Traffic Manager konfigurálva van.

Mivel minden LUIS-végpontnak saját útvonalra van szüksége, saját Traffic Manager-profilra van szüksége. A profilok közötti kezelés érdekében hozzon létre egy beágyazott Traffic Manager architektúrát. [ _nested_ ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) Az egyik szülőprofil a gyermekprofilokra mutat, és kezelheti a forgalmat azok között.

A Traffic Manager konfigurálása után ne felejtse el módosítani a naplózás=hamis lekérdezési karakterlánc paraméter használatának elérési útját, hogy a napló ne töltődjön fel lekérdezéssel.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Traffic Manager konfigurálása beágyazott profilokkal
A következő szakaszok két gyermekprofilt hoznak létre, egyet a kelet-LUIS-kulcshoz és egyet a West LUIS-kulcshoz. Ezután létrejön egy fölérendelt profil, és a két gyermekprofil hozzáadódik a szülőprofilhoz. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Az USA keleti részes forgalomkezelő-profiljának létrehozása a PowerShell segítségével
Az USA keleti részes forgalomkezelő-profiljának létrehozásához több lépés ből áll: profil létrehozása, végpont hozzáadása és végpont beállítása. A Traffic Manager-profil nak több végpontja is lehet, de minden végpont nak ugyanaz az ellenőrzési útvonala. Mivel a luis végpont URL-címek a keleti és a nyugati előfizetések régió és végpont kulcs miatt eltérőek, minden LUIS-végpontnak egyetlen végpontnak kell lennie a profilban. 

1. Profil létrehozása **[a New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** parancsmaggal

    A profil létrehozásához használja a következő parancsmamot. Ügyeljen arra, `appIdLuis` hogy `subscriptionKeyLuis`módosítsa a és a . Az előfizetési kulcs a kelet-amerikai LUIS-kulcshoz használható. Ha az elérési út nem megfelelő, beleértve a LUIS-alkalmazásazonosítót és a `degraded` végpontkulcsot, a Traffic Manager lekérdezése állapota, mert a Traffic Manage nem tudja sikeresen kérni a LUIS-végpontot. Győződjön meg `q` arról, hogy az érték, `traffic-manager-east` így láthatja ezt az értéket a LUIS végpont naplók.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Ez a táblázat a parancsmag egyes változóit ismerteti:
    
    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Name|luis-profil-eastus|Traffic Manager-név az Azure Portalon|
    |-Erőforráscsoportneve|luis-forgalom-menedzser|Az előző szakaszban létrehozva|
    |-TrafficRoutingMetódus|Teljesítmény|További információt a [Traffic Manager útválasztási módszerei című][routing-methods]témakörben talál. Teljesítmény használata esetén a Traffic Manager URL-kérelemnek a felhasználó régiójából kell származnia. Ha egy chatboton vagy más alkalmazáson keresztül megy keresztül, a chatbot felelőssége, hogy utánozza a régiót a Traffic Manager hívásában. |
    |-RelativeDnsName|luis-dns-eastus|Ez a szolgáltatás altartománya: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A LUIS portja és protokollja HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Cserélje `<appIdLuis>` `<subscriptionKeyLuis>` ki, és a saját értékeit.|
    
    A sikeres kérelemre nincs válasz.

2. Usa keleti keleti szintű végpontjának hozzáadása **[az Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** parancsmaggal

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|luis-kelet-végpont|A profil alatt megjelenített végpontnév|
    |-TrafficManagerProfil|$eastprofile|Az 1.|
    |-Típus|Külső végpontok|További információ: [Traffic Manager endpoint][traffic-manager-endpoints] |
    |-Cél|eastus.api.cognitive.microsoft.com|Ez a LUIS-végpont tartománya.|
    |-Végponthelye|"Eastus"|A végpont régiója|
    |-Végpont állapota|Engedélyezve|Végpont engedélyezése létrehozáskor|

    A sikeres válasz így néz ki:

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

3. Usa keleti szintű végpontjának beállítása **[set-aztrafficmanagerprofil](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** parancsmaggal

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    A sikeres válasz ugyanaz lesz, mint a 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>A Nyugat-usabeli Traffic Manager-profil létrehozása a PowerShell segítségével
A Nyugat-us Traffic Manager-profil létrehozásához kövesse ugyanazokat a lépéseket: profil létrehozása, végpont hozzáadása és végpont beállítása.

1. Profil létrehozása **[a New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** parancsmaggal

    A profil létrehozásához használja a következő parancsmamot. Ügyeljen arra, `appIdLuis` hogy `subscriptionKeyLuis`módosítsa a és a . Az előfizetési kulcs a kelet-amerikai LUIS-kulcshoz használható. Ha az elérési út nem megfelelő, beleértve a LUIS alkalmazásazonosítót és `degraded` a végpontkulcsot, a Traffic Manager lekérdezésállapota, mert a Traffic Manage nem tudja sikeresen kérni a LUIS-végpontot. Győződjön meg `q` arról, hogy az érték, `traffic-manager-west` így láthatja ezt az értéket a LUIS végpont naplók.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Ez a táblázat a parancsmag egyes változóit ismerteti:
    
    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Name|luis-profil-westus|Traffic Manager-név az Azure Portalon|
    |-Erőforráscsoportneve|luis-forgalom-menedzser|Az előző szakaszban létrehozva|
    |-TrafficRoutingMetódus|Teljesítmény|További információt a [Traffic Manager útválasztási módszerei című][routing-methods]témakörben talál. Teljesítmény használata esetén a Traffic Manager URL-kérelemnek a felhasználó régiójából kell származnia. Ha egy chatboton vagy más alkalmazáson keresztül megy keresztül, a chatbot felelőssége, hogy utánozza a régiót a Traffic Manager hívásában. |
    |-RelativeDnsName|luis-dns-westus|Ez a szolgáltatás altartománya: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A LUIS portja és protokollja HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Cserélje `<appId>` `<subscriptionKey>` ki, és a saját értékeit. Ne feledje, hogy ez a végpontkulcs eltér a keleti végpontkulcstól|
    
    A sikeres kérelemre nincs válasz.

2. UsA nyugati végpontjának hozzáadása **[az Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** parancsmaggal

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|luis-west-végpont|A profil alatt megjelenített végpontnév|
    |-TrafficManagerProfil|$westprofile|Az 1.|
    |-Típus|Külső végpontok|További információ: [Traffic Manager endpoint][traffic-manager-endpoints] |
    |-Cél|westus.api.cognitive.microsoft.com|Ez a LUIS-végpont tartománya.|
    |-Végponthelye|"Nyugat"|A végpont régiója|
    |-Végpont állapota|Engedélyezve|Végpont engedélyezése létrehozáskor|

    A sikeres válasz így néz ki:

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

3. Az USA nyugati oldalán lévő végpont beállítása **[set-aztrafficmanagerprofil](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** parancsmaggal

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    A sikeres válasz ugyanaz, mint a 2.

### <a name="create-parent-traffic-manager-profile"></a>Szülő forgalomkezelő-profil létrehozása
Hozza létre a szülő Traffic Manager-profilt, és csatoljon két gyermek Traffic Manager-profilt a szülőhöz.

1. Szülőprofil létrehozása **[a New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** parancsmaggal

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Name|luis-profil-szülő|Traffic Manager-név az Azure Portalon|
    |-Erőforráscsoportneve|luis-forgalom-menedzser|Az előző szakaszban létrehozva|
    |-TrafficRoutingMetódus|Teljesítmény|További információt a [Traffic Manager útválasztási módszerei című][routing-methods]témakörben talál. Teljesítmény használata esetén a Traffic Manager URL-kérelemnek a felhasználó régiójából kell származnia. Ha egy chatboton vagy más alkalmazáson keresztül megy keresztül, a chatbot felelőssége, hogy utánozza a régiót a Traffic Manager hívásában. |
    |-RelativeDnsName|luis-dns-szülő|Ez a szolgáltatás altartománya: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A LUIS portja és protokollja HTTPS/443|
    |-MonitorPath|`/`|Ez az elérési út nem számít, mert a gyermekvégpont-elérési utakat használják helyette.|

    A sikeres kérelemre nincs válasz.

2. Kelet-usa gyermekprofil hozzáadása a szülőhöz **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** és **NestedEndpoints** type

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|gyermek-végpont-useast|Keleti profil|
    |-TrafficManagerProfil|$parentprofile|Profil a végpont hozzárendeléséhez|
    |-Típus|Egymásba ágyazott végpontok|További információ: [Add-ATrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId azonosító|$eastprofile. Id|A gyermekprofil azonosítója|
    |-Végpont állapota|Engedélyezve|Végpont állapota a szülőhöz való hozzáadás után|
    |-Végponthelye|"Eastus"|[Az erőforrás Azure-régióneve](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimális szám a gyermekvégpontokhoz|

    A sikeres válasz a következőkre `child-endpoint-useast` néz, és tartalmazza az új végpontot:    

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

3. Nyugat-usa gyermekprofil hozzáadása a szülőhöz **[Az-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** parancsmaggal és **NestedEndpoints** type -val

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Végpontneve|gyermek-végpont-uswest|Nyugati profil|
    |-TrafficManagerProfil|$parentprofile|Profil a végpont hozzárendeléséhez|
    |-Típus|Egymásba ágyazott végpontok|További információ: [Add-ATrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId azonosító|$westprofile. Id|A gyermekprofil azonosítója|
    |-Végpont állapota|Engedélyezve|Végpont állapota a szülőhöz való hozzáadás után|
    |-Végponthelye|"Nyugat"|[Az erőforrás Azure-régióneve](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimális szám a gyermekvégpontokhoz|

    A sikeres válasz az előző `child-endpoint-useast` és az új `child-endpoint-uswest` végponthoz hasonlóan néz ki, és tartalmazza az:

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

4. Végpontok beállítása **[set-aztrafficmanagerprofile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** parancsmaggal 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    A sikeres válasz ugyanaz, mint a 3.

### <a name="powershell-variables"></a>PowerShell-változók
Az előző szakaszokban három PowerShell-változó `$eastprofile`jött `$westprofile` `$parentprofile`létre: , , . Ezek a változók a Traffic Manager-konfiguráció vége felé használatosak. Ha úgy döntött, hogy nem hozza létre a változókat, vagy elfelejtette, vagy a PowerShell ablak időtúloldalán, használhatja a PowerShell-parancsmag, **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)**, a profil újbóli lekérése és hozzárendelése egy változóhoz. 

Cserélje le a szögletes zárójelben lévő elemeket `<>`a szükséges három profil megfelelő értékére. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>A Traffic Manager működésének ellenőrzése
Annak ellenőrzéséhez, hogy a Traffic Manager-profilok működnek-e, a profiloknak `Online` a végpont lekérdezési útvonalán alapuló állapotúnak kell lenniük. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Új profilok megtekintése az Azure Portalon
Az `luis-traffic-manager` erőforráscsoport erőforrásainak vizsgálatával ellenőrizheti, hogy mindhárom profil létrejön-e.

![Képernyőkép az Azure-erőforráscsoport luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Annak ellenőrzése, hogy a profil állapota Online-e
A Traffic Manager lekérdezi az utat az egyes végpontok, hogy megbizonyosodjon arról, hogy online állapotban van. Ha online állapotban van, a `Online`gyermekprofilok állapota . Ez az egyes profilok **áttekintése** látható. 

![Képernyőkép az Azure Traffic Manager profiljáról – áttekintés az online figyelő állapotának megjelenítéséről](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>A Traffic Manager lekérdezési működésének ellenőrzése
Egy másik módja annak, hogy érvényesítse a forgalomkezelő lekérdezési működik a LUIS végpont naplók. A [LUIS][LUIS] webhelyalkalmazások listalapján exportálja az alkalmazás végpontnaplóját. Mivel a Traffic Manager gyakran lekérdezi a két végpontot, vannak bejegyzések a naplókban, még akkor is, ha csak néhány percig voltak. Ne felejtse el megkeresni `traffic-manager-`azon bejegyzéseket, amelyekkel a lekérdezés kezdődik.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>A Traffic Manager működéséből származó DNS-válasz ellenőrzése
Annak ellenőrzéséhez, hogy a DNS-válasz luis-végpontot ad-e vissza, kérje a Traffic Manage szülőprofil DNS-ét egy DNS-ügyféltár használatával. A szülőprofil DNS-neve `luis-dns-parent.trafficmanager.net`.

A következő Node.js kód kérelmet küld a szülőprofilhoz, és luis-végpontot ad vissza:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A SIKERES válasz a LUIS-végpont:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>A Traffic Manager szülői profil használata
A végpontok közötti forgalom kezeléséhez be kell szúrnia egy hívást a Traffic Manager DNS-hez a LUIS-végpont megkereséséhez. Ez a hívás minden LUIS-végpont-kérelemhez történik, és szimulálnia kell a LUIS-ügyfélalkalmazás felhasználójának földrajzi helyét. Adja hozzá a DNS-válaszkódot a LUIS-ügyfélalkalmazás és a kérelem a LUIS a végpont előrejelzése között. 

## <a name="resolving-a-degraded-state"></a>Leromlott állapot feloldása

Engedélyezze a Traffic Manager [diagnosztikai naplóit,](../../traffic-manager/traffic-manager-diagnostic-logs.md) hogy lássa, miért romlik a végpont állapota.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Távolítsa el a két LUIS-végpontkulcsot, a három Traffic Manager-profilt és az öt erőforrást tartalmazó erőforráscsoportot. Ez az Azure Portalon történik. Az öt erőforrást törli az erőforrások listájából. Ezután törölje az erőforráscsoportot. 

## <a name="next-steps"></a>További lépések

Tekintse át a [köztes szoftver](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) beállításokat a BotFramework v4-ben, hogy megértse, hogyan adható hozzá ez a forgalomkezelési kód a BotFramework robothoz. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
