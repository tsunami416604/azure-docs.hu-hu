---
title: Végpontok közötti SSL-titkosítás konfigurálása a portál használatával
titleSuffix: Azure Application Gateway
description: Ebből a témakörből megtudhatja, hogyan hozhat létre teljes körű SSL-titkosítással rendelkező Application Gateway-t a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a878b966266bdd326db35d266bc14b2f81161e92
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075126"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Végpontok közötti SSL konfigurálása Application Gateway használatával a portálon

Ez a cikk azt ismerteti, hogyan használható az Azure Portal a végpontok közötti SSL (SSL-) titkosítás konfigurálásához az Azure Application Gateway v1 SKU használatával.

> [!NOTE]
> Application Gateway v2 SKU-nak megbízható legfelső szintű tanúsítványokra van szüksége a végpontok közötti konfiguráció engedélyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

A végpontok közötti SSL az Application Gateway szolgáltatással való konfigurálásához tanúsítvány szükséges az átjáróhoz. A háttér-kiszolgálókhoz is szükség van tanúsítványokra. Az átjáró tanúsítványa egy szimmetrikus kulcs származtatása az SSL protokoll specifikációjának megfelelően történik. A szimmetrikus kulcs ezután az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. 

A végpontok közötti SSL-titkosításhoz a megfelelő háttér-kiszolgálókat engedélyezni kell az Application gatewayben. A hozzáférés engedélyezéséhez töltse fel a háttér-kiszolgálók, más néven hitelesítési tanúsítványok (v1) vagy megbízható főtanúsítványok (v2) nyilvános tanúsítványát az Application gatewaybe. A tanúsítvány hozzáadásával biztosítható, hogy az Application Gateway csak ismert háttérbeli példányokkal kommunikáljon. Ez a konfiguráció tovább biztosítja a végpontok közötti kommunikációt.

