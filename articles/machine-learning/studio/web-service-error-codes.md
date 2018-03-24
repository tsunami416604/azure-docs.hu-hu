---
title: Az Azure Machine Learning REST API-hibakódok |} Microsoft Docs
description: Az ezen hibakódok által az Azure Machine Learning webszolgáltatás művelet sikerült visszaadni.
keywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 0ba44b2a93bcd542db1350def2d0554c8c44233c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="machine-learning-rest-api-error-codes"></a>Gépi tanulási REST API-hibakódok
 
A következő hibakódok visszatérő kiszolgálón az Azure Machine Learning webszolgáltatás művelet.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-állapotkód: 400)
 
Érvénytelen argumentum lett megadva.
 
Ez az osztály a hiba azt jelenti, hogy valahol megadott argumentum érvénytelen volt. Ennek oka az lehet, a hitelesítő adatok vagy egy webkiszolgáló átadott Azure tárolási helyét. Tekintse meg a hiba a "kód" mező "a Részletek" szakaszban diagnosztizálásához mely megadott argumentum érvénytelen volt.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BadParameterValue | A megadott paraméter értéke nem felel meg a paraméter a szabály a paraméter |
| BadSubscriptionId | Az előfizetési azonosító, amellyel pontozása nincs jelen az erőforrás egy |
| BadVersionCall | Érvénytelen verzió paramétert API-hívása során: {0}. Ellenőrizze a API súgólapján átadja a megfelelő verzióra, és próbálkozzon újra. |
| BatchJobInputsNotSpecified | A következő kötelező input(s) nem voltak megadva a kérelemhez: {0}. Ellenőrizze, hogy az összes bemeneti adatok van megadva, és próbálkozzon újra. |
| BatchJobInputsTooManySpecified | A kérelemben megadott további bemenetek, mint a szolgáltatásban meghatározott. Elfogadott input(s) listája: {0}. Ellenőrizze, hogy az összes bemeneti adatok helyesen van megadva, és próbálkozzon újra. |
| BlobNameTooLong | A diagnosztikai kimenetet túl hosszú a megadott Azure-blobot. tárolási elérési útja: {0}. Rövidítse le a elérési utat, és próbálkozzon újra. |
| BlobNotFound | Nem lehet hozzáférni a megadott Azure blob - {0}.  Azure-hibaüzenet: {1}. |
| ContainerIsEmpty | Nincs az Azure storage-tároló neve lett megadva. Adjon meg egy érvényes tároló nevet, és próbálkozzon újra. |
| ContainerSegmentInvalid | Érvénytelen a tároló nevét. Adjon meg egy érvényes tároló nevet, és próbálkozzon újra. |
| ContainerValidationFailed | BLOB-tároló érvényesítése sikertelen, hiba: {0}. |
| DataTypeNotSupported | A megadott adattípus nem támogatott. Adjon meg érvényes adattípus(ok), majd próbálja meg újból. |
| DuplicateInputInBatchCall | A kötegelt kérelem érvénytelen. Egy- és több bemeneti nem adható meg egyszerre. Az elem eltávolítása a kérelmet, és próbálkozzon újra. |
| ExpiryTimeInThePast | A múltban van megadva lejárati idő: {0}. Adjon meg egy jövőbeli lejárati időpontja (UTC), majd próbálja meg újból. Soha le nem járó, állítson be NULL lejárati idő. |
| IncompleteSettings | Diagnosztikai beállítások nem teljesek. |
| InputBlobRelativeLocationInvalid | Nincs az Azure storage blob megadott név. Adjon meg egy érvényes blob nevet, és próbálkozzon újra. |
| InvalidBlob | Érvénytelen blob specification BLOB: {0}. Ellenőrizze a kapcsolati karakterlánc / relatív elérési út vagy SAS-token specification helyes-e, és próbálkozzon újra. |
| InvalidBlobConnectionString | Egy érvénytelen bemeneti/kimeneti blobok megadott kapcsolódási karakterlánc: {0}. Ennek javításához, és próbálkozzon újra. |
| InvalidBlobExtension | A blokkblob hivatkozását: {0} érvénytelen vagy hiányzó fájl kiterjesztése. Támogatott fájltípusok a kimeneti típus: "{1}". |
| InvalidInputNames | Érvénytelen bemeneti a kérelemben megadott neve: {0}. A bemeneti adatok leképezése a megfelelő bemenetei, és próbálkozzon újra. |
| InvalidOutputOverrideName | Érvénytelen kimeneti felülbírálása név: {0}. A szolgáltatás nem rendelkezik egy ilyen nevű kimeneti csomópont. Adjon meg egy megfelelő kimeneti csomópontnév felülbírálására (kis-és nagybetűk vonatkozik). |
| InvalidQueryParameter | Érvénytelen a lekérdezési paraméter a(z) "{0}". {1} |
| MissingInputBlobInformation | Az Azure storage-blob adatok hiányoznak. Adjon meg egy érvényes kapcsolati karakterláncot és a relatív elérési út vagy URI Azonosítóját, és próbálkozzon újra. |
| MissingJobId | Egyetlen feladat sem a megadott azonosító. Egy feladat azonosító érték érkezett vissza egy feladatot az első alkalommal lett küldve. Győződjön meg a feladat azonosító helyességéről, majd próbálkozzon újra. |
| MissingKeys | Nincsenek kulcsok megadott, vagy egy elsődleges vagy másodlagos kulcs nincs megadva. |
| MissingModelPackage | Nincs modell csomagazonosító vagy a modell csomag megadott. Adjon meg egy érvényes modell csomagazonosító vagy csomag modell, és próbálkozzon újra. |
| MissingOutputOverrideSpecification | A kérelemhez nincs megadva a blob megadását kimeneti felülbírálás {0}. Adjon meg egy érvényes blob helyére a kéréshez, vagy távolítsa el a kimeneti megadott, ha nincs hely felülbírálás van szükség. |
| MissingRequestInput | A webszolgáltatás bemenete vár, de nincs bemenet lett megadva. Ellenőrizze, érvényes bemeneti adatok találhatók a közzétett bemeneti portok a modell alapján, és próbálja meg újból. |
| MissingRequiredGlobalParameters | Nem minden kötelező web service paraméter(ek) megadott. Ellenőrizze, hogy a várt, de a modul(ok) paraméterek helyesek, és próbálkozzon újra. |
| MissingRequiredOutputOverrides | Kötelező adjon át egy titkosított szolgáltatásvégpont meghívásakor kimeneti felülbírálásai a szolgáltatás kimenetek. Hiányzik a kimenetek most felülbírálások: {0} |
| MissingWebServiceGroupId | A megadott azonosító nem webes szolgáltatási csoport. Adjon meg egy érvényes web service csoport azonosítója, és próbálkozzon újra. |
| MissingWebServiceId | Nincs webes szolgáltatás a megadott azonosító. Adjon meg egy érvényes webszolgáltatás azonosítója, és próbálkozzon újra. |
| MissingWebServicePackage | Nincs web Service csomag megadott. Adjon meg egy érvényes web service-csomag, és próbálkozzon újra. |
| MissingWorkspaceId | Nincs munkaterület megadott azonosító. Adjon meg egy érvényes munkaterület azonosítója, és próbálkozzon újra. |
| ModelConfigurationInvalid | A modell csomagban konfigurációs modell érvénytelen. Győződjön meg arról, a modell konfigurációs kimeneti végpont definícióját, szabványos hiba végpont, tartalmazza, és szabványos végpont ki, és próbálkozzon újra. |
| ModelPackageIdInvalid | Érvénytelen modell csomag azonosítóját. Győződjön meg arról, hogy a modell csomagazonosító megfelelő, és próbálkozzon újra. |
| RequestBodyInvalid | Nincs kérés törzsében megadott vagy hiba történt a kérelem törzsének deszerializálása során. |
| RequestIsEmpty | Kérelem nem biztosított. Adjon meg egy érvényes kérelmet, és próbálkozzon újra. |
| UnexpectedParameter | Nem várt paraméterek megadva. Ellenőrizze a paraméternevek helyesen írta-e, csak a várt paraméterek át lettek adva, majd próbálkozzon újra. |
| UnknownError | Ismeretlen hiba. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nem módosítható az egyidejű kérelmek követelmények {0} webszolgáltatáshoz. |
| WebServiceIdInvalid | Érvénytelen webszolgáltatás megadott felügyeletiszolgáltatás-azonosító. Webes szolgáltatás azonosítója érvényes guid-nak kell lennie. |
| WebServiceTooManyConcurrentRequestRequirement | Nem állítható be az egyidejűleg futtatható kérelmek követelmény nagyobb {0}. |
| WebServiceTypeInvalid | Érvénytelen webszolgáltatás szolgáltatástípus. Győződjön meg arról a érvényes webes szolgáltatás típusának megfelelő, és próbálkozzon újra. Érvényes webes szolgáltatás típusa: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-állapotkód: 400)
 
