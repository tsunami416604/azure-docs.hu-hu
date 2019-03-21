---
title: Az Azure Application Gateway engedélyezési háttérrendszereket szükséges tanúsítványok
description: Ez a cikk példákat hogyan SSL-tanúsítvány lehet alakítani hitelesítési tanúsítvány és a megbízható legfelső szintű tanúsítványt az engedélyezési háttérbeli példányok az Azure Application Gateway szükséges
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260496"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Engedélyezési háttér-tanúsítványok létrehozása az Azure Application Gateway segítségével

Teljes körű SSL végrehajtásához az application gateway az engedélyezési listára kell hitelesítést/megbízható legfelső szintű tanúsítványok feltöltésével háttérpéldányokkal igényel. V1 Termékváltozatot, esetén hitelesítési tanúsítványokra szükség, mivel a esetén v2 szintű Termékváltozatot, megbízható legfelső szintű tanúsítványok szükségesek az engedélyezési tanúsítványok

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> - Hitelesítési tanúsítvány (a v1 Termékváltozatot) a háttérkiszolgáló-tanúsítvány exportálása
> - Megbízható főtanúsítvány exportálása a háttérkiszolgáló-tanúsítvány (a v2 szintű Termékváltozatot)

## <a name="prerequisites"></a>Előfeltételek

Háttérbeli meglévő tanúsítvány használatával hozzon létre a hitelesítési tanúsítványokat vagy engedélyezési háttérpéldányokkal az application gatewayen a szükséges megbízható főtanúsítványok van szüksége. A háttérkiszolgáló-tanúsítvány ugyanaz, mint az SSL-tanúsítvány vagy a fokozott biztonság eltérő lehet. Az Application gateway nem biztosít minden olyan mechanizmus, amellyel vagy a vételi SSL-tanúsítvány. Tesztelési célokra is létrehozhat egy önaláírt tanúsítványt, de ne használja azt a termelési számítási feladatokhoz. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Export-hitelesítési tanúsítvány (a v1 Termékváltozatot)

Hitelesítési tanúsítványra szükség az application gateway v1-ben engedélyezési lista háttérpéldányokkal Termékváltozat. Hitelesítési tanúsítvány, a nyilvános kulcsot a háttérkiszolgáló-tanúsítvány a Base-64 kódolású X.509 (. CER) formátumban. Ebben a példában azt egy SSL-tanúsítványt használja a háttérkiszolgáló-tanúsítvány, és a használható hitelesítési tanúsítvány nyilvános kulcsának exportálásához. Emellett ebben a példában használjuk a Tanúsítványkezelő Windows-eszköz exportálása a szükséges tanúsítványokat. Ha szeretné, a felhasználók kényelme érdekében megfelelően más eszköz használata.

Az SSL-tanúsítvány a exportálja a nyilvános kulcs .cer fájlt (ne a titkos kulcsot). Az alábbi lépések segítségével a .cer fájl Base-64 kódolású X.509 (. A tanúsítvány CER) formátuma:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg a tanúsítványt, általában a "Tanúsítványok – aktuális felhasználó\személyes\tanúsítványok", és kattintson a jobb gombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt aktuális felhasználó\személyes\tanúsítványok útvonalon, előfordulhat, hogy véletlenül nyitotta "Tanúsítványok – helyi számítógép", "Tanúsítványok – aktuális felhasználó" helyett). Ha meg szeretné nyitni a Certificate Manager PowerShell-lel, adja meg jelenlegi felhasználó hatókörében *certmgr* a konzolablakban.

   ![Exportálás](./media/certificates-for-backend-authentication/export.png)

2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/certificates-for-backend-authentication/exportwizard.png)

3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Nem akarom exportálni a titkos kulcs](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolású](./media/certificates-for-backend-authentication/base64.png)

5. A **exportálandó fájl**, **Tallózás** arra a helyre, amelyhez exportálja a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

   ![Tallózás](./media/certificates-for-backend-authentication/browse.png)

