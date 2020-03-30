---
title: Fejlesztés v3 API-kkal
titleSuffix: Azure Media Services
description: Ismerje meg az entitásokra és API-kra vonatkozó szabályokat a Media Services 3-as v3-as szolgáltatással való fejlesztése során.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472084"
---
# <a name="develop-with-media-services-v3-apis"></a>Fejlesztés a Media Services 3-as vAPI-ival

Fejlesztőként használhatja a Media Services [REST API-t](https://docs.microsoft.com/rest/api/media/) vagy az ügyféltárakat, amelyek lehetővé teszik a REST API-val való interakciót egyéni médiamunkafolyamatok egyszerű létrehozásához, kezeléséhez és karbantartásához. A [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API-ja az OpenAPI specifikáción alapul (korábbi nevén Swagger).

Ez a cikk ismerteti az entitásokra és API-kra vonatkozó szabályokat, amikor a Media Services 3-as v3-as ával fejleszt.

## <a name="accessing-the-azure-media-services-api"></a>Az Azure Media Services API elérése

Ahhoz, hogy jogosult legyen a Media Services-erőforrások és a Media Services API elérésére, először hitelesítenie kell magát. A Media Services támogatja az [Azure Active Directory (Azure AD) alapú](../../active-directory/fundamentals/active-directory-whatis.md) hitelesítést. Két közös hitelesítési lehetőség a következő:
 
* **Szolgáltatásegyszerű hitelesítés:** Egy szolgáltatás hitelesítésére használható (például: webalkalmazások, függvényalkalmazások, logikai alkalmazások, API és mikroszolgáltatások). A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek démonszolgáltatásokat, középső rétegbeli szolgáltatásokat vagy ütemezett feladatokat futtatnak. A webalkalmazások esetében például mindig kell lennie egy középszintű, amely egy egyszerű szolgáltatással csatlakozik a Media Serviceshez.
* **Felhasználói hitelesítés:** Az alkalmazást használó személy hitelesítésére használható a Media Services erőforrásaival való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználó hitelesítő adatait. Egy példa egy felügyeleti konzol alkalmazás által használt jogosult felhasználók kódolási feladatok figyelésére vagy élő streamelés.

A Media Services API megköveteli, hogy a REST API-kérelmeket létrehozó felhasználó vagy alkalmazás hozzáférjen a Media Services-fiók erőforrásához, és **közreműködői** vagy **tulajdonosi** szerepkört használjon. Az API érhető el a **Reader** szerepkör, de csak **a Beolvasás** vagy **lista** műveletek lesznek elérhetők.További információt a [Media Services-fiókok szerepköralapú hozzáférés-vezérlése című témakörben talál.](rbac-overview.md)

Ahelyett, hogy egyszerű szolgáltatáslétrehozása, fontolja meg felügyelt identitások azure-erőforrások eléréséhez a Media Services API-t az Azure Resource Manager használatával. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásairól, olvassa el [a Mi a felügyelt identitások az Azure-erőforrásokhoz.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="azure-ad-service-principal"></a>Azure AD egyszerű szolgáltatás

Ha egy Azure AD-alkalmazást és egyszerű szolgáltatást hoz létre, az alkalmazásnak a saját bérlőjében kell lennie. Az alkalmazás létrehozása után adjon hozzáférést az alkalmazás **közreműködői** vagy **tulajdonosi** szerepkörének a Media Services-fiókhoz.

Ha nem biztos abban, hogy rendelkezik-e engedéllyel egy Azure AD-alkalmazás létrehozásához, olvassa el a Szükséges engedélyek című [témakört.](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)

A következő ábrán a számok a kérelmek áramlását időrendi sorrendben jelölik:

![Középső szintű alkalmazáshitelesítés AAD-vel webes API-ból](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Egy középső rétegű alkalmazás olyan Azure AD-hozzáférési jogkivonatot kér, amely a következő paraméterekkel rendelkezik:  

   * Azure AD-bérlői végpont.
   * Media Services erőforrás-URI.
   * A REST Media Services erőforrás-URI-ja.
   * Az Azure AD-alkalmazás értékei: az ügyfélazonosító és az ügyfél titkos.

   Az összes szükséges érték beolvassa: Access Azure Media Services API az Azure CLI.To get all the needed values, see [Access Azure Media Services API with the Azure CLI.](access-api-cli-how-to.md)

2. Az Azure AD-hozzáférési jogkivonat a középső rétegre kerül elküldésre.
4. A középső réteg az Azure Media REST API-nak küldi a kérelmet az Azure AD-jogkivonattal.
5. A középső réteg visszakapja az adatokat a Media Servicesből.

### <a name="samples"></a>Példák

Tekintse meg az alábbi mintákat, amelyek bemutatják, hogyan csatlakozhat az Azure AD egyszerű szolgáltatáshoz:

* [Csatlakozás a REST-hez](media-rest-apis-with-postman.md)  
* [Kapcsolódás Javával](configure-connect-java-howto.md)
* [Kapcsolódás .NET-tel](configure-connect-dotnet-howto.md)
* [Kapcsolódás Node.js-sel](configure-connect-nodejs-howto.md)
* [Kapcsolódás Pythonnal](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként.

A Media Services erőforrásnevei nem tartalmazhatnak "<", ">", "%", "&", ":", "&#92;", "?", "/",',',','+', '.', az egyidézőjelű karaktereket vagy bármely vezérlőkaraktert. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter.

Az Azure Resource Manager elnevezéséről további információt az [Elnevezési követelmények](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és elnevezési konvenciók című [témakörben talál.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="names-of-filesblobs-within-an-asset"></a>Egy eszközön belüli fájlok/blobok nevei

Az egy eszközön belüli fájlok/blobok nevének meg kell felelnie mind a [blobnév-követelményeknek,](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) mind az [NTFS-névkövetelményeknek.](https://docs.microsoft.com/windows/win32/fileio/naming-a-file) Ezeknek a követelményeknek az az oka, hogy a fájlok a blobstorage-ból feldolgozásra egy helyi NTFS-lemezre másolhatók.

## <a name="long-running-operations"></a>Hosszú távú műveletek

Az Azure Media `x-ms-long-running-operation` Services [swagger-fájlokban](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) megjelölt műveletek hosszú ideig futó műveletek. 

Az aszinkron Azure-műveletek nyomon követéséről az [Async-műveletek című témakörben talál](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)további információt.

A Media Services a következő, hosszú ideig futó műveleteket futtatja:

* [Élő események létrehozása](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Élő események frissítése](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Élő esemény törlése](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Élő esemény indítása](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [LiveEvent leállítása](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  A `removeOutputsOnStop` paraméter használatával törölheti az összes társított élő kimenetet az esemény leállításakor.  
* [LiveEvent visszaállítása](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput létrehozása](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput törlése](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [StreamingVégpont létrehozása](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Streamelésvégpont frissítése](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Streamelésvégpont törlése](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Streamelés indításaVégpont](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [StreamingVégpont leállítása](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Streamelésvégpont méretezése](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Egy hosszú művelet sikeres beküldése esetén egy "202 Elfogadva" értéket kap, és a visszaadott műveletazonosító használatával le kell kérnie a művelet befejezését.

A [pálya aszinkron Azure-műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) cikk részletesen ismerteti, hogyan követheti nyomon az aszinkron Azure-műveletek állapotát a válaszban visszaadott értékeken keresztül.

Egy adott élő eseményhez vagy annak bármely kapcsolódó élő kimenetéhez csak egy hosszú ideig futó művelet támogatott. Indítás után egy hosszú ideig futó műveletnek be kell fejeződnie, mielőtt egy későbbi, hosszú ideig futó műveletet indítana el ugyanazon a LiveEventen vagy bármely kapcsolódó Live Outputon. Több élő kimenettel rendelkező élő események esetén meg kell várnia egy hosszú ideig futó művelet befejezését egy élő kimeneten, mielőtt egy hosszú ideig futó műveletet indítana egy másik élő kimeneten. 

## <a name="sdks"></a>SDK-k

> [!NOTE]
> Az Azure Media Services v3 SDK-k nem garantált, hogy szál biztos. Többszálas alkalmazás fejlesztése kor, hozzá kell adnia a saját szál szinkronizálási logika az ügyfél védelme érdekében, vagy egy új AzureMediaServicesClient-objektum szálonként. Ügyeljen arra is, hogy a kód által az ügyfélnek biztosított választható objektumok (például a .NET-ben httpclient példány) által bevezetett többszálas problémák forduljanak elő.

|SDK|Referencia|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-referencia](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-referencia](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-referencia](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-referencia](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-referencia](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Lásd még

- [Media Service-eseményeket tartalmazó EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [A Media Services eseményeinek meghatározásai](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Az Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) egy olyan eszköz, amely a Media Services szolgáltatásról szeretnének többet megtudni a Windows-ügyfelek számára. Az AMSE egy Winforms/C# alkalmazás, amely feltölti, letölti, kódolja, továbbítja a VOD-t és az élő tartalmat a Media Services szolgáltatással. Az AMSE eszköz azoknak az ügyfeleknek szól, akik kód írása nélkül szeretnék tesztelni a Media Services szolgáltatást. Az AMSE-kód erőforrásként szolgál a Media Services szolgáltatással fejleszteni kívánt ügyfelek számára.

Az AMSE egy nyílt forráskódú projekt, a támogatást a https://github.com/Azure/Azure-Media-Services-Explorer/issues)közösség biztosítja (a problémákat jelenteni lehet a programnak. A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információkért tekintse meg a magatartási kódex opencode@microsoft.com [gyik- kérdéseit,](https://opensource.microsoft.com/codeofconduct/faq/) vagy lépjen kapcsolatba más kérdésekkel vagy megjegyzésekkel.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services-entitások szűrése, rendezése, lapozása

Lásd: [Az Azure Media Services-entitások szűrése, rendelése, lapozása.](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>További lépések

* [Csatlakozás médiaszolgáltatásokhoz a Java segítségével](configure-connect-java-howto.md)
* [Csatlakozás a Media Services szolgáltatáshoz a .NET segítségével](configure-connect-dotnet-howto.md)
* [Csatlakozás a Media Services szolgáltatáshoz a Node.js szolgáltatóval](configure-connect-nodejs-howto.md)
* [Csatlakozás mediaservices python-nal](configure-connect-python-howto.md)
