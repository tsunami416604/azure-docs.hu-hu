---
title: Gyors útmutató – végpontok közötti SSL-titkosítás konfigurálása az Azure Application Gateway-Azure Portal használatával | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan hozhat létre teljes körű SSL-titkosítással rendelkező Azure-Application Gatewayokat a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097197"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Végpontok közötti SSL konfigurálása Application Gateway használatával a portálon

Ez a cikk bemutatja, hogyan konfigurálhatja a végpontok közötti SSL-titkosítást egy Application Gateway v1-es SKU-val a Azure Portal használatával.  

> [!NOTE]
> Application Gateway v2 SKU-nak megbízható legfelső szintű tanúsítványokra van szüksége a végpontok közötti konfiguráció engedélyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

A végpontok közötti SSL Application Gatewayhoz való konfigurálásához tanúsítvány szükséges ahhoz, hogy az átjáró és a tanúsítványok a háttér-kiszolgálókhoz szükségesek legyenek. Az átjáró tanúsítványa az SSL protokoll specifikációja alapján szimmetrikus kulcs származtatása céljából használható. A szimmetrikus kulcs ezután az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. A végpontok közötti SSL-titkosításhoz a megfelelő háttér-kiszolgálókat engedélyezni kell az Application gatewayben. Ehhez töltse fel a háttér-kiszolgálók, más néven hitelesítési tanúsítványok (v1) vagy megbízható főtanúsítványok (v2) nyilvános tanúsítványát a Application Gateway. A tanúsítvány hozzáadásával biztosítható, hogy a Application Gateway csak az ismert háttérbeli példányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

