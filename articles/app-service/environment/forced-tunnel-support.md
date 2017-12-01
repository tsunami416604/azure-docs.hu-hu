---
title: "Konfigurálja az Azure App Service-környezet kell bújtatott kényszerítése"
description: "Az App Service-környezet működéséhez a kimenő forgalom esetén kényszerített bújtatott engedélyezése"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: f5f099042cefe666e22a9d561afeb4584db3d92c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Az App Service-környezet konfigurálása a kényszerített bújtatás

Az App Service Environment-környezet az Azure App Service Azure virtuális hálózat az ügyfél-példány központi telepítését. Sok ügyfél konfigurálni kell a helyszíni hálózatokhoz a virtuális magánhálózatok vagy Azure ExpressRoute bővítményei a virtuális hálózatok kapcsolatok. Vállalati házirendek vagy a más biztonsági korlátozások miatt konfigurálják az útvonalakat minden kimenő forgalom helyszíni küldése előtt, lépjen az interneten. Az Útválasztás a virtuális hálózat, hogy a virtuális hálózat a kimenő forgalom a VPN- vagy ExpressRoute kapcsolat a helyszíni áthaladó módosítása a kényszerített bújtatás nevezik. 

A kényszerített bújtatás, a problémákat okozhat az App Service Environment-környezet. Az App Service Environment-környezet van egy külső függőségeit, amelyek enumerálása a száma a [App Service Environment-környezet hálózati architektúra] [ network] dokumentum. Az App Service Environment-környezet alapértelmezés szerint megköveteli, hogy minden kimenő kommunikáció végighalad az App Service-környezet ki van építve VIP.

Útvonalak rendszer egyik fontos szempontja, hogy milyen a kényszerített bújtatás, és azt kezelése. Egy Azure virtuális hálózatban útválasztási történik, a leghosszabb előtag egyezés (LPM) alapján. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, a útvonal van jelölve, a következő sorrendben Kiindulás alapján:

* Felhasználó által megadott útvonal (UDR)
* BGP-útvonal (ExpressRoute használatánál)
* Rendszerútvonal

Útválasztás virtuális hálózaton kapcsolatos további tudnivalókért olvassa el [felhasználó által definiált útvonalak és IP-továbbítás][routes]. 

Ha azt szeretné, hogy az App Service-környezet működését a kényszerített bújtatás virtuális hálózat, két lehetősége van:

* Engedélyezze a közvetlen internet-hozzáféréssel rendelkezzenek az App Service-környezet.
* Módosítsa a kimenő forgalom végpont az App Service Environment-környezet.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Engedélyezze a közvetlen internet-hozzáféréssel rendelkezzenek az App Service-környezet

Az App Service Environment-környezet működéséhez, miközben a virtuális hálózat ExpressRoute kapcsolat van konfigurálva a következő műveletek végezhetők el:

* Konfigurálja az ExpressRoute 0.0.0.0/0 hivatkozik. Alapértelmezés szerint azt kényszerített bújtatás minden kimenő forgalom a helyszínen.
* Hozzon létre egy UDR. Azokon az alhálózatot, amely tartalmazza az App Service-környezet egy 0.0.0.0/0 és a következő ugrás típusa Internet címelőtaggal rendelkező.

A módosítások két, ha az App Service Environment-környezet alhálózat származó internet felé forgalom nem kényszerített le az ExpressRoute-kapcsolatot, és az App Service Environment-környezet működését.

> [!IMPORTANT]
> Lehet, hogy egy UDR definiált útvonalak kellően specifikus elsőbbséget élveznek a bármely az ExpressRoute-konfiguráció által hirdetett útvonalakat. Az előző példában a széles körű 0.0.0.0/0 címtartomány. Az esetlegesen véletlenül felülbírálhatja pontosabb címtartományai használó útvonal-hirdetéseinek.
>
> App Service-környezetek kereszt-hirdetményt a magánfelhő-társviszony létesítése – elérési utat a nyilvános társviszony elérési útvonalak ExpressRoute beállításokkal nem támogatottak. A nyilvános társviszony konfigurált ExpressRoute-konfigurációk útvonal-hirdetéseinek kapni a Microsofttól. A hirdetmények a Microsoft Azure IP-címtartományok nagy foglal magában. Ha a címtartomány határokon meghirdetett privát társviszony elérési útján, minden kimenő hálózati csomag az App Service Environment-alhálózatból egy ügyfél a helyi hálózati infrastruktúra bújtatott hatályba. A hálózati folyamat jelenleg nem támogatott az App Service Environment-környezetek alapértelmezett. Egy megoldást a problémára, hogy állítsa le a kereszt-közzététel útvonalak nyilvános társviszony elérési útjáról a magánfelhő-társviszony létesítése – elérési utat. Egy másik megoldás, az App Service-környezet működését a kényszerített bújtatás konfigurálása a engedélyezéséhez.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>A kimenő forgalom végpont módosítsa az App Service Environment-környezet ##

