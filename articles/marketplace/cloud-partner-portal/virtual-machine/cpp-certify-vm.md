---
title: A virtuálisgép-rendszerkép tanúsítása az Azure Marketplace-en
description: Elmagyarázza, hogyan lehet tesztelni és elküldeni a virtuálisgép-rendszerképeket az Azure Marketplace minősítéshez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 81dec159a8a84adce97750666baba455ae3fdd93
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147073"
---
# <a name="certify-your-vm-image"></a>A virtuális gép rendszerképének tanúsítása

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [Azure VM-rendszerkép minősítésének](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) utasításait.

A virtuális gép (VM) létrehozása és üzembe helyezése után tesztelje és küldje el a virtuálisgép-lemezképet az Azure Marketplace minősítéshez. Ez a cikk bemutatja, hogyan kérheti le az *Azure Certified minősítési teszt eszközét*, hogyan használhatja ezt az eszközt a virtuálisgép-lemezkép tanúsítására, valamint az ellenőrzési eredmények feltöltésére az Azure-tárolóba, ahol a VHD-k találhatók. 


## <a name="download-and-run-the-certification-test-tool"></a>A minősítési teszt eszköz letöltése és futtatása

Az Azure Certified minősítési teszt eszköze egy helyi Windows-gépen fut, de tesztel egy Azure-alapú Windows vagy Linux rendszerű virtuális gépet.  Ellenőrzi, hogy a felhasználói virtuálisgép-rendszerkép kompatibilis-e a Microsoft Azureval – a virtuális merevlemez előkészítésére vonatkozó útmutatás és követelmények teljesültek. Az eszköz kimenete egy kompatibilitási jelentés, amelyet a rendszer feltölthet a [Cloud Partner Portalre](https://cloudpartner.azure.com) , és a virtuális gép minősítését kéri.

1. Töltse le és telepítse a legújabb [minősítési teszt eszközt az Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299)minősítéshez. 
2. Nyissa meg a minősítési eszközt, majd kattintson az **új teszt indítása**lehetőségre.
3. A teszt **adatai** képernyőn adjon meg egy **teszt nevet** a teszt futtatásához.
4. Válassza ki a virtuális gép **platformját** `Windows Server` vagy a `Linux`-t. A platform választása hatással van a fennmaradó lehetőségekre.
5. Ha a virtuális gép ezt az adatbázis-szolgáltatást használja, jelölje be **Azure SQL Database** jelölőnégyzetet.

   ![Tanúsítvány-tesztelési eszköz kezdeti lapja](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítési eszköz összekötése egy virtuálisgép-lemezképpel

  Az eszköz a [PowerShell](https://docs.microsoft.com/powershell/) használatával csatlakozik a Windows-alapú virtuális gépekhez, és a [SSH.net](https://www.ssh.com/ssh/protocol/)-on keresztül csatlakozik a Linux rendszerű virtuális gépekhez.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>A minősítési eszköz összekötése Linux rendszerű virtuálisgép-lemezképpel

1. Válassza ki az **SSH hitelesítési** módot `Password Authentication` : `key File Authentication`vagy.
2. Jelszó-alapú hitelesítés használata esetén adja meg a **virtuális gép DNS-nevének**, **felhasználónevének**és **jelszavának**értékeit.  Igény szerint megváltoztathatja az alapértelmezett **SSH-portszámot** .

     ![Linux rendszerű virtuális gép rendszerképének jelszavas hitelesítése](./media/publishvm_026.png)

3. Ha Key file-alapú hitelesítést használ, adja meg a **virtuális gép DNS-neve**, a **Felhasználónév**és a **titkos kulcs** helyének értékeit.  Opcionálisan megadhat egy **hozzáférési kódot** vagy megváltoztathatja az alapértelmezett **SSH-portszámot** .

     ![Linux rendszerű virtuálisgép-rendszerkép fájljának hitelesítése](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**A minősítési eszköz összekötése Windows-alapú VM-lemezképpel**
1. Adja meg a teljes **virtuális gép DNS-nevét** (például `MyVMName.Cloudapp.net`:).
2. Adja meg a **Felhasználónév** és a **jelszó**értékeit.

   ![Windows rendszerű virtuális gép rendszerképének jelszavas hitelesítése](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Minősítési teszt futtatása

Miután megadta a virtuálisgép-rendszerkép paramétereinek értékét a minősítési eszközben, válassza a **Kapcsolódás tesztelése** lehetőséget a virtuális géphez való érvényes kapcsolódás biztosításához. A kapcsolatok ellenőrzése után kattintson a **tovább** gombra a teszt elindításához.  A teszt befejezésekor egy tábla jelenik meg a teszt eredményeivel (Pass/Fail/Warning).  Az alábbi példa a Linux rendszerű virtuális gépek tesztelési eredményeit mutatja be. 

![A Linux rendszerű virtuális gép rendszerképének minősítési teszt eredményei](./media/publishvm_029.png)

Ha a tesztek bármelyike meghiúsul, a rendszerkép *nem* rendelkezik tanúsítvánnyal. Ebben az esetben tekintse át a követelményeket és a hibaüzeneteket, hajtsa végre a jelzett módosításokat, majd futtassa újra a tesztet. 

Az automatizált teszt után további információkat kell megadnia a virtuálisgép-rendszerképről a **Kérdőív** képernyőjén.  Két fület tartalmaz, amelyeket el kell végeznie.  Az **általános értékelés** lap **igaz/hamis** kérdéseket tartalmaz, míg a **kernel testreszabása** több kiválasztási és szabadkézi kérdést is tartalmaz.  Végezze el a kérdéseket mindkét lapon, majd válassza a **tovább**lehetőséget.

![Minősítési eszköz kérdőíve](./media/publishvm_030.png)

Az utolsó képernyőn további információk is megadhatók, például az SSH-hozzáférési információk egy linuxos virtuálisgép-rendszerképhez, valamint a sikertelen értékelések magyarázata, ha a rendszer kivételeket keres. 

Végül kattintson a **jelentés előállítása** lehetőségre, hogy letöltse a teszt eredményeit és a naplófájlokat a végrehajtott tesztelési esetekhez, továbbá a kérdőívre adott válaszokat is. Mentse az eredményeket ugyanabba a tárolóba, mint a VHD (k).

![A minősítési tesztek eredményeinek mentése](./media/publishvm_031.png)


## <a name="next-steps"></a>További lépések

Ezután [létrehoz egy egységes erőforrás-azonosítót (URI-t) minden olyan virtuális merevlemezhez](./cpp-get-sas-uri.md) , amelyet a piactéren küld el. 
