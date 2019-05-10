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
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4c5b30ab075bbca22b6a58ccf65e55d332820937
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406542"
---
# <a name="developing-with-media-services-v3-apis"></a>Fejlesztés a Media Services v3 API-k

Ez a cikk ismerteti a szabályokat, amelyek a alkalmazni az entitások és API-k, a Media Services v3 fejlesztése során.

## <a name="accessing-the-azure-media-services-api"></a>Az Azure Media Services API elérése

Azt, hogy a Media Services-erőforrások és a Media Services API eléréséhez, akkor először hitelesíteni kell. A Media Services támogatja a [Azure Active Directory (Azure AD)-alapú](../../active-directory/fundamentals/active-directory-whatis.md) hitelesítést. Két gyakori hitelesítési lehetőségek közül választhat:
 
* **Egyszerű szolgáltatásnév hitelesítése** – szolgáltatás hitelesítéséhez használatos (például: web apps, a függvényalkalmazások, a logic apps, API és mikroszolgáltatás-alapú). Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyeket démonszolgáltatásokat, a középső rétegű services vagy az ütemezett feladatok futtatásához. Például webes alkalmazások nem mindig kell egy középső rétegbeli, amely a Media Services az egyszerű szolgáltatás csatlakozik.
* **Felhasználói hitelesítés** – az a személy, aki használja az alkalmazást az való kommunikációhoz a Media Services-erőforrások hitelesítéséhez. Az interaktív alkalmazást először kell kérni a felhasználót, a felhasználói hitelesítő adatokat. Ilyen például, a kódolási feladatok figyelésére, vagy az élő adások online közvetítése jogosult felhasználók által használt felügyeleti konzolalkalmazást.

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

### <a name="samples"></a>Minták

Tekintse meg a következő példák azt mutatják be, hogyan csatlakozhat az Azure AD-szolgáltatásnév:

* [Csatlakozás a REST segítségével](media-rest-apis-with-postman.md)  
* [Kapcsolódás Javával](configure-connect-java-howto.md)
* [Kapcsolódás .NET-tel](configure-connect-dotnet-howto.md)
* [Kapcsolódás Node.js-sel](configure-connect-nodejs-howto.md)
* [Kapcsolódás Pythonnal](configure-connect-python-howto.md)

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

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="next-steps"></a>További lépések

[A Media Services v3 API SDK-k és eszközök használata a fejlesztés megkezdése](developers-guide.md)
