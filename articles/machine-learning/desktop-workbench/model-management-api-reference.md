---
title: Docker-rendszerkép létrehozása az Azure Machine Learning modellkezeléshez |} A Microsoft Docs
description: Ez a cikk a webszolgáltatást a Docker-rendszerképek létrehozásának lépéseit ismerteti.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: c0f51e47038737d6aa743be718ad6b28c161c766
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645764"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Az Azure Machine Learning minta felügyeleti fiók API-referencia

A környezet beállításával kapcsolatos további információkért lásd: [Modellkezelési fiók beállítása](deployment-setup-configuration.md).

Az Azure Machine Learning minta felügyeleti fiók API valósít meg a következő műveleteket:

- Modell-regisztráció
- Jegyzékfájl létrehozása
- Docker-rendszerkép létrehozása
- Webszolgáltatás létrehozása

Ez a rendszerkép használatával akár helyileg és a egy távoli Azure Container Service-fürt vagy egy másik Docker-val támogatott környezetben tetszőleges webszolgáltatás létrehozása.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy elvégezte a telepítés lépéseit, a [telepítése és létrehozási rövid útmutató](../service/quickstart-installation.md) dokumentumot.

A következőkre szükség, a folytatás előtt:
1. Minta felügyeleti fiók kiépítése
2. Környezetek létrehozása, üzembe helyezéséhez és felügyeletéhez modellek
3. A Machine Learning-modell

### <a name="azure-ad-token"></a>Az Azure AD-token
Ha az Azure parancssori felület használata esetén jelentkezzen be a `az login`. A parancssori felület a .azure-fájlból az Azure Active Directory (Azure AD-) token használ. Ha azt szeretné, az API-k, az alábbi lehetőségek állnak rendelkezésére.

##### <a name="acquire-the-azure-ad-token-manually"></a>Az Azure AD-jogkivonat beszerzése manuálisan
Használhat `az login` és a legújabb token beszerzése a kezdőkönyvtár az .azure-fájlból.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Programozott módon az Azure ad-ben jogkivonat beszerzése
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Miután létrehozta az egyszerű szolgáltatás, mentse a kimenetet. Most már használhatja, amely az Azure AD-ből származó jogkivonat beszerzéséhez:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
A jogkivonat az API-hívásokhoz engedélyeztetési fejléc helyezi.


## <a name="register-a-model"></a>Regisztrálja a modellt

Modell lépésben regisztrálja a Machine Learning-modellel létrehozott Azure Modellkezelés-fiókot. Ez a regisztráció lehetővé teszi, hogy a modellek és azok verzióinak regisztrációs időpontjában rendelt nyomon követése. A felhasználó megadja a modell neve. Modellek ugyanazzal a névvel a későbbi regisztrációs hoz létre egy új verziója és azonosító.

### <a name="request"></a>Kérés

| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Leírás
Regisztrálja a modellt.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| típus | törzs | Regisztrálja a modellt használt adattartalma. | Igen | [Modell](#model) |


### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | OK. A modell regisztráció sikeres volt. | [Modell](#model) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Egy fiók modellek listájának lekérdezése
### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Leírás
Lekérdezi egy fiók a modellek listája. A találatok listájában, a címke és név szerint szűrheti. Egyetlen szűrő sincs átadott, a lekérdezés felsorolja a fiókban lévő összes modellek. A visszaadott listában többoldalas van, és minden oldalon szereplő elemek száma egy nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| név | lekérdezés | Objektum neve. | Nem | sztring |
| címke | lekérdezés | A modellekre címke. | Nem | sztring |
| count | lekérdezés | Egy oldal a beolvasható elemek száma. | Nem | sztring |
| $skipToken | lekérdezés | Folytatási token a következő lap beolvasásához. | Nem | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedModelList](#paginatedmodellist) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="get-model-details"></a>Modell részleteinek beolvasása
### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Leírás
Lekérdezi egy modell azonosítóját.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Objektum azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Modell](#model) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>A regisztrált modell és annak összes függőségét regisztrálja a jegyzék

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Leírás
A jegyzékfájl regisztrálja a regisztrált modell és minden függőségét.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| manifestRequest | törzs | Amellyel egy jegyzékfájl regisztrálása adattartalma. | Igen | [Manifest](#manifest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Jegyzékfájl a regisztráció sikeres volt. | [Manifest](#manifest) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Egy fiók a jegyzékek listájának lekérdezése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Leírás
Lekérdezi egy fiókot a jegyzékek listájának. Az eredménylista Modellazonosító szerint szűrheti és jegyzékfájl neve. Egyetlen szűrő sincs átadott, a lekérdezés felsorolja a fiókban lévő összes jegyzékek. A visszaadott listában többoldalas van, és minden oldalon szereplő elemek száma egy nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| modelId | lekérdezés | Forrásmodell azonosítóját. | Nem | sztring |
| manifestName | lekérdezés | Jegyzékfájl neve. | Nem | sztring |
| count | lekérdezés | Egy oldal a beolvasható elemek száma. | Nem | sztring |
| $skipToken | lekérdezés | Folytatási token a következő lap beolvasásához. | Nem | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedManifestList](#paginatedmanifestlist) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="get-manifest-details"></a>Jegyzékfájl részleteinek beolvasása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Leírás
A jegyzékfájl által beolvasása

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Objektum azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Manifest](#manifest) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="create-an-image"></a>Rendszerkép létrehozása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Leírás
Létrehozza a kép egy Docker-rendszerképet az Azure Container Registryben.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| imageRequest | törzs | Rendszerkép létrehozásához használt adattartalma. | Igen | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. Egy GET hívást kell bemutatják, a lemezkép-készítési feladat állapotát. | A művelet-helye |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Lekérdezés-fiókban lévő rendszerképek listája

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Leírás
Lekérdezi egy fiók található rendszerképek listáját. Az eredménylista jegyzékfájl azonosító és név szerint szűrheti. Egyetlen szűrő sincs átadott, a lekérdezés felsorolja a fiókban lévő összes rendszerkép. A visszaadott listában többoldalas van, és minden oldalon szereplő elemek száma egy nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| Jegyzékfájl azonosítója | lekérdezés | Jegyzékazonosítót. | Nem | sztring |
| manifestName | lekérdezés | Jegyzékfájl neve. | Nem | sztring |
| count | lekérdezés | Egy oldal a beolvasható elemek száma. | Nem | sztring |
| $skipToken | lekérdezés | Folytatási token a következő lap beolvasásához. | Nem | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedImageList](#paginatedimagelist) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="get-image-details"></a>Részletek a kép

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Leírás
Lekérdezi egy lemezkép azonosítója.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Lemezkép-azonosító. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Rendszerkép](#image) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |


## <a name="create-a-service"></a>Szolgáltatás létrehozása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Leírás
Létrehoz egy szolgáltatást egy rendszerképből.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| serviceRequest | törzs | A szolgáltatás létrehozásához használt adattartalma. | Igen | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. Egy GET hívást kell bemutatják, a szolgáltatás-létrehozási feladat állapotát. | A művelet-helye |
| 409 | Már létezik ilyen nevű szolgáltatás. |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Lekérdezés-fiókban lévő szolgáltatások listája

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Leírás
Lekérdezi egy fiókot a szolgáltatások listájában. Szűrheti a találatok listájában, a modell neve és az azonosító, a jegyzékfájl neve és az azonosító, a lemezkép-azonosító, a szolgáltatás neve vagy a Machine Learning számítási erőforrás-azonosítója. Egyetlen szűrő sincs átadott, a lekérdezés felsorolja a fiókban lévő összes szolgáltatást. A visszaadott listában többoldalas van, és minden oldalon szereplő elemek száma egy nem kötelező paraméter.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| serviceName | lekérdezés | Szolgáltatás neve. | Nem | sztring |
| modelId | lekérdezés | Modell neve. | Nem | sztring |
| modelName | lekérdezés | Forrásmodell azonosítóját. | Nem | sztring |
| Jegyzékfájl azonosítója | lekérdezés | Jegyzékazonosítót. | Nem | sztring |
| manifestName | lekérdezés | Jegyzékfájl neve. | Nem | sztring |
| imageId | lekérdezés | Lemezkép-azonosító. | Nem | sztring |
| computeResourceId | lekérdezés | Machine Learning számítási erőforrás-azonosítója. | Nem | sztring |
| count | lekérdezés | Egy oldal a beolvasható elemek száma. | Nem | sztring |
| $skipToken | lekérdezés | Folytatási token a következő lap beolvasásához. | Nem | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [PaginatedServiceList](#paginatedservicelist) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse) |

## <a name="get-service-details"></a>Szolgáltatás részleteinek beolvasása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Leírás
A szolgáltatás által beolvasása

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Objektum azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [ServiceResponse](#serviceresponse) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

## <a name="update-a-service"></a>Szolgáltatás frissítése

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Leírás
Frissíti egy meglévő szolgáltatás.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Objektum azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| serviceUpdateRequest | törzs | Frissítheti a meglévő service adattartalom. | Igen |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Fejlécek | Séma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Az aszinkron művelet hely URL-címe. Egy GET hívást kell bemutatják, a frissítési szolgáltatás a feladat állapotát. | A művelet-helye |
| 404 | A megadott azonosítójú szolgáltatás nem létezik. |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

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
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Objektum azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. |  |
| 204 | A megadott azonosítójú szolgáltatás nem létezik. |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

## <a name="get-service-keys"></a>Szolgáltatás-kulcsok beolvasása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Leírás
Szolgáltatáskulcsok beolvasása.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Service ID. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [AuthKeys](#authkeys)
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

## <a name="regenerate-service-keys"></a>Szolgáltatás-kulcsok újragenerálása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| POST |  / API/subscriptions / {subscriptionId} {a(z) resourceGroupName} /resourceGroups/ {accountName} /accounts/ /services/ {id} / kulcsainak újragenerálása | 

### <a name="description"></a>Leírás
Szolgáltatás kulcsainak újragenerálása, és adja vissza őket.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Service ID. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| regenerateKeyRequest | törzs | Frissítheti a meglévő service adattartalom. | Igen | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [AuthKeys](#authkeys)
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Lekérdezés-fiókban lévő telepítések listáját

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Leírás
Lekérdezi egy fiók telepítések listáját. Szűrhet az eredménylista szolgáltatás azonosítója, amely csak a létrehozott központi telepítésekre az adott szolgáltatáshoz adja vissza. Egyetlen szűrő sincs átadott, a lekérdezés felsorolja a fiókban lévő összes központi telepítést.

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |
| serviceId | lekérdezés | Service ID. | Nem | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [DeploymentList](#deploymentlist) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

## <a name="get-deployment-details"></a>Telepítés részleteinek beolvasása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Leírás
A központi telepítés által beolvasása

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | Központi telepítési azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [Üzembe helyezés](#deployment) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)

## <a name="get-operation-details"></a>A művelet részleteinek beolvasása

### <a name="request"></a>Kérés
| Módszer | Kérés URI-ja |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Leírás
A művelet által az aszinkron műveleti állapotának beolvasása

### <a name="parameters"></a>Paraméterek
| Name (Név) | Hely | Leírás | Szükséges | Séma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | elérési út | Az Azure előfizetés-azonosítójára. | Igen | sztring |
| resourceGroupName | elérési út | Az erőforrás nevét, ahol a Modellkezelési fiók is található. | Igen | sztring |
| Fióknév | elérési út | A Modellkezelési fiók nevét. | Igen | sztring |
| id | elérési út | A művelet azonosítója. | Igen | sztring |
| API-verzió | lekérdezés | Microsoft.Machine.Learning erőforrás-szolgáltató használata API verziója. | Igen | sztring |
| Engedélyezés | header | Engedélyezési jogkivonat. Lehet például "Tulajdonosi XXXXXX." | Igen | sztring |

### <a name="responses"></a>Válaszok
| Kód | Leírás | Séma |
|--------------------|--------------------|--------------------|
| 200 | Siker. | [OperationStatus](#asyncoperationstatus) |
| alapértelmezett | Hiba választ, amely azt ismerteti, miért érdemes a művelet sikertelen volt. | [Byl vrácen Prvek](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Meghatározások

<a name="asset"></a>
### <a name="asset"></a>Objektum
Az eszközintelligencia objektum, amely a Docker-rendszerkép létrehozása során szükség lesz.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**id**  <br>*optional*|Eszköz azonosítója.|sztring|
|**mimeType**  <br>*optional*|MIME-típusát modelltartalmat. MIME-típus kapcsolatos további információkért lásd: a [IANA adathordozó-típusok listája](https://www.iana.org/assignments/media-types/media-types.xhtml).|sztring|
|**kicsomagolása**  <br>*optional*|Azt jelzi, ha ellenőriznünk kell a csomagolja ki a tartalmat a Docker-rendszerkép létrehozása során.|logikai|
|**url**  <br>*optional*|Az eszközintelligencia-hely URL-címe.|sztring|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Az aszinkron művelet állapotát.

*Típus*: enum (NotStarted, futó, Cancelled, sikeres, sikertelen)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
A művelet állapotát.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdTime**  <br>*optional*  <br>*read-only*|Az aszinkron művelet létrehozásának időpontja (UTC).|karakterlánc (dátum-idő)|
|**endTime**  <br>*optional*  <br>*read-only*|Az aszinkron művelet befejezésének időpontja (UTC).|karakterlánc (dátum-idő)|
|**Hiba történt**  <br>*optional*||[Byl vrácen Prvek](#errorresponse)|
|**id**  <br>*optional*|Az aszinkron művelet azonosítója.|sztring|
|**operationType**  <br>*optional*|Az aszinkron művelet típusa.|Enum (kép, szolgáltatás)|
|**resourceLocation**  <br>*optional*|Erőforrás vagy frissítése hiányosan által az aszinkron művelet.|sztring|
|**állapot**  <br>*optional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
A hitelesítési kulcsokat, a szolgáltatás.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**primaryKey**  <br>*optional*|Elsődleges kulcs.|sztring|
|**secondaryKey**  <br>*optional*|Másodlagos kulcs.|sztring|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>Méretező
Az automatikus méretező beállításai.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**autoscaleEnabled**  <br>*optional*|Engedélyezi vagy letiltja az automatikus méretező.|logikai|
|**maxReplicas**  <br>*optional*|A podreplikák méretezését akár maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**minReplicas**  <br>*optional*|Vertikális le a podreplikák minimális számát.  <br>**Minimális érték**: `0`|egész szám|
|**refreshPeriodInSeconds**  <br>*optional*|Frissítés az automatikus skálázás eseményindító ideje.  <br>**Minimális érték**: `1`|egész szám|
|**targetUtilization**  <br>*optional*|Kihasználtság aránya, amely elindítja az automatikus skálázást.  <br>**Minimális érték**: `0`  <br>**Maximális érték**: `100`|egész szám|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
A Machine Learning számítási erőforrás.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**id**  <br>*optional*|Erőforrás-azonosítója.|sztring|
|**type**  <br>*optional*|Az erőforrás típusát.|Enum (fürt)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfiguráció egy tárolóhoz, a fürtben lévő erőforrásokat.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**cpu**  <br>*optional*|Megadja a CPU-foglalás. A Kubernetes esetében a formátum: lásd: [jelentése a CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|sztring|
|**Memória**  <br>*optional*|Adja meg a memória foglalás. A Kubernetes esetében a formátum: lásd: [memória jelentése](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|sztring|


<a name="deployment"></a>
### <a name="deployment"></a>Környezet
Az Azure Machine Learning központi telepítésének egy példányát.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*optional*  <br>*read-only*|Üzembe helyezés létrehozásának időpontja (UTC).|karakterlánc (dátum-idő)|
|**expiredAt**  <br>*optional*  <br>*read-only*|Üzembe helyezés lejárt időpontja (UTC).|karakterlánc (dátum-idő)|
|**id**  <br>*optional*|Központi telepítési azonosítója.|sztring|
|**imageId**  <br>*optional*|A központi telepítéshez társított lemezkép-azonosító.|sztring|
|**serviceName**  <br>*optional*|Szolgáltatás neve.|sztring|
|**Állapot**  <br>*optional*|Jelenlegi üzembe helyezési állapota.|sztring|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Központi telepítési objektumok egy tömbjét.

*Típus*: <[üzembe helyezési](#deployment)> tömb


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Minta felügyeleti szolgáltatás a hiba részletei.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Kód**  <br>*Szükséges*|Hibakód.|sztring|
|**üzenet**  <br>*Szükséges*|Hibaüzenet jelenik meg.|sztring|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>Byl vrácen Prvek
A Modellkezelési szolgáltatás hiba objektum.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Kód**  <br>*Szükséges*|Hibakód.|sztring|
|**Részletek**  <br>*optional*|Hiba részletei objektumok tömbje.|<[ErrorDetail](#errordetail)> tömb|
|**üzenet**  <br>*Szükséges*|Hibaüzenet jelenik meg.|sztring|
|**statusCode**  <br>*optional*|HTTP-állapotkódot.|egész szám|


<a name="image"></a>
### <a name="image"></a>Kép
Az Azure Machine Learning-lemezképet.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**computeResourceId**  <br>*optional*|A környezet létrehozása a Machine Learning számítási erőforrás azonosítója.|sztring|
|**createdTime**  <br>*optional*|Lemezkép létrehozásának időpontja (UTC).|karakterlánc (dátum-idő)|
|**creationState**  <br>*optional*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*optional*|Kép leíró szöveg.|sztring|
|**Hiba történt**  <br>*optional*||[Byl vrácen Prvek](#errorresponse)|
|**id**  <br>*optional*|Lemezkép-azonosító.|sztring|
|**imageBuildLogUri**  <br>*optional*|URI-ját a feltöltött naplók a rendszerkép összeállítását.|sztring|
|**imageLocation**  <br>*optional*|A létrehozott lemezkép az Azure Container Registry helye karakterlánca.|sztring|
|**ImageType**  <br>*optional*||[ImageType](#imagetype)|
|**Manifest**  <br>*optional*||[Manifest](#manifest)|
|**name**  <br>*optional*|Kép neve.|sztring|
|**Verzió**  <br>*optional*|Lemezkép verziója állítsa be a Modellkezelési szolgáltatás.|egész szám|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
Az Azure Machine Learning-lemezkép létrehozására irányuló kérelem.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**computeResourceId**  <br>*Szükséges*|A környezet létrehozása a Machine Learning számítási erőforrás azonosítója.|sztring|
|**description**  <br>*optional*|Kép leíró szöveg.|sztring|
|**ImageType**  <br>*Szükséges*||[ImageType](#imagetype)|
|**Jegyzékfájl azonosítója**  <br>*Szükséges*|A jegyzékfájl, amelyből létrehozza a kép azonosítója.|sztring|
|**name**  <br>*Szükséges*|Kép neve.|sztring|


<a name="imagetype"></a>
### <a name="imagetype"></a>Képtípus
Megadja a kép típusát.

*Típus*: enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Jegyzék
Az Azure Machine Learning-jegyzékfájlt.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Eszközök**  <br>*Szükséges*|Eszközök listája.|<[Az eszközintelligencia](#asset)> tömb|
|**createdTime**  <br>*optional*  <br>*read-only*|Jegyzékfájl létrehozásának időpontja (UTC).|karakterlánc (dátum-idő)|
|**description**  <br>*optional*|A leíró szöveg jegyzékfájl.|sztring|
|**driverProgram**  <br>*Szükséges*|A jegyzékfájl illesztőprogramra.|sztring|
|**id**  <br>*optional*|Jegyzékazonosítót.|sztring|
|**modelIds**  <br>*optional*|A regisztrált modellek modell-azonosítók listáját. A kérelem sikertelen lesz, ha a belefoglalt modellek bármelyikét nincs regisztrálva.|<string> Pole|
|**modelType**  <br>*optional*|Itt adhatja meg, hogy a modell már regisztrálva van a Modellkezelési szolgáltatásban.|Enum (ajánlott)|
|**name**  <br>*Szükséges*|Jegyzékfájl neve.|sztring|
|**targetRuntime**  <br>*Szükséges*||[TargetRuntime](#targetruntime)|
|**Verzió**  <br>*optional*  <br>*read-only*|A Modellkezelési szolgáltatás által hozzárendelt fürtjegyzék verziója.|egész szám|
|**webserviceType**  <br>*optional*|Megadja a kívánt webszolgáltatás, amelyet a-jegyzékfájlból jön.|Enum (valós idejű)|


<a name="model"></a>
### <a name="model"></a>Modell
Az Azure Machine Learning-modell egy példányát.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*optional*  <br>*read-only*|Modell-létrehozási időpontja (UTC).|karakterlánc (dátum-idő)|
|**description**  <br>*optional*|A leíró szöveg modellje.|sztring|
|**id**  <br>*optional*  <br>*read-only*|Forrásmodell azonosítóját.|sztring|
|**mimeType**  <br>*Szükséges*|MIME-típusát a modell tartalma. MIME-típus kapcsolatos további információkért lásd: a [IANA adathordozó-típusok listája](https://www.iana.org/assignments/media-types/media-types.xhtml).|sztring|
|**name**  <br>*Szükséges*|Modell neve.|sztring|
|**címkét**  <br>*optional*|Modell címkelista.|<string> Pole|
|**kicsomagolása**  <br>*optional*|Azt jelzi, hogy kell-e a modell csomagolja ki a Docker-rendszerkép létrehozása során.|logikai|
|**url**  <br>*Szükséges*|A modell URL-címe. Általában azt itt adja meg a közös hozzáférésű jogosultságkód URL-címet.|sztring|
|**Verzió**  <br>*optional*  <br>*read-only*|A Modellkezelési szolgáltatás által hozzárendelt modell verziója.|egész szám|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
A modell adatait gyűjtemény adatait.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**eventHubEnabled**  <br>*optional*|Engedélyezze a szolgáltatáshoz egy eseményközpontba.|logikai|
|**storageEnabled**  <br>*optional*|Engedélyezze a storage szolgáltatás.|logikai|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Rendszerképek listájának többoldalas.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-t) mutató hivatkozás a következő lapra az eredmények listájában.|sztring|
|**value**  <br>*optional*|Adatmodell-objektumokat tömbje.|<[Kép](#image)> tömb|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Jegyzékek többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-t) mutató hivatkozás a következő lapra az eredmények listájában.|sztring|
|**value**  <br>*optional*|Jegyzékfájl objektumok tömbje.|<[Manifest](#manifest)> tömb|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Modellek többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-t) mutató hivatkozás a következő lapra az eredmények listájában.|sztring|
|**value**  <br>*optional*|Adatmodell-objektumokat tömbje.|<[Modell](#model)> tömb|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Szolgáltatások többoldalas listáját.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**nextLink**  <br>*optional*|Folytatási (abszolút URI-t) mutató hivatkozás a következő lapra az eredmények listájában.|sztring|
|**value**  <br>*optional*|Szolgáltatási objektumok tömbje.|<[ServiceResponse](#serviceresponse)> tömb|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Egy kérelem egy szolgáltatás létrehozásához.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**appInsightsEnabled**  <br>*optional*|Az application insights szolgáltatás engedélyezése.|logikai|
|**autoScaler**  <br>*optional*||[Méretező](#autoscaler)|
|**ComputeResource**  <br>*Szükséges*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*optional*||[ContainerResourceReservation](#containerresourcereservation)|
|**datacollection objektumot**  <br>*optional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Szükséges*|Kép a szolgáltatás létrehozásához.|sztring|
|**maxConcurrentRequestsPerContainer**  <br>*optional*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**name**  <br>*Szükséges*|Szolgáltatás neve.|sztring|
|**numReplicas**  <br>*optional*|Bármikor futó podreplikák száma. Nelze zadat, ha engedélyezve van-e a méretező.  <br>**Minimális érték**: `0`|egész szám|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
A használatához a kulcs újragenerálása kérést.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**KeyType**  <br>*optional*|Itt adhatja meg, melyik kulcsot kell generálni.|Enum (elsődleges, másodlagos)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
A szolgáltatás állapotának részletes leírását.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**createdAt**  <br>*optional*|A szolgáltatás létrehozásának időpontja (UTC).|karakterlánc (dátum-idő)|
|**Azonosító**  <br>*optional*|Service ID.|sztring|
|**image**  <br>*optional*||[Rendszerkép](#image)|
|**Manifest**  <br>*optional*||[Manifest](#manifest)|
|**modellek**  <br>*optional*|A modellek listája.|<[Modell](#model)> tömb|
|**name**  <br>*optional*|Szolgáltatás neve.|sztring|
|**scoringUri**  <br>*optional*|A szolgáltatás pontozó URI.|sztring|
|**állapot**  <br>*optional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*optional*|Utolsó frissítés időpontja (UTC).|karakterlánc (dátum-idő)|
|**appInsightsEnabled**  <br>*optional*|Az application insights szolgáltatás engedélyezése.|logikai|
|**autoScaler**  <br>*optional*||[Méretező](#autoscaler)|
|**ComputeResource**  <br>*Szükséges*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*optional*||[ContainerResourceReservation](#containerresourcereservation)|
|**datacollection objektumot**  <br>*optional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*optional*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**numReplicas**  <br>*optional*|Bármikor futó podreplikák száma. Nelze zadat, ha engedélyezve van-e a méretező.  <br>**Minimális érték**: `0`|egész szám|
|**Hiba történt**  <br>*optional*||[Byl vrácen Prvek](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
A szolgáltatás frissítésére irányuló kérelem.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**appInsightsEnabled**  <br>*optional*|Az application insights szolgáltatás engedélyezése.|logikai|
|**autoScaler**  <br>*optional*||[Méretező](#autoscaler)|
|**containerResourceReservation**  <br>*optional*||[ContainerResourceReservation](#containerresourcereservation)|
|**datacollection objektumot**  <br>*optional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*optional*|Kép a szolgáltatás létrehozásához.|sztring|
|**maxConcurrentRequestsPerContainer**  <br>*optional*|Egyidejű kérelmek maximális számát.  <br>**Minimális érték**: `1`|egész szám|
|**numReplicas**  <br>*optional*|Bármikor futó podreplikák száma. Nelze zadat, ha engedélyezve van-e a méretező.  <br>**Minimális érték**: `0`|egész szám|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
A cél futtatókörnyezet típusa.


|Name (Név)|Leírás|Séma|
|---|---|---|
|**Tulajdonságok**  <br>*Szükséges*||< string, string > térkép|
|**runtimeType**  <br>*Szükséges*|Adja meg a futtatókörnyezet.|Enum (SparkPython, Python)|

