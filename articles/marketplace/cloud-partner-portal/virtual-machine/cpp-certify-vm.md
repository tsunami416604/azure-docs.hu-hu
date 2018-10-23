---
title: A Virtuálisgép-lemezkép hitelesíttetni az Azure Marketplace-en |} A Microsoft Docs
description: Azt ismerteti, hogyan tesztelheti, és küldje el a Virtuálisgép-lemezkép minősítésre Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ebe7fde454d2f0e98371406de56f48c9ea97ae
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639775"
---
# <a name="certify-your-vm-image"></a>A Virtuálisgép-lemezkép tanúsítása

Miután létrehozta és üzembe helyezése a virtuális gép (VM), tesztelnie kell és küldje el a Virtuálisgép-lemezkép minősítésre Azure Marketplace-en. Ez a cikk azt ismerteti, beszerzése a *Minősítőeszköz tesztelése az Azure Certified*, ez az eszköz használata a Virtuálisgép-lemezkép minősítéssel, és hogyan tölthetők fel az ellenőrzési eredmények a VHD-ket az Azure container. 


## <a name="download-and-run-the-certification-test-tool"></a>A minősítési tesztelési eszköz letöltése és futtatása

A Minősítőeszköz tesztelése az Azure Certified egy helyi gépen Windows fut, de teszteli, az Azure-alapú Windows vagy Linux rendszerű virtuális gép.  Azt ellenőrzi, hogy a felhasználó Virtuálisgép-lemezkép a Microsoft Azure-ral kompatibilis – irányelvek és a virtuális merevlemez előkészítésére körül követelmények teljesülnek. Az eszköz kimenete egy kompatibilitási jelentés, amely, fel kell tölteni a [Cloud Partner Portalon](https://cloudpartner.azure.com) VM tanúsítvány kéréséhez.

1. Töltse le és telepítse a legfrissebb [Minősítőeszköz tesztelése az Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Nyissa meg a minősítőeszközt, és kattintson **új Teszt indítása**.
3. Az a **információ tesztelése** képernyőn írja be a **teszt neve** a tesztfuttatás.
4. Válassza ki a **Platform** a virtuális gép vagy `Windows Server` vagy `Linux`. A választott platformtól befolyásolja a fennmaradó beállításokat.
5. Ha a virtuális gép nem használja az adatbázis-szolgáltatás, válassza ki a **tesztelése az Azure SQL Database** jelölőnégyzetet.

   ![Tanúsítvány vizsgálati eszköz kezdőlapján](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítőeszköz csatlakozhat egy Virtuálisgép-lemezkép

  Az eszköz csatlakozik a Windows-alapú virtuális gépek [PowerShell](https://docs.microsoft.com/powershell/) Linux rendszerű virtuális gépek keresztül csatlakozik, és [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>A minősítőeszköz csatlakozhat egy Linux rendszerű Virtuálisgép-lemezkép

1. Válassza ki a **SSH hitelesítés** mód: `Password Authentication` vagy `key File Authentication`.
2. Ha jelszóalapú hitelesítést használ, adja meg az értékeket a **virtuális gép DNS-név**, **felhasználónév**, és **jelszó**.  Szükség esetén módosíthatja az alapértelmezett **SSH-Port** számát.

     ![Linux virtuális gép lemezképének jelszavas hitelesítés](./media/publishvm_026.png)

3. Ha használja a fájlalapú hitelesítés, adjon meg értéket a a **virtuális gép DNS-név**, **felhasználónév**, és **titkos kulcs** helyét.  Igény szerint megadhat egy **jelszót** vagy módosítsa az alapértelmezett **SSH-Port** számát.

     ![Linux virtuális gép lemezképének fájl hitelesítés](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**A minősítőeszköz csatlakozhat Windows-alapú Virtuálisgép-lemezkép**
1. Adja meg a teljes **virtuális gép DNS-név** (például `MyVMName.Cloudapp.net`).
2. Adja meg az értékeket a **felhasználónév** és **jelszó**.

   ![Jelszavas hitelesítés Windows VM-lemezkép](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Egy hitelesítésszolgáltató-teszt futtatása

Miután a Virtuálisgép-rendszerkép a minősítőeszközt a megadott paraméter értékét, válassza ki **kapcsolat tesztelése** a virtuális gépre érvényes kapcsolat biztosítása érdekében. A kapcsolat ellenőrzése után válassza ki a **tovább** a teszt elindításához.  A teszt befejeződése után a táblázat a teszteredményeket (Pass/sikertelen vagy figyelmeztetés) jelenik meg.  Az alábbi példa bemutatja a vizsgálati eredmények Linux rendszerű virtuális gép teszteléséhez. 

![Linux Virtuálisgép-lemezkép minősítési terhelésiteszt-eredményei](./media/publishvm_029.png)

Ha bármelyik teszt sikertelen, van-e a lemezkép *nem* certified. Ebben az esetben tekintse át a követelményeket és hibaüzenetek, a megadott módosítások, és futtassa újból a vizsgálatot. 

Az automatikus vizsgálat után kell ezek a Virtuálisgép-lemezkép kapcsolatban további információt a **kérdőív** képernyő.  Meg kell adnia a két lap található benne.  A **általános Assessment** lap tartalmaz **True/False** kérdésekre, mivel a **Kernel testreszabási** kiválasztása és a szabadkézi kérdések tartalmazza.  Végezze el a kérdések mindkét lapon, majd válassza ki **tovább**.

![Minősítési eszköz kérdőív](./media/publishvm_030.png)

Utolsó lapján adja meg a további tudnivalókért – például SSH információ a Linux rendszerű Virtuálisgép-rendszerkép és annak magyarázatát, bármely sikertelen értékelések, ha a kivételek tudásbázison teszi lehetővé. 

Végül kattintson a **jelentés készítése** töltse le a vizsgálati eredmények, és a naplófájlokat a végrehajtott teszt esetek emellett a válaszokat a kérdőívet. A-eredményeket menteni a virtuális ugyanabban a tárolóban.

![Mentse a minősítési terhelésiteszt-eredményei](./media/publishvm_031.png)


## <a name="next-steps"></a>További lépések

Ezután kell [hozzon létre egy egységes erőforrás-azonosítók (URI) minden egyes virtuális merevlemezhez](./cpp-get-sas-uri.md) , amely a Marketplace-en való elküldését. 
