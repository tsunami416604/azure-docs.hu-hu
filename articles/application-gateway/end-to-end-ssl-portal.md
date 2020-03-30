---
title: Végpontok közötti TLS-titkosítás konfigurálása a portál használatával
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan használhatja az Azure Portalon egy alkalmazásátjáró végpontok közötti TLS-titkosítással.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133014"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Végpontok között a TLS konfigurálása az Application Gateway és a portál használatával

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon a végpontok közötti átviteli réteg biztonsága (TLS) titkosítás, korábbi nevén Secure Sockets Layer (SSL) titkosítás, az Azure Application Gateway v1 termékváltozaton keresztül konfigurálása.

> [!NOTE]
> Az Application Gateway v2 termékváltozat megbízható legfelső szintű tanúsítványokat igényel a végpontok közötti konfiguráció engedélyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

A végpontok között TLS alkalmazásátjáróval történő konfigurálásához tanúsítványra van szükség az átjáróhoz. A háttérkiszolgálókhoz tanúsítványokra is szükség van. Az átjárótanúsítvány a TLS protokoll specifikációjának megfelelő szimmetrikus kulcs származtatására szolgál. A szimmetrikus kulcs ezután az átjárónak küldött forgalom titkosítására és visszafejtésére szolgál. 

Végpontok közötti TLS-titkosítás esetén a megfelelő háttérkiszolgálókat engedélyezni kell az alkalmazásátjáróban. A hozzáférés engedélyezéséhez töltse fel a háttérkiszolgálók nyilvános tanúsítványát, más néven hitelesítési tanúsítványokat (v1) vagy megbízható főtanúsítványokat (v2) az alkalmazásátjáróba. A tanúsítvány hozzáadása biztosítja, hogy az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikáljon. Ez a konfiguráció tovább biztosítja a végpontok közötti kommunikációt.

