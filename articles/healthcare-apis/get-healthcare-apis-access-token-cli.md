---
title: Hozzáférési jogkivonat lekérése az Azure CLI-vel – Azure API a FHIR-hez
description: Ez a cikk azt ismerteti, hogyan szerezhet be hozzáférési tokent a FHIR készült Azure API-hoz az Azure CLI használatával.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: 4eb4c10a6c98aa847c9fa6c239aacde891db5aae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871895"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Hozzáférési jogkivonat beszerzése a FHIR készült Azure API-hoz az Azure CLI használatával

Ebből a cikkből megtudhatja, hogyan szerezhet be hozzáférési jogkivonatot a FHIR készült Azure API-hoz az Azure CLI használatával. Amikor [kiépíti az Azure API](fhir-paas-portal-quickstart.md)-t a FHIR-hoz, olyan felhasználók vagy egyszerű szolgáltatások készletét konfigurálja, amelyek hozzáférnek a szolgáltatáshoz. Ha a felhasználói objektumazonosító szerepel az engedélyezett objektumazonosítók listáján, akkor az Azure CLI használatával kapott token használatával férhet hozzá a szolgáltatáshoz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Bejelentkezés az Azure CLI használatával

A token beszerzése előtt be kell jelentkeznie arra a felhasználóra, akinek jogkivonatot szeretne beszerezni:

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>Jogkivonat beszerzése

A FHIR készült Azure API a `resource` vagy a `Audience` FHIR-kiszolgáló URI azonosítójának megfelelő URI-t használ `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . A jogkivonat beszerzése és tárolása egy változóban (nevű `$token` ) a következő paranccsal végezhető el:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com | jq -r .accessToken)
```

## <a name="use-with-azure-api-for-fhir"></a>Használat az Azure API-val a FHIR-hez

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan szerezhet be hozzáférési jogkivonatot a FHIR készült Azure API-hoz az Azure CLI használatával. Ha szeretné megtudni, hogyan érheti el a FHIR API-t a Poster használatával, folytassa a Poster-oktatóanyaggal.

>[!div class="nextstepaction"]
>[FHIR API elérése a Poster használatával](access-fhir-postman-tutorial.md)