---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: f98f750c14666400f41a249b3d8379b423e6dee2
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572250"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Az SSL-tanúsítványok a TLS-kézfogás az APIM-Proxy kiszolgáló reakciója

### <a name="clients-calling-with-sni-header"></a>Fejléc SNI-hívó ügyfelek
Az ügyfél konfigurálva Proxy egy vagy több egyéni tartománnyal rendelkezik, APIM válaszolni tud HTTPS-kéréseket az egyéni tartomány (például contoso.com), valamint alapértelmezett tartomány (például az apim-szolgáltatás-name.azure-API.NET webhelyen). APIM válaszként a kiszolgálónév jelzése (SNI) fejléc adatai alapján a megfelelő kiszolgálói tanúsítványt.

### <a name="clients-calling-without-sni-header"></a>SNI-fejléc nélküli meghívó ügyfelek
Ha az ügyfél használja az ügyfél, amely nem küld a [SNI](https://tools.ietf.org/html/rfc6066#section-3) fejléc APIM válaszok a következő logika alapján hoz létre:

* A szolgáltatásnak van konfigurálva Proxy egy egyéni tartományt, az alapértelmezett tanúsítvány-e a Proxy egyéni tartományhoz kiadott tanúsítványt.
* Ha a szolgáltatás több egyéni tartományok beállítva proxy (csak a támogatott a **prémium** réteg), az ügyfél melyik tanúsítványt kell lennie az alapértelmezett tanúsítvány is kijelölhet. Az alapértelmezett tanúsítvány beállítása a [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#hostnameconfiguration) tulajdonság állítható igaz ("defaultSslBinding": "true"). Ha az ügyfél nem állítja be a tulajdonság, az alapértelmezett tanúsítvány alapértelmezett Proxy tartomány *.azure-API.NET webhelyen üzemeltetett kiadott tanúsítvánnyal.

## <a name="support-for-putpost-request-with-large-payload"></a>A nagyméretű adattartalom PUT/POST kérés támogatása

APIM-proxykiszolgáló-kérésekre, és nagyméretű adattartalom támogatja az ügyféloldali tanúsítványok használatával a HTTPS (például adattartalom > 40 KB). A kiszolgálói kérelem fagy megakadályozni, ügyfelek állíthatja be a tulajdonság ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#hostnameconfiguration) a Proxy gazdagépnevét. Ha a tulajdonság értéke igaz, az ügyfél tanúsítványt kér, SSL/TLS-kapcsolati idő, mielőtt bármilyen HTTP-kérelem exchange. Mivel a beállítás jelenleg csak a **Proxyeszköznév** szint összes csatlakozási kérések kérje meg az ügyféltanúsítványt. Ügyfelek is legfeljebb 20 egyéni tartományok konfigurálása a proxyhoz (csak a támogatott a **prémium** réteg) ezt a korlátozást úgy tudnak dolgozni.

