---
title: Hibakódok REST API – Azure Machine Learning Studióban |} A Microsoft Docs
description: Az ezen hibakódok által az Azure Machine Learning webszolgáltatás-művelet sikerült visszaadni.
keywords: ''
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: b7f27e981ceb12191e58a3e0824c7e0709a49013
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251322"
---
# <a name="azure-machine-learning-studio-rest-api-error-codes"></a>Az Azure Machine Learning Studio REST API-t hibakódjai
 
Az alábbi hibakódok által az Azure Machine Learning Studio webszolgáltatás-művelet sikerült visszaadni.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-állapotkód: 400)
 
Érvénytelen argumentum van megadva.
 
Ez az osztály a hibák azt jelenti, hogy valahol a megadott argumentum érvénytelen volt. Ez lehet egy hitelesítő adatok vagy az Azure storage értékről a web Service átadott helyét. Tekintse meg a hiba a "code" mező a "details" szakaszban diagnosztizálásához mely adott argumentum érvénytelen volt.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BadParameterValue | A megadott paraméter értéke nem felel meg a paraméter a szabály a paraméter |
| BadSubscriptionId | Előfizetés-azonosítója, amellyel pontozása nem található az erőforrás egy |
| BadVersionCall | Neplatná verze paraméter lett átadva az API-hívás közben: {0}. Ellenőrizze az API súgóoldalt átadására a megfelelő verziót, és próbálkozzon újra. |
| BatchJobInputsNotSpecified | A következő szükséges input(s) nem voltak megadva a kérelem: {0}. Ellenőrizze, hogy az összes bemeneti adat van megadva, és próbálkozzon újra. |
| BatchJobInputsTooManySpecified | A kérelem, mint a szolgáltatásban definiált további bemenetei között megadott. Elfogadott input(s) listája: {0}. Ellenőrizze, hogy az összes bemeneti adatok helyesen van megadva, és próbálkozzon újra. |
| BlobNameTooLong | Az Azure blob storage elérési utat a diagnosztikai kimenetet érték túl hosszú: {0}. Lerövidítheti az elérési utat, és próbálkozzon újra. |
| BlobNotFound | Nem érhető el a megadott Azure blob - {0}.  Azure-hibaüzenet: {1}. |
| ContainerIsEmpty | Nincs az Azure storage-tároló neve lett megadva. Adjon meg egy érvényes tároló nevet, és próbálkozzon újra. |
| ContainerSegmentInvalid | A Tárolónév érvénytelen. Adjon meg egy érvényes tároló nevet, és próbálkozzon újra. |
| ContainerValidationFailed | BLOB-tároló érvényesítése sikertelen volt, hiba: {0}. |
| DataTypeNotSupported | A megadott adattípus nem támogatott. Adjon meg érvényes adatot típus(ok), és próbálkozzon újra. |
| DuplicateInputInBatchCall | A kötegelt kérelem érvénytelen. Egy- és több bemenet egyszerre nem adható meg. Távolítsa el, ezek az elemek egyikét a kérelemből, majd próbálkozzon újra. |
| ExpiryTimeInThePast | Megadott lejárati időpont egy múltbeli időpont: {0}. Adjon meg egy jövőbeli lejárati ideje UTC Formátumban, és próbálkozzon újra. Soha ne járjon le a lejárati idő NULL értékre állítja. |
| IncompleteSettings | Diagnosztikai beállítások nem teljesek. |
| InputBlobRelativeLocationInvalid | Nincs az Azure storage blob megadott név. Adjon meg egy érvényes blob nevet, és próbálkozzon újra. |
| InvalidBlob | Érvénytelen blob-specifikációt blob: {0}. Győződjön meg arról, hogy a kapcsolati karakterlánc / relatív elérési út vagy SAS-token specifikációnak megfelelő, és próbálkozzon újra. |
| InvalidBlobConnectionString | Érvénytelen a bemeneti/kimeneti blobok egyik kapcsolati karakterlánca: {0}. A probléma megoldása, és próbálkozzon újra. |
| InvalidBlobExtension | A blob-hivatkozás: {0} érvénytelen vagy hiányzó fájl kiterjesztése. Támogatott fájltípusok a kimeneti típus: "{1}". |
| InvalidInputNames | Érvénytelen szolgáltatási bemeneti a kérelemben megadott neve(i): {0}. A bemeneti adatok leképezése a megfelelő szolgáltatás bemenetei között, és próbálkozzon újra. |
| InvalidOutputOverrideName | Érvénytelen kimeneti név felülbírálása: {0}. A szolgáltatás nem rendelkezik egy ilyen nevű kimeneti csomópont. Adjon meg egy megfelelő kimeneti csomópontnév felülbírálása (kis-és nagybetűk vonatkozik). |
| InvalidQueryParameter | Érvénytelen a lekérdezési paraméter "{0}". {1} |
| MissingInputBlobInformation | Az Azure storage blob adatainak hiányzik. Adjon meg egy érvényes kapcsolati karakterláncot és a relatív elérési út vagy URI-t, és próbálkozzon újra. |
| MissingJobId | Nincs feladat megadott azonosítója. Egy feladat azonosítóját adja vissza, amikor egy feladat el lett küldve az első alkalommal. Ellenőrizze, hogy a feladat-azonosító helyességéről, majd próbálkozzon újra. |
| MissingKeys | Nincsenek kulcsok a megadott, vagy egy elsődleges vagy másodlagos kulcs nincs megadva. |
| MissingModelPackage | Nincs modell csomag azonosítója és a modell csomag. Adjon meg egy érvényes modellt csomag azonosítója vagy csomag modellezheti, és próbálkozzon újra. |
| MissingOutputOverrideSpecification | A kérelem nem tartalmaz a blob-specifikációt kimeneti felülbírálás {0}. Adjon meg egy érvényes blob helyére a kérelemmel, vagy távolítsa el a kimeneti specifikációját, ha nincs hely felülbírálás van szükség. |
| MissingRequestInput | A web service bemenetet vár, de nincs bemenet lett megadva. Ellenőrizze, érvényes biztosított a közzétett bemeneti portok a modell alapján, és próbálkozzon újra. |
| MissingRequiredGlobalParameters | Nem minden kötelező a web service pedig a megadott. Ellenőrizze, hogy helyesek-e a várt a társításhoz tartozó modulok paraméter(ek) használatával történt, és próbálkozzon újra. |
| MissingRequiredOutputOverrides | Kötelező megadni egy titkosított szolgáltatásvégpont hívása során kimenetet a szolgáltatás kimenetek felülbírálásokat. Hiányzó felülbírálások most ezeket a kimeneteket: {0} |
| MissingWebServiceGroupId | Nincs webes szolgáltatás csoport megadva azonosító. Adjon meg egy érvényes webes szolgáltatás csoport azonosítóját, és próbálkozzon újra. |
| MissingWebServiceId | Nincs webes szolgáltatás megadott azonosító. Adjon meg egy érvényes webszolgáltatás azonosítója, és próbálkozzon újra. |
| MissingWebServicePackage | Nincs webes szolgáltatás csomag megadott. Adjon meg egy érvényes web service-csomag, és próbálkozzon újra. |
| MissingWorkspaceId | Nincs munkaterület-azonosító. Adjon meg egy érvényes munkaterület azonosítóját, és próbálkozzon újra. |
| ModelConfigurationInvalid | A modell csomagban konfigurációja érvénytelen. Győződjön meg arról, a modell konfiguráció tartalmazza a kimeneti végpont definíciójában standard hiba végpont, és a standard végpont, és próbálkozzon újra. |
| ModelPackageIdInvalid | Érvénytelen modell csomag azonosítója. Ellenőrizze, hogy a modell csomag azonosítója helyes-e, és próbálkozzon újra. |
| RequestBodyInvalid | Nincs megadva a kérelem törzsében vagy a kéréstörzs deszerializálása során hiba. |
| RequestIsEmpty | Nem biztosított kérelmet. Adjon meg egy érvényes kérelmet, és próbálkozzon újra. |
| UnexpectedParameter | Váratlan paramétereknek. Ellenőrizze, helyesen írja be a minden paraméternévre, csak a várt paraméterek vannak-e átadva, és próbálkozzon újra. |
| UnknownError | Ismeretlen hiba. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nem tudja módosítani az egyidejű kérelmek követelményei {0} webes szolgáltatás. |
| WebServiceIdInvalid | Érvénytelen webszolgáltatás-azonosítót a megadott. Webszolgáltatás-azonosítót érvényes guid lehet. |
| WebServiceTooManyConcurrentRequestRequirement | Nem állítható be a párhuzamos kérés követelmény több mint {0}. |
| WebServiceTypeInvalid | Érvénytelen webes szolgáltatás típusa a megadott. Ellenőrizze, hogy az érvényes webes szolgáltatás típus helyességéről, majd próbálkozzon újra. Érvénytelen a webes szolgáltatástípusok: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-állapotkód: 400)
 
