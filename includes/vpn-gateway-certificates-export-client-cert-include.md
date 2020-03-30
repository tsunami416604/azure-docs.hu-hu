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
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059971"
---
Ügyféltanúsítvány létrehozásakor a rendszer automatikusan telepíti azt a létrehozáshoz használt számítógépre. Ha az ügyféltanúsítványt egy másik ügyfélszámítógépre szeretné telepíteni, exportálnia kell a létrehozott ügyféltanúsítványt.

1. Ügyféltanúsítvány exportálásához nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. A létrehozott ügyféltanúsítványok alapértelmezés szerint a "Tanúsítványok - Aktuális felhasználó\Személyes\Tanúsítványok" mappában találhatók. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, kattintson az **összes feladatra**, majd az **Exportálás** parancsra a **Tanúsítványexportáló varázsló**megnyitásához.

   ![Exportálás](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. A Folytatáshoz kattintson a Tanúsítványexportáló varázsló **Tovább** gombjára.

   ![Következő lépés](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Válassza **az Igen lehetőséget, exportálja a személyes kulcsot**, majd kattintson a **Tovább**gombra.

   ![személyes kulcs exportálása](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve. Ez a beállítás továbbá exportálja a sikeres ügyfélhitelesítéshez szükséges főtanúsítvány-adatokat. Enélkül az ügyfélhitelesítés sikertelen, mert az ügyfél nem rendelkezik a megbízható főtanúsítvány. Ezután kattintson a **Tovább gombra.**

   ![fájlformátum exportálása](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **Tovább gombra.**

   ![biztonság](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább gombra.**

   ![exportálandó fájl](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezni](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)