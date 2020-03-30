---
title: Háttérszolgáltatások biztonságossá tétele az ügyféltanúsítvány-hitelesítés használatával
titleSuffix: Azure API Management
description: Ismerje meg, hogyan biztosíthat háttérszolgáltatásokat az Azure API Management ben az ügyféltanúsítvány-hitelesítés használatával.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347107"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Háttérszolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az Azure API Managementben

Az API Management lehetővé teszi az API-k háttérszolgáltatásához való biztonságos hozzáférést az ügyféltanúsítványok használatával. Ez az útmutató bemutatja, hogyan kezelheti a tanúsítványokat az Azure API Management szolgáltatáspéldány az Azure Portalon. Azt is ismerteti, hogyan konfigurálhatja az API-t egy tanúsítvány használatával egy háttérszolgáltatás eléréséhez.

Az API Management REST API használatával a tanúsítványok kezeléséről az <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API-kezelés REST API-tanúsítvány entitása című témakörben</a>talál további információt.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldány t az ügyféltanúsítvány-hitelesítés használatával egy API-hoz a háttérszolgáltatás eléréséhez. A cikkben ismertetett lépések beépítése előtt a háttérszolgáltatásnak konfigurálnia kell az ügyféltanúsítvány-hitelesítést[(az Azure App Service tanúsítványhitelesítésének konfigurálásához olvassa el ezt a cikket).][to configure certificate authentication in Azure WebSites refer to this article] Az API Management szolgáltatásba való feltöltéshez hozzá kell férnie a tanúsítványhoz és a jelszóhoz.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Tanúsítvány feltöltése

> [!NOTE]
> A feltöltött tanúsítvány helyett az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) szolgáltatásban tárolt tanúsítványt is használhatja, ahogy az ebben a [példában](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml)látható.

![Ügyféltanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Új ügyféltanúsítvány feltöltéséhez kövesse az alábbi lépéseket. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [OKTATÓanyag Egy API Management szolgáltatáspéldány létrehozása című témakört.][Create an API Management service instance]

1. Keresse meg az Azure API Management szolgáltatáspéldányát az Azure Portalon.
2. Válassza a menü **Tanúsítványok** parancsát.
3. Kattintson a **+ Hozzáadás** gombra.
    ![Ügyféltanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Keresse meg a tanúsítványt, adja meg azonosítóját és jelszavát.
5. Kattintson **a Létrehozás gombra.**

> [!NOTE]
> A tanúsítványnak **.pfx** formátumúnak kell lennie. Az önaláírt tanúsítványok engedélyezettek.

A tanúsítvány feltöltése után megjelenik a **Tanúsítványok .**  Ha sok tanúsítvánnyal rendelkezik, jegyezze fel a kívánt tanúsítvány ujjlenyomatát annak [érdekében, hogy konfiguráljon egy API-t egy ügyféltanúsítvány használatára az átjáró hitelesítéséhez.][Configure an API to use a client certificate for gateway authentication]

> [!NOTE]
> Ha például önaláírt tanúsítvány használata esetén ki szeretné kapcsolni a tanúsítványlánc-ellenőrzést, kövesse a gyIK [elemben](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)leírt lépéseket.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ügyféltanúsítvány törlése

Tanúsítvány törléséhez kattintson a helyi **menüre ...** és válassza a **Törlés** parancsot a tanúsítvány mellett.

![Ügyféltanúsítványok törlése](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Ha a tanúsítványt egy API használja, akkor egy figyelmeztető képernyő jelenik meg. A tanúsítvány törléséhez először el kell távolítania a tanúsítványt minden olyan API-ból, amely úgy van beállítva, hogy használja azt.

![Ügyféltanúsítványok törlése hiba](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>API konfigurálása ügyféltanúsítvány használatára az átjáró hitelesítéséhez

1. Kattintson az **API-k** a bal oldali **API Management** menüben, és keresse meg az API-t.
    ![Ügyféltanúsítványok engedélyezése](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. A **Tervezés** lapon kattintson a **Háttérterület** ceruza ikonjára.
3. Módosítsa az **átjáró hitelesítő adatait** **ügyféltanúsítványra,** és válassza ki a tanúsítványt a legördülő menüből.
    ![Ügyféltanúsítványok engedélyezése](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kattintson a **Mentés** gombra.

> [!WARNING]
> Ez a módosítás azonnal hatályba lép, és az API műveleteinek hívásai a tanúsítványt fogják használni a háttérkiszolgálón való hitelesítéshez.


> [!TIP]
> Ha egy tanúsítvány meg van adva az API háttérszolgáltatásához az átjáróhitelesítéshez, az az adott API házirendjének részévé válik, és megtekinthető a házirend-szerkesztőben.

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokat használ, le kell tiltania a tanúsítványlánc-érvényesítést ahhoz, hogy az API Management kommunikáljon a háttérrendszerrel. Ellenkező esetben 500-as hibakódot ad vissza. Ennek konfigurálásához használhatja [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) az (új háttérrendszerhez) vagy [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (meglévő háttérrendszerhez) PowerShell-parancsmagokat, és beállíthatja a `-SkipCertificateChainValidation` paramétert. `True`

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
