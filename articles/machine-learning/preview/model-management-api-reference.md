---
title: "Docker-lemezkép létrehozása az Azure Machine Learning modell kezelése |} Microsoft Docs"
description: "Ez a cikk a webes szolgáltatás Docker lemezkép létrehozása lépéseit ismerteti."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 1939a18fbaf0aed0f62ac9e7641b9901ec4762ea
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Az Azure Machine Learning modell felügyeleti fiók API-referencia

A telepítési környezet beállításával kapcsolatos információkért lásd: [modell kezelése a fiókok telepítése](model-management-configuration.md).

Az Azure Machine Learning modell felügyeleti fiók API valósítja meg a következő műveleteket:

- Modell regisztrációs
- Jegyzékfájl létrehozása
- Docker lemezkép létrehozása
- Webes szolgáltatás létrehozása

Ez a rendszerkép használatával hozzon létre egy webszolgáltatás-bővítmény vagy helyileg vagy a távoli Azure Tárolószolgáltatás-fürt vagy egy másik Docker-val támogatott környezetben az Ön által választott.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a telepítés lépéseit a megtettünk mindent a [telepítse, és hozzon létre a gyors üzembe helyezés](quickstart-installation.md) dokumentum.

A következőkre szükség, a folytatás előtt:
1. Modellhez tartozó felügyeleti fiók kiépítése
2. Központi telepítésére és felügyeletére modellek környezet létrehozása
3. A gépi tanulási modell

### <a name="azure-ad-token"></a>Az Azure AD-jogkivonat
Ha az Azure CLI használata esetén jelentkezzen be `az login`. A CLI-t használja az Azure Active Directory (Azure AD-) token .azure fájlból. Ha az API-kat használni kívánt, akkor a következő beállításokat.

##### <a name="acquire-the-azure-ad-token-manually"></a>Szerezzen be manuálisan az Azure AD-jogkivonat
Használhat `az login` és a legújabb-token beszerzése a kezdőkönyvtár .azure fájlból.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Az Azure AD-jogkivonat megszerzése programozott módon
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Az egyszerű szolgáltatás létrehozása után mentse a kimeneti. Most már használhatja, amely egy token lekérni az Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
A jogkivonat az authorization fejlécet az API-hívásokhoz kerül.


## <a name="register-a-model"></a>A modell regisztrálása

A modell regisztrációs lépésében létrehozott Azure modell felügyeleti fiókot a gépi tanulási modell regisztrálja. Ez a regisztráció lehetővé teszi, hogy nyomon kövesse a modellek és a regisztrációs időpontjában rendelt verzióját. A felhasználó megadja a modell neve. Későbbi regisztrációs modellekhez változatlan néven hoz létre egy új verziója és az azonosítót.

### <a name="request"></a>Kérés

| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| POST |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modellek 
 |
