---
title: A háttérkiszolgálók engedélyezéséhez szükséges tanúsítványok
titleSuffix: Azure Application Gateway
description: Ez a cikk példákat tartalmaz arra, hogy a TLS/SSL-tanúsítványok hogyan konvertálhatók hitelesítési tanúsítványlá és megbízható főtanúsítványsá, amelyek szükségesek a háttérpéldányok engedélyezéséhez az Azure Application Gateway-ben
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133050"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Tanúsítványok létrehozása a háttérrendszer engedélyezéséhez az Azure Application Gateway alkalmazással

A végpontok közötti TLS-hez az Application Gateway megköveteli, hogy a háttérpéldányok hitelesítési/megbízható főtanúsítványok feltöltésével engedélyezhetők legyenek. A v1 Termékváltozat esetében hitelesítési tanúsítványok szükségesek, de a v2 termékváltozat megbízható főtanúsítványainak engedélyezése a tanúsítványok engedélyezéséhez szükséges.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> - Hitelesítési tanúsítvány exportálása háttértanúsítványból (v1 Termékváltozatesetén)
> - Megbízható legfelső szintű tanúsítvány exportálása háttértanúsítványból (v2 Termékváltozat esetén)

## <a name="prerequisites"></a>Előfeltételek

Meglévő háttér-tanúsítvány szükséges a háttérpéldányok alkalmazásátjáróval való engedélyezéséhez szükséges hitelesítési tanúsítványok vagy megbízható főtanúsítványok létrehozásához. A háttértanúsítvány lehet ugyanaz, mint a TLS/SSL tanúsítvány, vagy a nagyobb biztonság érdekében eltérő lehet. Az Application Gateway nem biztosít semmilyen mechanizmust a TLS/SSL-tanúsítvány létrehozására vagy megvásárlására. Tesztelési célokra létrehozhat egy önaláírt tanúsítványt, de nem használhatja éles számítási feladatokhoz. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Hitelesítési tanúsítvány exportálása (v1 Termékváltozat esetén)

Az Application Gateway v1 termékváltozatban a háttérpéldányok engedélyezéséhez hitelesítési tanúsítvány szükséges. A hitelesítési tanúsítvány a háttérkiszolgáló-tanúsítványok nyilvános kulcsa a Base-64 kódolású X.509(. CER) formátumban. Ebben a példában egy TLS/SSL-tanúsítványt fog használni a háttértanúsítványhoz, és exportálja a nyilvános kulcsot hitelesítési tanúsítványként való használatra. Ebben a példában a Windows Tanúsítványkezelő eszközzel exportálja a szükséges tanúsítványokat. Választhat, hogy bármilyen más eszköz, amely kényelmes.

A TLS/SSL tanúsítványból exportálja a nyilvános kulcs .cer fájlt (ne a személyes kulcsot). Az alábbi lépések segítségével exportálhatja a .cer fájlt a Base-64 kódolású X.509(. CER) formátuma a tanúsítványhoz:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg a tanúsítványt, általában a "Tanúsítványok - Aktuális felhasználó\Személyes\Tanúsítványok" mappában, és kattintson a jobb gombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt az Aktuális felhasználó\Személyes\Tanúsítványok mappában, előfordulhat, hogy véletlenül a "Tanúsítványok - helyi számítógép" mappát nyitotta meg a "Tanúsítványok – jelenlegi felhasználó" helyett. Ha meg szeretné nyitni a Tanúsítványkezelőt az aktuális felhasználói hatókörben a PowerShell használatával, írja be a *certmgr parancsot* a konzolablakba.

   ![Exportálás](./media/certificates-for-backend-authentication/export.png)

2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/certificates-for-backend-authentication/exportwizard.png)

3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Ne exportálja a személyes kulcsot](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolt](./media/certificates-for-backend-authentication/base64.png)

5. Exportálási **fájl**esetén **keresse meg** azt a helyet, ahhoz a helyhez, ahba exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább gombra.**

   ![Tallózás](./media/certificates-for-backend-authentication/browse.png)

