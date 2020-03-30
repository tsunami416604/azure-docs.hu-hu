---
title: REST API-hibakódok – Azure Machine Learning Studio (klasszikus) | Microsoft dokumentumok
description: Ezeket a hibakódokat egy Azure Machine Learning-webszolgáltatás on egy művelet adja vissza.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217762"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasszikus) REST API-hibakódok

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
A következő hibakódokat egy Azure Machine Learning Studio (klasszikus) webszolgáltatás on operation.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-állapotkód 400)
 
Érvénytelen argumentum van megadva.
 
Ez a hibaosztály azt jelenti, hogy egy valahol megadott argumentum érvénytelen volt. Ez lehet egy hitelesítő adatok vagy az Azure storage-tároló egy adott a webszolgáltatásnak átadott. Kérjük, tekintse meg a hiba "kód" mezőt a "részletek" szakaszban, hogy diagnosztizálja, hogy melyik konkrét argumentum érvénytelen volt.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BadParameterValue érték | A megadott paraméterérték nem felel meg a paraméterszabályának. |
| BadSubscriptionId azonosító | A pontozáshoz használt előfizetés-azonosító nem az erőforrásban található azonosító. |
| BadVersionCall hívás | Érvénytelen verzióparaméter tért át az {0}API-hívás során: . Ellenőrizze az API súgólapján, hogy a megfelelő verziót adta-e át, majd próbálkozzon újra. |
| BatchJobInputsNotSpecified | A kérelemmel nem a következő szükséges bemenet(eke)t nem adták meg: {0}. Ellenőrizze, hogy minden bemeneti adat meg van-e adva, majd próbálkozzon újra. |
| BatchJobInputsTooManySpecified | A kérelem a szolgáltatásban megadottnál több bemenetet adott meg. Az elfogadott input(ok) listája: {0}. Ellenőrizze, hogy minden bemeneti adat helyesen van-e megadva, majd próbálkozzon újra. |
| BlobNameTooLong | A diagnosztikai kimenethez biztosított Azure blob {0}storage elérési útja túl hosszú: . Rövidítse le az elérési utat, és próbálkozzon újra. |
| Blob Nem található | Nem lehet elérni a {0}megadott Azure blob -.  Azure-hibaüzenet: {1}. |
| ContainerIsEmpty között | Nem adott meg Azure storage-tároló nevet. Adjon meg érvényes tárolónevet, majd próbálkozzon újra. |
| ContainerSegmentInvalid | Érvénytelen tárolónév. Adjon meg érvényes tárolónevet, majd próbálkozzon újra. |
| ContainerValidationFailed | A blobtároló érvényesítése {0}nem sikerült a következő hiba miatt: . |
| DataTypeNotSupported | Nem támogatott adattípus biztosított. Adjon meg érvényes adattípus(oka)t, majd próbálkozzon újra. |
| DuplicateInputInBatchCall | A kötegkérelem érvénytelen. Egyszerre nem adható meg egyszerre egyszerre egy- és több bemenet. Távolítsa el az egyik elemet a kérelemből, majd próbálkozzon újra. |
| ExpiryTimeInThePast | A rendelkezésre álló lejárati idő a múltban történt: {0}. Adjon meg egy jövőbeli lejárati időt UTC-ben, majd próbálkozzon újra. Ha soha nem jár le, állítsa a lejárati időt NULL értékre. |
| Hiányosbeállítások | A diagnosztikai beállítások hiányosak. |
| InputBlobRelativeLocationInvalid | Nincs megadva Azure storage blob név. Adjon meg egy érvényes blobnevet, majd próbálkozzon újra. |
| InvalidBlob | Érvénytelen blob specifikáció blob: {0}. Ellenőrizze, hogy a kapcsolati karakterlánc / relatív elérési út vagy a SAS-token specifikációja helyes-e, és próbálkozzon újra. |
| InvalidBlobConnectionString | Az egyik bemeneti/kimeneti blobhoz megadott kapcsolati {0}karakterlánc érvénytelen: . Javítsa ki ezt, majd próbálkozzon újra. |
| InvalidBlobExtension | A blob {0} hivatkozás: érvénytelen vagy hiányzó fájlkiterjesztéssel rendelkezik. A kimeneti típus támogatott fájlkiterjesztései a következők: "{1}". |
| Érvénytelen bemeneti nevek | A kérelemben megadott érvénytelen szolgáltatásbemeneti név(ek): {0}. Rendelje hozzá a bemeneti adatokat a megfelelő szolgáltatásbemenetekhez, majd próbálkozzon újra. |
| InvalidOutputOverrideName | Érvénytelen kimeneti felülbírálási név: {0}. A szolgáltatás nem rendelkezik ezzel a névvel rendelkező kimeneti csomóddal. Adja meg a megfelelő kimeneti csomópontnevét a felülbíráláshoz (a kis- és nagybetűk megkülönböztetése érvényes). |
| InvalidQueryParameter paraméter | Érvénytelen lekérdezési{0}paraméter ' '. {1} |
| MissingInputBlobInformation | Hiányzó Azure storage blob információk. Adjon meg érvényes kapcsolati karakterláncot és relatív elérési utat vagy URI-t, majd próbálkozzon újra. |
| Hiányzó JobId | Nincs munkaazonosító. A feladatazonosítót a rendszer akkor adja vissza, amikor egy feladatot először küldtek be. Ellenőrizze, hogy helyes-e a feladatazonosító, és próbálkozzon újra. |
| Hiányzó billentyűk | Nincs megadva kulcs, illetve elsődleges vagy másodlagos kulcs nem biztosított. |
| MissingModelPackage csomag | Nincs rendelkezésre álló modellcsomag-azonosító vagy modellcsomag. Adjon meg érvényes modellcsomag-azonosítót vagy modellcsomagot, majd próbálkozzon újra. |
| MissingOutputOverrideSpecification | A kérelemből hiányzik a blob {0}specifikációja a kimenet felülbírálásához. Adjon meg egy érvényes blobhelyet a kérelemmel, vagy távolítsa el a kimeneti specifikációt, ha nincs szükség helyfelülbírálásra. |
| HiányzóKérésbemenet | A webszolgáltatás bemenetet vár, de nem adott meg bemenetet. Győződjön meg arról, hogy az érvényes bemenetek a modellben közzétett bemeneti portok alapján vannak megadva, majd próbálkozzon újra. |
| MissingRequiredGlobalParameters | Nem minden szükséges webszolgáltatás-paraméter(ek) biztosított. Ellenőrizze, hogy a modul(ok)hoz szükséges paraméter(ek) helyesek-e, és próbálkozzon újra. |
| MissingRequiredOutputOverrides | Titkosított szolgáltatásvégpont hívásakor kötelező a kimeneti felülírások átadása a szolgáltatás összes kimenetén. A kimenetek jelenleg hiányzó felülbírálásai:{0} |
| MissingWebServiceGroupId azonosító | Nincs megadva webszolgáltatás-csoportazonosító. Adjon meg érvényes webszolgáltatás-csoportazonosítót, majd próbálkozzon újra. |
| Hiányzó Webszolgáltatás-azonosító | Nincs webszolgáltatás-azonosító. Adjon meg érvényes webszolgáltatás-azonosítót, majd próbálkozzon újra. |
| MissingWebServicePackage csomag | Nincs webszolgáltatás-csomag. Adjon meg egy érvényes webszolgáltatás-csomagot, és próbálkozzon újra. |
| Hiányzó munkaterületazonosító | Nincs megadva munkaterület-azonosító. Adjon meg érvényes munkaterület-azonosítót, majd próbálkozzon újra. |
| ModelConfigurationInvalid | Érvénytelen modellkonfiguráció a modellcsomagban. Győződjön meg arról, hogy a modell konfigurációja tartalmazza a kimeneti végpont(ok) definícióját, az std hibavégpontot és az std out végpontot, majd próbálkozzon újra. |
| ModelPackageIdInvalid | Érvénytelen modellcsomag-azonosító. Ellenőrizze, hogy a modellcsomag-azonosító helyes-e, majd próbálkozzon újra. |
| RequestBodyInvalid | Nincs kérés törzs e vagy hiba deszerializálása a kérelem törzse. |
| RequestIsEmpty (Kérés) | Nincs kérés. Adjon meg érvényes kérelmet, majd próbálkozzon újra. |
| Váratlan paraméter | Nem várt paraméterek biztosítva. Ellenőrizze, hogy az összes paraméternév helyesen van-e beírva, csak a várt paraméterek et adja át, majd próbálkozzon újra. |
| Ismeretlen hiba | Ismeretlen hiba. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | A {0} webszolgáltatás egyidejű kérési követelményei nem módosíthatók. |
| WebServiceIdÉrvénytelen | Érvénytelen webszolgáltatás-azonosító t. A webszolgáltatás-azonosítónak érvényes guid azonosítónak kell lennie. |
| WebServiceTooManyConcurrentRequestRequirement | Az egyidejű kérelemkövetelmény nem {0}állítható be a programnál nagyobb ra. |
| WebServiceTypeInvalid | Érvénytelen webszolgáltatás-típus van megadva. Ellenőrizze, hogy helyes-e az érvényes webszolgáltatás-típus, majd próbálkozzon újra. Érvényes webszolgáltatás-típusok: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP 400-as állapotkód)
 