Ez a szakasz egy App Service-környezet működését a kényszerített bújtatás konfigurálása a kimenő forgalom végpont az App Service Environment-környezet által használt módosításával engedélyezését ismerteti. Ha a kimenő forgalom az App Service Environment-környezet egy a helyszíni hálózatra bújtatott kényszerített, a forgalom engedélyezéséhez a forrás IP-címekről az App Service-környezet VIP-cím nem szüksége.

Az App Service-környezetek nem csak külső függőségei vannak, de is kell figyelni a bejövő forgalom és válaszadás ilyen forgalom. A válaszok nem küldhető vissza egy másik címről, mert, amely megsérti TCP. Az App Service Environment-környezet a kimenő forgalom végpont módosításához szükséges három lépésben történik:

1. Állítsa be annak érdekében, hogy bejövő felügyeleti forgalom is visszatérhet a az azonos IP-címről egy útválasztási táblázatot.

2. Adja hozzá az IP-címek, amelyek a kimenő forgalom az App Service Environment-környezet tűzfalhoz használható.

3. Az útvonalak a kimenő forgalom bújtatott kell az App Service-környezet állítható be.

   ![A kényszerített bújtatás hálózati folyamata][1]

Az App Service Environment-környezet konfigurálható különböző kimenő címek után az App Service Environment-környezet már és működik, vagy azok App Service Environment-környezet üzembe helyezése során állítható be.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>A kimenő forgalom címének módosítása után az App Service Environment-környezet működési ###
1. A virtuális IP-címek használata az App Service-környezet kívánt IP-címek lekérése. Ha végzett a kényszerített bújtatás, ezeknél a címeknél határozza meg a NAT vagy az átjáró IP-címek. Ha szeretné az App Service Environment-környezet kimenő forgalmat NVA keresztül, a kimenő forgalom címe a nyilvános IP-címe az NVA.

2. A kimenő címek beállítása az App Service Environment-környezet konfigurációs adatait. Ugrás resource.azure.com és előfizetés Ugrás /<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Ezután láthatja a JSON-NÁ, mely leírja az App Service-környezet. Győződjön meg arról, hogy a felirat látható **olvasási/írási** tetején. Válassza ki **szerkesztése**. Görgessen le a képernyő aljára, és módosítsa a **userWhitelistedIpRanges** értéket **null** a következőhöz való. A címek be szeretné állítani, a virtuális címtartomány használata. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Válassza ki **PUT** tetején. Ez a beállítás az App Service-környezet egy skálázási műveletet váltja ki, és módosítja a tűzfal.
 
3. Hozzon létre vagy szerkessze egy útválasztási táblázatot, és tölthető fel a szabályokat, hogy a felügyeleti címek, amelyek kapcsolódnak az App Service Environment-környezet helyet és a hozzáférés. A felügyeleti címek megkereséséhez lásd: [App Service Environment-környezet felügyeleti címek][management].

4. Állítsa be úgy az útvonalakat az App Service Environment-környezet alhálózat egy útválasztási táblázathoz alkalmazott vagy a BGP-útvonalakat. 

Az App Service Environment-környezet kerül nem válaszol a portálról, ha probléma van a módosításokat. Lehet, hogy a probléma, hogy a kimenő címek listája nem fejeződött be, a forgalom megszakadt, vagy a forgalom blokkolása. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Hozzon létre egy új App Service Environment-környezet egy másik kimenő címmel ###

Ha a virtuális hálózat már be van állítva a kényszerített bújtatás a forgalmat, akkor az App Service-környezet létrehozása, hogy az képes elérni sikeresen további lépések. Az App Service Environment-környezet létrehozása során egy másik kimenő végpont az engedélyezni kell. Ehhez szüksége az App Service Environment-környezet, amely meghatározza az engedélyezett kimenő címek a sablon létrehozásához.

1. Az App Service-környezet, a kimenő címek szolgál az IP-címek lekérése.

2. Előre hozza létre az alhálózatot, az App Service Environment-környezet által használható. Szüksége van rá, hogy az útvonalak, állíthatja be, továbbá mivel a sablon írja elő.

3. Hozzon létre egy útválasztási táblázatot a felügyeleti IP-címek, amelyek az App Service Environment-környezet helyre van leképezve. Rendelje hozzá az App Service-környezet.

4. Kövesse a riasztásban megjelenő utasításokat [hozzon létre egy sablont az App Service-környezetek][template]. Húzza a megfelelő sablon le.

5. Szerkessze az azuredeploy.json fájlt a "források" szakasz. Egy sor felvétele **userWhitelistedIpRanges** ehhez hasonló a értékekkel:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Ha ez a szakasz megfelelően van konfigurálva, az App Service Environment-környezet nem jelez problémákat kell kezdődnie. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