További információ: SSL- [lezárás és végpontok közötti SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Új Application Gateway létrehozása végpontok közötti SSL-vel

A végpontok közötti SSL-titkosítással rendelkező új Application Gateway létrehozásához először engedélyeznie kell az SSL-lezárást az új Application Gateway létrehozásakor. Ez lehetővé teszi az SSL-titkosítást az ügyfél és az Application Gateway közötti kommunikációhoz. Ezután a HTTP-beállításokban engedélyeznie kell a háttér-kiszolgálók tanúsítványait, hogy engedélyezze az SSL-titkosítást az Application Gateway és a háttér-kiszolgálók közötti kommunikációhoz, a végpontok közötti SSL-titkosítás megvalósítása érdekében.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>SSL-lezárás engedélyezése új Application Gateway létrehozásakor

Ebből a cikkből megtudhatja, hogyan [engedélyezheti az SSL-lezárást az új Application Gateway létrehozásakor](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Hitelesítő/főtanúsítvány hozzáadása a háttér-kiszolgálókhoz

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. A bal oldali menüben válassza a **http-beállítások** lehetőséget. Az Azure automatikusan létrehozta a **appGatewayBackendHttpSettings**alapértelmezett http-beállítását az Application Gateway létrehozásakor. 

3. Válassza a **appGatewayBackendHttpSettings**lehetőséget.

4. A **protokoll**területen válassza a **https**lehetőséget. Ekkor megjelenik a **háttér-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája. 

5. Válassza az **új létrehozása**lehetőséget.

6. Adjon meg egy megfelelő **nevet**.

7. Válassza ki a Tanúsítványfájl használatával a **CER-tanúsítvány feltöltése** mezőt.

   A standard és a WAF (v1) alkalmazás-átjárók esetében a háttér-kiszolgáló tanúsítványának nyilvános kulcsát. cer formátumban kell feltöltenie.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 és WAF_v2 Application Gateway esetén a háttér-kiszolgálói tanúsítvány **főtanúsítványát** . cer formátumban kell feltöltenie. Ha a háttér-tanúsítványt egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, akkor jelölje be a "jól ismert HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használata" jelölőnégyzetet, és nincs szükség a tanúsítvány feltöltésére.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Kattintson a **Mentés** gombra.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Végpontok közötti SSL engedélyezése meglévő Application gatewayhez

Egy meglévő Application Gateway végpontok közötti SSL-titkosítással való konfigurálásához először engedélyeznie kell az SSL-lezárást a figyelőben. Ez lehetővé teszi az SSL-titkosítást az ügyfél és az Application Gateway közötti kommunikációhoz. Ezután a HTTP-beállításokban engedélyeznie kell a háttér-kiszolgálók tanúsítványait, hogy engedélyezze az SSL-titkosítást az Application Gateway és a háttér-kiszolgálók közötti kommunikációhoz, a végpontok közötti SSL-titkosítás megvalósítása érdekében.

Az SSL-lezárás engedélyezéséhez egy figyelőt kell használnia a HTTPS protokoll és a tanúsítvány használatával. Így választhat, hogy egy meglévő figyelőt használ a HTTPS protokollal és a tanúsítvánnyal, vagy létrehozhat egy új figyelőt. Ha az előbbi lehetőséget választja, figyelmen kívül hagyhatja az alább említett lépéseket, amelyekkel **engedélyezheti az SSL-megszakítást a meglévő Application gatewayben** , és közvetlenül áthelyezheti a **hitelesítés/megbízható főtanúsítványok hozzáadását a háttér-kiszolgálók** szakaszhoz. Ha az utóbbit választja, kövesse az alábbi lépéseket.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>SSL-lezárás engedélyezése a meglévő Application gatewayben

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. A bal oldali menüben válassza a **figyelők** lehetőséget.

3. Az **alapszintű** és a **többhelyes** figyelő közül választhat a követelmények szerint.

4. A **protokoll**területen válassza a **https**lehetőséget. Ekkor megjelenik a **tanúsítvány** panelje.

5. Töltse fel az ügyfél és az Application Gateway közötti SSL-lezáráshoz használni kívánt PFX-tanúsítványt.

   > [!NOTE]
   > Tesztelési célból önaláírt tanúsítványt is használhat. azonban nem ajánlott éles számítási feladatokhoz, mivel azok nehezebben kezelhetők és nem teljesen biztonságosak. Ismerje meg, hogyan [hozhat létre önaláírt tanúsítványt](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adja hozzá a **figyelő** egyéb szükséges beállításait a követelménynek megfelelően.

7. A mentéshez kattintson az **OK** gombra.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Hitelesítési/megbízható legfelső szintű tanúsítványok hozzáadása a háttér-kiszolgálókhoz

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. A bal oldali menüben válassza a **http-beállítások** lehetőséget. Egy meglévő háttérbeli HTTP-beállításban engedélyezheti a tanúsítványokat, vagy létrehozhat egy új HTTP-beállítást. Az alábbi lépésben az alapértelmezett HTTP-beállításhoz ( **appGatewayBackendHttpSettings**) tanúsítványt kell kiállítani.

3. Válassza a **appGatewayBackendHttpSettings**lehetőséget.

4. A **protokoll**területen válassza a **https**lehetőséget. Ekkor megjelenik a **háttér-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája. 

5. Válassza az **új létrehozása**lehetőséget.

6. Adja meg a megfelelő **nevet**.

7. Válassza ki a Tanúsítványfájl használatával a **CER-tanúsítvány feltöltése** mezőt.

   A standard és a WAF (v1) alkalmazás-átjárók esetében a háttér-kiszolgáló tanúsítványának nyilvános kulcsát. cer formátumban kell feltöltenie.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 és WAF_v2 Application Gateway esetén a háttér-kiszolgálói tanúsítvány **főtanúsítványát** . cer formátumban kell feltöltenie. Ha a háttér-tanúsítványt egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, akkor jelölje be a "jól ismert HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használata" jelölőnégyzetet, és nincs szükség a tanúsítvány feltöltésére.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure CLI használatával](./tutorial-manage-web-traffic-cli.md)
