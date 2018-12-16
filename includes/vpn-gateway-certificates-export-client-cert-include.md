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
ms.openlocfilehash: b657d54c3ebbe5afc20fc98c1348bb783410df60
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444175"
---
Ügyféltanúsítvány létrehozása, ha automatikusan települ a létrehozáshoz használt számítógépen. Ha telepíti az ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne, kell exportálni az ügyféltanúsítványt, ami akkor jön létre.

1. Ügyféltanúsítvány exportálásához nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Az ügyféltanúsítványokat, ami akkor jön létre, alapértelmezés szerint találhatók "Tanúsítványok – aktuális felhasználó\személyes\tanúsítványok". Kattintson a jobb gombbal az ügyféltanúsítvány exportálása, kattintson a kívánt **feladatok**, és kattintson a **exportálása** megnyitásához a **Tanúsítványexportáló varázsló**.

   ![Exportálás](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. A Tanúsítványexportáló varázslóban kattintson **tovább** folytatásához.

   ![Következő lépés](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Válassza ki **Igen, a titkos kulcs exportálásának**, és kattintson a **tovább**.

   ![titkos kulcs exportálása](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve. Ez a beállítás emellett exportálja a főtanúsítvány információit a sikeres ügyfél-hitelesítés szükséges. Ennek hiányában ügyfél-hitelesítés meghiúsul, mert az ügyfél nem rendelkezik a megbízható legfelső szintű tanúsítvánnyal. Ezután kattintson a **Tovább** gombra.

   ![Exportálás fájlformátuma](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **Tovább** gombra.

   ![biztonság](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

   ![fájl exportálása](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![befejezés](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)