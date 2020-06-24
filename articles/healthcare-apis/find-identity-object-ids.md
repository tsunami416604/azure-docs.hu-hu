---
title: Azonosító objektumok azonosítóinak keresése a hitelesítéshez – Azure API for FHIR
description: Ez a cikk azt ismerteti, hogyan lehet megkeresni a FHIR Azure API-hoz való hitelesítésének konfigurálásához szükséges azonosító objektumok azonosítóit
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 88c0349ce220229920a39ba4cb6640f3d4dde93b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "84871937"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Azonosító objektumok azonosítóinak keresése a hitelesítési konfigurációhoz

Ebből a cikkből megtudhatja, hogyan találhatja meg az Azure API FHIR való konfigurálásához szükséges Identity Object-azonosítókat, hogy [külső vagy másodlagos Active Directory bérlőt használjon](configure-local-rbac.md) adatsíkon.

## <a name="find-user-object-id"></a>Felhasználói objektum AZONOSÍTÓjának keresése

Ha a felhasználó felhasználónevet tartalmaz, a `myuser@consoso.com` következő PowerShell-paranccsal keresheti meg a felhasználókat `ObjectId` :

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

vagy használhatja az Azure CLI-t is:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id"></a>Szolgáltatásnév objektum AZONOSÍTÓjának keresése

Tegyük fel, hogy regisztrált egy [szolgáltatás-ügyfélalkalmazás](register-service-azure-ad-client-app.md) , és szeretné engedélyezni a szolgáltatás ügyfelének számára a FHIR készült Azure API elérését, a következő PowerShell-paranccsal megkeresheti az ügyfél-szolgáltatásnév objektum-azonosítóját:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

hol `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` található a szolgáltatás ügyfélalkalmazás-azonosítója. Azt is megteheti, hogy a `DisplayName` szolgáltatás ügyfelét használja:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Ha az Azure CLI-t használja, használhatja a következőket:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="find-a-security-group-object-id"></a>Biztonsági csoport objektum-AZONOSÍTÓjának keresése

Ha meg szeretné találni egy biztonsági csoport objektumazonosítóát, használja a következő PowerShell-parancsot:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Ahol a az `mygroup` Önt érdeklő csoport neve.

Ha az Azure CLI-t használja, használhatja a következőket:

```azurecli-interactive
az ad group show --group "mygroup" | jq -r .objectId
```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan találhatja meg az Azure API FHIR való konfigurálásához szükséges Identity Object-azonosítókat külső vagy másodlagos Azure Active Directory bérlő használatához. Ezután olvassa el, hogyan használhatók az objektumazonosítók a helyi RBAC-beállítások konfigurálásához:
 
>[!div class="nextstepaction"]
>[Helyi RBAC-beállítások konfigurálása](configure-local-rbac.md)