További információ: [A TLS-végződtetés áttekintése és a TLS végpontok között az Application Gateway alkalmazásátjáróval című témakörben olvashat.](https://docs.microsoft.com/azure/application-gateway/ssl-overview)

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Új alkalmazásátjáró létrehozása végpontok között TLS-sel

Új alkalmazásátjáró létrehozásához végpontok közötti TLS-titkosítással, először engedélyeznie kell a TLS-végződtetést egy új alkalmazásátjáró létrehozása közben. Ez a művelet lehetővé teszi a TLS-titkosítást az ügyfél és az alkalmazásátjáró közötti kommunikációhoz. Ezután fel kell helyeznie a Megbízható címzettek listájára a háttérkiszolgálók tanúsítványait a HTTP-beállításokban. Ez a konfiguráció lehetővé teszi a TLS-titkosítást az alkalmazásátjáró és a háttérkiszolgálók közötti kommunikációhoz. Ez végpontok közötti TLS titkosítást valósít meg.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>TLS-végződtetés engedélyezése új alkalmazásátjáró létrehozása közben

További információ: [TLS-végződtetés engedélyezése új alkalmazásátjáró létrehozása közben.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Háttérkiszolgálók hitelesítési/főtanúsítványainak hozzáadása

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

2. Válassza a BAL oldali menü **HTTP-beállítások parancsát.** Az Azure automatikusan létrehozott egy alapértelmezett HTTP-beállítást, **az appGatewayBackendHttpSettings**protokollt, amikor létrehozta az alkalmazásátjárót. 

3. Válassza **az appGatewayBackendHttpSettings lehetőséget.**

4. A **Protokoll csoportban**válassza a **HTTPS**lehetőséget. Megjelenik a **háttérrendszer-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája.

5. Válassza az **Új létrehozása** lehetőséget.

6. A **Név** mezőbe írjon be egy megfelelő nevet.

7. Jelölje ki a tanúsítványfájlt a **CER-tanúsítvány feltöltése** mezőben.

   Standard és WAF (v1) alkalmazásátjárók esetén a háttérkiszolgálói tanúsítvány nyilvános kulcsát .cer formátumban kell feltölteni.

   ![Tanúsítvány hozzáadása](./media/end-to-end-ssl-portal/addcert.png)

   A Standard_v2 és WAF_v2 alkalmazásátjárók esetén a háttérkiszolgálói tanúsítvány főtanúsítványát .cer formátumban kell feltölteni. Ha a háttértanúsítványt egy jól ismert hitelesítésszolgáltató adja ki, jelölje be a **Jól ismert hitelesítésszolgáltatói tanúsítvány használata jelölőnégyzetet,** és akkor nem kell feltöltenie egy tanúsítványt.

   ![Megbízható főtanúsítvány hozzáadása](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Főtanúsítvány](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Kattintson a **Mentés** gombra.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Végpontok között a TLS engedélyezése meglévő alkalmazásátjáróhoz

Ha egy meglévő alkalmazásátjárót végpontok közötti TLS-titkosítással szeretne konfigurálni, először engedélyeznie kell a TLS-végződést a figyelőben. Ez a művelet lehetővé teszi a TLS-titkosítást az ügyfél és az alkalmazásátjáró közötti kommunikációhoz. Ezután helyezze a háttérkiszolgálók tanúsítványait a HTTP-beállítások ba a Megbízható címzettek listában. Ez a konfiguráció lehetővé teszi a TLS-titkosítást az alkalmazásátjáró és a háttérkiszolgálók közötti kommunikációhoz. Ez végpontok közötti TLS titkosítást valósít meg.

A TLS-végződés engedélyezéséhez egy figyelőt kell használnia a HTTPS protokollal és egy tanúsítvánnyal. Használhatja a meglévő figyelő, amely megfelel ezeknek a feltételeknek, vagy hozzon létre egy új figyelőt. Ha az előbbi lehetőséget választja, figyelmen kívül hagyhatja a következő "TLS-végződtetés engedélyezése meglévő alkalmazásátjáróban" című szakaszt, és közvetlenül a "Hitelesítés/megbízható legfelső szintű tanúsítványok hozzáadása háttérkiszolgálókhoz" szakaszba léphet.

Ha az utóbbi lehetőséget választja, alkalmazza a következő eljárás lépéseit.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>TLS-végződtetés engedélyezése meglévő alkalmazásátjáróban

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

2. Válassza a **Figyelők** lehetőséget a bal oldali menüből.

3. Válassza ki **az alapszintű** vagy **többhelyes** figyelő a ttól függően, hogy a követelményeknek.

4. A **Protokoll csoportban**válassza a **HTTPS**lehetőséget. Megjelenik a **Tanúsítvány ablaktáblája.**

5. Töltse fel az ügyfél és az alkalmazásátjáró közötti TLS-végződtetéshez használni kívánt PFX-tanúsítványt.

   > [!NOTE]
   > Tesztelési célokra önaláírt tanúsítványt is használhat. Ez azonban nem ajánlott éles számítási feladatok, mert nehezebb kezelni, és nem teljesen biztonságos. További információt az Önaláírt tanúsítvány létrehozása című [témakörben talál.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)

6. Adja meg a **figyelő**höz szükséges egyéb szükséges beállításokat, a követelményektől függően.

7. A mentéshez kattintson az **OK** gombra.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Háttérkiszolgálók hitelesítési/megbízható főtanúsítványainak hozzáadása

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

2. Válassza a BAL oldali menü **HTTP-beállítások parancsát.** A tanúsítványokat a Megbízható címzettek listában meglévő háttérHTTP-beállításba helyezheti, vagy létrehozhat egy új HTTP-beállítást. (A következő lépésben az alapértelmezett HTTP-beállítás, az **appGatewayBackendHttpSettings**tanúsítványa felkerül a Megbízható címzettek listájára.)

3. Válassza **az appGatewayBackendHttpSettings lehetőséget.**

4. A **Protokoll csoportban**válassza a **HTTPS**lehetőséget. Megjelenik a **háttérrendszer-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája. 

5. Válassza az **Új létrehozása** lehetőséget.

6. A **Név** mezőbe írjon be egy megfelelő nevet.

7. Jelölje ki a tanúsítványfájlt a **CER-tanúsítvány feltöltése** mezőben.

   Standard és WAF (v1) alkalmazásátjárók esetén a háttérkiszolgálói tanúsítvány nyilvános kulcsát .cer formátumban kell feltölteni.

   ![Tanúsítvány hozzáadása](./media/end-to-end-ssl-portal/addcert.png)

   A Standard_v2 és WAF_v2 alkalmazásátjárók esetén a háttérkiszolgálói tanúsítvány főtanúsítványát .cer formátumban kell feltölteni. Ha a háttértanúsítványt egy jól ismert hitelesítésszolgáltató adja ki, jelölje be a **Jól ismert hitelesítésszolgáltatói tanúsítvány használata jelölőnégyzetet,** és akkor nem kell feltöltenie egy tanúsítványt.

   ![Megbízható főtanúsítvány hozzáadása](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)
