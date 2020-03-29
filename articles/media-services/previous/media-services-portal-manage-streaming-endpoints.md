---
title: Streamelési végpontok kezelése az Azure Portalon | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan kezelheti a streamelési végpontok az Azure Portalon.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 57ec22cb02512577f8737718cae9175403fb5603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900884"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Streamvégpontok kezelése az Azure Portalon 

Ez a cikk bemutatja, hogyan használhatja az Azure Portalon a streamelési végpontok kezelésére. 

>[!NOTE]
>Győződjön meg róla, hogy tekintse át az [áttekintő](media-services-streaming-endpoints-overview.md) cikket. 

A streamelési végpont méretezéséről ebben a [cikkben](media-services-portal-scale-streaming-endpoints.md) olvashat.

## <a name="start-managing-streaming-endpoints"></a>A streamelési végpontok kezelésének megkezdése 

A fiók streamelési végpontjainak kezeléséhez tegye a következőket.

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** panelen válassza a **Streamelési végpontok**lehetőséget.
   
    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> A számlázás csak akkor történik meg, ha a streamelési végpont futó állapotban van.

## <a name="adddelete-a-streaming-endpoint"></a>Streamelési végpont hozzáadása/törlése

>[!NOTE]
>Az alapértelmezett streamelési végpont nem törölhető.

Streamelési végpont hozzáadása/törlése az Azure Portalon, tegye a következőket:

1. Streamelési végpont hozzáadásához kattintson a lap tetején található **+ végpontra.** 

    Előfordulhat, hogy több streamelési végpontra van szüksége, ha különböző CDN-ekkel vagy CDN-kapcsolatokkal és közvetlen hozzáféréssel kíván rendelkezni.

2. A streamelési végpont törléséhez nyomja meg a **Törlés** gombot.      
3. Kattintson a **Start** gombra a streamelési végpont elindításához.
   
    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>A streamelési végpont konfigurálása
A streamelési végpont lehetővé teszi a következő tulajdonságok konfigurálását:

* Hozzáférés-vezérlés
* Gyorsítótár-vezérlés
* Helyek közötti hozzáférési házirendek

Ezekről a tulajdonságokról a [StreamingEndpoint című](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)témakörben talál részletes információt.

>[!NOTE]
>Ha a CDN engedélyezve van, nem lehet hozzáférni az IP-hozzáféréshez. Az IP-hozzáférés csak akkor alkalmazható, ha nincs CDN-je.

A streamelési végpontot az alábbi módon állíthatja be:

1. Válassza ki a konfigurálni kívánt streamelési végpontot.
2. Kattintson a **Beállítások** elemre.

A mezők rövid leírása következik.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Gyorsítótár-házirend maximális: a gyorsítótár élettartamának konfigurálására szolgál az ezen a streamelési végponton keresztül kiszolgált eszközök számára. Ha nincs beállítva érték, a program az alapértelmezett értéket használja. Az alapértelmezett értékek közvetlenül az Azure storage-ban is definiálhatók. Ha az Azure CDN engedélyezve van a streamelési végponthoz, ne állítsa a gyorsítótár-szabályzat értékét 600 másodpercnél rövidebbre.  
2. Engedélyezett IP-címek: olyan IP-címek megadására szolgál, amelyek a közzétett streamelési végponthoz csatlakozhatnak. Ha nincs megadva IP-cím, bármely IP-cím képes lesz csatlakozni. Az IP-címek megadhatók egyetlen IP-címként (például "10.0.0.1"), IP-címet és CIDR alhálózati maszkot használó IP-tartományként (például "10.0.0.1/22"), vagy IP-címet és pontozott decimális alhálózati maszkot használó IP-tartományként (például "10.0.0.1(255.25.25.0)).
3. Az Akamai aláírásfejléc-hitelesítés konfigurációja: az Akamai-kiszolgálók aláírásfejléc-hitelesítési kérelmének konfigurálására szolgál. A lejárat UTC-ben van.

## <a name="scale-your-premium-streaming-endpoint"></a>A prémium szintű streamelési végpont méretezése

További információt [ebben a cikkben](media-services-portal-scale-streaming-endpoints.md) talál.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Az Azure CDN-integráció engedélyezése

Amikor új fiókot hoz létre, alapértelmezés szerint engedélyezve van az alapértelmezett streamelési Azure CDN-integráció.

Ha később le szeretné tiltani/engedélyezni szeretné a CDN-t, a streamelési végpontnak **leállított** állapotban kell lennie. Akár két órát is igénybe vehet, amíg az Azure CDN-integráció engedélyezve van, és a módosítások az összes CDN-POP-ban aktívak lesznek. Azonban a streamelési végpont és a streamelés megszakítás nélkül a streamelési végpont, és ha az integráció befejeződött, az adatfolyam a CDN-ből. A kiépítési időszak alatt a streamelési végpont **lesz a kezdő** állapotban, és előfordulhat, hogy megfigyeli a csökkenő teljesítményt.

A CDN-integráció kína és szövetségi kormányzati régiók kivételével minden Azure-adatközpontban engedélyezve van.

Ha engedélyezve van, a **hozzáférés-vezérlés**, **Egyéni állomásnév és **az Akamai aláírás hitelesítési** konfigurációja le lesz tiltva.
 
> [!IMPORTANT]
> Az Azure Media Services és az Azure CDN integrációja a **Verizon Azure CDN-en** valósul meg a szabványos streamelési végpontokhoz. A prémium szintű streamelési végpontok konfigurálhatók az összes **Azure CDN-díjszabási csomag és szolgáltató**használatával. Az Azure CDN-szolgáltatásairól a [CDN áttekintése](../../cdn/cdn-overview.md)című témakörben olvashat bővebben.
 
### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* Ha a CDN engedélyezve van egy streamelési végponthoz, az ügyfelek nem kérhetnek tartalmat közvetlenül az eredeti forrásból. Ha a tartalom CDN-nel vagy anélkül is tesztelhető, létrehozhat egy másik streamelési végpontot, amely nincs engedélyezve a CDN-en.
* A streamelési végpont állomásneve ugyanaz marad a CDN engedélyezése után. A CDN engedélyezése után nem kell módosítania a médiaszolgáltatások munkafolyamatát. Ha például a streamelési végpont állomásneve strasbourg.streaming.mediaservices.windows.net, a CDN engedélyezése után a cdn pontosan ugyanazt az állomásnevet használja a készülék.
* Új streamelési végpontok esetén engedélyezheti a CDN-t egyszerűen egy új végpont létrehozásával; a meglévő streamelési végpontok esetén először le kell állítania a végpontot, majd engedélyeznie/le kell tiltania a CDN-t.
* A szabványos streamelési végpont csak a Klasszikus Azure-portál használatával konfigurálható **a Verizon Standard CDN-szolgáltató** használatával. Azonban engedélyezheti más Azure CDN-szolgáltatók rest API-k használatával.

## <a name="configure-cdn-profile"></a>CDN-profil konfigurálása

A CDN-profilt a **CDN kezelése** gombra kattintva állíthatja be felülről.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

