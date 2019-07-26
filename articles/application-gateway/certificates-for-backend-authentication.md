---
title: Az Azure-ban Application Gateway
description: Ez a cikk példákat mutat be arra, hogy az SSL-tanúsítványok hogyan alakíthatók át hitelesítési tanúsítványra és megbízható főtanúsítványra, amelyek szükségesek az Azure-beli háttérbeli példányok engedélyezési feltételéhez Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: absha
ms.openlocfilehash: 2d808548ef91ed416f27b0dbb3e3e93d79ade30c
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382057"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Tanúsítványok létrehozása a háttér Azure Application Gateway való engedélyezéséhez

A végpontok közötti SSL végrehajtásához Application Gateway szükséges a háttérbeli példányok engedélyezése a hitelesítés/megbízható főtanúsítványok feltöltésével. A v1 SKU esetében hitelesítési tanúsítványokra van szükség, de a v2 SKU megbízható főtanúsítványokhoz a tanúsítványok engedélyezéséhez van szükség.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> - Hitelesítési tanúsítvány exportálása háttérbeli tanúsítványból (v1 SKU esetében)
> - Megbízható főtanúsítvány exportálása háttérbeli tanúsítványból (v2 SKU)

## <a name="prerequisites"></a>Előfeltételek

Meglévő háttér-tanúsítvány szükséges ahhoz, hogy a Application Gateway használó backend-példányok engedélyezéséhez szükséges hitelesítési tanúsítványok vagy megbízható főtanúsítványok előállítása megtörténjen. A háttérbeli tanúsítvány megegyező lehet az SSL-tanúsítvánnyal, vagy eltérő a további biztonsághoz. A Application Gateway nem biztosít SSL-tanúsítvány létrehozásához vagy megvásárlásához szükséges mechanizmusokat. Tesztelési célból létrehozhat egy önaláírt tanúsítványt, de nem használhatja éles számítási feladatokhoz. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Hitelesítési tanúsítvány exportálása (v1 SKU esetében)

A Application Gateway v1 SKU-ban a háttérbeli példányok engedélyezéséhez hitelesítési tanúsítvány szükséges. A hitelesítési tanúsítvány a háttér-kiszolgálói tanúsítványok nyilvános kulcsa a Base-64 kódolású X. 509 (. CER) formátum. Ebben a példában egy SSL-tanúsítványt fog használni a háttér-tanúsítványhoz, és exportálja a nyilvános kulcsát hitelesítési tanúsítványként való használatra. Emellett ebben a példában a Windows tanúsítványkezelő eszköz használatával exportálja a szükséges tanúsítványokat. Dönthet úgy is, hogy bármilyen más, kényelmes eszközt használ.

Az SSL-tanúsítványból exportálja a nyilvános kulcsú. cer fájlt (ne a titkos kulcsot). Az alábbi lépések segítségével exportálhatja a. cer fájlt a Base-64 kódolású X. 509 fájlba (. CER) formátum a tanúsítványhoz:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg a tanúsítványt, amely jellemzően a "Certificates-current User\Personal\Certificates" elemben található, és kattintson a jobb gombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt a jelenlegi User\Personal\Certificates alatt, előfordulhat, hogy véletlenül megnyitotta a "tanúsítványok – helyi számítógép" kifejezést, és nem a "tanúsítványok – aktuális felhasználó" beállítást. Ha a Tanúsítványkezelőt a jelenlegi felhasználói hatókörben szeretné megnyitni a PowerShell használatával, írja be a *certmgr* a konzol ablakba.

   ![Exportálás](./media/certificates-for-backend-authentication/export.png)

2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/certificates-for-backend-authentication/exportwizard.png)

3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Ne exportálja a titkos kulcsot](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolású](./media/certificates-for-backend-authentication/base64.png)

5. A **fájl exportálásához** **tallózással keresse** meg azt a helyet, ahová exportálni kívánja a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

   ![Tallózás](./media/certificates-for-backend-authentication/browse.png)

