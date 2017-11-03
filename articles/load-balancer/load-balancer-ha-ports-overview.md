---
title: "Magas rendelkezésre állás portok – áttekintés az Azure-ban |} Microsoft Docs"
description: "További tudnivalók magas rendelkezésre állású portok terheléselosztási egy belső terheléselosztón"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>Magas rendelkezésre állás portok áttekintése (előzetes verzió)

Azure betöltése terheléselosztó szabványos betöltésére egyenleg TCP és UDP-forgalom összes porton egyszerre egy belső terheléselosztó használata esetén egy új lehetőséget vezet be. 

>[!NOTE]
> A Load Balancer Standard és jelenleg előzetes verzióban érhetők portokat a magas rendelkezésre állási funkció érhető el. Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Fontos regisztrálhat a terhelés terheléselosztó szabványos előzetes verziójára Load Balancer szabványos erőforrásokkal magas rendelkezésre ÁLLÁSÚ portok használatára. Kövesse az utasításokat az előfizetési mellett terheléselosztó [szabványos előzetes](https://aka.ms/lbpreview#preview-sign-up) is.

Egy magas rendelkezésre ÁLLÁSÚ portok szabály variant egy terheléselosztási szabályt egy belső betöltése terheléselosztó Standard konfigurálva.  Forgatókönyvek egyszerűsítettek, adja meg a Terheléselosztó egyetlen szabály egy belső terheléselosztó szabványos betöltése időtúllépést az összes porton érkező összes TCP és UDP-forgalom terheléselosztásához. A betöltési terheléselosztási döntés ne 5 rekordos forrás IP-cím, a forrásport, a cél IP-cím, a célport és a protokoll alapján.

Magas rendelkezésre ÁLLÁSÚ portok kritikus olyan forgatókönyveket tesz lehetővé, mint például terhelésű magas rendelkezésre állás és a virtuális hálózatokon belül a hálózati virtuális készülékek (NVA) skálázási, valamint egyéb forgatókönyvek, ahol nagyszámú portokat kell lennie. 

Magas rendelkezésre ÁLLÁSÚ portok van konfigurálva az előtér- és háttérszolgáltatások portok értékre állításával **0** és protokoll használatával **összes**.  A belső terheléselosztó erőforrás most kiegyensúlyozza a portszám függetlenül minden TCP és UDP-forgalom.

## <a name="why-use-ha-ports"></a>Miért érdemes használni a magas rendelkezésre ÁLLÁSÚ portok

### <a name="nva"></a>Virtuális hálózati készülékek

Az Azure számítási a több biztonsági fenyegetések elleni védelmét biztosító hálózati virtuális készülékek (NVA) is használhatja. NVA forgatókönyvekben használják, ha azok megbízható, magas rendelkezésre állású és igény szerint a kibővített kell lennie.

A forgatókönyvben ezen célok érhet el, egyszerűen NVA példányok felvétele az Azure belső terheléselosztó háttérkészletének és egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztó szabály konfigurálása.

Magas rendelkezésre ÁLLÁSÚ portok számos előnyt kínálnak NVA magas rendelkezésre ÁLLÁSÚ forgatókönyvek esetén:
- gyors feladatok átadása a példány állapotának mintavételt a kifogástalan példánya
- az n-aktív példányok kibővített magasabb teljesítmény
- n aktív és aktív-passzív forgatókönyvek
- így a nem kell összetett megoldások, például figyelés készülékek Zookeeper csomópontok

Az alábbi példában egy virtuális hálózati hub és küllős telepítése esetén a küllők kényszerített forgalmukat a központ virtuális hálózatra és az NVA keresztül mielőtt elhagynák a megbízható terület a mutatja be. A NVAs egy belső terheléselosztási terheléselosztó Standard magas rendelkezésre ÁLLÁSÚ portok konfiguráció mögött találhatók.  Az összes forgalom dolgozhatók fel, és ennek megfelelően továbbítani. 

![magas rendelkezésre állású portok – Példa](./media/load-balancer-ha-ports-overview/nvaha.png)

1. ábra – Hub és küllős virtuális hálózat magas rendelkezésre ÁLLÁSÚ módban üzembe helyezett NVAs

Ha a virtuális hálózati berendezések használ, ellenőrizze, hogy a megfelelő szolgáltató legjobban a magas rendelkezésre ÁLLÁSÚ portok használata, és melyik forgatókönyvek is támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Terheléselosztás nagy számú portok

Magas rendelkezésre ÁLLÁSÚ portok is használható a nagy számú portok terhelés balanicng igénylő alkalmazás-forgatókönyveket. Ezek a forgatókönyvek belső használatával egyszerűsíthető [terheléselosztó szabványos betöltése](https://aka.ms/lbpreview) magas rendelkezésre ÁLLÁSÚ porttal ahol egyetlen terheléselosztási szabály lecseréli több egyedi terheléselosztási szabályok, egy a minden port.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Magas rendelkezésre ÁLLÁSÚ portok érhető el a [Load Balancer szabványos azonos régiók](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Előnézet-előfizetés

Az előzetes betöltési terheléselosztó szabványos magas rendelkezésre ÁLLÁSÚ portok szolgáltatásának részt, regisztrálja az előfizetés hozzáférést Azure CLI 2.0 vagy a PowerShell használatával.  Kövesse az alábbi három lépéseket:

>[!NOTE]
>Ez a funkció használatához le kell is előfizetési terheléselosztóhoz [szabványos Preview](https://aka.ms/lbpreview#preview-sign-up) magas rendelkezésre ÁLLÁSÚ portok mellett. A magas rendelkezésre ÁLLÁSÚ portok vagy a Load Balancer szabványos előzetes regisztrációja egy óráig is tarthat.

### <a name="sign-up-using-azure-cli-20"></a>Regisztráljon az Azure CLI 2.0 verziót használja

1. A szolgáltatás regisztrálása a szolgáltató
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. Az előző művelet elvégzéséhez akár 10 percet is igénybe vehet.  A állapotának a művelet a következő paranccsal:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Csak a 3. lépés során a szolgáltatás regisztrációs állapota "Regisztrált" alább látható módon adja vissza:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Az előfizetési előzetes befejezéseként újra az erőforrás-szolgáltató előfizetésének regisztrálása:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>Regisztráció PowerShell használatával

1. A szolgáltatás regisztrálása a szolgáltató
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. Az előző művelet elvégzéséhez akár 10 percet is igénybe vehet.  A állapotának a művelet a következő paranccsal:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Csak a 3. lépés során a szolgáltatás regisztrációs állapota "Regisztrált" alább látható módon adja vissza:
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Az előfizetési előzetes befejezéseként újra az erőforrás-szolgáltató előfizetésének regisztrálása:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Korlátozások

Az alábbiakban a támogatott konfigurációk vagy a kivételeket a magas rendelkezésre ÁLLÁSÚ portokhoz:

- Egy egyetlen előtérbeli IP-konfigurációja lehet magas rendelkezésre ÁLLÁSÚ porttal egy DSR terheléselosztó szabályhoz, vagy azt, hogy egy egyetlen nem DSR terheléselosztási szabály magas rendelkezésre ÁLLÁSÚ porttal. Mindkettő nem lehet.
- Egy egyetlen hálózati illesztő IP-konfiguráció csak van egy nem-DSR terheléselosztói szabálynak, a magas rendelkezésre ÁLLÁSÚ portokkal. Az ipconfig nincs más szabályok konfigurálhatók.
- Egyetlen hálózati illesztő IP-konfigurációja egy vagy több DSR terheléselosztási szabály a magas rendelkezésre ÁLLÁSÚ portokkal, feltéve, hogy az a megfelelő előtérbeli IP-konfigurációjának egyediek.
- Ha az összes a terheléselosztási szabályok, magas rendelkezésre ÁLLÁSÚ-portok (csak DSR) vannak, vagy, a szabályok mindegyike nem - magas rendelkezésre ÁLLÁSÚ (DSR és port nem DSR), két (vagy több) terheléselosztó-szabály a azonos háttér címkészletet is üzemel. Két ilyen terheléselosztási szabályok nem lehetnek jelen, ha a magas rendelkezésre ÁLLÁSÚ és a nem - magas rendelkezésre ÁLLÁSÚ portokkal szabályok kombinációját.
- Magas rendelkezésre ÁLLÁSÚ portok nincs elérhető IPv6.
- Csak egyetlen hálózati adapter folyamata szimmetria NVA forgatókönyvek esetén támogatott. Lásd a leírást, és az ábra [hálózati virtuális készülékek](#nva). 



## <a name="next-steps"></a>Következő lépések

- [Egy belső Load Balancer Standard a magas rendelkezésre ÁLLÁSÚ portok konfigurálása](load-balancer-configure-ha-ports.md)
- [További tudnivalók a Load Balancer szabványos előzetes verzió](https://aka.ms/lbpreview)

