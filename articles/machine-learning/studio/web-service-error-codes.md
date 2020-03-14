---
title: REST API hibakódok – Azure Machine Learning Studio (klasszikus) | Microsoft Docs
description: Az ezen hibakódok által az Azure Machine Learning webszolgáltatás-művelet sikerült visszaadni.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217762"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasszikus) REST API hibakódok

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
A következő hibakódokat egy Azure Machine Learning Studio (klasszikus) webszolgáltatás egyik művelete is visszaküldheti.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-állapotkód: 400)
 
Érvénytelen argumentum van megadva.
 
Ez az osztály a hibák azt jelenti, hogy valahol a megadott argumentum érvénytelen volt. Ez lehet egy hitelesítő adatok vagy az Azure storage értékről a web Service átadott helyét. A "Részletek" szakasz "code" (kód) mezőjéből megtudhatja, hogy melyik adott argumentum érvénytelen.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BadParameterValue | A megadott paraméter értéke nem felel meg a paraméter a szabály a paraméter |
| BadSubscriptionId | Előfizetés-azonosítója, amellyel pontozása nem található az erőforrás egy |
| BadVersionCall | Érvénytelen Version paraméter lett átadva az API-hívás során: {0}. Ellenőrizze az API súgóoldalt átadására a megfelelő verziót, és próbálkozzon újra. |
| BatchJobInputsNotSpecified | A kérelemben nem voltak megadva a következő kötelező bevitel (ek): {0}. Ellenőrizze, hogy az összes bemeneti adat van megadva, és próbálkozzon újra. |
| BatchJobInputsTooManySpecified | A kérelem, mint a szolgáltatásban definiált további bemenetei között megadott. Az elfogadott bemenet (ek) listája: {0}. Ellenőrizze, hogy az összes bemeneti adatok helyesen van megadva, és próbálkozzon újra. |
| BlobNameTooLong | A diagnosztikai kimenethez megadott Azure Blob Storage-elérési út túl hosszú: {0}. Lerövidítheti az elérési utat, és próbálkozzon újra. |
| BlobNotFound | Nem érhető el a megadott Azure Blob-{0}.  Azure-hibaüzenet: {1}. |
| ContainerIsEmpty | Nincs az Azure storage-tároló neve lett megadva. Adjon meg egy érvényes tároló nevet, és próbálkozzon újra. |
| ContainerSegmentInvalid | A Tárolónév érvénytelen. Adjon meg egy érvényes tároló nevet, és próbálkozzon újra. |
| ContainerValidationFailed | A blob-tároló érvényesítése a következő hiba miatt sikertelen volt: {0}. |
| DataTypeNotSupported | A megadott adattípus nem támogatott. Adjon meg érvényes adatot típus(ok), és próbálkozzon újra. |
| DuplicateInputInBatchCall | A kötegelt kérelem érvénytelen. Egy- és több bemenet egyszerre nem adható meg. Távolítsa el, ezek az elemek egyikét a kérelemből, majd próbálkozzon újra. |
| ExpiryTimeInThePast | A megadott lejárati idő a múltban: {0}. Adjon meg egy jövőbeli lejárati ideje UTC Formátumban, és próbálkozzon újra. Soha ne járjon le a lejárati idő NULL értékre állítja. |
| IncompleteSettings | Diagnosztikai beállítások nem teljesek. |
| InputBlobRelativeLocationInvalid | Nincs az Azure storage blob megadott név. Adjon meg egy érvényes blob nevet, és próbálkozzon újra. |
| InvalidBlob | Érvénytelen blob-specifikáció a blobhoz: {0}. Győződjön meg arról, hogy a kapcsolati karakterlánc / relatív elérési út vagy SAS-token specifikációnak megfelelő, és próbálkozzon újra. |
| InvalidBlobConnectionString | A bemeneti/kimeneti Blobok egyikéhez megadott, érvénytelen: {0}. A probléma megoldása, és próbálkozzon újra. |
| InvalidBlobExtension | A blob-hivatkozás: a {0} érvénytelen vagy hiányzik a fájlkiterjesztés. A következő kimeneti típushoz támogatott fájlkiterjesztések: "{1}". |
| InvalidInputNames | Érvénytelen a kérésben megadott szolgáltatás-bemeneti név (ek): {0}. A bemeneti adatok leképezése a megfelelő szolgáltatás bemenetei között, és próbálkozzon újra. |
| InvalidOutputOverrideName | A kimeneti felülbírálás neve érvénytelen: {0}. A szolgáltatás nem rendelkezik egy ilyen nevű kimeneti csomópont. Adjon meg egy megfelelő kimeneti csomópontnév felülbírálása (kis-és nagybetűk vonatkozik). |
| InvalidQueryParameter | A (z) "{0}" lekérdezési paraméter érvénytelen. {1} |
| MissingInputBlobInformation | Az Azure storage blob adatainak hiányzik. Adjon meg egy érvényes kapcsolati karakterláncot és a relatív elérési út vagy URI-t, és próbálkozzon újra. |
| MissingJobId | Nincs feladat megadott azonosítója. Egy feladat azonosítóját adja vissza, amikor egy feladat el lett küldve az első alkalommal. Ellenőrizze, hogy a feladat-azonosító helyességéről, majd próbálkozzon újra. |
| MissingKeys | Nincsenek kulcsok a megadott, vagy egy elsődleges vagy másodlagos kulcs nincs megadva. |
| MissingModelPackage | Nincs modell csomag azonosítója és a modell csomag. Adjon meg egy érvényes modellt csomag azonosítója vagy csomag modellezheti, és próbálkozzon újra. |
| MissingOutputOverrideSpecification | A kérelemből hiányzik a blob-specifikáció a kimenet felülbírálásához {0}. Adjon meg egy érvényes blob helyére a kérelemmel, vagy távolítsa el a kimeneti specifikációját, ha nincs hely felülbírálás van szükség. |
| MissingRequestInput | A web service bemenetet vár, de nincs bemenet lett megadva. Ellenőrizze, érvényes biztosított a közzétett bemeneti portok a modell alapján, és próbálkozzon újra. |
| MissingRequiredGlobalParameters | Nem minden kötelező a web service pedig a megadott. Ellenőrizze, hogy helyesek-e a várt a társításhoz tartozó modulok paraméter(ek) használatával történt, és próbálkozzon újra. |
| MissingRequiredOutputOverrides | Kötelező megadni egy titkosított szolgáltatásvégpont hívása során kimenetet a szolgáltatás kimenetek felülbírálásokat. A következő kimeneteken jelenleg nincsenek felülbírálások: {0} |
| MissingWebServiceGroupId | Nincs webes szolgáltatás csoport megadva azonosító. Adjon meg egy érvényes webes szolgáltatás csoport azonosítóját, és próbálkozzon újra. |
| MissingWebServiceId | Nincs webes szolgáltatás megadott azonosító. Adjon meg egy érvényes webszolgáltatás azonosítója, és próbálkozzon újra. |
| MissingWebServicePackage | Nincs webes szolgáltatás csomag megadott. Adjon meg egy érvényes web service-csomag, és próbálkozzon újra. |
| MissingWorkspaceId | Nincs munkaterület-azonosító. Adjon meg egy érvényes munkaterület azonosítóját, és próbálkozzon újra. |
| ModelConfigurationInvalid | A modell csomagban konfigurációja érvénytelen. Győződjön meg arról, a modell konfiguráció tartalmazza a kimeneti végpont definíciójában standard hiba végpont, és a standard végpont, és próbálkozzon újra. |
| ModelPackageIdInvalid | A modell-csomag azonosítója érvénytelen. Ellenőrizze, hogy helyes-e a modell-csomag azonosítója, és próbálkozzon újra. |
| RequestBodyInvalid | Nincs megadva a kérelem törzsében vagy a kéréstörzs deszerializálása során hiba. |
| RequestIsEmpty | Nem biztosított kérelmet. Adjon meg egy érvényes kérelmet, és próbálkozzon újra. |
| UnexpectedParameter | Váratlan paramétereknek. Ellenőrizze, helyesen írja be a minden paraméternévre, csak a várt paraméterek vannak-e átadva, és próbálkozzon újra. |
| UnknownError | Ismeretlen hiba. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | {0} webszolgáltatáshoz tartozó egyidejű kérelmekre vonatkozó követelmények nem módosíthatók. |
| WebServiceIdInvalid | Érvénytelen webszolgáltatás-azonosítót a megadott. Webszolgáltatás-azonosítót érvényes guid lehet. |
| WebServiceTooManyConcurrentRequestRequirement | Az egyidejű kérelmek nem állíthatók be több mint {0}. |
| WebServiceTypeInvalid | Érvénytelen webes szolgáltatás típusa a megadott. Ellenőrizze, hogy az érvényes webes szolgáltatás típus helyességéről, majd próbálkozzon újra. Érvényes webszolgáltatás-típusok: {0}. |
 
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
| CannotStartJob | A feladatot nem lehet elindítani, mert {0} állapotban van. |
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
| OutOfMemoryLimit | A modell több memóriára, a rendszer sajátíthatja használja fel. A modell számára engedélyezett maximális memória {0} MB. Tekintse meg a problémákat a modellt. |
 
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
| ContainerValidationFailed | A blob-tároló érvényesítése a következő hiba miatt sikertelen volt: {0}. |
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
| PortDataTypeNotSupported | A port ID ={0} nem támogatott adattípussal rendelkezik: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | A hencegés létrehozása nem sikerült, részletek: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Ismeretlen a feladathoz tartozó állapotkód {0}. |
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
| RequestThrottled | A kérelmet {0} végpontra vonatkozóan szabályozták. A végpontok maximális párhuzamossága {1}. |
| TooManyConcurrentRequests | Túl sok egyidejű kérelem érkezett. |
| TooManyHostsBeingInitialized | Túl sok gazdagép egyszerre inicializálása közben. Fontolja meg a szabályozás / újrapróbálkozás. |
| TooManyHostsBeingInitializedPerModel | Túl sok gazdagép egyszerre inicializálása közben. Fontolja meg a szabályozás / újrapróbálkozás. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-állapotkód: 504)
 
A műveletet nem sikerült a rendelkezésre álló időn belül befejezni.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BackendInitializationTimeout | A webes szolgáltatás inicializálása nem fejeződött be a rendelkezésre álló időn belül. |
| BackendScoreTimeout | A web service kérelem végrehajtása nem sikerült a rendelkezésre álló időn belül. |
 
