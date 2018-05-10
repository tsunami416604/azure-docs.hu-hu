---
title: Adatfolyam-végpontokat kezelheti a az Azure portálon |} Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan adatfolyam-végpontokat kezelheti a az Azure-portálon.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: 542780766cfa90026d5ff492fcf7b579cb2d7029
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Az Azure-portálon az adatfolyam-továbbítási végpontok kezelése

Ez a cikk bemutatja, hogyan adatfolyam-továbbítási végpontok kezelése az Azure-portál használatával. 

>[!NOTE]
>Mindenképpen tekintse át a [áttekintése](media-services-streaming-endpoints-overview.md) cikk. 

A streamvégpont méretezése kapcsolatos információkért lásd: [ez](media-services-portal-scale-streaming-endpoints.md) cikk.

## <a name="start-managing-streaming-endpoints"></a>Adatfolyam-továbbítási végpontok kezelése – első lépések 

Indítsa el a fiókjához streamvégpontok, tegye a következőket.

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Az a **beállítások** panelen válassza **adatfolyam-végpontok**.
   
    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Csak számlázása, amikor a Streamvégpontot futó állapotban van.

## <a name="adddelete-a-streaming-endpoint"></a>A streamvégpont hozzáadása vagy törlése

>[!NOTE]
>Az alapértelmezett streamvégpontból nem lehet törölni.

Az Azure portál használatával streamvégpont hozzáadása és törlése, tegye a következőket:

1. A streamvégpont hozzáadásához kattintson a **+ végpont** az oldal tetején. 

    Ha azt tervezi, különböző tartalomtovábbító vagy a CDN, valamint a közvetlen hozzáférés érdemes lehet több adatfolyam-végpontot.

2. Törli a streamvégpontján, nyomja le az **törlése** gombra.      
3. Kattintson a **Start** gombra kattintva indítsa el a streamvégpontra.
   
    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>A Streamvégpont konfigurálása
Adatfolyam-továbbítási végpontra lehetővé teszi, hogy konfigurálja a következő tulajdonságokat:

* Hozzáférés-vezérlés
* Gyorsítótár-vezérlés
* Kereszt-hely hozzáférési házirendek

Ezek a Tulajdonságok kapcsolatos részletes információkért lásd: [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Ha engedélyezve van a CDN, IP-hozzáférés nem érhető el. IP-hozzáférés csak is alkalmazható, amikor nincs CDN.

Streamvégpont konfigurálása a következő módon:

1. Válassza ki a konfigurálni kívánt streamvégpontra.
2. Kattintson a **beállítások**.

A következő mezők rövid leírása.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. A gyorsítótár maximális házirend: gyorsítótár élettartamát a streamvégpontján keresztül kiszolgált eszközök konfigurálásához. Ha nincs érték megadva, az alapértelmezett szolgál. Az alapértelmezett értékeket közvetlenül az Azure storage-ban is definiálhatók. Azure CDN szolgáltatás engedélyezve van, annál a streamvégpontnál, ha akkor kell beállítania a gyorsítótár házirend értéke nem kisebb, mint 600 másodperc.  
2. Engedélyezett IP-cím: IP-címek, amelyek a közzétett streamvégpont csatlakozni engedélyezett meghatározására szolgál. Ha nincs megadva IP-címeket, IP-címeket kapcsolódhatnak lesz. IP-címeket adhat meg egyetlen IP-címet (például 10.0.0.1), egy IP-címet és egy CIDR alhálózati maszk (például "10.0.0.1/22") IP-címtartomány vagy egy IP-címtartomány IP-címet és egy pontozott decimális alhálózati maszk segítségével (például "10.0.0.1(255.255.255.0)').
3. Akamai aláírás fejléc hitelesítési konfigurációját: használatával adja meg, hogyan aláírás fejléc hitelesítési kérelem Akamai kiszolgálókról van konfigurálva. Lejáratának UTC formátumban van.

## <a name="scale-your-premium-streaming-endpoint"></a>Az adatfolyam-továbbítási végpontra prémium méretezése

További információkért tekintse meg [ezt](media-services-portal-scale-streaming-endpoints.md) a cikket.

## <a id="enable_cdn"></a>Azure CDN-integráció engedélyezése

Amikor létrehoz egy új fiókot, alapértelmezett végpont Azure CDN adatfolyam-integráció alapértelmezés szerint engedélyezve van.

Ha később szeretné a CDN engedélyezése vagy tiltása, az adatfolyam-továbbítási végpontjának szerepelnie kell a **leállt** állapotát. Azt az Azure CDN integráció aktiválható és a változások keresztül minden a CDN POP ismét aktív legfeljebb két óráig is eltarthat. Azonban Ön indítsa el a streamelési végpont és működésének felfüggesztése nélkül adatfolyam a streamvégpontból és az integráció végrehajtása után az adatfolyam a CDN kerül. Az üzembe helyezési időszak alatt az adatfolyam-továbbítási végpontjának lesz **indítása** állapotát, és előfordulhat, hogy tekintse át az degredad teljesítményét.

CDN-integráció engedélyezve van minden az Azure-adatközpont Kína és Szövetségi Kormánya régiók kivételével.

Ha engedélyezve van, a **hozzáférés-vezérlés**, ** egyéni állomásnév, és **Akamai aláírás hitelesítési** konfigurációs lekérdezi-e tiltva.
 
> [!IMPORTANT]
> Azure Media Services-integráció az Azure CDN van megvalósítva a **Azure CDN Verizon** standard adatfolyam-végpontok. Prémium szintű adatfolyam-végpontok beállítható, hogy az összes **árképzési szinteket, és szolgáltatók Azure CDN**. Azure CDN funkciókkal kapcsolatos további információkért lásd: a [CDN áttekintésével](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* CDN engedélyezve van a streamvégpontján, amikor az ügyfelek tartalmat nem közvetlenül a forrásból igényelnek. Tesztelje a tartalom vagy anélkül CDN lehetősége van szüksége, ha egy másik streamvégpontra, amely nem engedélyezett CDN is létrehozhat.
* Az adatfolyam-továbbítási végpont állomásnevet változatlan marad, miután engedélyezte a CDN-t. Nem kell módosítani a media services munkafolyamat CDN engedélyezése után. Például, ha a folyamatos átviteli végpont állomásnevéhez strasbourg.streaming.mediaservices.windows.net, miután engedélyezte a CDN, pontos azonos rendszer az állomásnevet használja.
* Az új streamvégpontok engedélyezéséhez CDN egyszerűen egy új végpont; létrehozása a meglévő streamvégpontok kell előbb leállítani a végpontot, és majd engedélyezését vagy letiltását a CDN-t.
* Adatfolyam-továbbítási végpontra standard csak használatával konfigurálható **Verizon standard szintű CDN-szolgáltató** klasszikus Azure portál használatával. Azonban más Azure CDN szolgáltatók REST API-k használatával engedélyezheti.

## <a name="configure-cdn-profile"></a>CDN-profil konfigurálása

A CDN-profil kiválasztásával konfigurálhatja a **kezelése CDN** gombra a lista elejéről.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

