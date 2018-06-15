---
title: Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványok integrált Azure verem rendszerek üzembe helyezésének előkészítése |} Microsoft Docs
description: Ismerteti, hogyan készíti elő az Azure verem PKI-tanúsítványok integrált Azure verem rendszerekhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203476"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>A telepítés előkészítéséhez Azure verem PKI-tanúsítványok
A tanúsítványfájlokat [választott a hitelesítésszolgáltatótól kapott](azure-stack-get-pki-certs.md) kell importálni és exportált Azure verem szemben támasztott követelményeknek megfelelő tulajdonságokkal.


## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok üzembe helyezésének előkészítése
Használja ezeket a lépéseket az Azure verem PKI-tanúsítványok ellenőrzése: 

### <a name="import-the-certificate"></a>A tanúsítvány importálása

1.  Másolja át az eredeti tanúsítvány verziók [választott a hitelesítésszolgáltatótól kapott](azure-stack-get-pki-certs.md) egy olyan könyvtárba, a központi telepítés gazdagépen. 
  > [!WARNING]
  > Nem másolja a fájlokat, amelyek már importált, exportált, vagy módosítani bármely olyan módon, közvetlenül a CA által nyújtott fájlokból.

2.  Kattintson a jobb gombbal a tanúsítványt, és válassza ki a **tanúsítvány telepítése** vagy **PFX telepítése** attól függően, hogy a tanúsítványt a hitelesítésszolgáltatóról lett kézbesítve.

3. Az a **Tanúsítványimportáló varázsló**, jelölje be **helyi számítógép** importálási helyeként. Kattintson a **Tovább** gombra. A következő képernyő kattintson a Tovább újra.

    ![Helyi számítógép importálási hely](.\media\prepare-pki-certs\1.png)

4.  Válasszon **az összes tanúsítvány ebben a tárolóban helyi** , és válassza **vállalati szintű megbízhatóság** helyeként. Kattintson a **OK** a tároló kiválasztása párbeszédpanel bezárásához, majd **következő**.

    ![A tanúsítványtároló konfigurálása](.\media\prepare-pki-certs\3.png)

    a. Ha importál egy PFX választhat egy további párbeszédpanelen. Az a **kulcsvédelem** lapon, adja meg a jelszót a tanúsítványfájlokat, majd engedélyezze a **kulcs megjelölése exportálhatóként. Ez lehetővé teszi, hogy készítsen biztonsági másolatot, vagy szeretné a kulcsok későbbi** lehetőséget. Kattintson a **Tovább** gombra.

    ![Kulcs megjelölése exportálhatóként](.\media\prepare-pki-certs\2.png)

5. Kattintson a Befejezés gombra az importálás befejeződik.

### <a name="export-the-certificate"></a>A tanúsítvány exportálása

Nyissa meg a Tanúsítványkezelő MMC-konzolt, és csatlakozzon a helyi számítógép tanúsítványtárolójába.

1. Nyissa meg a Microsoft Management Console, a Start menüben kattintson jobb gombbal a Windows 10, majd kattintson a Futtatás gombra. Típus **mmc** kattintson az OK gombra.

2. Kattintson a fájl, beépülő modul hozzáadása/eltávolítása, majd válassza ki tanúsítványokat kattintson a Hozzáadás gombra.

    ![Tanúsítványok beépülő modul hozzáadása](.\media\prepare-pki-certs\mmc-2.png)
 
3. Válassza ki a számítógépfiók, kattintson a Tovább gombra, majd válassza ki a helyi számítógépen, majd Befejezés. Kattintson az OK gombra kattintva zárja be a beépülő modul hozzáadása/eltávolítása lapon.

    ![Tanúsítványok beépülő modul hozzáadása](.\media\prepare-pki-certs\mmc-3.png)

4. Keresse meg a tanúsítványok > Vállalati szintű megbízhatóság > Tanúsítványhely. Győződjön meg arról, hogy a jobb oldalon tekintse meg a tanúsítvány.

5. A feladat sáv a tanúsítvány Manager konzolból, válassza ki a **műveletek** > **feladataival** > **exportálása**. Kattintson a **Tovább** gombra.

  > [!NOTE]
  > Attól függően, hogy hány Azure verem tanúsítványok, akkor esetleg a folyamat befejezéséhez egynél többször.

4. Válassza ki **Igen, a titkos kulcs exportálását választom**, és kattintson a **következő**.

5. A Exportfájlformátum területen válassza ki a **minden kiterjesztett tulajdonság exportálása** , majd **következő**.

6. Válassza ki **jelszó** , és adjon meg egy jelszót a tanúsítványokat. Jegyezze meg jelszót, a központi telepítés paraméterként szolgál. Kattintson a **Tovább** gombra.

7. Válassza ki a fájl nevét és exportálása pfx-fájljának helyét. Kattintson a **Tovább** gombra.

8. Válassza a **Finish** (Befejezés) elemet.

## <a name="next-steps"></a>További lépések
[PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)