6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/certificates-for-backend-authentication/finish.png)

7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Sikeres](./media/certificates-for-backend-authentication/success.png)

   Az exportált tanúsítványt ehhez hasonlóan néz ki:

   ![Exportálva](./media/certificates-for-backend-authentication/exported.png)

8. Nyissa meg a Jegyzettömbben az exportált tanúsítványt, ha valami hasonló ebben a példában láthatja. A kék szakaszában az application gateway feltöltött adatokat tartalmazza. A Jegyzettömb alkalmazásban nyissa meg a tanúsítványt, és nem tűnik ehhez hasonló, általában ez azt jelenti, hogy nem exportált, használja a Base-64 kódolású X.509 (. CER) formátumban. Emellett ha egy másik szöveges szerkesztő használni kívánt, ismerje meg, hogy egyes szerkesztők megjelentetni nem kívánt formázása a háttérben. Ez problémákat, ha ezt a tanúsítványt a szöveget az Azure-bA feltöltött hozhat létre.

   ![Nyissa meg a Jegyzettömbben](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportálja a megbízható legfelső szintű tanúsítványt (a v2 szintű Termékváltozatot)

Megbízható legfelső szintű tanúsítványra szükség, az engedélyezési lista háttérpéldányokkal az application gateway v2 Termékváltozat. A legfelső szintű tanúsítvány egy Base-64 kódolású X.509 (. CER) formátumú legfelső szintű tanúsítványt a háttérkiszolgáló-tanúsítvány. Ebben a példában azt fogja SSL-tanúsítvány használata a háttérkiszolgáló-tanúsítvány, a nyilvános kulcsának exportálásához és majd a nyilvános kulcsot base64-kódolású formátumban a megbízható legfelső szintű tanúsítvány exportálása a legfelső szintű tanúsítványt a megbízható hitelesítésszolgáltató. 

A következő lépések segítenek a tanúsítvány .cer fájlját:

1. Az 1 – 9 a szakaszban ismertetett lépésekkel **exportálási hitelesítési tanúsítvány egy tanúsítványból háttér (v1 Termékváltozatot)** fenti nyilvános kulcs exportálása a háttér-tanúsítványból.

2. A nyilvános kulcs exportálása után nyissa meg a fájlt.

   ![Nyissa meg a hitelesítési tanúsítvány](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![tanúsítvány információ](./media/certificates-for-backend-authentication/general.png)

3. Helyezze át a tanúsítványláncban nézetet, megtekintheti a hitelesítésszolgáltató.

   ![tanúsítvány részletei](./media/certificates-for-backend-authentication/certdetails.png)

4. Válassza ki a legfelső szintű tanúsítványt, majd kattintson a **tanúsítvány megtekintése**.

   ![tanúsítvány elérési útja](./media/certificates-for-backend-authentication/rootcert.png)

   Meg kell tudni a legfelső szintű tanúsítvány részleteinek megtekintéséhez.

   ![tanúsítvány információ](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Helyezze át a **részletek** megtekintheti, és kattintson a **Másolás fájlba...**

   ![másolási root-cert](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Ezen a ponton kicsomagolta a legfelső szintű tanúsítvány adatait a háttérkiszolgáló-tanúsítvány. Látni fogja a **Tanúsítványexportáló varázsló**. Most használja 2 – 9 a szakaszban ismertetett lépések **exportálási hitelesítési tanúsítvány egy tanúsítványból háttér (v1 Termékváltozatot)** fent, exportálja a megbízható legfelső szintű tanúsítvány Base-64 kódolású X.509 (. CER) formátumban.

## <a name="next-steps"></a>További lépések

Most már rendelkezik a hitelesítési tanúsítvány/megbízható legfelső szintű tanúsítvány Base-64 kódolású X.509 (. CER) formátumban. Is hozzáadhat Ez az engedélyezési listára az application gateway a végpontok közötti SSL-titkosítást a háttérkiszolgálókhoz. Lásd: [végpontok közötti SSL-titkosítás konfigurálása](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).