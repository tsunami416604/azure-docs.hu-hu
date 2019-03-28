---
title: Az Azure Application Gateway HTTP-fejlécek újraírási |} A Microsoft Docs
description: Ez a cikk áttekintést teszi, hogy az Azure Application Gateway HTTP-fejlécek újraírása
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 67603e326583400e8fc250ea6120297e7a94d101
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520921"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>HTTP-fejlécek átfogalmazás az Application Gateway (nyilvános előzetes verzió)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-fejlécek engedélyezése az ügyfél és a kiszolgáló át a kérelem vagy válasz további információkat. Ezek HTTP fejlécek segít, például a biztonsági fejléc számos fontos forgatókönyv újraírását mezők, például HSTS / X-XSS-védelmet, vagy válasz üzenetfejlécének mezői eltávolítása, amely felfedhet bizalmas adatokat, például a háttér-kiszolgáló nevét.

Az Application Gateway mostantól támogatja a bejövő HTTP-kéréseket, valamint a kimenő HTTP-válaszok fejlécek újraírási képessége. Lesz hozzá, távolíthatja el vagy frissítse a HTTP-kérelmek és válaszfejlécek, amíg a kérelem/válasz-csomagok áthelyezése az ügyfél és a háttérkiszolgáló készletek között. Mind a standard szintű, valamint az olyan nem szabványos üzenetfejlécének mezői is újraírása.

