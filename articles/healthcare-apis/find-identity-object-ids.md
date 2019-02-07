---
title: Hitelesítés – az Azure API FHIR-identitás objektumazonosítójának található
description: Ez a cikk azt ismerteti, hogyan keresse meg az identitás objektumazonosítók FHIR Azure API-hoz hitelesítésének konfigurálásához szükséges
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: b120b21a89a7105a25ba610402da99f9dce4b7e1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824102"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Található az identitás objektumazonosítójának hitelesítési konfiguráció

Ebből a cikkből megismerheti, hogyan található identitás objektumazonosítójának engedélyezett identitás objektumazonosítók listájának konfigurálása az Azure API-hoz a FHIR lesz.

A teljes körűen felügyelt Azure API FHIR&reg; szolgáltatás engedélyezi a hozzáférést csak előre meghatározott listát az identitás objektumazonosítójának van konfigurálva. Ha egy alkalmazás vagy a felhasználó megpróbál hozzáférni a FHIR API, a tulajdonosi jogkivonatot kell bemutatni. A tulajdonosi jogkivonatot kell bizonyos jogcímek (mezők). Annak érdekében, hogy hozzáférést biztosítani a FHIR API, a jogkivonat tartalmaznia kell a megfelelő kiállítótól (`iss`), célközönség (`aud`), és a egy objektum azonosítója (`oid`) engedélyezett az objektumazonosítók listájából. Identitás Objektumazonosító, vagy a felhasználó Objektumazonosítóját vagy az Azure Active Directory egyszerű szolgáltatás.

## <a name="configure-list-of-allowed-object-ids"></a>Engedélyezett objektumazonosítók listájának konfigurálása

Amikor létrehoz egy új Azure API FHIR-példány, engedélyezett objektumazonosítók listájának konfigurálhatja:

![Engedélyezett objektumazonosítók konfigurálása](media/quickstart-paas-portal/configure-allowed-oids.png)

Ezek az objektumazonosítók azonosítók adott felhasználók vagy az Azure Active Directory egyszerű szolgáltatások lehet.

## <a name="find-user-object-id-using-powershell"></a>Keresse meg a PowerShell használatával a felhasználói objektum azonosítója

Ha egy felhasználó felhasználónévvel rendelkezik `myuser@consoso.com`, a felhasználók megkereséséhez `ObjectId` a következő PowerShell-paranccsal:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

vagy használhatja az Azure CLI-vel:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id-using-powershell"></a>Keresse meg a azonosítója PowerShell-lel

Tegyük fel, hogy regisztrált egy [ügyfélalkalmazás szolgáltatás](register-service-azure-ad-client-app.md) , és szeretné, hogy a hozzáférés az Azure API-FHIR szolgáltatásügyfél, annak Objektumazonosítóját az ügyfél a következő PowerShell-parancsot a szolgáltatásnév:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

ahol `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` a szolgáltatás ügyfél-alkalmazás azonosítója. Másik lehetőségként használhatja a `DisplayName` a szolgáltatási ügyfél:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Ha az Azure CLI-vel használ, akkor használhatja:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben bemutattuk, identitás objektumazonosítók konfigurálása az identitások, amelyek jogosultak hozzáférni az Azure API-FHIR-példány keresése. Ezután üzembe helyezése az Azure teljes körűen felügyelt API FHIR:
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló telepítése](fhir-paas-portal-quickstart.md)