---
title: Docker-lemezkép létrehozása az Azure Machine Learning modell kezelése |} Microsoft Docs
description: Ez a cikk a webes szolgáltatás Docker lemezkép létrehozása lépéseit ismerteti.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 233ae50246619c3e503e42081c3b4de88090f411
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835032"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Az Azure Machine Learning modell felügyeleti fiók API-referencia

A telepítési környezet beállításával kapcsolatos információkért lásd: [modell kezelése a fiókok telepítése](deployment-setup-configuration.md).

Az Azure Machine Learning modell felügyeleti fiók API valósítja meg a következő műveleteket:

- Modell regisztrációs
- Jegyzékfájl létrehozása
- Docker lemezkép létrehozása
- Webes szolgáltatás létrehozása

Ez a rendszerkép használatával hozzon létre egy webszolgáltatás-bővítmény vagy helyileg vagy a távoli Azure Tárolószolgáltatás-fürt vagy egy másik Docker-val támogatott környezetben az Ön által választott.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a telepítés lépéseit a megtettünk mindent a [telepítse, és hozzon létre a gyors üzembe helyezés](../service/quickstart-installation.md) dokumentum.

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

| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Leírás
Regisztrálja a modell.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| típus | törzs | A modell regisztrálásához használt adattartalmában. | Igen | [Modell](#model) |


### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | OK. A modell regisztrálása sikeres volt. | [Modell](#model) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Lekérdezési fiók a modellek listája
### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Leírás
Lekérdezi a fiók a modellek listája. Az eredménylista címke és név szerint szűrhetők. Ha nincs szűrő fogad el, a lekérdezés felsorolja a fiók összes modellt. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| név | lekérdezés | Objektum neve. | Nem | karakterlánc |
| címke | lekérdezés | Minta címke. | Nem | karakterlánc |
| darab | lekérdezés | Lapon beolvasása elemek száma. | Nem | karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedModelList](#paginatedmodellist) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Részletek a modell
### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Leírás
Lekérdezi a modell azonosítóját.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Objektum azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Modell](#model) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>A regisztrált modellhez és az összes függősége a jegyzékfájl regisztrálása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Leírás
A jegyzék regisztrálja a regisztrált modellhez és annak függőségeit.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| manifestRequest | törzs | Amellyel egy jegyzékfájl regisztrálása hasznos. | Igen | [Manifest](#manifest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Manifest a regisztráció sikeres volt. | [Manifest](#manifest) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>A lekérdezés a jegyzékfájlokat az fiók listája

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Leírás
Lekérdezi a jegyzékfájlokat az fiók listáját. Az eredménylista modell-azonosító szerinti szűrése, és a jegyzékfájl neve. Ha nincs szűrő fogad el, a lekérdezés felsorolja az összes a jegyzékfájlokat az a fiók. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| modelId | lekérdezés | Forrásmodell azonosítóját. | Nem | karakterlánc |
| ManifestName | lekérdezés | A jegyzékfájl neve. | Nem | karakterlánc |
| darab | lekérdezés | Lapon beolvasása elemek száma. | Nem | karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedManifestList](#paginatedmanifestlist) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Részletek a jegyzék

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Leírás
Lekérdezi a jegyzékfájl által azonosítóját.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Objektum azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Manifest](#manifest) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Lemezkép létrehozása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Leírás
Lemezkép létrehozása az Azure-tároló beállításjegyzék Docker lemezképként.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| imageRequest | törzs | A képfájl létrehozásához használt tartalom. | Igen | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. GET hívást megtudhatja, a lemezkép-készítési feladat állapotát. | A művelet-helye |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Egy fiók képek listájának lekérdezése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Leírás
Egy fiók képek listájának lekérdezése. Az eredménylista jegyzék azonosító és név szerint szűrhetők. Ha nincs szűrő fogad el, a lekérdezés felsorolja az a fiók a lemezképeket. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| Jegyzékfájl azonosítója | lekérdezés | Manifest azonosítóját. | Nem | karakterlánc |
| ManifestName | lekérdezés | A jegyzékfájl neve. | Nem | karakterlánc |
| darab | lekérdezés | Lapon beolvasása elemek száma. | Nem | karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedImageList](#paginatedimagelist) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Részletek a kép

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Leírás
Lekérdezi a lemezkép azonosítóját.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Lemezkép-azonosítót. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Rendszerkép](#image) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Szolgáltatás létrehozása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Leírás
Szolgáltatás létrehozása lemezkép.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| serviceRequest | törzs | A szolgáltatás létrehozásához használt tartalom. | Igen | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. GET hívást megtudhatja, a szolgáltatás-készítési feladat állapotát. | A művelet-helye |
| 409 | Már létezik ilyen nevű szolgáltatás. |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Lekérdezési fiók szolgáltatások listája

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Leírás
Lekérdezi egy fiókot a szolgáltatások listájában. Szűrheti az eredménylista modell név és azonosító, név/Jegyzékfájl, lemezkép-Azonosítót, szolgáltatásnév vagy Machine Learning számítási erőforrás-azonosító. Nincs szűrő fogad el, ha a lekérdezés felsorolja az összes szolgáltatás a fiókban. A visszaadott lista paginated van, és minden oldalon szereplő elemek száma nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| serviceName | lekérdezés | Szolgáltatás neve. | Nem | karakterlánc |
| modelId | lekérdezés | Modell neve. | Nem | karakterlánc |
| modelName | lekérdezés | Forrásmodell azonosítóját. | Nem | karakterlánc |
| Jegyzékfájl azonosítója | lekérdezés | Manifest azonosítóját. | Nem | karakterlánc |
| ManifestName | lekérdezés | A jegyzékfájl neve. | Nem | karakterlánc |
| imageId | lekérdezés | Lemezkép-azonosítót. | Nem | karakterlánc |
| computeResourceId | lekérdezés | Machine Learning számítási erőforrás-azonosító. | Nem | karakterlánc |
| darab | lekérdezés | Lapon beolvasása elemek száma. | Nem | karakterlánc |
| $skipToken | lekérdezés | A folytatási beolvasása a következő oldalra. | Nem | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedServiceList](#paginatedservicelist) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Szolgáltatás-részletek

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Leírás
Lekérdezi a szolgáltatás által azonosítóját.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Objektum azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [ServiceResponse](#serviceresponse) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Szolgáltatás frissítése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Leírás
Frissíti a meglévő szolgáltatás.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Objektum azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| serviceUpdateRequest | törzs | Meglévő szolgáltatás frissítéséhez használt hasznos. | Igen |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. GET hívást megtudhatja, a frissítési feladat állapotát. | A művelet-helye |
| 404 | A szolgáltatást, amely a megadott azonosító nem létezik. |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>A szolgáltatás törlése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| DELETE |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Leírás
Szolgáltatás törlése.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Objektum azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. |  |
| 204 | A szolgáltatást, amely a megadott azonosító nem létezik. |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Szolgáltatás-kulcsok beszerzése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Leírás
Lekérdezi a kulcsai.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Service ID. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [AuthKeys](#authkeys)
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Szolgáltatás kulcsok újragenerálása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /API /Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} /accounts/ /services/ {azonosítójú} / regenerateKeys | 

### <a name="description"></a>Leírás
Szolgáltatás kulcsainak újragenerálása, és visszaadja azokat.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Service ID. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| regenerateKeyRequest | törzs | Meglévő szolgáltatás frissítéséhez használt hasznos. | Igen | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [AuthKeys](#authkeys)
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>A központi telepítések fiók listájának lekérdezése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Leírás
Lekérdezi a fiók üzemelő példányok listáját. Felügyeletiszolgáltatás-azonosító csak az adott szolgáltatáshoz létrehozott telepítések visszaadható által az eredménylista végezhet. Ha nincs szűrő fogad el, a lekérdezés felsorolja a fiók összes központi telepítést.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |
| serviceId | lekérdezés | Service ID. | Nem | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [DeploymentList](#deploymentlist) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Központi telepítés részletes

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Leírás
Lekérdezi a központi telepítéskor azonosítóját.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | Központi telepítési azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Üzembe helyezés](#deployment) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>A művelet részletes

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Leírás
Művelet azonosító által aszinkron művelet állapotát olvassa be

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosító. | Igen | karakterlánc |
| resourceGroupName | elérési út | Az erőforráscsoport, ahol a modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| Fióknév | elérési út | A modell felügyeleti fiókjának neve. | Igen | karakterlánc |
| id | elérési út | A művelet azonosítója. | Igen | karakterlánc |
| API-verzió | lekérdezés | A Microsoft.Machine.Learning erőforrás-szolgáltató használatára API verziója. | Igen | karakterlánc |
| Engedélyezés | header | Engedélyezési jogkivonat. "Tulajdonosi XXXXXX." hasonlót kell | Igen | karakterlánc |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [OperationStatus](#asyncoperationstatus) |
| alapértelmezett | Az hibaüzenetet, amely leírja, hogy miért volt sikertelen a művelet. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Meghatározások

<a name="asset"></a>
### <a name="asset"></a>Adategység
Az eszköz objektum Docker lemezkép létrehozása során szükséges.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**id**  <br>*optional*|Eszköz azonosítója.|karakterlánc|
|**mimeType**  <br>*optional*|A modell tartalma MIME-típus. A MIME-típus kapcsolatos további információkért tekintse meg a [IANA adathordozó-típusok listája](https://www.iana.org/assignments/media-types/media-types.xhtml).|karakterlánc|
|**Csomagolja ki**  <br>*optional*|Azt jelzi, ha igazolnia kell a csomagolja ki a tartalom Docker lemezkép létrehozása során.|logikai|
|**url**  <br>*optional*|Eszköz hely URL-címe.|karakterlánc|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Az aszinkron művelet állapotát.

*Típus*: az enum (Getnextbatch, futó, megszakítva, sikeres, sikertelen)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
A művelet állapotát.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdTime**  <br>*optional*  <br>*read-only*|Az aszinkron művelet létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**endTime**  <br>*optional*  <br>*read-only*|Az aszinkron művelet befejezésének időpontja (UTC).|karakterlánc (dátum, idő)|
|**Hiba történt**  <br>*optional*||[ErrorResponse](#errorresponse)|
|**id**  <br>*optional*|Az aszinkron művelet azonosítója.|karakterlánc|
|**operationType**  <br>*optional*|Az aszinkron művelet típusa.|Enum (a képet, a szolgáltatás)|
|**resourceLocation**  <br>*optional*|Erőforrás létrehozása, vagy frissíti az aszinkron művelet.|karakterlánc|
|**Állapot**  <br>*optional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
A hitelesítési kulcsokat, a szolgáltatás számára.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**primaryKey**  <br>*optional*|Elsődleges kulcs.|karakterlánc|
|**secondaryKey**  <br>*optional*|Másodlagos kulcsát.|karakterlánc|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
A autoscaler beállításait.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**autoscaleEnabled**  <br>*optional*|Engedélyezi vagy letiltja a autoscaler.|logikai|
|**maxReplicas**  <br>*optional*|Akár méretezési pod másodpéldányok maximális száma.  <br>**Minimális érték**: `1`|egész szám|
|**minReplicas**  <br>*optional*|Pod replikák le a méretezési minimális száma.  <br>**Minimális érték**: `0`|egész szám|
|**refreshPeriodInSeconds**  <br>*optional*|Frissítési időpont automatikus skálázás eseményindító.  <br>**Minimális érték**: `1`|egész szám|
|**targetUtilization**  <br>*optional*|Kihasználtsági százaléka, amely elindítja az automatikus skálázást.  <br>**Minimális érték**: `0`  <br>**Maximális érték**: `100`|egész szám|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
A Machine Learning számítási erőforrással.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**id**  <br>*optional*|Erőforrás-azonosító.|karakterlánc|
|**type**  <br>*optional*|Az erőforrás típusát.|Enum (fürt)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
A fürt egy tároló erőforrásokat beállításait.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**cpu**  <br>*optional*|Megadja a CPU-foglalást. Kubernetes formátuma: lásd: [jelentése a CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|karakterlánc|
|**Memória**  <br>*optional*|Megadja a memória-foglalás. Kubernetes formátuma: lásd: [memória szerinti](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|karakterlánc|


<a name="deployment"></a>
### <a name="deployment"></a>Környezet
Az Azure Machine Learning központi telepítése egy példányát.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*optional*  <br>*read-only*|Központi telepítés létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**expiredAt**  <br>*optional*  <br>*read-only*|Központi telepítés a lejárt időpontja (UTC).|karakterlánc (dátum, idő)|
|**id**  <br>*optional*|Központi telepítési azonosítója.|karakterlánc|
|**imageId**  <br>*optional*|A központi telepítéshez tartozó kép azonosítója.|karakterlánc|
|**serviceName**  <br>*optional*|Szolgáltatás neve.|karakterlánc|
|**Állapot**  <br>*optional*|Aktuális központi telepítési állapot.|karakterlánc|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Központi telepítés objektumokból álló tömb.

*Típus*: <[telepítési](#deployment)> tömb


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Modellhez tartozó felügyeleti szolgáltatás hiba részletei.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Kód**  <br>*Szükséges*|Hibakód.|karakterlánc|
|**Üzenet**  <br>*Szükséges*|Hibaüzenet jelenik meg.|karakterlánc|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
A modell felügyeleti szolgáltatás hiba objektum.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Kód**  <br>*Szükséges*|Hibakód.|karakterlánc|
|**Részletek**  <br>*optional*|Hiba részletei objektumokból álló tömb.|<[ErrorDetail](#errordetail)> tömb|
|**Üzenet**  <br>*Szükséges*|Hibaüzenet jelenik meg.|karakterlánc|
|**statusCode**  <br>*optional*|HTTP-állapotkódot.|egész szám|


<a name="image"></a>
### <a name="image"></a>Kép
Az Azure Machine Learning kép.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**computeResourceId**  <br>*optional*|A környezet létre a Machine Learning számítási erőforrás-azonosítója.|karakterlánc|
|**createdTime**  <br>*optional*|Lemezkép létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**creationState**  <br>*optional*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*optional*|Kép leíró szöveg.|karakterlánc|
|**Hiba történt**  <br>*optional*||[ErrorResponse](#errorresponse)|
|**id**  <br>*optional*|Lemezkép-azonosítót.|karakterlánc|
|**imageBuildLogUri**  <br>*optional*|A kép összeállítása a feltöltött naplók URI Azonosítóját.|karakterlánc|
|**imageLocation**  <br>*optional*|A létrehozott lemezképet Azure tároló beállításjegyzék helye karakterláncot.|karakterlánc|
|**ImageType**  <br>*optional*||[ImageType](#imagetype)|
|**Manifest**  <br>*optional*||[Manifest](#manifest)|
|**name**  <br>*optional*|Kép neve.|karakterlánc|
|**Verzió**  <br>*optional*|A modell szolgáltatás beállítása lemezkép verziója.|egész szám|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
Azure Machine Learning lemezképeket készíthet a kérést.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**computeResourceId**  <br>*Szükséges*|A környezet létre a Machine Learning számítási erőforrás-azonosítója.|karakterlánc|
|**description**  <br>*optional*|Kép leíró szöveg.|karakterlánc|
|**ImageType**  <br>*Szükséges*||[ImageType](#imagetype)|
|**Jegyzékfájl azonosítója**  <br>*Szükséges*|A jegyzékfájl, amelyből létrehozza a kép azonosítója.|karakterlánc|
|**name**  <br>*Szükséges*|Kép neve.|karakterlánc|


<a name="imagetype"></a>
### <a name="imagetype"></a>Képtípus
Megadja a kép típusa.

*Típus*: az enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Jegyzék
Az Azure Machine Learning jegyzék.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Eszközök**  <br>*Szükséges*|Eszközök listája.|<[Eszköz](#asset)> tömb|
|**createdTime**  <br>*optional*  <br>*read-only*|Manifest létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**description**  <br>*optional*|A leíró szöveg manifest.|karakterlánc|
|**driverProgram**  <br>*Szükséges*|A jegyzékfájl illesztőprogramra.|karakterlánc|
|**id**  <br>*optional*|Manifest azonosítóját.|karakterlánc|
|**modelIds**  <br>*optional*|A regisztrált modellek modell azonosítók listáját. A kérelem sikertelen lesz, ha az összes belefoglalt modell nincs regisztrálva.|<string> A tömb|
|**modelType**  <br>*optional*|Meghatározza, hogy a modell már regisztrálva van a modell felügyeleti szolgáltatással.|Enum (regisztrált)|
|**name**  <br>*Szükséges*|A jegyzékfájl neve.|karakterlánc|
|**targetRuntime**  <br>*Szükséges*||[TargetRuntime](#targetruntime)|
|**Verzió**  <br>*optional*  <br>*read-only*|A modell felügyeleti szolgáltatása által hozzárendelt jegyzékfájljának verziószámát.|egész szám|
|**webserviceType**  <br>*optional*|Adja meg a kívánt webes szolgáltatás, amely a jegyzékfájlból jön létre.|Enum (valós idejű)|


<a name="model"></a>
### <a name="model"></a>Modell
Az Azure Machine Learning modell példánya.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*optional*  <br>*read-only*|Modell létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**description**  <br>*optional*|Modell leíró szöveg.|karakterlánc|
|**id**  <br>*optional*  <br>*read-only*|Forrásmodell azonosítóját.|karakterlánc|
|**mimeType**  <br>*Szükséges*|A modell tartalma MIME-típusát. A MIME-típus kapcsolatos további információkért tekintse meg a [IANA adathordozó-típusok listája](https://www.iana.org/assignments/media-types/media-types.xhtml).|karakterlánc|
|**name**  <br>*Szükséges*|Modell neve.|karakterlánc|
|**címkét**  <br>*optional*|Címkelista modell.|<string> A tömb|
|**Csomagolja ki**  <br>*optional*|Azt jelzi, hogy igazolnia kell a csomagolja ki a modell Docker lemezkép létrehozása során.|logikai|
|**url**  <br>*Szükséges*|A modell URL-CÍMÉT. Általában azt egy megosztott hozzáférési aláírást URL-cím ide kerül.|karakterlánc|
|**Verzió**  <br>*optional*  <br>*read-only*|A modell felügyeleti szolgáltatása által hozzárendelt modellverziója.|egész szám|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Az adatok gyűjtése információt.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**eventHubEnabled**  <br>*optional*|Az event hubs szolgáltatás engedélyezése.|logikai|
|**storageEnabled**  <br>*optional*|Engedélyezi egy szolgáltatás-tároló.|logikai|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Lemezképek többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|karakterlánc|
|**value**  <br>*optional*|Modell objektumokból álló tömb.|<[Kép](#image)> tömb|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Jegyzékfájlokban többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|karakterlánc|
|**value**  <br>*optional*|Manifest objektumokból álló tömb.|<[Manifest](#manifest)> tömb|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Modellek többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|karakterlánc|
|**value**  <br>*optional*|Modell objektumokból álló tömb.|<[Modell](#model)> tömb|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Szolgáltatások többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-azonosító) a következő lapra mutató hivatkozás az eredmények listájában.|karakterlánc|
|**value**  <br>*optional*|Szolgáltatás objektumokból álló tömb.|<[ServiceResponse](#serviceresponse)> tömb|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Hozzon létre egy szolgáltatási kérelem.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**appInsightsEnabled**  <br>*optional*|Engedélyezze az application insights szolgáltatáshoz.|logikai|
|**autoScaler**  <br>*optional*||[AutoScaler](#autoscaler)|
|**ComputeResource**  <br>*Szükséges*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*optional*||[ContainerResourceReservation](#containerresourcereservation)|
|**Datacollection objektumot**  <br>*optional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Szükséges*|A szolgáltatás létrehozása lemezkép.|karakterlánc|
|**maxConcurrentRequestsPerContainer**  <br>*optional*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**name**  <br>*Szükséges*|Szolgáltatás neve.|karakterlánc|
|**numReplicas**  <br>*optional*|Bármikor futtató pod replikák száma. Nem adható, ha engedélyezve van-e a Autoscaler.  <br>**Minimális érték**: `0`|egész szám|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
A kérelem újragenerálni a kulcsot a szolgáltatás számára.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**keyType**  <br>*optional*|Itt adhatja meg, melyik újragenerálni a kulcsot.|Enum (elsődleges, másodlagos)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
A szolgáltatás részletes állapotát.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*optional*|A szolgáltatás létrehozásának időpontja (UTC).|karakterlánc (dátum, idő)|
|**Azonosító**  <br>*optional*|Service ID.|karakterlánc|
|**image**  <br>*optional*||[Rendszerkép](#image)|
|**Manifest**  <br>*optional*||[Manifest](#manifest)|
|**modellek**  <br>*optional*|Modellek listája.|<[Modell](#model)> tömb|
|**name**  <br>*optional*|Szolgáltatás neve.|karakterlánc|
|**scoringUri**  <br>*optional*|A szolgáltatás pontozó URI.|karakterlánc|
|**Állapot**  <br>*optional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*optional*|Legutóbbi frissítése (UTC).|karakterlánc (dátum, idő)|
|**appInsightsEnabled**  <br>*optional*|Engedélyezze az application insights szolgáltatáshoz.|logikai|
|**autoScaler**  <br>*optional*||[AutoScaler](#autoscaler)|
|**ComputeResource**  <br>*Szükséges*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*optional*||[ContainerResourceReservation](#containerresourcereservation)|
|**Datacollection objektumot**  <br>*optional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*optional*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**numReplicas**  <br>*optional*|Bármikor futtató pod replikák száma. Nem adható, ha engedélyezve van-e a Autoscaler.  <br>**Minimális érték**: `0`|egész szám|
|**Hiba történt**  <br>*optional*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
A kérelem egy szolgáltatásnak a frissítésére.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**appInsightsEnabled**  <br>*optional*|Engedélyezze az application insights szolgáltatáshoz.|logikai|
|**autoScaler**  <br>*optional*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*optional*||[ContainerResourceReservation](#containerresourcereservation)|
|**Datacollection objektumot**  <br>*optional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*optional*|A szolgáltatás létrehozása lemezkép.|karakterlánc|
|**maxConcurrentRequestsPerContainer**  <br>*optional*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**numReplicas**  <br>*optional*|Bármikor futtató pod replikák száma. Nem adható, ha engedélyezve van-e a Autoscaler.  <br>**Minimális érték**: `0`|egész szám|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
A tervezett futásidőt típusa.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Tulajdonságok**  <br>*Szükséges*||< karakterlánc, karakterlánc > térkép|
|**runtimeType**  <br>*Szükséges*|Megadja a futtatókörnyezetben.|Enum (SparkPython, Python)|