Érvénytelen felhasználói argumentum van megadva.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| Bemenetimmonmatchhiba | A bemeneti adatok nem egyeznek meg a bemeneti port sémájával. |
| InputParseHiba | A bemeneti vektor elemzése nem sikerült.  Ellenőrizze, hogy a bemeneti vektor megfelelő számú oszlopot és adattípust tartalmaz-e.  További részletek: {0}. |
| MissingRequiredGlobalParameters | A webszolgáltatás által várt paraméter(ek) hiányoznak. Ellenőrizze, hogy a webszolgáltatás által várt összes szükséges paraméter helyes-e, és próbálkozzon újra. |
| Váratlan paraméter | Ellenőrizze, hogy csak a webszolgáltatás által várt szükséges paraméterek kerülnek átadásra, majd próbálkozzon újra. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>Érvénytelen művelet (HTTP-állapotkód 400)
 
A kérelem érvénytelen az aktuális környezetben.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| Nem indítható meg a feladat | A feladat nem indítható {0} el, mert állapotban van. |
| Inkompatibilismodell | A modell nem kompatibilis a kérelem verziójával. A kérelem verziója csak egyetlen adatlapra vonatkozó kimeneti modelleket támogat. |
| Többbemenetnem engedélyezett | A modell nem engedélyez több bemenetet. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-állapotkód 400)
 