### <a name="description"></a>Leírás
Regisztrálja a modell.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| Modell | Törzs | A modell regisztrálásához használt adattartalmában. | Igen | [Modell](#model) |


### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | OKÉ. A modell regisztrálása sikeres volt. | [Modell](#model) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Lekérdezési fiók a modellek listája
### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modellek 
 |
### <a name="description"></a>Leírás
Lekérdezi a fiók a modellek listája. Az eredménylista címke és név szerint szűrhetők. Ha nincs szűrő fogad el, a lekérdezés felsorolja a fiók összes modellt. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| név | lekérdezés | Objektum neve. | Nem | Karakterlánc |
| Címke | lekérdezés | Minta címke. | Nem | Karakterlánc |
| Száma | lekérdezés | Lapon beolvasása elemek száma. | Nem | Karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedModelList](#paginatedmodellist) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Részletek a modell
### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /models/ {azonosító}  
 |

### <a name="description"></a>Leírás
Lekérdezi a modell azonosítóját.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Objektum azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Modell](#model) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>A regisztrált modellhez és az összes függősége a jegyzékfájl regisztrálása

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| POST |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / jelentkezni | 

### <a name="description"></a>Leírás
A jegyzék regisztrálja a regisztrált modellhez és annak függőségeit.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| manifestRequest | Törzs | Amellyel egy jegyzékfájl regisztrálása hasznos. | Igen | [Manifest](#manifest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Manifest a regisztráció sikeres volt. | [Manifest](#manifest) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>A lekérdezés a jegyzékfájlokat az fiók listája

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / jelentkezni | 

### <a name="description"></a>Leírás
Lekérdezi a jegyzékfájlokat az fiók listáját. Az eredménylista modell-azonosító szerinti szűrése, és a jegyzékfájl neve. Ha nincs szűrő fogad el, a lekérdezés felsorolja az összes a jegyzékfájlokat az a fiók. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| modelId | lekérdezés | Forrásmodell azonosítóját. | Nem | Karakterlánc |
| ManifestName | lekérdezés | A jegyzékfájl neve. | Nem | Karakterlánc |
| Száma | lekérdezés | Lapon beolvasása elemek száma. | Nem | Karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedManifestList](#paginatedmanifestlist) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Részletek a jegyzék

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /manifests/ {azonosító} | 

### <a name="description"></a>Leírás
Lekérdezi a jegyzékfájl által azonosítóját.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Objektum azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Manifest](#manifest) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Lemezkép létrehozása

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| POST |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / lemezképek | 

### <a name="description"></a>Leírás
Lemezkép létrehozása az Azure-tároló beállításjegyzék Docker lemezképként.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| imageRequest | Törzs | A képfájl létrehozásához használt tartalom. | Igen | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. GET hívást megtudhatja, a lemezkép-készítési feladat állapotát. | A művelet-helye |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Egy fiók képek listájának lekérdezése

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / lemezképek | 

### <a name="description"></a>Leírás
Egy fiók képek listájának lekérdezése. Az eredménylista jegyzék azonosító és név szerint szűrhetők. Ha nincs szűrő fogad el, a lekérdezés felsorolja az a fiók a lemezképeket. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| Jegyzékfájl azonosítója | lekérdezés | Manifest azonosítóját. | Nem | Karakterlánc |
| ManifestName | lekérdezés | A jegyzékfájl neve. | Nem | Karakterlánc |
| Száma | lekérdezés | Lapon beolvasása elemek száma. | Nem | Karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedImageList](#paginatedimagelist) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Részletek a kép

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /images/ {azonosító} | 

### <a name="description"></a>Leírás
Lekérdezi a lemezkép azonosítóját.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Lemezkép-azonosítót. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Kép](#image) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Szolgáltatás létrehozása

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| POST |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / szolgáltatások | 

### <a name="description"></a>Leírás
Szolgáltatás létrehozása lemezkép.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| ServiceRequest elemen | Törzs | A szolgáltatás létrehozásához használt tartalom. | Igen | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. GET hívást megtudhatja, a szolgáltatás-készítési feladat állapotát. | A művelet-helye |
| 409 | Már létezik ilyen nevű szolgáltatás. |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Lekérdezési fiók szolgáltatások listája

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / szolgáltatások | 

### <a name="description"></a>Leírás
Lekérdezi egy fiókot a szolgáltatások listájában. Szűrheti az eredménylista modell név és azonosító, név/Jegyzékfájl, lemezkép-Azonosítót, szolgáltatásnév vagy Machine Learning számítási erőforrás-azonosító. Nincs szűrő fogad el, ha a lekérdezés felsorolja az összes szolgáltatás a fiókban. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| Szolgáltatásnév | lekérdezés | Szolgáltatás neve. | Nem | Karakterlánc |
| modelId | lekérdezés | Modell neve. | Nem | Karakterlánc |
| modelName | lekérdezés | Forrásmodell azonosítóját. | Nem | Karakterlánc |
| Jegyzékfájl azonosítója | lekérdezés | Manifest azonosítóját. | Nem | Karakterlánc |
| ManifestName | lekérdezés | A jegyzékfájl neve. | Nem | Karakterlánc |
| imageId | lekérdezés | Lemezkép-azonosítót. | Nem | Karakterlánc |
| computeResourceId | lekérdezés | Machine Learning számítási erőforrás-azonosító. | Nem | Karakterlánc |
| Száma | lekérdezés | Lapon beolvasása elemek száma. | Nem | Karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedServiceList](#paginatedservicelist) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Szolgáltatás-részletek

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /services/ {azonosító} | 

### <a name="description"></a>Leírás
Lekérdezi a szolgáltatás által azonosítóját.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Objektum azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [ServiceResponse](#serviceresponse) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Szolgáltatás frissítése

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| A PUT |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /services/ {azonosító} | 

### <a name="description"></a>Leírás
Frissíti a meglévő szolgáltatás.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Objektum azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| serviceUpdateRequest | Törzs | Meglévő szolgáltatás frissítéséhez használt hasznos. | Igen |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. GET hívást megtudhatja, a frissítési feladat állapotát. | A művelet-helye |
| 404 | A szolgáltatást, amely a megadott azonosító nem létezik. |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>A szolgáltatás törlése

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| TÖRLÉSE |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /services/ {azonosító} | 

### <a name="description"></a>Leírás
Szolgáltatás törlése.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Objektum azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. |  |
| 204 | A szolgáltatást, amely a megadott azonosító nem létezik. |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Szolgáltatás-kulcsok beszerzése

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /services/ {azonosítójú} / kulcsok | 

### <a name="description"></a>Leírás
Lekérdezi a kulcsai.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Szolgáltatás azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [AuthKeys](#authkeys)
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Szolgáltatás kulcsok újragenerálása

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| POST |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /services/ {azonosítójú} / kulcsok | 

### <a name="description"></a>Leírás
Szolgáltatás kulcsainak újragenerálása, és visszaadja azokat.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Szolgáltatás azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| regenerateKeyRequest | Törzs | Meglévő szolgáltatás frissítéséhez használt hasznos. | Igen | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [AuthKeys](#authkeys)
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>A központi telepítések fiók listájának lekérdezése

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / központi telepítések | 

### <a name="description"></a>Leírás
Lekérdezi a fiók üzemelő példányok listáját. Felügyeletiszolgáltatás-azonosító csak az adott szolgáltatáshoz létrehozott telepítések visszaadható által az eredménylista végezhet. Ha nincs szűrő fogad el, a lekérdezés felsorolja a fiók összes központi telepítést.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |
| serviceId | lekérdezés | Szolgáltatás azonosítója. | Nem | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [DeploymentList](#deploymentlist) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Központi telepítés részletes

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /deployments/ {azonosító} | 

### <a name="description"></a>Leírás
Lekérdezi a központi telepítéskor azonosítóját.

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | Központi telepítési azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Üzembe helyezés](#deployment) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>A művelet részletes

### <a name="request"></a>Kérés
| Módszer | Kérelem URI-azonosítója |
|------------|------------|
| GET |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /operations/ {azonosító} | 

### <a name="description"></a>Leírás
Művelet azonosító által aszinkron művelet állapotát olvassa be

### <a name="parameters"></a>Paraméterek
| Név | A található | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Elérési út | Az Azure előfizetés-azonosító. | Igen | Karakterlánc |
| erőforráscsoport-név | Elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| Fióknév | Elérési út | A modell felügyeleti fiókjának neve. | Igen | Karakterlánc |
| id | Elérési út | A művelet azonosítója. | Igen | Karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | Karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | Karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [OperationStatus](#asyncoperationstatus) |
| Alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Meghatározások

<a name="asset"></a>
### <a name="asset"></a>Eszköz
Az eszköz objektum Docker lemezkép létrehozása során szükséges.


|Név|Leírás|Séma|
|---|---|---|
|**azonosítója**  <br>*nem kötelező*|Eszköz azonosítója.|Karakterlánc|
|**mimeType**  <br>*nem kötelező*|A modell tartalma MIME-típus. A MIME-típus kapcsolatos további információkért tekintse meg a [IANA adathordozó-típusok listája](https://www.iana.org/assignments/media-types/media-types.xhtml).|Karakterlánc|
|**Csomagolja ki**  <br>*nem kötelező*|Azt jelzi, ha igazolnia kell a csomagolja ki a tartalom Docker lemezkép létrehozása során.|Logikai érték|
|**URL-címe**  <br>*nem kötelező*|Eszköz hely URL-címe.|Karakterlánc|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Az aszinkron művelet állapotát.

*Típus*: az enum (Getnextbatch, futó, megszakítva, sikeres, sikertelen)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
A művelet állapotát.


|Név|Leírás|Séma|
|---|---|---|
|**createdTime**  <br>*nem kötelező*  <br>*csak olvasható*|Az aszinkron művelet létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**Befejezés időpontja**  <br>*nem kötelező*  <br>*csak olvasható*|Az aszinkron művelet befejezésének időpontja (UTC).|karakterlánc (dátum, idő)|
|**Hiba történt**  <br>*nem kötelező*||[ErrorResponse](#errorresponse)|
|**azonosítója**  <br>*nem kötelező*|Az aszinkron művelet azonosítója.|Karakterlánc|
|**Művelettípus**  <br>*nem kötelező*|Az aszinkron művelet típusa.|Enum (a képet, a szolgáltatás)|
|**resourceLocation**  <br>*nem kötelező*|Erőforrás létrehozása, vagy frissíti az aszinkron művelet.|Karakterlánc|
|**állapot**  <br>*nem kötelező*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
A hitelesítési kulcsokat, a szolgáltatás számára.


|Név|Leírás|Séma|
|---|---|---|
|**primaryKey**  <br>*nem kötelező*|Elsődleges kulcs.|Karakterlánc|
|**másodlagos kulcs**  <br>*nem kötelező*|Másodlagos kulcsát.|Karakterlánc|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
A autoscaler beállításait.


|Név|Leírás|Séma|
|---|---|---|
|**autoscaleEnabled**  <br>*nem kötelező*|Engedélyezi vagy letiltja a autoscaler.|Logikai érték|
|**maxReplicas**  <br>*nem kötelező*|Akár méretezési pod másodpéldányok maximális száma.  <br>**Minimális érték**:`1`|egész szám|
|**minReplicas**  <br>*nem kötelező*|Pod replikák le a méretezési minimális száma.  <br>**Minimális érték**:`0`|egész szám|
|**refreshPeriodInSeconds**  <br>*nem kötelező*|Frissítési időpont automatikus skálázás eseményindító.  <br>**Minimális érték**:`1`|egész szám|
|**targetUtilization**  <br>*nem kötelező*|Kihasználtsági százaléka, amely elindítja az automatikus skálázást.  <br>**Minimális érték**:`0`  <br>**Maximális érték**:`100`|egész szám|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
A Machine Learning számítási erőforrással.


|Név|Leírás|Séma|
|---|---|---|
|**azonosítója**  <br>*nem kötelező*|Erőforrás-azonosító.|Karakterlánc|
|**típusa**  <br>*nem kötelező*|Az erőforrás típusát.|Enum (fürt)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
A fürt egy tároló erőforrásokat beállításait.


|Név|Leírás|Séma|
|---|---|---|
|**processzor**  <br>*nem kötelező*|Megadja a CPU-foglalást. Kubernetes formátuma: lásd: [jelentése a CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|Karakterlánc|
|**memória**  <br>*nem kötelező*|Megadja a memória-foglalás. Kubernetes formátuma: lásd: [memória szerinti](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|Karakterlánc|


<a name="deployment"></a>
### <a name="deployment"></a>Környezet
Az Azure Machine Learning központi telepítése egy példányát.


|Név|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*nem kötelező*  <br>*csak olvasható*|Központi telepítés létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**expiredAt**  <br>*nem kötelező*  <br>*csak olvasható*|Központi telepítés a lejárt időpontja (UTC).|karakterlánc (dátum, idő)|
|**azonosítója**  <br>*nem kötelező*|Központi telepítési azonosítója.|Karakterlánc|
|**imageId**  <br>*nem kötelező*|A központi telepítéshez tartozó kép azonosítója.|Karakterlánc|
|**Szolgáltatásnév**  <br>*nem kötelező*|Szolgáltatás neve.|Karakterlánc|
|**állapot**  <br>*nem kötelező*|Aktuális központi telepítési állapot.|Karakterlánc|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Központi telepítés objektumokból álló tömb.

*Típus*: <[telepítési](#deployment)> tömb


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Modellhez tartozó felügyeleti szolgáltatás hiba részletei.


|Név|Leírás|Séma|
|---|---|---|
|**kód**  <br>*szükséges*|Hibakód.|Karakterlánc|
|**üzenet**  <br>*szükséges*|Hibaüzenet jelenik meg.|Karakterlánc|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
A modell felügyeleti szolgáltatás hiba objektum.


|Név|Leírás|Séma|
|---|---|---|
|**kód**  <br>*szükséges*|Hibakód.|Karakterlánc|
|**Részletek**  <br>*nem kötelező*|Hiba részletei objektumokból álló tömb.|<[ErrorDetail](#errordetail)> tömb|
|**üzenet**  <br>*szükséges*|Hibaüzenet jelenik meg.|Karakterlánc|
|**statusCode**  <br>*nem kötelező*|HTTP-állapotkódot.|egész szám|


<a name="image"></a>
### <a name="image"></a>Kép
Az Azure Machine Learning kép.


|Név|Leírás|Séma|
|---|---|---|
|**computeResourceId**  <br>*nem kötelező*|A környezet létre a Machine Learning számítási erőforrás-azonosítója.|Karakterlánc|
|**createdTime**  <br>*nem kötelező*|Lemezkép létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**creationState**  <br>*nem kötelező*||[AsyncOperationState](#asyncoperationstate)|
|**Leírás**  <br>*nem kötelező*|Kép leíró szöveg.|Karakterlánc|
|**Hiba történt**  <br>*nem kötelező*||[ErrorResponse](#errorresponse)|
|**azonosítója**  <br>*nem kötelező*|Lemezkép-azonosítót.|Karakterlánc|
|**imageBuildLogUri**  <br>*nem kötelező*|A kép összeállítása a feltöltött naplók URI Azonosítóját.|Karakterlánc|
|**imageLocation**  <br>*nem kötelező*|A létrehozott lemezképet Azure tároló beállításjegyzék helye karakterláncot.|Karakterlánc|
|**imageType**  <br>*nem kötelező*||[ImageType](#imagetype)|
|**manifest**  <br>*nem kötelező*||[Manifest](#manifest)|
|**név**  <br>*nem kötelező*|Kép neve.|Karakterlánc|
|**verzió**  <br>*nem kötelező*|A modell szolgáltatás beállítása lemezkép verziója.|egész szám|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
Azure Machine Learning lemezképeket készíthet a kérést.


|Név|Leírás|Séma|
|---|---|---|
|**computeResourceId**  <br>*szükséges*|A környezet létre a Machine Learning számítási erőforrás-azonosítója.|Karakterlánc|
|**Leírás**  <br>*nem kötelező*|Kép leíró szöveg.|Karakterlánc|
|**imageType**  <br>*szükséges*||[ImageType](#imagetype)|
|**Jegyzékfájl azonosítója**  <br>*szükséges*|A jegyzékfájl, amelyből létrehozza a kép azonosítója.|Karakterlánc|
|**név**  <br>*szükséges*|Kép neve.|Karakterlánc|


<a name="imagetype"></a>
### <a name="imagetype"></a>Képtípus
Megadja a kép típusa.

*Típus*: az enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Jegyzék
Az Azure Machine Learning jegyzék.


|Név|Leírás|Séma|
|---|---|---|
|**eszközök**  <br>*szükséges*|Eszközök listája.|<[Eszköz](#asset)> tömb|
|**createdTime**  <br>*nem kötelező*  <br>*csak olvasható*|Manifest létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**Leírás**  <br>*nem kötelező*|A leíró szöveg manifest.|Karakterlánc|
|**driverProgram**  <br>*szükséges*|A jegyzékfájl illesztőprogramra.|Karakterlánc|
|**azonosítója**  <br>*nem kötelező*|Manifest azonosítóját.|Karakterlánc|
|**modelIds**  <br>*nem kötelező*|A regisztrált modellek modell azonosítók listáját. A kérelem sikertelen lesz, ha az összes belefoglalt modell nincs regisztrálva.|<string>a tömb|
|**modelType**  <br>*nem kötelező*|Meghatározza, hogy a modell már regisztrálva van a modell felügyeleti szolgáltatással.|Enum (regisztrált)|
|**név**  <br>*szükséges*|A jegyzékfájl neve.|Karakterlánc|
|**targetRuntime**  <br>*szükséges*||[TargetRuntime](#targetruntime)|
|**verzió**  <br>*nem kötelező*  <br>*csak olvasható*|A modell felügyeleti szolgáltatása által hozzárendelt jegyzékfájljának verziószámát.|egész szám|
|**webserviceType**  <br>*nem kötelező*|Adja meg a kívánt webes szolgáltatás, amely a jegyzékfájlból jön létre.|Enum (valós idejű)|


<a name="model"></a>
### <a name="model"></a>Modell
Az Azure Machine Learning modell példánya.


|Név|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*nem kötelező*  <br>*csak olvasható*|Modell létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**Leírás**  <br>*nem kötelező*|Modell leíró szöveg.|Karakterlánc|
|**azonosítója**  <br>*nem kötelező*  <br>*csak olvasható*|Forrásmodell azonosítóját.|Karakterlánc|
|**mimeType**  <br>*szükséges*|A modell tartalma MIME-típusát. A MIME-típus kapcsolatos további információkért tekintse meg a [IANA adathordozó-típusok listája](https://www.iana.org/assignments/media-types/media-types.xhtml).|Karakterlánc|
|**név**  <br>*szükséges*|Modell neve.|Karakterlánc|
|**címkét**  <br>*nem kötelező*|Címkelista modell.|<string>a tömb|
|**Csomagolja ki**  <br>*nem kötelező*|Azt jelzi, hogy igazolnia kell a csomagolja ki a modell Docker lemezkép létrehozása során.|Logikai érték|
|**URL-címe**  <br>*szükséges*|A modell URL-CÍMÉT. Általában azt egy megosztott hozzáférési aláírást URL-cím ide kerül.|Karakterlánc|
|**verzió**  <br>*nem kötelező*  <br>*csak olvasható*|A modell felügyeleti szolgáltatása által hozzárendelt modellverziója.|egész szám|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Az adatok gyűjtése információt.


|Név|Leírás|Séma|
|---|---|---|
|**eventHubEnabled**  <br>*nem kötelező*|Az event hubs szolgáltatás engedélyezése.|Logikai érték|
|**storageEnabled**  <br>*nem kötelező*|Engedélyezi egy szolgáltatás-tároló.|Logikai érték|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Lemezképek többoldalas listáját.


|Név|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*nem kötelező*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|Karakterlánc|
|**érték**  <br>*nem kötelező*|Modell objektumokból álló tömb.|<[Kép](#image)> tömb|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Jegyzékfájlokban többoldalas listáját.


|Név|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*nem kötelező*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|Karakterlánc|
|**érték**  <br>*nem kötelező*|Manifest objektumokból álló tömb.|<[Manifest](#manifest)> tömb|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Modellek többoldalas listáját.


|Név|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*nem kötelező*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|Karakterlánc|
|**érték**  <br>*nem kötelező*|Modell objektumokból álló tömb.|<[Modell](#model)> tömb|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Szolgáltatások többoldalas listáját.


|Név|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*nem kötelező*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|Karakterlánc|
|**érték**  <br>*nem kötelező*|Szolgáltatás objektumokból álló tömb.|<[ServiceResponse](#serviceresponse)> tömb|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Hozzon létre egy szolgáltatási kérelem.


|Név|Leírás|Séma|
|---|---|---|
|**appInsightsEnabled**  <br>*nem kötelező*|Engedélyezze az application insights szolgáltatáshoz.|Logikai érték|
|**autoScaler**  <br>*nem kötelező*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*szükséges*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*nem kötelező*||[ContainerResourceReservation](#containerresourcereservation)|
|**datacollection objektumot**  <br>*nem kötelező*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*szükséges*|A szolgáltatás létrehozása lemezkép.|Karakterlánc|
|**maxConcurrentRequestsPerContainer**  <br>*nem kötelező*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**:`1`|egész szám|
|**név**  <br>*szükséges*|Szolgáltatás neve.|Karakterlánc|
|**numReplicas**  <br>*nem kötelező*|Bármikor futtató pod replikák száma. Nem adható, ha engedélyezve van-e a Autoscaler.  <br>**Minimális érték**:`0`|egész szám|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
A kérelem újragenerálni a kulcsot a szolgáltatás számára.


|Név|Leírás|Séma|
|---|---|---|
|**keyType**  <br>*nem kötelező*|Itt adhatja meg, melyik újragenerálni a kulcsot.|Enum (elsődleges, másodlagos)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
A szolgáltatás részletes állapotát.


|Név|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*nem kötelező*|A szolgáltatás létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**Azonosító**  <br>*nem kötelező*|Szolgáltatás azonosítója.|Karakterlánc|
|**kép**  <br>*nem kötelező*||[Kép](#image)|
|**manifest**  <br>*nem kötelező*||[Manifest](#manifest)|
|**modellek**  <br>*nem kötelező*|Modellek listája.|<[Modell](#model)> tömb|
|**név**  <br>*nem kötelező*|Szolgáltatás neve.|Karakterlánc|
|**scoringUri**  <br>*nem kötelező*|A szolgáltatás pontozó URI.|Karakterlánc|
|**állapot**  <br>*nem kötelező*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*nem kötelező*|Legutóbbi frissítése (UTC).|karakterlánc (dátum, idő)|
|**appInsightsEnabled**  <br>*nem kötelező*|Engedélyezze az application insights szolgáltatáshoz.|Logikai érték|
|**autoScaler**  <br>*nem kötelező*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*szükséges*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*nem kötelező*||[ContainerResourceReservation](#containerresourcereservation)|
|**datacollection objektumot**  <br>*nem kötelező*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*nem kötelező*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**:`1`|egész szám|
|**numReplicas**  <br>*nem kötelező*|Bármikor futtató pod replikák száma. Nem adható, ha engedélyezve van-e a Autoscaler.  <br>**Minimális érték**:`0`|egész szám|
|**Hiba történt**  <br>*nem kötelező*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>serviceUpdateRequest
A kérelem egy szolgáltatásnak a frissítésére.


|Név|Leírás|Séma|
|---|---|---|
|**appInsightsEnabled**  <br>*nem kötelező*|Engedélyezze az application insights szolgáltatáshoz.|Logikai érték|
|**autoScaler**  <br>*nem kötelező*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*nem kötelező*||[ContainerResourceReservation](#containerresourcereservation)|
|**datacollection objektumot**  <br>*nem kötelező*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*nem kötelező*|A szolgáltatás létrehozása lemezkép.|Karakterlánc|
|**maxConcurrentRequestsPerContainer**  <br>*nem kötelező*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**:`1`|egész szám|
|**numReplicas**  <br>*nem kötelező*|Bármikor futtató pod replikák száma. Nem adható, ha engedélyezve van-e a Autoscaler.  <br>**Minimális érték**:`0`|egész szám|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
A tervezett futásidőt típusa.


|Név|Leírás|Séma|
|---|---|---|
|**Tulajdonságok**  <br>*szükséges*||< karakterlánc, karakterlánc > térkép|
|**runtimeType**  <br>*szükséges*|Megadja a futtatókörnyezetben.|Enum (SparkPython, Python)|

