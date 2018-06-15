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
ms.openlocfilehash: 4ae4cfb91fb3a746c73d6b098a1adc9e4dee8698
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414705"
---
A önaláírt legfelső szintű tanúsítvány létrehozása után a tanúsítvány nyilvános kulcs .cer-fájl (nem a titkos kulcs) exportálja. Az Azure-bA később fel kell töltenie a fájlt. A következő lépések segítenek a .cer fájl a önaláírt legfelső szintű tanúsítvány exportálása:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt az aktuális User\Personal\Certificates lehet tanúsítványokat kezelő megnyitotta a helyi számítógép-tanúsítványok (a cím "Tanúsítványok – helyi számítógép", "Tanúsítványok – aktuális felhasználó" nem lesz). Tanúsítványok-kezelő megnyitása az aktuális felhasználó hatóköre indítsa el ahol írja be a tanúsítványok létrehozott azonos powershellből ```certmgr```.

  ![Exportálás](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. A varázslóban kattintson a **Tovább** gombra.

  ![Tanúsítvány exportálása](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

  ![A titkos kulcsot nem exportálja.](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

  ![Base-64 kódolású](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. A **exportálandó fájl**, **Tallózás** arra a helyre, amelyhez a tanúsítvány. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

  ![Tallózás](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

  ![Befejezés](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. A tanúsítvány exportálása sikeresen befejeződött.

  ![Sikeres](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Az exportált tanúsítványt ehhez hasonlóan néz ki:

  ![Exportálva](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Nyissa meg a Jegyzettömbben az exportált tanúsítványt, ha valami hasonló ebben a példában. A kék részt az Azure-bA feltöltött adatokat tartalmazza. Ha a tanúsítványt, nyissa meg a Jegyzettömbben, és nem tűnik hasonló, általában ez azt jelenti, hogy nem exportálta azt használja a Base-64 kódolású X.509 (. CER) formátumban. Továbbá ha szeretne egy másik szövegszerkesztővel, ismerje meg, hogy néhány szerkesztők vethet fel nem kívánt formázás a háttérben. Ez problémákat, amikor ez a tanúsítvány szöveget az Azure-bA feltöltött hozhat létre.

  ![Nyissa meg a Jegyzettömbben](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