A modul végrehajtása belső könyvtárhibát észlelt.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP 400-as állapotkód)
 
A modul végrehajtása hibát észlelt.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP 400-as állapotkód)
 
Érvénytelen webszolgáltatás-csomag. Ellenőrizze, hogy a megadott webszolgáltatás-csomag helyes-e, és próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| FormatError (Formátumhiba) | A webszolgáltatás-csomag hibás. Részletek:{0} |
| Futásidők hiba | A webszolgáltatás-csomag grafikonja érvénytelen. Részletek:{0} |
| Érvényesítési hiba | A webszolgáltatás-csomag grafikonja érvénytelen. Részletek:{0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nem engedélyezett (HTTP-állapotkód 401)
 
A kérelem nem fér hozzá az erőforráshoz.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminRequestJogosulatlan | Nem engedélyezett |
| ManagementRequestJogosulatlan | Nem engedélyezett |
| ScoreRequestJogosulatlan | Érvénytelen hitelesítő adatok at adtak meg. |
 
## <a name="notfound-http-status-code-404"></a>Nem található (HTTP-állapotkód 404)
 
Az erőforrás nem található.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelPackageNem található | A modellcsomag nem található. Ellenőrizze, hogy helyes-e a modellcsomag-azonosító, és próbálkozzon újra. |
| WebServiceIdNotFoundInWorkspace | A munkaterület alatti webszolgáltatás nem található. A webServiceId és a munkaterületazonosító között eltérés van. Ellenőrizze, hogy a nyújtott webszolgáltatás a munkaterület része-e, majd próbálkozzon újra. |
| A WebService Nem található | A webszolgáltatás nem található. Ellenőrizze, hogy helyes-e a webszolgáltatás-azonosító, majd próbálkozzon újra. |
| Munkaterület Nem található | A munkaterület nem található. Ellenőrizze, hogy helyes-e a munkaterület-azonosító, majd próbálkozzon újra. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-állapotkód 408)
 
A művelet nem hajtható végre a megengedett időn belül.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| Kéréstörölve | A kérést az ügyfél visszavonta. |
| ScoreRequestTimeout | A végrehajtási kérelem idővel csökkent. |
 
## <a name="conflict-http-status-code-409"></a>Ütközés (HTTP-állapotkód 409)
 
