---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c6485406c67bf84b9e0fdfb9f4683abe5062d6c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444162"
---
Önaláírt főtanúsítvány létrehozása után exportálja a főtanúsítvány nyilvános kulcsú .cer fájlját (ne a titkos kulcsot). Később fogja feltölteni ezt a fájlt az Azure-bA. A következő lépések segítségével exportálhatja a az önaláírt főtanúsítványhoz tartozó .cer fájlt:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt aktuális felhasználó\személyes\tanúsítványok útvonalon, megnyitni a tanúsítványkezelőben a helyi számítógép-tanúsítványok (a cím "Tanúsítványok – helyi számítógép", ne pedig a "Tanúsítványok – aktuális felhasználó" lesz) lehet. Tanúsítványokat kezelő megnyitásához az aktuális felhasználó hatóköre indítsa el, írja be a tanúsítványok létrehozott azonos powershellből ```certmgr```.

   ![Exportálás](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Nem akarom exportálni a titkos kulcs](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolású](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. A **exportálandó fájl**, **Tallózás** arra a helyre, amelyhez exportálja a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

   ![Tallózás](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Sikeres](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Az exportált tanúsítványt ehhez hasonlóan néz ki:

   ![Exportálva](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Nyissa meg a Jegyzettömbben az exportált tanúsítványt, ha valami hasonló ebben a példában láthatja. A kék szakaszában az Azure-bA feltöltött adatokat tartalmazza. A Jegyzettömb alkalmazásban nyissa meg a tanúsítványt, és nem tűnik ehhez hasonló, általában ez azt jelenti, hogy nem exportált, használja a Base-64 kódolású X.509 (. CER) formátumban. Emellett ha egy másik szöveges szerkesztő használni kívánt, ismerje meg, hogy egyes szerkesztők megjelentetni nem kívánt formázása a háttérben. Ez problémákat, ha ezt a tanúsítványt a szöveget az Azure-bA feltöltött hozhat létre.

   ![Nyissa meg a Jegyzettömbben](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
