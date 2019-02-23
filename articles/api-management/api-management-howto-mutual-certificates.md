---
title: Ügyfél háttérszolgáltatások biztonságossá Tanúsítványalapú hitelesítés – az Azure API Management |} A Microsoft Docs
description: Ügyféltanúsítvány-alapú hitelesítés használata az Azure API Management háttérszolgáltatások biztonságossá tételének ismertetése.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 13a2eb080c6822a8a6786be1952bc588fa8afd80
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729203"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Hogyan teheti biztonságossá a háttérszolgáltatások ügyfél Tanúsítványalapú hitelesítés az Azure API Management

Az API Management lehetővé teszi, hogy a biztonságos hozzáférés az API-háttérszolgáltatás ügyféltanúsítványok használatát. Ez az útmutató bemutatja, hogyan kezelheti a tanúsítványokat az Azure API Management szolgáltatáspéldányt, az Azure Portalon. Emellett ismerteti, hogyan konfigurálhatja az API-t használja egy tanúsítványt egy háttér-szolgáltatás eléréséhez.

Az API Management REST API használatával tanúsítványok kezelésével kapcsolatos információkért lásd: <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API-tanúsítvány entitás</a>.

## <a name="prerequisites"> </a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldányhoz ügyféltanúsítvány-alapú hitelesítés használatát a háttér-szolgáltatás API-k eléréséhez. Ebben a cikkben található lépések elvégzése előtt rendelkeznie kell az ügyféltanúsítvány-alapú hitelesítés konfigurált háttérszolgáltatás ([konfigurálása az Azure-webhelyeken tanúsítványhitelesítés tekintse meg ebben a cikkben] [ to configure certificate authentication in Azure WebSites refer to this article]). Feltölteni az API Management szolgáltatás a hozzáférést a tanúsítvány és a jelszó szükséges.

## <a name="step1"> </a>Ügyfél-tanúsítvány feltöltése

![Ügyfél-tanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Kövesse az alábbi lépéseket egy új ügyfél-tanúsítvány feltöltéséhez. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az oktatóanyag [az API Management szolgáltatáspéldány létrehozása][Create an API Management service instance].

1. Keresse meg az Azure API Management szolgáltatáspéldányt, az Azure Portalon.
2. Válassza ki **ügyféltanúsítványok** a menüből.
3. Kattintson a **+ Hozzáadás** gombra.  
    ![Ügyfél-tanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Keresse meg a tanúsítványt, adja meg az Azonosítót és jelszót.  
5. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> A tanúsítványnak kell lennie a **.pfx** formátumban. Önaláírt tanúsítvány adható meg.

Miután feltöltötte a tanúsítványt, a mutatja a **ügyféltanúsítványok**.  Ha sok tanúsítvány, jegyezze fel annak érdekében, hogy a kívánt tanúsítvány ujjlenyomatát [átjáró hitelesítéshez használandó ügyféltanúsítványt API konfigurálása][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Ki szeretné kapcsolni a tanúsítványláncok érvényesítése használatakor, például egy önaláírt tanúsítványt, ez a GYIK a ismertetett lépéseket követve [elem](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Ügyfél-tanúsítvány törlése

A tanúsítvány törléséhez kattintson a helyi menü **...**  válassza **törlése** mellett a tanúsítványt.

![Ügyfél-tanúsítványok törlése](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Ha a tanúsítvány egy API-t használja, akkor a figyelmeztetési képernyő jelenik meg. A tanúsítvány törléséhez először távolítsa el a tanúsítványt bármely API-kon keresztül a használatára konfigurált.

![Ügyfél-tanúsítványok hiba törlése](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Átjáró-hitelesítéshez használandó ügyféltanúsítványt API konfigurálása

1. Kattintson a **API-k** származó a **az API Management** a bal oldali menüben, és keresse meg az API-t.  
    ![Engedélyezze az ügyféltanúsítványok használatát](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Az a **tervezési** fülre, kattintson a ceruza ikonra, az a **háttérrendszer** szakaszban. 
3. Módosítsa a **átjáró hitelesítő adatok** való **ügyféltanúsítvány** és a legördülő listából válassza ki a tanúsítványt.  
    ![Engedélyezze az ügyféltanúsítványok használatát](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kattintson a **Save** (Mentés) gombra. 

> [!WARNING]
> Ez a változás azonnal érvényben, és a műveletek az API-hívások a tanúsítvány hitelesítéséhez használandó a háttér-kiszolgálón.


> [!TIP]
> Átjáró-hitelesítési a háttér-szolgáltatás API-tanúsítvány megadása esetén a szabályzat részévé válik, hogy API-hoz, és a házirendszerkesztőben lehet megtekinteni.

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokat használ, szüksége lesz ahhoz, hogy a háttérrendszer kommunikálni az API Management tanúsítványlánc érvényesítésének letiltása. Ellenkező esetben egy 500 hibakódot ad vissza. Ennek konfigurálásához használhatja a [ `New-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (az új háttér-) vagy [ `Set-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (a meglévő háttér) PowerShell-parancsmagok és állítsa be a `-SkipCertificateChainValidation` paramétert `True`.

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
