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
ms.openlocfilehash: dadb443f8b7739e3a18c0d3beb558d8c42e9d19c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>A telepítés előkészítéséhez Azure verem PKI-tanúsítványok
A tanúsítványfájlokat [választott a hitelesítésszolgáltatótól kapott](azure-stack-get-pki-certs.md) kell importálni és exportált Azure verem szemben támasztott követelményeknek megfelelő tulajdonságokkal.


## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok üzembe helyezésének előkészítése
Használja ezeket a lépéseket az Azure verem PKI-tanúsítványok ellenőrzése: 

1.  Másolja át az eredeti tanúsítvány verziók [választott a hitelesítésszolgáltatótól kapott](azure-stack-get-pki-certs.md) egy olyan könyvtárba, a központi telepítés gazdagépen. 
  > [!WARNING]
  > Nem másolja a fájlokat, amelyek már importált, exportált, vagy módosítani bármely olyan módon, közvetlenül a CA által nyújtott fájlokból.

2.  Importálja a tanúsítványokat a helyi számítógép tanúsítványtárolójába:

    a.  Kattintson a jobb gombbal a tanúsítványt, és válassza ki a **PFX telepítése**.

    b.  Az a **Tanúsítványimportáló varázsló**, jelölje be **helyi számítógép** importálási helyeként. Kattintson a **Tovább** gombra.

    ![Helyi számítógép importálási hely](.\media\prepare-pki-certs\1.png)

    c.  Válassza ki **következő** a a **importálandó fájl kiválasztása** lap.

    d.  Az a **kulcsvédelem** lapon, adja meg a jelszót a tanúsítványfájlokat, majd engedélyezze a **kulcs megjelölése exportálhatóként. Ez lehetővé teszi, hogy készítsen biztonsági másolatot, vagy szeretné a kulcsok későbbi** lehetőséget. Kattintson a **Tovább** gombra.

    ![Kulcs megjelölése exportálhatóként](.\media\prepare-pki-certs\2.png)

    e.  Válasszon **az összes tanúsítvány ebben a tárolóban helyi** , és válassza **vállalati szintű megbízhatóság** helyeként. Kattintson a **OK** a tároló kiválasztása párbeszédpanel bezárásához, majd **következő**.

    ![A tanúsítványtároló konfigurálása](.\media\prepare-pki-certs\3.png)

  f.    Kattintson a **Befejezés** a Tanúsítványimportáló varázsló befejezéséhez.

  g.    Minden tanúsítványt ad meg a központi telepítés ismételje meg a műveletet.

3. Exportálja a tanúsítványt PFX fájlformátumot Azure verem követelményeknek:

  a.    Nyissa meg a Tanúsítványkezelő MMC-konzolt, és csatlakozzon a helyi számítógép tanúsítványtárolójába.

  b.    Lépjen a **vállalati szintű megbízhatóság** könyvtár.

  c.    Válasszon ki egy, az importált tanúsítványok a 2.

  d.    A feladat sáv a tanúsítvány Manager konzolból, válassza ki a **műveletek** > **feladataival** > **exportálása**.

  e.    Kattintson a **Tovább** gombra.

  f.    Válassza ki **Igen, a titkos kulcs exportálását választom**, és kattintson a **következő**.

  g.    A Exportfájlformátum területen válassza ki a **minden kiterjesztett tulajdonság exportálása** , majd **következő**.

  h.    Válassza ki **jelszó** , és adjon meg egy jelszót a tanúsítványokat. Jegyezze meg jelszót, a központi telepítés paraméterként szolgál. Kattintson a **Tovább** gombra.

  i.    Válassza ki a fájl nevét és exportálása pfx-fájljának helyét. Kattintson a **Tovább** gombra.

  j.    Válassza a **Finish** (Befejezés) elemet.

  k.    Ismételje meg az eljárást az összes fenti 2. lépésben a telepítéshez az importált tanúsítványt.

## <a name="next-steps"></a>További lépések
[PKI-tanúsítványok ellenőrzése](validate-pki-certs.md)