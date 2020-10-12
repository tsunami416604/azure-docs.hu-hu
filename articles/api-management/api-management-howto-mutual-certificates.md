---
title: Az ügyféltanúsítvány-alapú hitelesítés használatával biztosíthatja a háttérbeli szolgáltatásokat
titleSuffix: Azure API Management
description: Ismerje meg, hogyan védheti a háttér-szolgáltatásokat az ügyféltanúsítvány-alapú hitelesítés használatával az Azure API Managementban.
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
ms.openlocfilehash: 8e02a47cd6ae6e4883b5113b07d4049cd723232d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250192"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Háttérszolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az Azure API Managementben

API Management lehetővé teszi, hogy az Ügyféltanúsítványok segítségével biztonságossá tegye az API-k háttér-szolgáltatásának elérését. Ez az útmutató bemutatja, hogyan kezelheti a tanúsítványokat az Azure API Management Service-példányban a Azure Portalban. Azt is ismerteti, hogyan konfigurálhat egy API-t egy tanúsítvány használatára a háttér-szolgáltatás eléréséhez.

A tanúsítványok a API Management REST API használatával történő kezelésével kapcsolatos információkért lásd: <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API Certificate Entity</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management-szolgáltatási példányt az ügyféltanúsítvány-alapú hitelesítés használatára a háttér-szolgáltatás API-hoz való hozzáféréséhez. A cikkben ismertetett lépések végrehajtása előtt rendelkeznie kell az ügyféltanúsítvány-alapú hitelesítéshez konfigurált háttér-szolgáltatással (a[tanúsítvány-hitelesítés konfigurálásához a Azure app Service tekintse meg ezt a cikket][to configure certificate authentication in Azure WebSites refer to this article]). Hozzá kell férnie a tanúsítványhoz és a jelszóhoz a API Management szolgáltatásba való feltöltéshez.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Tanúsítvány feltöltése

> [!NOTE]
> Feltöltött tanúsítvány helyett használhat a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) szolgáltatásban tárolt tanúsítványt, ahogy az ebben a [példában](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml)is látható.

![Ügyféltanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Új ügyféltanúsítvány feltöltéséhez kövesse az alábbi lépéseket. Ha még nem hozott létre API Management Service-példányt, tekintse meg az [API Management Service-példány létrehozása][Create an API Management service instance]című oktatóanyagot.

1. Navigáljon az Azure API Management Service-példányhoz a Azure Portal.
2. Válassza a **tanúsítványok** lehetőséget a menüből.
3. Kattintson a **+ Hozzáadás** gombra.
    ![Ügyféltanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Keresse meg a tanúsítványt, adja meg annak AZONOSÍTÓját és jelszavát.
5. Kattintson a **Létrehozás** elemre.

> [!NOTE]
> A tanúsítványnak **. pfx** formátumúnak kell lennie. Az önaláírt tanúsítványok engedélyezettek.

A tanúsítvány feltöltése után megjelenik **a tanúsítványokban.**  Ha sok tanúsítványa van, jegyezze fel a kívánt tanúsítvány ujjlenyomatát, hogy az [API-t úgy konfigurálja, hogy az ügyfél tanúsítványát használja az átjáró-hitelesítéshez][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Ha például önaláírt tanúsítványt használ a tanúsítványlánc-ellenőrzés kikapcsolásához, kövesse az ebben a GYIK- [elemben](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)ismertetett lépéseket.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ügyféltanúsítvány törlése

A tanúsítvány törléséhez kattintson a helyi menü **...** lehetőségre, és válassza a **Törlés** lehetőséget a tanúsítvány mellett.

![Ügyféltanúsítványok törlése](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Ha a tanúsítványt egy API használja, megjelenik egy figyelmeztető képernyő. A tanúsítvány törléséhez először el kell távolítania a tanúsítványt a használatára konfigurált API-kkal.

![Ügyféltanúsítvány törlésének hibája](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>API konfigurálása ügyféltanúsítvány használatára az átjáró-hitelesítéshez

1. Kattintson a bal oldali **API Management** menüjében **az API-** k elemre, és navigáljon az API-hoz.
    ![Ügyféltanúsítványok engedélyezése](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. A **tervezés** lapon kattintson a **háttér** szakasz ceruza ikonjára.
3. Módosítsa az **átjáró hitelesítő adatait** az **ügyfél-tanúsítványra** , és válassza ki a tanúsítványt a legördülő listából.
    ![Ügyféltanúsítványok engedélyezése](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kattintson a **Mentés** gombra.

> [!WARNING]
> Ez a változás azonnal hatályba lép, és az API műveleteinek meghívása a tanúsítványt használja a háttér-kiszolgálón történő hitelesítéshez.


> [!TIP]
> Ha egy API háttér-szolgáltatásához tanúsítvány van megadva az átjáró-hitelesítéshez, az az adott API-hoz tartozó szabályzat részévé válik, és megtekinthető a házirend-szerkesztőben.

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokat használ, le kell tiltania a tanúsítványlánc érvényesítését annak érdekében, hogy a API Management kommunikálni tudjon a háttérrendszer-rendszerrel. Ellenkező esetben 500 hibakódot ad vissza. Ennek konfigurálásához használhatja az [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (új háttérrendszer) vagy a [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (meglévő háttérrendszer) PowerShell-parancsmagokat, és beállíthatja a paramétert a következőre: `-SkipCertificateChainValidation` `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
