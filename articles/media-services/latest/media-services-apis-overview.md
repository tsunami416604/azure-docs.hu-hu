---
title: Fejlesztés a v3 API-k – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a szabályokat, amelyek a alkalmazni az entitások és API-k, a Media Services v3 fejlesztése során.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492141"
---
# <a name="developing-with-media-services-v3-apis"></a>Fejlesztés a Media Services v3 API-k

Ez a cikk ismerteti a szabályokat, amelyek a alkalmazni az entitások és API-k, a Media Services v3 fejlesztése során.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

A Media Services-erőforrás neve nem tartalmazhatja a következőket: "<", ">", "%", "&", ': ','&#92;','?', '/', "*", "+",".", szimpla idézőjel vagy bármely egyéb vezérlőkarakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter. 

Azure Resource Manager elnevezésével kapcsolatos további információkért lásd: [Elnevezési követelményeknek](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>V3 API-tervezési alapelvek és RBAC

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. V3 API-k nem adnak vissza titkos kulcsok és hitelesítő adatok a **első** vagy **lista** műveleteket. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak kell beolvasni a titkos kulcsok és hitelesítő adatok külön műveletet metódusának meghívása. A **olvasó** szerepkör nelze volat operations, műveletek, mint a Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets, nelze volat. Önálló műveletek kellene lehetővé teszi a részletesebb RBAC biztonsági engedélyeinek beállítása egy egyéni szerepkör, ha szükséges.

További információkért lásd:

- [Beépített szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Az RBAC használatával hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Szerepköralapú hozzáférés-vezérlés a Media Services-fiókok](rbac-overview.md)
- [Tartalom fő házirend - lekérése a .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Hosszú ideig futó műveletek

A műveletek jelölése `x-ms-long-running-operation` az Azure Media Services [swagger-fájlok](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) hosszú futó műveletek. 

Az Azure aszinkron műveletek követése kapcsolatos részletekért lásd: [aszinkron műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

A Media Services a következő hosszú ideig futó műveleteket tartalmazza:

* Létrehozása videókhoz
* Frissítés videókhoz
* Törölje a videókhoz
* Indítsa el a videókhoz
* Állítsa le a videókhoz
* Visszaállítás videókhoz
* LiveOutput létrehozása
* Delete LiveOutput
* Streamvégpontok létrehozása
* Streamvégpontok frissítése
* Streamvégpontok törlése
* Start Streamvégpontok
* Streamvégpontok leállítása
* Méretezési csoport Streamvégpontok

## <a name="filtering-ordering-paging-of-media-services-entities"></a>A Media Services entitások szűrési, rendezési, stránkování

Lásd: [szűrése, rendezése, az Azure Media Services entitások lapozás](entities-overview.md)

## <a name="next-steps"></a>További lépések

[A Media Services v3 API SDK-k és eszközök használata a fejlesztés megkezdése](developers-guide.md)
