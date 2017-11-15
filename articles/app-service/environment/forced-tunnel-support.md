---
title: "Konfigurálja az Azure App Service-környezet kell bújtatott kényszerítése"
description: "A kimenő forgalom esetén kényszerített bújtatott működéséhez ASE engedélyezése"
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
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Az App Service-környezet konfigurálása a kényszerített bújtatás

Az App Service környezetben (ASE) az Azure App Service egy ügyfél az Azure Virtual Network (VNet) a központi telepítését. Sok ügyfél konfigurálni kell a helyszíni hálózatokhoz a virtuális magánhálózatok vagy ExpressRoute bővítményei a Vnetek kapcsolatok. Vállalati házirendek vagy a más biztonsági korlátozások miatt konfigurálják az útvonalakat minden kimenő forgalom helyszíni küldése előtt, lépjen az interneten. Az Útválasztás a vnet, hogy a virtuális hálózat a kimenő forgalom a VPN- vagy ExpressRoute kapcsolat a helyszíni áthaladó módosítása a kényszerített bújtatás nevezik.  

A kényszerített bújtatás egy ASE problémákat okozhat. A ASE számos külső függőségei, amely ezen enumerálása [ASE hálózati architektúra] [ network] dokumentum. A ASE alapértelmezés szerint megköveteli, hogy minden kimenő kommunikáció végighalad a VIP-címhez, amely ki van építve a mértékéig.

Útvonalak egyik fontos szempontja, mi a kényszerített bújtatás, és azt kezelése. Egy Azure virtuális hálózatban útválasztási alapján történik a leghosszabb előtag-megfeleltetés (LPM).  Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, majd egy útvonal elemet a következő sorrendben Kiindulás alapján:

1. Felhasználó által megadott útvonal
1. BGP-útvonal (ExpressRoute használatánál)
1. Rendszerútvonal

A Vneten belül útválasztási kapcsolatos további tudnivalókért olvassa el [felhasználó által definiált útvonalak és IP-továbbítás][routes]. 

Ha azt szeretné, hogy a ASE a kényszerített bújtatás virtuális hálózat a működését, két lehetősége van:

1. Közvetlen internet-hozzáféréssel rendelkezzenek a ASE engedélyezése
1. A kimenő forgalom végpont a ASE módosítása

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Közvetlen internet-hozzáféréssel rendelkezzenek a ASE engedélyezése

A ASE működjön, miközben a virtuális hálózat egy ExpressRoute van konfigurálva, a következőket teheti:

* Konfigurálja az ExpressRoute 0.0.0.0/0 hivatkozik. Alapértelmezés szerint azt kényszerített bújtatás minden kimenő forgalom a helyszínen.
* Hozzon létre egy UDR. Azokon az alhálózat egy 0.0.0.0/0 és a következő ugrás típusa Internet címelőtaggal rendelkező ASE tartalmaz.

Ha a módosítások két, a ASE alhálózatról érkező forgalmat az internet felé nem működik az ExpressRoute- és a ASE le kényszerített.

> [!IMPORTANT]
> Lehet, hogy egy UDR definiált útvonalak kellően specifikus elsőbbséget élveznek a bármely az ExpressRoute-konfiguráció által hirdetett útvonalakat. Az előző példában a széles körű 0.0.0.0/0 címtartomány. Az esetlegesen véletlenül felülbírálhatja pontosabb címtartományai használó útvonal-hirdetéseinek.
>
> ASEs cross-hirdetményt a magánfelhő-társviszony létesítése – elérési utat a nyilvános társviszony elérési útvonalak ExpressRoute beállításokkal nem támogatottak. A nyilvános társviszony konfigurált ExpressRoute-konfigurációk útvonal-hirdetéseinek kapni a Microsofttól. A hirdetmények a Microsoft Azure IP-címtartományok nagy foglal magában. Ha a címtartomány határokon meghirdetett privát társviszony elérési útján, minden kimenő hálózati csomagokat a ASE alhálózatból egy ügyfél a helyi hálózati infrastruktúra bújtatott hatályba. A hálózati folyamat jelenleg nem támogatott ASEs alapértelmezés szerint. Egy megoldást a problémára, hogy állítsa le a kereszt-közzététel útvonalak nyilvános társviszony elérési útjáról a magánfelhő-társviszony létesítése – elérési utat.  A másik megoldás, hogy engedélyezze a munkát a kényszerített bújtatás konfigurálása ASE.

