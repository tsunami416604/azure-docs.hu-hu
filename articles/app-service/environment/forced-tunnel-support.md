---
title: "Az Azure App Service Environment konfigurálása kényszerített bújtatáshoz"
description: "Az App Service-környezet működtetése kimenő forgalom kényszerített bújtatása közben"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Az App Service-környezet konfigurálása kényszerített bújtatással

Az App Service-környezet az Azure App Service üzemelő példánya az ügyfelek Azure Virtual Network-példányában. Számos ügyfél konfigurálja virtuális hálózatait VPN-ek vagy Azure ExpressRoute-kapcsolatok segítségével úgy, hogy azok a helyszíni hálózatok kiterjesztéseként működjenek. Vállalati szabályzatok vagy más biztonsági korlátozások miatt az útvonalakat úgy konfigurálják, hogy a helyszíni hálózatokba küldjék az összes kimenő forgalmat, mielőtt kijutna az internetre. Kényszerített bújtatásnak nevezzük a virtuális hálózat útválasztásának abból a célból történő megváltoztatását, hogy a virtuális hálózatból származó kimenő forgalom VPN-en vagy ExpressRoute-kapcsolaton keresztül érkezzen a helyszíni hálózatba. 

A kényszerített bújtatás problémát okozhat egy App Service-környezetnek. Az App Service-környezet több külső függőséggel rendelkezik, amelyek listáját megtalálhatja az [App Service-környezet hálózati architektúráját][network] ismertető dokumentumban. Az App Service-környezet alapértelmezése szerint minden kimenő kommunikációnak az App Service-környezet virtuális IP-címén kell keresztülmennie.

Az útvonalak a kényszerített bújtatás és a kényszerített bújtatással folytatott munka kulcsfontosságú elemei. Egy Azure-beli virtuális hálózatban az útválasztás a leghosszabb előtag-megfeleltetés (LPM) alapján történik. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, akkor a rendszer az útvonalat a kiindulás alapján választja ki, az alábbi sorrendben:

* Felhasználó által meghatározott útvonal (UDR)
* BGP-útvonal (ExpressRoute használatánál)
* Rendszerútvonal

Ha többet szeretne megtudni a virtuális hálózatokban történő útválasztásról, olvassa el a [felhasználó által megadott útvonalakat és IP-továbbítást][routes] ismertető cikket. 

Ha szeretné, hogy App Service-környezete kényszerített bújtatásos virtuális hálózatban működjön, két lehetősége van:

* Engedélyezze a közvetlen internetelérést az App Service-környezet számára.
* Módosítsa a kimenő forgalom végpontját az App Service-környezetben.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Közvetlen internetelérés engedélyezése az App Service-környezet számára

Ahhoz, hogy az App Service-környezet ExpressRoute-kapcsolattal konfigurált virtuális hálózat esetében is működjön, a következőket teheti:

* Konfigurálja az ExpressRoute-ot a 0.0.0.0/0 tartomány meghirdetésére. Ez alapértelmezés szerint kényszerített bújtatás mellett irányít minden kimenő forgalmat a helyszíni hálózatba.
* Hozzon létre egy UDR-t. A 0.0.0.0/0 címelőtaggal és következő ugrási típusú internettel rendelkező App Service-környezetet tartalmazó alhálózat esetén alkalmazza.

Ha végrehajtja ezt a két módosítást, az App Service-környezet alhálózatából származó, internetre küldött forgalom nem lesz rákényszerítve az ExpressRoute-kapcsolatra, és az App Service-környezet működni fog.

> [!IMPORTANT]
> Az UDR-ben meghatározott útvonalaknak annyira pontosnak kell lenniük, hogy prioritást kapjanak az ExpressRoute-konfiguráció által meghirdetett bármely útvonallal szemben. Az előző példa a széles 0.0.0.0/0 címtartományt használja. Ezt véletlenül felülírhatják olyan útvonalhirdetések, amelyek pontosabb címtartományokat használnak.
>
> Az App Service-környezetek nem támogatottak olyan ExpressRoute-konfigurációk esetén, amelyek keresztbe hirdetnek útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra. A konfigurált nyilvános társviszonyt létesítő ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsofttól. A meghirdetések Microsoft Azure IP-címtartományok nagy készletét tartalmazzák. Ha a címtartományokat keresztbe hirdetik a privát társviszony-létesítési útvonalon, az összes, az App Service-környezet alhálózatából származó kimenő hálózati csomag kényszerített bújtatással kerül az ügyfél helyszíni hálózati infrastruktúrájába. Az App Service-környezetek jelenleg alapértelmezés szerint nem támogatják ezt a hálózati kialakítást. Egy megoldás erre a problémára az, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra. Egy másik megoldás az, ha lehetővé teszi az App Service-környezet működését kényszerített bújtatásos konfigurációban.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>A kimenő forgalom végpontjának módosítása az App Service-környezetben ##