Érvénytelen felhasználó a megadott argumentum.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| InputMismatchError | Bemeneti adatok nem egyezik a bemeneti portját séma. |
| InputParseError | Nem sikerült a bemeneti vektor elemzése.  Ellenőrizze a bemeneti vektor rendelkezik a megfelelő számú oszlopai és adattípusai.  További részletek: {0}. |
| MissingRequiredGlobalParameters | A webszolgáltatás által várt paraméterek hiányoznak. Ellenőrizze, hogy a webszolgáltatás által várt szükséges paraméterek helyesek, és próbálkozzon újra. |
| UnexpectedParameter | Győződjön meg arról, csak a szükséges paramétereket a webszolgáltatás által várt lesznek átadva, és próbálkozzon újra. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-állapotkód: 400)
 
A kérés nem érvényes az aktuális környezetben.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| CannotStartJob | A feladat nem indítható el, mert {0} állapota. |
| IncompatibleModel | A modell nem kompatibilis a kérelem verziójával. A kérelem verziója csak egyetlen datatable kimeneti modellek támogatja. |
| MultipleInputsNotAllowed | A modell nem engedélyezi több bemenet. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-állapotkód: 400)
 
Modul végrehajtása egy belső könyvtár hibába ütközött.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-állapotkód: 400)
 
