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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059930"
---
Az önaláírt főtanúsítvány létrehozása után exportálja a legfelső szintű tanúsítvány nyilvános kulcsának .cer fájlját (nem a személyes kulcsot). Ezt a fájlt később feltölti az Azure-ba. Az alábbi lépések segítségével exportálhatja a .cer fájlt az önaláírt főtanúsítványhoz:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt az Aktuális felhasználó\Személyes\Tanúsítványok mappában, előfordulhat, hogy véletlenül a "Tanúsítványok - helyi számítógép" mappát nyitotta meg a "Tanúsítványok – jelenlegi felhasználó" helyett. Ha meg szeretné nyitni a Tanúsítványkezelőt az aktuális felhasználói hatókörben a PowerShell használatával, írja be a *certmgr parancsot* a konzolablakba.

   ![Exportálás](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Ne exportálja a személyes kulcsot](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolt](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Exportálási **fájl**esetén **keresse meg** azt a helyet, ahhoz a helyhez, ahba exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább gombra.**

   ![Tallózás](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Sikeres](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Az exportált tanúsítvány a következőhöz hasonlóan néz ki:

   ![Exportált](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Ha az exportált tanúsítványt a Jegyzettömb segítségével nyitja meg, akkor valami hasonlót lát, mint ez a példa. A szakasz kék tartalmazza az Azure-ba feltöltött információkat. Ha a tanúsítványt a Jegyzettömbbel nyitja meg, és az nem hasonlít ehhez, ez általában azt jelenti, hogy nem a Base-64 kódolású X.509(. CER) formátumban. Továbbá, ha más szövegszerkesztőt szeretne használni, értse meg, hogy egyes szerkesztők nem kívánt formázást vezethetnek be a háttérben. Ez problémákat okozhat, ha a tanúsítványból az Azure-ba feltöltött szöveget.

   ![Megnyitás a Jegyzettömbbel](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