## <a name="change-the-egress-endpoint-for-your-ase"></a>A kimenő forgalom végpont a ASE módosítása ##

Ez a szakasz ismerteti a kényszerített bújtatás konfigurálása a kimenő forgalom végpont a ASE által használt módosításával működését egy ASE engedélyezése. Ha a kimenő forgalom a ASE kényszeríti bújtatott egy a helyszíni hálózathoz, majd engedélyeznie kell, hogy forgalom forrás IP-címekről a ASE címe eltérő.

Egy ASE nem csak a külső függőségekkel rendelkezik, de azt is figyeli a bejövő forgalom a ASE kezeléséhez. A ASE ilyen forgalom válaszolni képesnek kell lennie, és a válaszok nem küldött vissza egy másik címről módon, hogy megszakítja a TCP.  A kimenő forgalom végpont módosítását a ASE szükséges három lépésben így történik.

1. Egy útválasztási táblázatot, annak érdekében, hogy bejövő felügyeleti forgalom is visszatérhet a azonos IP-cím beállítása
1. Az IP-címek a ASE tűzfalhoz kilépő használandó hozzáadása
1. Állítson be úgy az útvonalakat a kimenő forgalom a való kell bújtatott ASE

![A kényszerített bújtatás hálózati folyamata][1]

Konfigurálhatja a ASE különböző virtuális címmel rendelkező, a ASE már és működik, vagy ASE központi telepítése során adhatja után.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>A kimenő forgalom cím megváltoztatása után a ASE működési ###
1. A virtuális IP-címek használata a ASE kívánt IP-címek lekérése. Akkor használatos, ha a kényszerített bújtatás, akkor válassza ezt a NAT vagy az átjáró IP-címek.  Ha szeretné a ASE kimenő forgalmat NVA keresztül, a kimenő forgalom cím a nyilvános IP-címe az NVA lenne.
2. A kimenő címek állítható be a ASE konfigurációs adatait. Ugrás a resource.azure.com, és keresse meg: előfizetés /<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> , majd a JSON-ná, amely leírja a ASE látható.  Ellenőrizze, hogy az olvasási/írási felső felirat látható.  Kattintson a Szerkesztés görgessen le a képernyő aljára, és módosítsa a userWhitelistedIpRanges  

       "userWhitelistedIpRanges": null 
      
  egy a következőhöz hasonló. A címek be szeretné állítani, a virtuális címtartomány használata. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  PUT kattintson a lap tetején. Ez elindítja a ASE egy skálázási műveletet, és állítsa be a tűzfalon.
   
3. Hozzon létre egy útválasztási táblázatot és szerkesztése a szabályokat, hogy a felügyeleti címek, amelyek a ASE hely hozzárendelése és a hozzáférés feltöltése.  A felügyeleti címei Itt [App Service Environment-környezet felügyeleti címei][management] 

4. Változtatható egy útválasztási táblázathoz ASE alkalmazva útvonalak és a BGP-útvonalakat.  

Ha a ASE kerül nem válaszol a portálról, majd probléma van a módosításokat.  Lehet, hogy a kimenő címek listája nem fejeződött be, a forgalom megszakadt, vagy a forgalom blokkolása.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Hozzon létre egy új ASE egy másik kimenő címmel  ###

Abban az esetben, ha a virtuális hálózat már be van állítva a kényszerített bújtatás a forgalmat, akkor néhány további lépések elvégzésével a ASE létrehozása úgy, hogy az képes elérni sikeresen. Ez azt jelenti, hogy engedélyeznie kell egy másik kimenő végpont használatát a ASE létrehozása során.  Ehhez szüksége, amely meghatározza az engedélyezett kimenő címek a sablon a ASE létrehozásához.

1. Az IP-címek használhatók a kimenő címek a ASE beolvasása.
1. Előre hozza létre az alhálózatot, a ASE által használható. Ez lehetővé teszi, hogy útvonalak beállítása szükséges, továbbá mivel a sablon írja elő.  
1. Hozzon létre egy útválasztási táblázatot, amely a ASE helyre van leképezve, és rendelje hozzá a ASE IP-címek kezelése
1. Kövesse az utasításokat itt [létrehozása egy ASE sablonnal][template], és húzza le a megfelelő sablon
1. Szerkessze az azuredeploy.json fájlt a "források" szakasz. Egy sor felvétele **userWhitelistedIpRanges** hasonlóan a értékekkel:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Ha megfelelően van konfigurálva, majd a ASE kell kezdődnie, nincs probléma.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
