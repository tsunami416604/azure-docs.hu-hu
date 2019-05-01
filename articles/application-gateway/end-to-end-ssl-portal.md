---
title: Rövid útmutató – teljes körű SSL-titkosítás konfigurálása az Azure Application Gatewayjel – Azure portal |} A Microsoft Docs
description: Ismerje meg az Azure portal használata az Azure Application Gateway létrehozása a végpontok közötti SSL-titkosítást.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: bd165f81b45e3ae0c121fb8876ed88e68d493195
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64946797"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Végpontok közötti SSL konfigurálása az Application Gateway használatával a portállal

Ez a cikk bemutatja, hogyan használhatja az Azure portal teljes körű SSL-titkosítás konfigurálása az application gateway szolgáltatással v1 Termékváltozatot.  

> [!NOTE]
> Application Gateway v2 szintű Termékváltozatot megbízható legfelső szintű tanúsítványok engedélyezése végpont konfiguráció szükséges. Portal-támogatás hozzáadása a megbízható legfelső szintű tanúsítványok még nem érhető el. Ezért esetén v2 Termékváltozat lásd [PowerShell-lel végpontok közötti SSL konfigurálása](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előzetes teendők

Végpontok közötti SSL konfigurálása az application gateway szolgáltatással, egy tanúsítvány szükséges az átjáró és a háttér-kiszolgálók számára tanúsítványokra szükség. Az átjáró tanúsítványa egy szimmetrikus kulcsot, az SSL protokoll specifikációja alapján az használható. A szimmetrikus kulcs titkosításához és visszafejtéséhez az átjáró küldött forgalmat majd szolgál. A végpontok közötti SSL-titkosítás a háttéralkalmazás az Alkalmazásátjáró engedélyezési listán kell lennie. Ehhez töltse fel a nyilvános tanúsítványt a háttér-kiszolgálók, más néven a hitelesítési tanúsítványok, az application gateway. A tanúsítvány hozzáadása biztosítja, hogy az application gateway csak kommunikál a háttér-ismert példányok. Ez további védi a végpontok közötti kommunikációt.

További tudnivalókért lásd: [SSL-megszakításként, valamint teljes körű SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Hozzon létre egy új application gateway a végpontok közötti SSL-lel

Szeretne létrehozni egy új application gateway a végpontok közötti SSL-titkosítást, akkor először engedélyeznie kell egy új application gateway létrehozása során az SSL-lezárást. Ez lehetővé teszi az ügyfél és az alkalmazás-átjáró közötti kommunikáció SSL-titkosítást. Ezután szüksége a HTTP-beállítások a háttérkiszolgálókhoz engedélyezett tanúsítványok a kiszolgálók közötti kommunikációt az application gateway és a háttérkiszolgáló, konzoljával végezhetők el a végpontok közötti SSL-titkosítást az SSL-titkosítás engedélyezéséhez.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Egy új application gateway létrehozása során az SSL-lezárás engedélyezése

Tekintse meg ezt a cikkből megismerheti, hogyan [egy új application gateway létrehozása során az SSL-lezárás engedélyezése](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Engedélyezési tanúsítványok háttérkiszolgálókhoz

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.

2. Válassza ki **HTTP-beállítások** a bal oldali menüből. Az Azure automatikusan létrejön egy alapértelmezett HTTP-beállítás **appGatewayBackendHttpSettings**, az application gateway létrehozása során. 

3. Válassza ki **appGatewayBackendHttpSettings**.

4. A **protokoll**válassza **HTTPS**. Ablaktábla **háttér-hitelesítési tanúsítványok** jelenik meg. 

5. A **háttér-hitelesítési tanúsítványok**, válassza a **új létrehozása**.

6. Adja meg a megfelelő **neve**.

7. Töltse fel a tanúsítvány használatával a **feltöltése CER-tanúsítvány** box.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Ebben a lépésben megadott tanúsítvány nyilvános kulcsát a .pfx tanúsítvány megtalálható a háttérrendszer a kell lennie. Exportálja a tanúsítványt (főtanúsítványt nem), a jogcím, tanúsítás és mintafelismerési (CER) formátumban a háttér-kiszolgálóra telepíthető, és használhatja ezt a lépést. Ezen lépés listáinak a háttéralkalmazáshoz a az application gateway.

8. Kattintson a **Mentés** gombra.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Meglévő application Gateway teljes körű SSL engedélyezése

Meglévő application gateway a végpontok közötti SSL titkosítás konfigurálásához kell első enable SSL-lezárást a figyelőben. Ez lehetővé teszi az ügyfél és az alkalmazás-átjáró közötti kommunikáció SSL-titkosítást. Ezután szüksége a HTTP-beállítások a háttérkiszolgálókhoz engedélyezett tanúsítványok a kiszolgálók közötti kommunikációt az application gateway és a háttérkiszolgáló, konzoljával végezhetők el a végpontok közötti SSL-titkosítást az SSL-titkosítás engedélyezéséhez.

Egy figyelő használja a HTTPS protokollt és a tanúsítvány az SSL-lezárás engedélyezése kell. Figyelő által használt protokoll nem módosítható. Tehát választhat használjon létező figyelőt, a HTTPS protokollt és a tanúsítvány, vagy hozzon létre egy új figyelőt. Abban az esetben, ha úgy dönt, hogy a korábbi, figyelmen kívül hagyhatja az alábbiakban említett lépéseket **forgalomelosztáshoz engedélyezze a meglévő application gateway** , és közvetlenül lépés **engedélyezett tanúsítványok háttérkiszolgálókhoz** a szakasz. Ha az utóbbi lehetőséget választja, kövesse az alábbi lépéseket.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Meglévő application-Gateway SSL-lezárás engedélyezése

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.

2. Válassza ki **figyelői** a bal oldali menüből.

3. Választhat **alapszintű** és **többhelyes** figyelőt a követelmény alapján.

4. A **protokoll**válassza **HTTPS**. Ablaktábla **tanúsítvány** jelenik meg.

5. Töltse fel a PFX-tanúsítványt, amely az ügyfél és az alkalmazás-átjáró közötti SSL-lezárást használni kíván.

   > [!NOTE]
   > Tesztelési célokra használhatja az önaláírt tanúsítványt. Ne használjon önaláírt tanúsítványt a termelési számítási feladatokhoz. Ismerje meg, hogyan [hozzon létre egy önaláírt tanúsítványt](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adja hozzá a többi szükséges beállításokat a **figyelő** a követelmény alapján.

7. A mentéshez kattintson az **OK** gombra.

### <a name="whitelist-certificates-for-backend-servers"></a>Engedélyezési tanúsítványok háttérkiszolgálókhoz

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.

2. Válassza ki **HTTP-beállítások** a bal oldali menüből. Beállíthatja a létező háttérrendszerek HTTP vagy engedélyezett tanúsítványok beállítását, vagy hozzon létre egy új HTTP-beállítás. Az az alábbi lépésben az engedélyezési lista tanúsítványt az alapértelmezett HTTP-beállítás lesz **appGatewayBackendHttpSettings**.

3. Válassza ki **appGatewayBackendHttpSettings**.

4. A **protokoll**válassza **HTTPS**. Ablaktábla **háttér-hitelesítési tanúsítványok** jelenik meg. 

5. A **háttér-hitelesítési tanúsítványok**, válassza a **új létrehozása**.

6. Adja meg a megfelelő **neve**.

7. Töltse fel a tanúsítvány használatával a **feltöltése CER-tanúsítvány** box.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Ebben a lépésben megadott tanúsítvány nyilvános kulcsát a .pfx tanúsítvány megtalálható a háttérrendszer a kell lennie. Exportálja a tanúsítványt (főtanúsítványt nem), a jogcím, tanúsítás és mintafelismerési (CER) formátumban a háttér-kiszolgálóra telepíthető, és használhatja ezt a lépést. Ezen lépés listáinak a háttéralkalmazáshoz a az application gateway.

8. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure CLI használatával](./tutorial-manage-web-traffic-cli.md)