Az erőforrás már létezik.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Érvénytelen kimeneti paraméternév. Próbálja meg a metaadat-szerkesztő modult használni az oszlopok átnevezéséhez, majd próbálkozzon újra. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-állapotkód: 413)
 
A modell túllépte a hozzá rendelt memóriakvótát.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| OutofMemoryLimit | A modell több memóriát fogyasztott, mint amennyit kisajátultak. A modell maximálisan {0} engedélyezett memóriája MB. Kérjük, ellenőrizze a modell problémákat. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP 500-as állapotkód)
 
A végrehajtás belső hibát észlelt.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminAuthenticationFailed sikertelen |  |
| Háttérvégpontargumenthiba |  |
| BackendBadRequest |  |
| ClusterConfigBlobMiskonfigurálva |  |
| ContainerProcessTerminatedWithSystemError | A tárolófolyamat rendszerhibával összeomlott |
| ContainerProcessTerminatedWithUnknownError | Ismeretlen hibával összeomlott a tárolófolyamat |
| ContainerValidationFailed | A blobtároló érvényesítése {0}nem sikerült a következő hiba miatt: . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError (Kivételdeszerializálás – hiba |  |
| SikertelenGetApiDocument |  |
| Nem sikerültakAwebszolgáltatás |  |
| Érvénytelen memóriakonfiguráció | InvalidMemoryConfiguration, ConfigValue érték:{0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nincs enek érv. Ellenőrizze, hogy az érvényes argumentumok átadnak-e, majd próbálkozzon újra. |
| ModelPackageÉrvénytelen |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port id={0} nem támogatott adattípussal rendelkezik: {1}. |
| Erőforrás letöltése |  |
| Erőforrás-terheléssikertelenül |  |
| ServiceUrisNem található |  |
| SwaggerGeneráció | Swagger generáció nem sikerült, Részletek:{0} |
| Váratlan PontszámÁllapota |  |
| Ismeretlenbackenderrorresponse |  |
| Ismeretlen hiba |  |
| Ismeretlen feladatállapotkód | Ismeretlen feladatállapot-kód {0}. |
| Ismeretlenmodulhiba |  |
| UpdateWebServiceResourceFailed |  |
| WebserviceGroupNem alapítva |  |
| WebServicePackageÉrvénytelen | InvalidWebServicePackage, Részletek:{0} |
| WorkerAuthorizationFailed (MunkavégzőengedélyezéseK Sikertelen) |  |
| Dolgozó nem érhető el |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP 500-as állapotkód)
 
A végrehajtás belső hibát észlelt. Kevés a rendszer memóriája. Próbálkozzon újra.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP 500-as állapotkód)
 
Érvénytelen modellcsomag. Ellenőrizze, hogy a megadott modellcsomag helyes-e, és próbálkozzon újra.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP 500-as állapotkód)
 
Érvénytelen webszolgáltatás-csomag. Ellenőrizze, hogy a megadott webes csomag helyes-e, és próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| Modulhiba | A webszolgáltatás-csomag grafikonja érvénytelen. Részletek:{0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>Inicializálótárolók (HTTP 503-as állapotkód)
 
A kérelem nem hajtható végre, mert a tárolók inicializálásalatt vannak.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>SzolgáltatásNem érhető el (HTTP 503-as állapotkód)
 
A szolgáltatás átmenetileg nem érhető el.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| NoMoreForrások | Nincs elérhető forrás a kéréshez. |
| Kérelem átrokán | A kérelem a végpontra {0} van szabályozva. A végpont maximális egyidejűsége {1}a . |
| TúlsokConcurrentRequests | Túl sok egyidejű kérelem érkezett. |
| TooManyHostsInitialized alatt | Túl sok gazdagép inicializálása van egyszerre. Fontolja meg a szabályozást / újrapróbálkozást. |
| TooManyHostsInitializedPerModel | Túl sok gazdagép inicializálása van egyszerre. Fontolja meg a szabályozást / újrapróbálkozást. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP 504-es állapotkód)
 
A művelet nem hajtható végre a megengedett időn belül.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| HáttérvégpontinicializálásIdő-out | A webszolgáltatás inicializálása nem fejeződhet be a megengedett időn belül. |
| HáttérendScoreTimeout | A webszolgáltatás-kérelem végrehajtása nem hajtható végre a megengedett időn belül. |
 
