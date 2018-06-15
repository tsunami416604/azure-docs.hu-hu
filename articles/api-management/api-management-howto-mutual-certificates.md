---
title: Háttér-szolgáltatásaihoz ügyfél használatával biztonságos tanúsítványhitelesítés - Azure API Management |} Microsoft Docs
description: Megtudhatja, mennyire biztonságos a háttér-szolgáltatásaihoz ügyféltanúsítvány-alapú hitelesítés használata az Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: c3060765022cabcb877041927886b59d6725c7cf
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204190"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Az Azure API Management tanúsítványhitelesítés biztonságossá tétele a háttér-szolgáltatásaihoz ügyfél használatával
API-kezelés lehetővé teszi a biztonságos hozzáférés a háttér-szolgáltatásra, az API-k az ügyféltanúsítványok. Ez az útmutató bemutatja az API-közzétevő portálon tanúsítványok kezelése és konfigurálása egy API-t egy tanúsítványt a háttér-szolgáltatás elérésére használhat.

Tanúsítványok, az API Management REST API használatával történő kezelésével kapcsolatos információkért lásd: <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API tanúsítvány entitás</a>.

## <a name="prerequisites"> </a>Előfeltételek
Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldány ügyféltanúsítvány-alapú hitelesítés használatát a háttér-szolgáltatás számára az API-k eléréséhez. Ez a témakör a lépések végrehajtása előtt rendelkeznie kell a háttér-szolgáltatás ügyféltanúsítvány-alapú hitelesítés beállítása ([konfigurálása az Azure Websitesra tanúsítványhitelesítés tekintse meg a cikk][to configure certificate authentication in Azure WebSites refer to this article]), és a tanúsítvány feltöltése az API Management publisher portálon a tanúsítvány és a jelszó hozzáféréssel rendelkeznek.

## <a name="step1"> </a>Egy ügyfél-tanúsítvány feltöltése
Első lépésként kattintson a **Közzétevő portál** elemre az API Management szolgáltatás Azure Portalján. Ezzel továbblép az API Management közzétevő portáljára.

![API-közzétevő portál][api-management-management-console]

> Ha még nem hozott létre az API Management szolgáltatáspéldány, lásd: [hozzon létre egy API-kezelés szolgáltatás példányt][Create an API Management service instance].
> 
> 

Kattintson a **biztonsági** a a **API Management** menüt, és kattintson **ügyféltanúsítványok**.

![Ügyféltanúsítványok][api-management-security-client-certificates]

Töltsön fel új tanúsítványt, kattintson a **feltöltés tanúsítvány**.

![Tanúsítvány feltöltése][api-management-upload-certificate]

Keresse meg a tanúsítványt, és írja be a jelszót a tanúsítványhoz.

> A tanúsítványnak kell lennie a **.pfx** formátumban. Önaláírt tanúsítványok használata engedélyezett.
> 
> 

![Tanúsítvány feltöltése][api-management-upload-certificate-form]

Kattintson a **feltöltése** töltse fel a tanúsítványt.

> A tanúsítvány jelszava most van hitelesítve. Ha az nem megfelelő egy hibaüzenet jelenik meg.
> 
> 

![Feltöltött tanúsítvány][api-management-certificate-uploaded]

A tanúsítvány a feltöltést követően megjelenik az **ügyféltanúsítványok** fülre. Ha több tanúsítvány, akkor a tulajdonos jegyezze fel, vagy az ujjlenyomat az utolsó négy számjegyét, válassza ki a tanúsítványt egy API-t a tanúsítványok, konfigurálásakor, az alábbi szabályozott módon használt [konfigurálása egy API-t használja a ügyféltanúsítvány a átjáró hitelesítési] [ Configure an API to use a client certificate for gateway authentication] szakasz.

> Kapcsolja ki a tanúsítványlánc érvényesítése használata esetén például egy önaláírt tanúsítványt, kövesse a lépéseket, ez a GYIK ismertetett [elem](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).
> 
> 

## <a name="step1a"> </a>Egy ügyfél-tanúsítvány törlése
Törli a tanúsítványt, kattintson a **törlése** mellett a kívánt tanúsítványt.

![Tanúsítvány törlése][api-management-certificate-delete]

Kattintson a **Igen, törölje azt** megerősítéséhez.

![Törlés megerősítése][api-management-confirm-delete]

Ha a tanúsítvány egy API-t használja, majd egy figyelmeztetés képernyő jelenik meg. A tanúsítvány törlése el kell távolítani a tanúsítványt bármely olyan API-, a használatára konfigurált.

![Törlés megerősítése][api-management-confirm-delete-policy]

## <a name="step2"> </a>Átjáró hitelesítéshez használandó ügyféltanúsítványt API konfigurálása
Kattintson a **API-k** a a **API Management** a bal oldali menüben kattintson a kívánt API nevére, majd kattintson a **biztonsági** fülre.

![API-biztonsági][api-management-api-security]

Válassza ki **ügyféltanúsítványok** a a **hitelesítő adatokkal rendelkező** legördülő listából.

![Ügyféltanúsítványok][api-management-mutual-certificates]

A kívánt tanúsítvány kiválasztása a **ügyféltanúsítvány** legördülő listából. Ha több tanúsítvány vessen egy pillantást a megfelelő tanúsítványt a tulajdonos vagy az ujjlenyomat az előző szakaszban leírtaknak megfelelően utolsó négy számjegyét.

![Válasszon tanúsítványt][api-management-select-certificate]

Kattintson a **mentése** menteni a konfiguráció módosítását az API-hoz.

> Ez a változás a következő időponttól érvényes azonnal, és az adott API műveletek hívások a tanúsítványt használni kívánt telefonszámot a háttér-kiszolgálón.
> 
> 

![API-módosítások mentése][api-management-save-api]

> Egy tanúsítványt egy API-t az átjáró a háttér-szolgáltatás hitelesítéséhez megadása esetén a szabályzat részévé válik, hogy az API-hoz, és tekintheti meg a Helyicsoportházirend-szerkesztő.
> 
> 

![Tanúsítványszabályzat][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokat használ, szüksége lesz ahhoz, hogy az API Management kommunikálni a háttérrendszer tanúsítványlánc érvényesítésének letiltása, ellenkező esetben egy 500 hibakódot ad vissza. Ennek beállításához használhatja a [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (az új háttér) vagy [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (a meglévő háttér) PowerShell-parancsmagok és állítsa be a `-SkipCertificateChainValidation` paramétert `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



