---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2dba0f657b418267db90c07014dc8996ed12a10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059930"
---
Az önaláírt főtanúsítványok létrehozása után exportálja a főtanúsítvány nyilvános kulcs. cer fájlját (nem a titkos kulcsot). Ezt a fájlt később fel kell töltenie az Azure-ba. Az alábbi lépések segítségével exportálhatja az önaláírt főtanúsítvány. cer fájlját:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt a jelenlegi User\Personal\Certificates alatt, előfordulhat, hogy véletlenül megnyitotta a "tanúsítványok – helyi számítógép" kifejezést, és nem a "tanúsítványok – aktuális felhasználó" beállítást. Ha a Tanúsítványkezelőt a jelenlegi felhasználói hatókörben szeretné megnyitni a PowerShell használatával, írja be a *certmgr* a konzol ablakba.

   ![Exportálás](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Ne exportálja a titkos kulcsot](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolású](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. A **fájl exportálásához** **tallózással keresse** meg azt a helyet, ahová exportálni kívánja a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **tovább**gombra.

   ![Tallózás](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Sikeres](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Az exportált tanúsítvány ehhez hasonlóan néz ki:

   ![Exportált](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Ha a Jegyzettömb használatával nyitja meg az exportált tanúsítványt, az ehhez hasonló példához hasonlóan jelenik meg. A kék szakasz az Azure-ba feltöltött információkat tartalmazza. Ha a tanúsítványt a Jegyzettömbben nyitja meg, és nem hasonlít ehhez, ez általában azt jelenti, hogy nem exportálta az alap-64 kódolású X. 509 (. CER) formátum. Emellett, ha egy másik szövegszerkesztőt szeretne használni, ismerkedjen meg azzal, hogy egyes szerkesztők nem kívánt formázást tudnak bevezetni a háttérben. Ez problémákat okozhat a tanúsítvány szövegének az Azure-ba való feltöltésekor.

   ![Megnyitás a Jegyzettömbvel](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
