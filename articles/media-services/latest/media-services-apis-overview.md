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
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5ae7563892cb4792f5c329b2850d7b88d37c0e7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698266"
---
# <a name="developing-with-media-services-v3-apis"></a>Fejlesztés a Media Services v3 API-k

Ez a cikk ismerteti a szabályokat, amelyek a alkalmazni az entitások és API-k, a Media Services v3 fejlesztése során.

## <a name="accessing-the-azure-media-services-api"></a>Az Azure Media Services API elérése

Az Azure Media Services-erőforrások eléréséhez, használhatja az Azure Active Directory (AD) egyszerű szolgáltatásnév hitelesítésével.
A Media Services API megköveteli, hogy a felhasználó vagy alkalmazás a REST API-t kér a Media Services-fiók erőforrás hozzáférése, és használja a **közreműködői** vagy **tulajdonosa** szerepkör. Az API az elérhető lesz a **olvasó** azonban csak a szerepkör **első** vagy **lista**   művelet elérhető lesz. További információkért lásd: [szerepköralapú hozzáférés-vezérlés a Media Services-fiókok](rbac-overview.md).

Egyszerű szolgáltatás létrehozása helyett érdemes lehet felügyelt identitások az Azure-erőforrások eléréséhez a Media Services API Azure Resource Manageren keresztül. Az Azure-erőforrások felügyelt identitások kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Az Azure AD-szolgáltatásnév 

Hoz létre egy Azure AD-alkalmazás és szolgáltatás egyszerű, ha az alkalmazásnak a saját bérlőben legyen. Miután létrehozta az alkalmazást, adjon az alkalmazásnak **közreműködői** vagy **tulajdonos** a Media Services-fiók eléréséhez. 

Ha nem biztos abban, hogy rendelkezik-e engedélyekkel hozzon létre egy Azure AD-alkalmazást, lásd: [szükséges engedélyek](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Az alábbi ábrán a számok jelölik a folyamatot a kérelmek időrendi sorrendben:

![Középső rétegbeli alkalmazásokhoz](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. A középső rétegű alkalmazás, amely a következő paraméterekkel rendelkezik az Azure AD hozzáférési jogkivonatot kér:  

   * Az Azure AD-bérlő végpont.
   * Media Services-erőforrás URI-t.
   * Erőforrás-URI REST Media Services.
   * Az Azure AD-alkalmazás értékeire: az ügyfél-Azonosítóját és ügyfélkulcsát.
   
   A szükséges értékek lekéréséhez lásd: [hozzáférés az Azure Media Services API az Azure CLI-vel](access-api-cli-how-to.md)

2. Az Azure AD hozzáférési jogkivonatot a középső réteg küld.
4. A középső réteg kérést küld az Azure Media REST API az Azure AD-jogkivonattal.
5. A középső réteg vissza az adatok lekérése a Media Services.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

A Media Services-erőforrás neve nem tartalmazhatja a következőket: "<", ">", "%", "&", ': ','&#92;','?', '/', "*", "+",".", szimpla idézőjel vagy bármely egyéb vezérlőkarakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter. 

Azure Resource Manager elnevezésével kapcsolatos további információkért lásd: [Elnevezési követelményeknek](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

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