Ez a szakasz azt ismerteti, hogyan teheti lehetővé az App Service-környezet működését kényszerített bújtatásos konfigurációban a kimenő forgalom App Service-környezet által használt végpontjának módosításával. Ha az App Service-környezet kimenő forgalma kényszerített bújtatással jut el egy helyszíni hálózatba, engedélyeznie kell a forgalom számára, hogy az App Service-környezet virtuális IP-címén kívüli más IP-címekből származzon.

Egy App Service-környezet nemcsak külső függőségekkel rendelkezik, hanem a bejövő forgalomra is figyelnie és reagálnia kell. A válaszok nem küldhetők vissza másik címről, mivel ez a TCP szabályainak megsértését eredményezi. Három lépés szükséges a kimenő forgalom végpontjának módosításához az App Service-környezetben:

1. Állítson be egy útválasztási táblázatot annak biztosítására, hogy a bejövő kezelési forgalom ugyanarról az IP-címről mehessen vissza.

2. Adja hozzá az App Service-környezet tűzfalához azokat az IP-címeket, amelyeket a kimenő forgalom használni fog.

3. Állítsa be az útvonalakat a bújtatni kívánt App Service-környezet kimenő forgalmára.

   ![Hálózati forgalom kényszerített bújtatása][1]

Miután az App Service-környezet elindult és működik, konfigurálhatja más kimenő forgalmi címekkel, vagy beállíthatja azokat az App Service-környezet üzembe helyezésekor.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>A kimenő forgalom címének módosítása működő App Service-környezetben ###
1. Válassza ki az App Service-környezetben a kimenő forgalom IP-címeiként használni kívánt IP-címeket. Ha kényszerített bújtatást végez, ezek a címek a NAT-okból vagy az átjárók IP-címeiből származnak. Ha az App Service-környezet kimenő forgalmát egy hálózati virtuális berendezésen keresztül szeretné vezetni, a kimenő forgalom címe a hálózati virtuális berendezés nyilvános IP-címe lesz.

2. Állítsa be a kimenő forgalom címeit az App Service-környezet konfigurációs adatai között. Látogasson el a resource.azure.com webhelyre, és váltson a következő elérési útra: Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Itt láthatja az App Service-környezetet leíró JSON-t. Győződjön meg arról, hogy a lap tetején ott az **olvasás/írás** felirat. Válassza a **Szerkesztés** elemet. Görgessen le a lap aljáig, és módosítsa a **userWhitelistedIpRanges** beállítást **null** értékről az alábbihoz hasonlóra. Használja azokat a címeket, amelyeket a kimenő forgalom címtartományának kíván beállítani. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   A lap tetején kattintson a **PUT** elemre. Ez a lehetőség aktivál egy méretezési műveletet az App Service-környezetben, és beállítja a tűzfalat.
 
3. Hozzon létre vagy szerkesszen egy útválasztási táblázatot, és töltse fel értékekkel a szabályokat úgy, hogy biztosítsa a hozzáférést az App Service-környezet helyére leképeződő kezelési címekhez és kezelési címekből. A kezelési címek megtalálásához olvassa el az [App Service-környezet kezelési címeit][management] ismertető szakaszt.

4. Állítsa be az App Service-környezet alhálózatában alkalmazott útvonalakat útválasztási táblázat vagy BGP-útvonalak segítségével. 

Ha az App Service-környezet nem válaszol a portálon, hibásak lehetnek a módosítások. Lehetséges problémák: a kimenő forgalom címeinek listája nem teljes, a forgalom elveszett vagy blokkolva van. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Új App Service-környezet létrehozása más kimenő forgalmi címmel ###

Ha már konfigurálta virtuális hálózatát az összes forgalom kényszerített bújtatására, további lépéseket kell tennie az App Service-környezet létrehozásához, hogy az sikeresen elindulhasson. Az App Service-környezet létrehozása közben engedélyeznie kell egy másik kimenő forgalmi végpont használatát. Ehhez az App Service-környezetet egy sablon alapján kell létrehoznia, amely meghatározza az engedélyezett kimenő forgalmi címeket.

1. Válassza ki azokat az IP-címeket, amelyeket az App Service-környezetben a kimenő forgalom címeiként használni fog.

2. Hozza létre előre azt az alhálózatot, amelyet az App Service-környezet használni fog. Szüksége lesz rá az útvonalak beállításához, és a sablonhoz is szükséges.

3. Hozzon létre egy útválasztási táblázatot az App Service-környezet helyére leképeződő kezelési IP-címekkel. Rendelje hozzá az App Service-környezethez.

4. Kövesse az [App Service-környezet sablonnal történő létrehozását][template] ismertető cikk utasításait. Kérje le a megfelelő sablont.

5. Szerkessze a „resources” (erőforrások) szakaszt az azuredeploy.json fájlban. Szúrjon be egy sort az **userWhitelistedIpRanges** paraméterrel és a saját értékeivel, az alábbiak szerint:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Ha megfelelően konfigurálta ezt a szakaszt, az App Service-környezetnek problémamentesen el kell indulnia. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