6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/certificates-for-backend-authentication/finish.png)

7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Sikeres](./media/certificates-for-backend-authentication/success.png)

   Az exportált tanúsítvány a következőhöz hasonlóan néz ki:

   ![Exportált](./media/certificates-for-backend-authentication/exported.png)

8. Ha az exportált tanúsítványt a Jegyzettömb segítségével nyitja meg, akkor valami hasonlót lát, mint ez a példa. A kék színű szakasz az alkalmazásátjáróba feltöltött információkat tartalmazza. Ha a tanúsítványt a Jegyzettömbbel nyitja meg, és az nem hasonlít ehhez, ez általában azt jelenti, hogy nem a Base-64 kódolású X.509(. CER) formátumban. Továbbá, ha más szövegszerkesztőt szeretne használni, értse meg, hogy egyes szerkesztők nem kívánt formázást vezethetnek be a háttérben. Ez problémákat okozhat, ha a tanúsítványból az Azure-ba feltöltött szöveget.

   ![Megnyitás a Jegyzettömbbel](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Megbízható főtanúsítvány exportálása (v2 Termékváltozat esetén)

Megbízható főtanúsítvány szükséges az alkalmazásátjáró v2 Termékváltozatában a háttérpéldányok engedélyezési listájához. A főtanúsítvány egy Base-64 kódolású X.509(. CER) a háttérkiszolgálói tanúsítványokból származó gyökértanúsítvány formázása. Ebben a példában egy TLS/SSL tanúsítványt fogunk használni a háttértanúsítványhoz, exportáljuk a nyilvános kulcsot, majd exportáljuk a megbízható hitelesítésszolgáltató legfelső szintű tanúsítványát a nyilvános kulcsból base64 kódolású formátumban a megbízható főtanúsítvány bekéséhez. A köztes tanúsítvány(oka)t kiszolgálói tanúsítvánnyal együtt kell elhelyezni, és telepíteni kell a háttérkiszolgálóra.

Az alábbi lépések segítenek a tanúsítvány .cer fájljának exportálásában:

1. A **háttérszintű tanúsítvány exportálása (a fenti v1 Termékváltozathoz)** című szakaszban említett 1–9.

2. A nyilvános kulcs exportálása után nyissa meg a fájlt.

   ![Engedélyezési tanúsítvány megnyitása](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![a tanúsítványról](./media/certificates-for-backend-authentication/general.png)

3. Lépjen a Tanúsítványgörbe nézetbe a hitelesítésszolgáltató megtekintéséhez.

   ![tanúsítvány részletei](./media/certificates-for-backend-authentication/certdetails.png)

4. Jelölje ki a főtanúsítványt, és kattintson a **Tanúsítvány megtekintése gombra.**

   ![tanúsítvány elérési útja](./media/certificates-for-backend-authentication/rootcert.png)

   Meg kell jelennie a főtanúsítvány részleteinek.

   ![tanúsítvány információ](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Ugrás a **Részletek** nézetre, és kattintson **a Fájlba másolás gombra...**

   ![gyökértanúsítvány másolása](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Ezen a ponton kibvette a főtanúsítvány részleteit a háttértanúsítványból. Megjelenik a **Tanúsítványexportáló varázsló**. Most használja a fenti **háttértanúsítványból (v1 Termékváltozathoz) a hitelesítési tanúsítvány exportálása című** szakaszban említett 2-9. CER) formátumban.

## <a name="next-steps"></a>További lépések

Most már rendelkezik a hitelesítési tanúsítvány/megbízható főtanúsítvány base-64 kódolású X.509(. CER) formátumban. Ezt hozzáadhatja az alkalmazásátjáróhoz, hogy a háttérkiszolgálókat a TLS-titkosítás végpontok közötti titkosításhoz engedélyezési listára tegye. Lásd: [A végpontok között A TLS konfigurálása az Application Gateway és a PowerShell használatával című témakört.](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)

