---
title: "Töltse le, és bontsa ki az Azure verem Development Kit (ASDK) |} Microsoft Docs"
description: "Ismerteti, hogyan letöltéséhez és kibontásához a Azure verem Development Kit (ASDK)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Töltse le, és bontsa ki az Azure verem Development Kit (ASDK)
Győződjön meg arról, hogy a development kit fogadó számítógép megfelel-e a ASDK telepítésének alapvető követelményeit, a következő lépésre letöltéséhez és kibontásához a ASDK központi telepítési csomag a Cloudbuilder.vhdx eléréséhez.

## <a name="download-the-asdk"></a>Töltse le a ASDK
1. A letöltés megkezdése előtt győződjön meg arról, hogy a számítógép megfelel-e a következő előfeltételek teljesülését:

  - A számítógépnek rendelkeznie kell legalább 60 GB lemezterülettel érhető el a négy különböző, azonos logikai merevlemezek továbbá az operációs rendszer lemezre.
  - [.NET-keretrendszer 4.6 (vagy újabb verzió)](https://aka.ms/r6mkiy) kell telepíteni.

2. [Ugrás az első lépések lap](https://azure.microsoft.com/overview/azure-stack/try/?v=try) ahol töltse le az Azure verem szoftverfejlesztői készlet, adja meg a adatait és kattintson a **Submit**.
3. Töltse le és futtassa a [telepítési Azure verem szoftverfejlesztői készlet-ellenőrzője](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) előfeltétel-ellenőrző parancsfájl. A különálló parancsprogram végighalad a szükséges előfeltételek ellenőrzése a telepítő az Azure verem szoftverfejlesztői készlet végezhető el. Is biztosítja az Azure verem szoftverfejlesztői készlet a nagyobb csomag letöltése előtt hardver- és szoftverkövetelmények teljesítésének megerősítéséhez.
4. A **a szoftverfrissítések letöltéséhez**, kattintson a **Azure verem szoftverfejlesztői készlet**.

  > [!NOTE]
  > A ASDK letöltési (AzureStackDevelopmentKit.exe) körülbelül 10 GB-os méret.

## <a name="extract-the-asdk"></a>Bontsa ki a ASDK
1. A letöltés befejezése után kattintson **futtatása** (AzureStackDevelopmentKit.exe) ASDK önkibontó elindításához.
2. Tekintse át és fogadja el a megjelenített licencszerződése a **licencszerződés** a önkibontó varázsló, és kattintson a lap **következő**.
3. Tekintse át az adatvédelmi nyilatkozat információkat jelenik meg a **fontos figyelmeztetés** a önkibontó varázsló, és kattintson a lap **következő**.
4. Válassza ki azt a helyet az Azure-verem telepítőfájlokat a kinyerni a **rendeltetési hely kiválasztása** a önkibontó varázsló, és kattintson a lap **következő**. Az alapértelmezett hely: *aktuális mappa*\Azure verem szoftverfejlesztői készlet. 
5. Tekintse át a összefoglalását a célhelyen a **készen áll a kivonatot** önkibontó varázsló, és kattintson a lap **kibontása** kibontása a CloudBuilder.vhdx (körülbelül 25 GB) és ThirdPartyLicenses.rtf fájlokat. Ez a folyamat egy ideig tart.
6. Másolja, vagy helyezze át a CloudBuilder.vhdx fájlt a C:\ meghajtó (C:\CloudBuilder.vhdx) gyökérkönyvtárában az ASDK állomáson.

> [!NOTE]
> A fájlok kibontása után már törölheti a. A következő EXE és. Merevlemez-terület helyreállítása lévő fájlokhoz. Másik lehetőségként a biztonsági másolatot készíthet fel ezeket a fájlokat, hogy nem kell újból letöltheti a fájlokat, ha a ASDK telepíteni kell.


## <a name="next-steps"></a>További lépések
[A ASDK számítógép előkészítése](asdk-prepare-host.md)