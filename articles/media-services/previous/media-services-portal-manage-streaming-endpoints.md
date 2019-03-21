---
title: Streamvégpontok skálázása az Azure Portallal |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan streamvégpontok skálázása az Azure portal használatával.
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
ms.openlocfilehash: 1775bbb2913f6b1a985ca7ec9e89bafed42fd0e6
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258207"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Streamvégpontok skálázása az Azure portal használatával 

Ez a cikk bemutatja, hogyan streamvégpontok kezelése az Azure portal használatával. 

>[!NOTE]
>Mindenképpen tekintse át a [áttekintése](media-services-streaming-endpoints-overview.md) cikk. 

A streamvégpont méretezése kapcsolatos információkért lásd: [ez](media-services-portal-scale-streaming-endpoints.md) cikk.

## <a name="start-managing-streaming-endpoints"></a>Start streamvégpontok felügyelete 

Start streamvégpontok a fiók felügyelete, tegye a következőket.

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Az a **beállítások** panelen válassza ki **Streamvégpontok**.
   
    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Ha a futó állapotban van a folyamatos átviteli végponton csak számolunk fel.

## <a name="adddelete-a-streaming-endpoint"></a>A streamvégpont hozzáadása/törlése

>[!NOTE]
>Nem lehet törölni az alapértelmezett streamvégpontot.

Az Azure portal használatával streamvégpont hozzáadása/törlése, tegye a következőket:

1. Adjon hozzá egy folyamatos átviteli végponton, kattintson a **+ végpont** az oldal tetején. 

    Több adatfolyam-továbbítási végpontok célszerű, ha azt tervezi, hogy a különböző CDN vagy egy CDN-t és a közvetlen hozzáférést.

2. Töröl egy streamvégponton, nyomja le a **törlése** gombra.      
3. Kattintson a **Start** gombra kattintva indítsa el a streamvégpontot.
   
    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>A Streamvégpont konfigurálása
Streamvégpont lehetővé teszi, hogy konfigurálja a következő tulajdonságokat:

* Hozzáférés-vezérlés
* Gyorsítótár-vezérlés
* Adatbázisközi webhely hozzáférési szabályzatok

Ezek a Tulajdonságok kapcsolatos részletes információkért lásd: [Streamvégpontok](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Ha engedélyezve van a CDN-t, IP hozzáférés nem érhető el. IP-hozzáférés csak akkor alkalmazható, ha a CDN nem rendelkezik.

A streamvégpont az alábbiak szerint konfigurálhatja:

1. Válassza ki a konfigurálni kívánt streamvégpontra.
2. Kattintson a **beállítások**.

A következő mezők rövid leírását.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. A gyorsítótár maximális házirend: ezen a streamvégponton kiszolgált objektumok tárazási élettartamának konfigurálásához használt. Ha nincs érték van beállítva, az alapértelmezett szolgál. Az alapértelmezett értékeket közvetlenül az Azure storage-ban is lehet definiálni. Ha az Azure CDN engedélyezve van a folyamatos átviteli végponton, nem kell beállítania a gyorsítótár-szabályzat értéke kisebb, mint 600 másodpercre.  
2. Engedélyezett IP-címei: a közzétett streamvégpont kapcsolódás engedélyezett IP-címek megadásához. Ha nincs megadott IP-címeket, IP-címeket lenne való kapcsolódást. IP-címeket adhat meg egyetlen IP-címet (például 10.0.0.1), egy IP-címtartományt az IP-címet és egy CIDR alhálózati maszk (például 10.0.0.1/22) vagy egy IP-címtartományt az IP-cím és egy pontozott decimális alhálózati maszk (például 10.0.0.1 ' () 255.255.255.0) ").
3. Akamai aláírásfejléc hitelesítése a konfiguráció: adható meg, hogyan van konfigurálva az aláírás fejléc hitelesítési kérelem Akamai-kiszolgálóktól. Lejárata UTC formátumban van.

## <a name="scale-your-premium-streaming-endpoint"></a>A prémium szintű streamvégpont méretezése

További információkért tekintse meg [ezt](media-services-portal-scale-streaming-endpoints.md) a cikket.

## <a id="enable_cdn"></a>Az Azure CDN-integráció engedélyezése

Amikor létrehoz egy új fiókot, alapértelmezett Streaming Endpoint Azure CDN-integrációnak alapértelmezés szerint engedélyezve van.

Ha később szeretné a CDN engedélyezése vagy tiltása, a streamvégpontnak kell lennie a **leállt** állapota. Azt az Azure CDN engedélyezve az integráció és a módosítások között minden a CDN jelenléti helyein lesz aktív akár két óráig is eltarthat. Azonban Ön telepítheti a streamelési végpontot és a stream nélkül megszakítások indítsa el a streamvégpontot, és az integráció befejezése után a CDN-ből továbbítsa a streamet. Az üzembe helyezési ideje alatt a tartalomstreameléshez használt streamvégpont szerepelni fog **indítása** állapotát, és előfordulhat, hogy figyelje a teljesítmény csökkenését.

Az összes az Azure adatközpontjaiban kivéve Kínát és a Szövetségi kormányzati régió engedélyezve van a CDN-integrációt.

Ha engedélyezve van, a **hozzáférés-vezérlés**, ** egyéni állomásnév, és **Akamai Jogosultságkódos hitelesítés** konfiguráció a lekérdezi le van tiltva.
 
> [!IMPORTANT]
> Az Azure Media Services integrációja az Azure CDN van alkalmazva **verizon Azure CDN** a standard streamvégpontok. Prémium szintű streamelési végpontok használatával az összes konfigurálható **tarifacsomagok és szolgáltatók az Azure CDN**. Az Azure CDN-funkciókkal kapcsolatos további információkért lásd: a [CDN áttekintése](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* Amikor a CDN engedélyezve egy streamvégponton, az ügyfelek tartalmat nem lehet közvetlenül a forrásból igényelnek. Ha a tartalom, vagy a CDN nélkül tesztelhetik, hozhat létre, amely nem a CDN engedélyezve van egy másik streamvégpont.
* A streamelési végpont gazdaneve változatlan marad, a CDN engedélyezése után. Nem kell semmilyen módosítást a media services munkafolyamat után a CDN engedélyezve van. Például ha a streamelési végpont gazdaneve strasbourg.streaming.mediaservices.windows.net, a CDN engedélyezése után, szolgál az pontos azonos állomásnévvel.
* Új streamelési végpontok engedélyezheti a CDN egyszerűen; új végpont létrehozása meglévő streamvégpontokra kell először állítsa le a végpont, és ezután engedélyezi/letiltja a CDN-t.
* Standard szintű streamvégpont csak használatával konfigurálható **Standard – Verizon CDN-szolgáltató** a klasszikus Azure portál használatával. Azonban más Azure CDN-szolgáltató REST API-k használatával engedélyezheti.

## <a name="configure-cdn-profile"></a>CDN-profil konfigurálása

A CDN-profil kiválasztásával konfigurálhatja úgy a **CDN kezelése** gombot a felső.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