> [!NOTE]
> 
> A HTTP-fejléc újraírási támogatás érhető el csak a [új Termékváltozat [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Application Gateway fejléc újraírási támogatási kínálja:

- **Globális fejléce módosítsa úgy**: A kérelmek és válaszok a helyre vonatkozó konkrét fejléceinek módosíthatja.
- **Fejléc-alapú újraírási**: módosítsa úgy az ilyen típusú lehetővé teszi, hogy csak olyan kérelmek és válaszok, amelyek a projektjükhöz csak az egy adott hely terület, például egy kosár vásárlási területen-lel /cart/ fejléc újraírási\*.

Ez a változás a következőket kell tennie:

1. A szükséges a http-fejlécek újraírási új objektumokat hozhat létre: 
   - **RequestHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a kérés üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.
   - **ResponseHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a válasz üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.
   - **ActionSet**: Ez az objektum tartalmaz a kérelmek és válaszfejlécek, a fentiekben ismertetett konfigurációit. 
   - **RewriteRule**: Ez az objektum tartalmazza az összes a *actionSets* fent megadott. 
   - **RewriteRuleSet**– Ez az objektum tartalmazza az összes a *rewriteRules* és a egy kérés útválasztási szabály – alap- vagy -alapú csatlakoztatni kell.
2. Ezután kell adnia a újraírási szabálykészlet-útválasztási szabály csatolni. Létrehozása után ezt írja újra a konfigurációt a forrás figyelőt az útválasztási szabály van csatolva. Egy alapszintű útválasztási szabályt használatakor a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használata esetén a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ezért csak vonatkozik egy helyet a megadott elérési út területéhez.

Http fejléce módosítsa úgy szabálykészletek hozhat létre, és minden egyes újraírási sadu pravidel több kérésfigyelőt is alkalmazható. Azonban csak egy http újraírási szabályt egy adott hallgató beállítása is alkalmazhat.

Módosíthatja a fejlécek értékét:

- A szöveges érték. 

  *Példa* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Egy másik fejléc értékét. 

  *1. példa:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Adja meg a fejléc, az szintaxissal kell: {http_req_headerName}

  *2. példa*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Adjon meg egy válasz fejléce, használja a szintaxist kell: {http_resp_headerName}

- Támogatott kiszolgálói változók értékét.

  *Példa* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Adjon meg egy, a szintaxissal kell: {var_serverVariable}

- A fenti kombinációját.

## <a name="server-variables"></a>Kiszolgálói változók

Kiszolgálói változók hasznos információkat tárolja egy webkiszolgálón. Ezeket a változókat a kiszolgáló, a kapcsolat az ügyfél és az aktuális kérelem információt nyújtanak a kapcsolaton, például az ügyfél IP-cím vagy webes böngésző típusa. Dinamikusan módosulnak, például egy új oldal betöltésekor, vagy az űrlap közzétételekor.  A változók a próbafelhasználók állíthatók be kérelemfejlécek, valamint a válaszfejlécek. 

Ez a funkció a következő kiszolgálói változó átírása fejlécek támogatja:

| Támogatott kiszolgálói változók | Leírás                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | az ügyfél által támogatott rejtjel listáját adja vissza          |
| ciphers_used               | a létrehozott SSL-kapcsolathoz használt Rejtjelek-karakterláncot ad vissza |
| client_ip                  | IP-cím, amelyről az application gateway az a kérelem érkezett. ügyfél. Ha van egy fordított proxy előtt az application gateway és az eredeti ügyfelet, majd *client_ip* az IP-cím a fordított proxy adja vissza. tjsi változó különösen hasznos forgatókönyvekben, ahol ügyfelek írja újra az Application Gateway által beállított X – továbbított – a fejléc, hogy a fejlécet tartalmazza, csak az IP-címet a portadatokat nélkül kíván. |
| client_port                | ügyfél-port                                                  |
| client_tcp_rtt             | információ az ügyfél; TCP-kapcsolat a rendszer támogatja a TCP_INFO szoftvercsatorna-lehetőség érhető el |
| client_user                | HTTP-hitelesítés használata esetén a felhasználónév a hitelesítéshez megadott |
| gazdagép                       | Ebben a sorrendben elsőbbségi: állomásnév, a kérelem-sor vagy az állomás nevét a "Host" kérelem fejléce mező, vagy a kiszolgáló neve egyezik a kérelem |
| cookie_*name*              | a *neve* cookie-k |
| http_method                | a módszer, hogy az URL-cím kérésére. Például GET, POST stb. |
| http_status                | munkamenet-állapot, például: 200-as, 400, 403-as stb.                       |
| http_version               | kérelem protokoll, általában "HTTP 1.0", "HTTP/1.1" vagy "HTTP és 2.0-s" |
| QUERY_STRING               | a lista a változó értékének párok olvashat a "?" a kért URL-címben. |
| received_bytes             | kérés hossza (beleértve a kérelem-sor, a fejlécet és a kérelem törzsében) |
| request_query              | a kérelem-sor argumentumai                                |
| request_scheme             | kérelem séma, "http" vagy "https"                            |
| request_uri                | teljes eredeti kérelem URI-t (argumentumok)                   |
| sent_bytes                 | egy az ügyfélnek küldött bájtok száma                             |
| server_port                | annak a kiszolgálónak, amely egy kérés elfogadva                 |
| ssl_connection_protocol    | adja vissza egy létrehozott SSL-kapcsolat által használt protokoll        |
| ssl_enabled                | "a" Ha a kapcsolat működik SSL módban, vagy egyéb üres karakterlánc |

## <a name="limitations"></a>Korlátozások

- HTTP-fejlécek újraírási képessége jelenleg csak Azure PowerShell, az Azure API és az Azure SDK keresztül érhető el. Portál és az Azure CLI-n keresztül támogatása hamarosan elérhető lesz.

- A HTTP-fejléc újraírási támogatása csak az új Termékváltozat esetében támogatott [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). A funkció nem fog támogatni a régi Termékváltozat.

- A kapcsolat, a frissítés és a gazdagép fejlécek újraírását még nem támogatott.

- Feltételesen újraírási a http-fejléceket a funkció hamarosan elérhető lesz.

- A fejlécnevek tartalmazhat bármilyen alfanumerikus karakterrel és adott szimbólumok meghatározott [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Azonban jelenleg nem támogatjuk a "aláhúzás" (\_) a fejléc neve speciális karaktert. 

## <a name="need-help"></a>Segítség

Írjon nekünk az [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) abban az esetben ez a képesség segítségre van szüksége.

## <a name="next-steps"></a>További lépések

Miután megismerkedett a HTTP-fejlécek újraírási képességét, látogasson el [hozzon létre egy automatikus méretezést és zónaredundáns application gateway, amelyek a HTTP-fejlécek újraírja](tutorial-http-header-rewrite-powershell.md) vagy [Újraírási HTTP-fejlécek a meglévő automatikus skálázást és zónaredundáns az application gateway](add-http-header-rewrite-rule-powershell.md)