Érvénytelen a megadott argumentum.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| InputMismatchError | A bemeneti adat nem felel meg a bemeneti porthoz séma. |
| InputParseError | Nem sikerült a bemeneti vektoros elemzése.  Ellenőrizze a bemeneti vektor rendelkezik a megfelelő számú oszlopot és adattípusokat.  További részletek: {0}. |
| MissingRequiredGlobalParameters | A webszolgáltatás által várt paraméterek hiányoznak. Ellenőrizze, hogy a webszolgáltatás által várt szükséges paraméterek helyesek, és próbálkozzon újra. |
| UnexpectedParameter | Ellenőrizze, csak a webszolgáltatás által várt szükséges paraméterek át lettek adva, majd próbálja újra. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-állapotkód: 400)
 
A kérés nem érvényes az aktuális környezetben.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| CannotStartJob | A feladat nem indítható el, mert {0} állapotban van. |
| IncompatibleModel | A modell nem kompatibilis a kérelem-verzióját. A kérelem verzióval csak egyetlen datatable kimeneti modellek. |
| MultipleInputsNotAllowed | A modell nem teszi lehetővé több bemeneti adatok. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-állapotkód: 400)
 
Modul végrehajtási belső könyvtár hibába ütközött.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-állapotkód: 400)
 
Modul végrehajtási hibát észlelt.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-állapotkód: 400)
 