Modul végrehajtási hibát észlelt.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-állapotkód: 400)
 
Érvénytelen web service-csomag. Ellenőrizze a megadott web service-csomag helyességét, és próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| FormatError | A web service-csomag formátuma hibás. Részletek: {0} |
| RuntimesError | A web service csomag graph je neplatná. Részletek: {0} |
| ValidationError | A web service csomag graph je neplatná. Részletek: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nem engedélyezett (HTTP 401-es állapotkód)
 
Kérelem nincs hitelesítve a hozzáférés-erőforráshoz.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminRequestUnauthorized | Nem engedélyezett |
| ManagementRequestUnauthorized | Nem engedélyezett |
| ScoreRequestUnauthorized | A megadott érvénytelen hitelesítő adatok. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-állapotkód: 404)
 
Az erőforrás nem található.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelPackageNotFound | Modell-csomag nem található. Ellenőrizze, hogy a modell csomag azonosító helyességéről, majd próbálkozzon újra. |
| WebServiceIdNotFoundInWorkspace | Webszolgáltatás alatt ez a munkaterület nem található. Nincs a webServiceId és a munkaterület azonosítója nem egyeznek. Ellenőrizze, hogy a megadott webes szolgáltatás része a munkaterületet, és próbálkozzon újra. |
| WebServiceNotFound | A webszolgáltatás nem található. Ellenőrizze, hogy a webszolgáltatás azonosító helyességéről, majd próbálkozzon újra. |
| WorkspaceNotFound | A munkaterület nem található. Ellenőrizze, hogy a munkaterület-azonosító helyességéről, majd próbálkozzon újra. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-állapotkódot 408)
 
A műveletet nem sikerült a rendelkezésre álló időn belül befejezni.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| RequestCanceled | Kérelem az ügyfél megszakította. |
| ScoreRequestTimeout | Végrehajtási kérelem túllépte az időkorlátot. |
 
## <a name="conflict-http-status-code-409"></a>Ütközés (HTTP-állapotkód: 409)
 
Erőforrás már létezik.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Érvénytelen kimeneti paraméter neve. Próbálja meg a metaadatok szerkesztő modul használatával nevezze át az oszlopokat, és próbálkozzon újra. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-állapotkód: 413)
 
A modell nagyobb volt hozzárendelve memóriakvótáját.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| OutOfMemoryLimit | A modell több memóriára, a rendszer sajátíthatja használja fel. A maximális megengedett memória a modell {0} MB. Tekintse meg a problémákat a modellt. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-állapotkód: 500)
 
Végrehajtási belső hibába ütközött.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | A rendszer hiba miatt összeomlott tárolófolyamat |
| ContainerProcessTerminatedWithUnknownError | Ismeretlen hiba miatt összeomlott a tárolófolyamat |
| ContainerValidationFailed | BLOB-tároló érvényesítése sikertelen volt, hiba: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nincsenek megadva argumentumok. Győződjön meg arról, hogy érvényes argumentumok lesznek átadva, és próbálkozzon újra. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Portazonosító ={0} nem támogatott adattípusú: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger létrehozása nem sikerült, részletek: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Ismeretlen feladat állapotkód {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, részletek: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-állapotkód: 500)
 
Végrehajtási belső hibába ütközött. A rendszer kevés a memória. Kérjük, próbálkozzon újból.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-állapotkód: 500)
 
Érvénytelen a csomag. Ellenőrizze a megadott modell csomag helyességét, és próbálkozzon újra.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-állapotkód: 500)
 
Érvénytelen web service-csomag. Ellenőrizze a megadott webes csomag helyességét, és próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModuleError | A web service csomag graph je neplatná. Részletek: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-állapotkód: 503)
 
A kérelem nem hajtható végre, mint a tárolók inicializálása közben.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-állapotkód: 503)
 
Szolgáltatás átmenetileg nem érhető el.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| NoMoreResources | Erőforrások nem érhetők el a kérelmet. |
| RequestThrottled | A kérelem szabályozta {0} végpont. A végpont a maximális egyidejűség {1}. |
| TooManyConcurrentRequests | Túl sok egyidejű kérelem érkezett. |
| TooManyHostsBeingInitialized | Túl sok gazdagép egyszerre inicializálása közben. Fontolja meg a szabályozás / újrapróbálkozás. |
| TooManyHostsBeingInitializedPerModel | Túl sok gazdagép egyszerre inicializálása közben. Fontolja meg a szabályozás / újrapróbálkozás. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-állapotkód: 504)
 
A műveletet nem sikerült a rendelkezésre álló időn belül befejezni.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BackendInitializationTimeout | A webes szolgáltatás inicializálása nem fejeződött be a rendelkezésre álló időn belül. |
| BackendScoreTimeout | A web service kérelem végrehajtása nem sikerült a rendelkezésre álló időn belül. |
 