6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/certificates-for-backend-authentication/finish.png)

7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Siker](./media/certificates-for-backend-authentication/success.png)

   Az exportált tanúsítvány ehhez hasonlóan néz ki:

   ![Exportálva](./media/certificates-for-backend-authentication/exported.png)

8. Ha a Jegyzettömb használatával nyitja meg az exportált tanúsítványt, az ehhez hasonló példához hasonlóan jelenik meg. A kék szakasz az Application Gateway szolgáltatásba feltöltött információkat tartalmazza. Ha a tanúsítványt a Jegyzettömbben nyitja meg, és nem hasonlít ehhez, ez általában azt jelenti, hogy nem exportálta az alap-64 kódolású X. 509 (. CER) formátum. Emellett, ha egy másik szövegszerkesztőt szeretne használni, ismerkedjen meg azzal, hogy egyes szerkesztők nem kívánt formázást tudnak bevezetni a háttérben. Ez problémákat okozhat a tanúsítvány szövegének az Azure-ba való feltöltésekor.

   ![Megnyitás a Jegyzettömbvel](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Megbízható főtanúsítvány exportálása (v2 SKU)

Application Gateway v2 SKU-ban a háttérbeli példányok engedélyezéséhez megbízható főtanúsítvány szükséges. A főtanúsítvány egy Base-64 kódolású X. 509 (. CER) a főtanúsítvány formázása a háttér-kiszolgálói tanúsítványok alapján. Ebben a példában egy SSL-tanúsítványt fog használni a háttérbeli tanúsítványhoz, és exportálja a nyilvános kulcsát. Ezután exportálja a megbízható HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványát a nyilvános kulcsból Base64 kódolású formátumban a megbízható legfelső szintű tanúsítvány beszerzéséhez. 

Az alábbi lépések segítségével exportálhatja a tanúsítványhoz tartozó. cer fájlt:

1. Használja a fentiekben említett 1-9 lépéseket a **hitelesítési tanúsítvány exportálása a háttér-tanúsítványból (a v1 SKU-hoz)** című részben a nyilvános kulcs háttérbeli tanúsítványból való exportálásához.

2. A nyilvános kulcs exportálását követően nyissa meg a fájlt.

   ![Engedélyezési tanúsítvány megnyitása](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![tudnivalók a tanúsítványról](./media/certificates-for-backend-authentication/general.png)

3. A hitelesítésszolgáltató megtekintéséhez váltson a tanúsítvány elérési útja nézetre.

   ![tanúsítvány részletei](./media/certificates-for-backend-authentication/certdetails.png)

4. Válassza ki a főtanúsítványt, és kattintson a **Tanúsítvány megtekintése**elemre.

   ![tanúsítvány elérési útja](./media/certificates-for-backend-authentication/rootcert.png)

   Ekkor meg kell jelennie a főtanúsítvány adatainak.

   ![tanúsítvány adatai](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Lépjen a **részletek** nézetre, és kattintson a **Másolás fájlba..** . elemre.

   ![legfelső szintű tanúsítvány másolása](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Ekkor kibontotta a főtanúsítvány részleteit a háttér-tanúsítványból. Ekkor megjelenik a **Tanúsítvány exportálása varázsló**. Ezután használja a fentiekben említett 2-9 lépéseket a **hitelesítési tanúsítvány exportálása a háttér-tanúsítványból (a v1 SKU** -hoz) című szakaszban, hogy exportálja a megbízható főtanúsítványt az alap-64 kódolású X. 509 (. CER) formátum.

## <a name="next-steps"></a>További lépések

Most már rendelkezik a hitelesítési tanúsítvánnyal/megbízható főtanúsítvánnyal a Base-64 kódolású X. 509 (. CER) formátum. Az Application gatewayhez hozzáadhatja, hogy a háttér-kiszolgálók teljes körű SSL-titkosítást engedélyezzenek. Lásd: [a végpontok közötti SSL-titkosítás konfigurálása](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