További információ: SSL- [lezárás és végpontok közötti SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Új Application Gateway létrehozása végpontok közötti SSL-vel

A végpontok közötti SSL-titkosítással rendelkező új Application Gateway létrehozásához először engedélyeznie kell az SSL-lezárást az új Application Gateway létrehozásakor. Ez a művelet engedélyezi az SSL-titkosítást az ügyfél és az Application Gateway közötti kommunikációhoz. Ezután fel kell helyeznie a biztonságos címzetteket a HTTP-beállításokban található háttér-kiszolgálók tanúsítványainak listájára. Ez a konfiguráció lehetővé teszi az SSL-titkosítást az Application Gateway és a háttér-kiszolgálók közötti kommunikációhoz. Ez a végpontok közötti SSL-titkosítást hajtja végre.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>SSL-lezárás engedélyezése új Application Gateway létrehozásakor

További információ: SSL- [Lezárás engedélyezése új Application Gateway létrehozásakor](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Hitelesítő/főtanúsítványok hozzáadása a háttér-kiszolgálókhoz

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. A bal oldali menüben válassza a **http-beállítások** lehetőséget. Az Azure automatikusan létrehozta a **appGatewayBackendHttpSettings**alapértelmezett http-beállítását az Application Gateway létrehozásakor. 

3. Válassza a **appGatewayBackendHttpSettings**lehetőséget.

4. A **protokoll**területen válassza a **https**lehetőséget. Megjelenik a **háttér-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája.

5. Válassza az **Új létrehozása** lehetőséget.

6. A **név** mezőben adjon meg egy megfelelő nevet.

7. Válassza ki a tanúsítványfájl elemet a **CER-tanúsítvány feltöltése** mezőben.

   A standard és a WAF (v1) alkalmazás-átjárók esetében a háttér-kiszolgálói tanúsítvány nyilvános kulcsát. cer formátumban kell feltöltenie.

   ![Tanúsítvány hozzáadása](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 és WAF_v2 Application Gateway esetén. cer formátumban töltse fel a háttér-kiszolgálói tanúsítvány főtanúsítványát. Ha a háttér-tanúsítványt egy jól ismert hitelesítésszolgáltató (CA) állítja ki, akkor jelölje be a **jól ismert hitelesítésszolgáltatói tanúsítvány használata** jelölőnégyzetet, és nem kell tanúsítványt feltöltenie.

   ![Megbízható főtanúsítvány hozzáadása](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Főtanúsítvány](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Kattintson a **Mentés** gombra.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Végpontok közötti SSL engedélyezése meglévő Application Gateway-átjáróhoz

Egy meglévő Application Gateway végpontok közötti SSL-titkosítással való konfigurálásához először engedélyeznie kell az SSL-lezárást a figyelőben. Ez a művelet engedélyezi az SSL-titkosítást az ügyfél és az Application Gateway közötti kommunikációhoz. Ezután helyezze ezeket a tanúsítványokat a háttér-kiszolgálókhoz a biztonságos címzettek listájának HTTP-beállításai között. Ez a konfiguráció lehetővé teszi az SSL-titkosítást az Application Gateway és a háttér-kiszolgálók közötti kommunikációhoz. Ez a végpontok közötti SSL-titkosítást hajtja végre.

Egy, a HTTPS protokollt használó figyelőt és egy tanúsítványt kell használnia az SSL-lezárás engedélyezéséhez. Használhat olyan meglévő figyelőt, amely megfelel a feltételeknek, vagy létrehozhat egy új figyelőt. Ha a korábbi lehetőséget választja, figyelmen kívül hagyhatja a következő "SSL-lezárás engedélyezése egy meglévő Application Gateway-ben" szakaszt, és közvetlenül a "hitelesítő és megbízható főtanúsítványok hozzáadása a háttér-kiszolgálókhoz" című szakaszt.

Ha az utóbbi lehetőséget választja, alkalmazza az alábbi eljárás lépéseit.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>SSL-lezárás engedélyezése egy meglévő Application gatewayben

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. Válassza a **figyelők** lehetőséget a bal oldali menüben.

3. A követelményektől függően válasszon az **alapszintű** vagy a **többhelyes** figyelő közül.

4. A **protokoll**területen válassza a **https**lehetőséget. Megjelenik a **tanúsítvány** ablaktáblája.

5. Töltse fel az ügyfél és az Application Gateway közötti SSL-lezáráshoz használni kívánt PFX-tanúsítványt.

   > [!NOTE]
   > Tesztelési célból önaláírt tanúsítványt is használhat. Ez azonban nem javasolt az éles számítási feladatokhoz, mivel azok nehezebben kezelhetők és nem teljesen biztonságosak. További információért lásd: [önaláírt tanúsítvány létrehozása](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adja hozzá a **figyelő**egyéb szükséges beállításait a követelményektől függően.

7. A mentéshez kattintson az **OK** gombra.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Hitelesítési/megbízható legfelső szintű tanúsítványok hozzáadása a háttér-kiszolgálókhoz

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. A bal oldali menüben válassza a **http-beállítások** lehetőséget. A tanúsítványokat egy meglévő háttérbeli HTTP-beállításban is elhelyezheti a biztonságos címzettek listáján, vagy létrehozhat egy új HTTP-beállítást. (A következő lépésben az alapértelmezett HTTP-beállításhoz ( **appGatewayBackendHttpSettings**) tartozó tanúsítvány hozzá lesz adva a biztonságos címzettek listájához.)

3. Válassza a **appGatewayBackendHttpSettings**lehetőséget.

4. A **protokoll**területen válassza a **https**lehetőséget. Megjelenik a **háttér-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája. 

5. Válassza az **Új létrehozása** lehetőséget.

6. A **név** mezőben adjon meg egy megfelelő nevet.

7. Válassza ki a tanúsítványfájl elemet a **CER-tanúsítvány feltöltése** mezőben.

   A standard és a WAF (v1) alkalmazás-átjárók esetében a háttér-kiszolgálói tanúsítvány nyilvános kulcsát. cer formátumban kell feltöltenie.

   ![Tanúsítvány hozzáadása](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 és WAF_v2 Application Gateway esetén. cer formátumban töltse fel a háttér-kiszolgálói tanúsítvány főtanúsítványát. Ha a háttér-tanúsítványt egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, akkor jelölje be a **jól ismert hitelesítésszolgáltatói tanúsítvány használata** jelölőnégyzetet, és nem kell tanúsítványt feltöltenie.

   ![Megbízható főtanúsítvány hozzáadása](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)
