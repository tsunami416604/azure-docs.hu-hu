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
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 844a7ea1c2dd8f7dbb4984fc148575529ac154db
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292857"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Az Azure API Management tanúsítványhitelesítés biztonságossá tétele a háttér-szolgáltatásaihoz ügyfél használatával

API-kezelés lehetővé teszi az API-k a háttérszolgáltatáshoz való hozzáférés az ügyféltanúsítványok. Ez az útmutató bemutatja, hogyan kezelheti az Azure-portálon az Azure API Management szolgáltatáspéldány tanúsítványokat. Azt is bemutatja egy API-t egy háttér-szolgáltatás eléréséhez használható tanúsítvány konfigurálása.

Tanúsítványok, az API Management REST API használatával történő kezelésével kapcsolatos információkért lásd: <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API tanúsítvány entitás</a>.

## <a name="prerequisites"> </a>Előfeltételek

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldány ügyféltanúsítvány-alapú hitelesítés használatát a háttér-szolgáltatás számára az API-k eléréséhez. Ebben a cikkben szereplő lépések végrehajtása előtt rendelkeznie kell a háttér-szolgáltatás ügyféltanúsítvány-alapú hitelesítés beállítása ([konfigurálása az Azure Websitesra tanúsítványhitelesítés tekintse meg a cikk] [ to configure certificate authentication in Azure WebSites refer to this article]). Feltölti az API Management szolgáltatás van szüksége a tanúsítvány és a jelszó a hozzáférést.

## <a name="step1"> </a>Egy ügyfél-tanúsítvány feltöltése

![Ügyféltanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Kövesse az alábbi lépéseket egy új ügyfél-tanúsítvány feltöltése. Ha az API Management szolgáltatáspéldány még nem létrehozott, tekintse meg az [hozzon létre egy API-kezelés szolgáltatás példányt][Create an API Management service instance].

1. Nyissa meg az Azure API Management szolgáltatáspéldány, az Azure portálon.
2. Válassza ki **ügyféltanúsítványok** a menüből.
3. Kattintson a **+ Hozzáadás** gombra.  
    ![Ügyféltanúsítványok hozzáadása](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Tallózással keresse meg a tanúsítványt, adja meg az Azonosítót és jelszót.  
5. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> A tanúsítványnak kell lennie a **.pfx** formátumban. Önaláírt tanúsítványok használata engedélyezett.

A tanúsítvány a feltöltést követően a mutatja a **ügyféltanúsítványok**.  Ha sok tanúsítvány, jegyezze fel annak érdekében, hogy a kívánt tanúsítvány ujjlenyomatának [átjáró hitelesítéshez használandó ügyféltanúsítványt API konfigurálása][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Kapcsolja ki a tanúsítványlánc érvényesítése használata esetén például egy önaláírt tanúsítványt, kövesse a lépéseket, ez a GYIK ismertetett [elem](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Egy ügyfél-tanúsítvány törlése

Törli a tanúsítványt, kattintson a helyi menü **...**  válassza **törlése** mellett a tanúsítványt.

![Ügyféltanúsítványok törlése](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Ha a tanúsítvány egy API-t használja, majd egy figyelmeztetés képernyő jelenik meg. Törli a tanúsítványt, el kell távolítani a tanúsítványt bármely olyan API-, a használatára konfigurált.

![Ügyfél-tanúsítványok hiba törlése](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Átjáró hitelesítéshez használandó ügyféltanúsítványt API konfigurálása

1. Kattintson a **API-k** a a **API Management** a bal oldali menüben, és keresse meg az API-t.  
    ![Engedélyezze az ügyféltanúsítványok](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Az a **tervezési** fülre, kattintson a ceruza ikonra a a **háttér** szakasz. 
3. Módosítsa a **átjáró hitelesítő adatok** való **ügyféltanúsítványt** és a legördülő listából válassza ki a tanúsítványt.  
    ![Engedélyezze az ügyféltanúsítványok](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kattintson a **Save** (Mentés) gombra. 

> [!WARNING]
> Ez a változás a következő időponttól érvényes azonnal, és az adott API műveletek hívások a tanúsítványt használni kívánt telefonszámot a háttér-kiszolgálón.


> [!TIP]
> Egy tanúsítványt egy API-t az átjáró a háttér-szolgáltatás hitelesítéséhez megadása esetén a szabályzat részévé válik, hogy az API-hoz, és tekintheti meg a Helyicsoportházirend-szerkesztő.

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokat használ, akkor ahhoz, hogy az API Management kommunikálni a háttérrendszer tanúsítványlánc érvényesítésének letiltása. Ellenkező esetben egy 500 hibakódot ad vissza. Ennek beállításához használhatja a [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (az új háttér) vagy [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (a meglévő háttér) PowerShell-parancsmagok és állítsa be a `-SkipCertificateChainValidation` paramétert `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
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

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
