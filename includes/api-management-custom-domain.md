---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 3b42d5fbcfb19f08b46241dbe92e6a300bec1df6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275372"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Az APIM-proxykiszolgáló válasza SSL-tanúsítványokkal a TLS-kézfogásban

### <a name="clients-calling-with-sni-header"></a>SNI fejléccel hívó ügyfelek
Ha az ügyfél egy vagy több egyéni tartományt konfigurált proxy, APIM válaszolhat HTTPS-kérelmekaz egyéni tartomány (például contoso.com), valamint az alapértelmezett tartomány (például apim-service-name.azure-api.net). A Kiszolgálónév-jelzés (SNI) fejlécben szereplő információk alapján az APIM a megfelelő kiszolgálói tanúsítvánnyal válaszol.

### <a name="clients-calling-without-sni-header"></a>SNI-fejléc nélküli hívások
Ha az ügyfél olyan ügyfelet használ, amely nem küldi el az [SNI-fejlécet,](https://tools.ietf.org/html/rfc6066#section-3) az APIM a következő logika alapján hoz létre válaszokat:

* Ha a szolgáltatás csak egy egyéni tartományt konfigurált proxy, az alapértelmezett tanúsítvány az a tanúsítvány, amely et adtak ki a proxy egyéni tartományban.
* Ha a szolgáltatás több egyéni tartományt konfigurált a proxyhoz (a **Fejlesztői** és a **Prémium** szint támogatja), az ügyfél kijelölheti, hogy melyik tanúsítvány legyen az alapértelmezett tanúsítvány. Az alapértelmezett tanúsítvány beállításához az [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) tulajdonságot true értékre kell állítani ("defaultSslBinding":"true"). Ha az ügyfél nem állítja be a tulajdonságot, az alapértelmezett tanúsítvány a *.azure-api.net helyen található alapértelmezett proxytartományhoz kiállított tanúsítvány.

## <a name="support-for-putpost-request-with-large-payload"></a>A PUT/POST kérelmek támogatása nagy mennyiségű adattal

Az APIM-proxykiszolgáló támogatja a nagy mennyiségű kérelem használatát, ha https-ben ügyféloldali tanúsítványokat használ (például 40 KB-> hasznos adat). A kiszolgáló kérésének lefagyásának megakadályozása érdekében az ügyfelek beállíthatják a ["negotiateClientCertificate" tulajdonságot: "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) a proxyállomásnéven. Ha a tulajdonság értéke igaz, az ügyféltanúsítvány t ssl/Tls-kapcsolati idővel kéri a rendszer, mielőtt bármilyen HTTP-kérelem-cserét folytatna. Mivel a beállítás a **Proxy állomásnév** szintjén érvényes, minden kapcsolatkérés az ügyféltanúsítványt kéri. Az ügyfelek legfeljebb 20 egyéni tartományt konfigurálhatnak a proxyhoz (csak a **prémium szinten** támogatottak), és megkerülhetik ezt a korlátozást.