Érvénytelen webszolgáltatás service-csomag. Győződjön meg a webes szolgáltatás csomag megadott helyességéről, majd próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| FormatError | A webes szolgáltatás csomag rosszul megformázva. Részletek: {0} |
| RuntimesError | A web service csomag graph érvénytelen. Részletek: {0} |
| ValidationError | A web service csomag graph érvénytelen. Részletek: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Jogosulatlan (HTTP-állapotkód 401-es)
 
Kérelme, mert jogosulatlan hozzáférés erőforráshoz.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminRequestUnauthorized | Nem engedélyezett |
| ManagementRequestUnauthorized | Nem engedélyezett |
| ScoreRequestUnauthorized | Érvénytelen hitelesítő adatok. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-állapotkód: 404)
 
Az erőforrás nem található.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelPackageNotFound | A csomag nem található modell. Győződjön meg a modell csomagazonosító helyességéről, majd próbálkozzon újra. |
| WebServiceIdNotFoundInWorkspace | A webszolgáltatás nem található a munkaterület alatt. A webServiceId és a workspaceId között eltérés tapasztalható. Ellenőrizze, hogy a megadott webes szolgáltatás része a munkaterületet, és próbálkozzon újra. |
| WebServiceNotFound | A webszolgáltatás nem található. Győződjön meg a webszolgáltatás azonosító helyességéről, majd próbálkozzon újra. |
| WorkspaceNotFound | A munkaterület nem található. Ellenőrizze, hogy a munkaterület-azonosító helyességéről, majd próbálja újra. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-állapotkód 408)
 
A műveletet nem sikerült a rendelkezésre álló időn belül befejezni.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| RequestCanceled | Az ügyfél megszakította a kérelem. |
| ScoreRequestTimeout | Végrehajtási kérelem túllépte az időkorlátot. |
 
## <a name="conflict-http-status-code-409"></a>Ütközés (HTTP-állapotkód 409)
 
Erőforrás már létezik.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Érvénytelen kimeneti paraméter neve. Próbálja meg átnevezni az oszlopot, majd próbálja meg ismét a metaadatok modul használatával. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-állapotkód: 413)
 
A modell nagyobb volt rendelve memóriakvótáját.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| OutOfMemoryLimit | A modell több memóriára hozzá lett sajátíthatja felhasználja. A modell engedélyezett maximális memória {0} MB. Ellenőrizze, hogy a modell problémák. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-állapotkód: 500)
 
Végrehajtási belső hibát észlelt.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | A rendszer hiba miatt összeomlott tároló folyamat |
| ContainerProcessTerminatedWithUnknownError | Ismeretlen hiba miatt összeomlott a tároló folyamat |
| ContainerValidationFailed | BLOB-tároló érvényesítése sikertelen, hiba: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | A megadott argumentumok. Győződjön meg arról, hogy érvényes argumentumok lettek adva, majd próbálja meg újból. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port azonosítója = {0} még nem támogatott adattípusú: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | A swagger létrehozása sikertelen, a részletek: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Ismeretlen feladat állapotkód: {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, részletek: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-állapotkód: 500)
 
Végrehajtási belső hibát észlelt. A rendszer kevés a memória. Kérjük, próbálkozzon újból.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-állapotkód: 500)
 
Érvénytelen modell csomag. Ellenőrizze a megadott modell csomag helyességét, és próbálkozzon újra.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-állapotkód: 500)
 
Érvénytelen webszolgáltatás service-csomag. Ellenőrizze a megadott webes csomag helyességét, és próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModuleError | A web service csomag graph érvénytelen. Részletek: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-állapotkód 503-as)
 
A kérelem nem hajtható végre, mert a tárolók inicializálás alatt.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-állapotkód 503-as)
 
Szolgáltatás átmenetileg nem érhető el.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| NoMoreResources | Erőforrások nem érhetők el a kérelmet. |
| RequestThrottled | Kérelem {0} végpont lett csökkentve. A végpont a maximális feldolgozási {1}. |
| TooManyConcurrentRequests | Túl sok egyidejű kérés küldése. |
| TooManyHostsBeingInitialized | Túl sok állomások egyszerre inicializálása. Fontolja meg a sávszélesség-szabályozás / újrapróbálkozás. |
| TooManyHostsBeingInitializedPerModel | Túl sok állomások egyszerre inicializálása. Fontolja meg a sávszélesség-szabályozás / újrapróbálkozás. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-állapotkód: 504)
 
A műveletet nem sikerült a rendelkezésre álló időn belül befejezni.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BackendInitializationTimeout | A webes szolgáltatás inicializálása nem sikerült a rendelkezésre álló időn belül. |
| BackendScoreTimeout | A webes szolgáltatás kérelem végrehajtása nem sikerült a rendelkezésre álló időn belül. |
 
