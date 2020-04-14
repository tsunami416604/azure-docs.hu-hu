---
title: A virtuális gép lemezképének hitelesítése az Azure Marketplace-en
description: Bemutatja, hogyan tesztelheti és küldheti el a virtuális gép lemezképét az Azure Marketplace-tanúsítványhoz.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: ce1e001b9cafff83a3f9bf546d6903cc4a4f450f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273494"
---
# <a name="certify-your-vm-image"></a>A virtuális gép lemezképének hitelesítése

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure VM-lemezkép-minősítés](https://aks.ms/CertifyVMimage) utasításait az áttelepített ajánlatok kezeléséhez.

Miután létrehozta és üzembe helyezte a virtuális gépet (VM), tesztelnie kell, és be kell küldenie a virtuális gép lemezképét az Azure Marketplace-tanúsítványhoz. Ez a cikk ismerteti, hogy hol szerezheti be az *Azure Certified minősítési teszteszközét,* hogyan használhatja ezt az eszközt a virtuális gép lemezképének hitelesítéséhez, és hogyan töltheti fel az ellenőrzési eredményeket az Azure-tárolóba, ahol a virtuális gépek tartózkodnak. 


## <a name="download-and-run-the-certification-test-tool"></a>A tanúsítási teszteszköz letöltése és futtatása

Az Azure Certified minősítési teszteszköze egy helyi Windows-gépen fut, de azure-alapú Windows vagy Linux virtuális gépet tesztel.  Ellenőrzi, hogy a felhasználói virtuálisgép-lemezkép kompatibilis-e a Microsoft Azure-ral– hogy a virtuális merevlemez előkészítésére vonatkozó útmutatás és követelmények teljesültek.It verifies that your user VM image is compatible with Microsoft Azure,that the guidance and requirements around preparing your VHD have met. Az eszköz kimenete egy kompatibilitási jelentés, amelyet a [felhőpartneri portálra](https://cloudpartner.azure.com) kell feltöltenie a virtuális gép minősítésének kérelmezéséhez.

1. Töltse le és telepítse az Azure Certified legújabb [minősítési teszteszközét.](https://www.microsoft.com/download/details.aspx?id=44299) 
2. Nyissa meg a minősítő eszközt, majd kattintson **az Új teszt indítása gombra.**
3. A **Tesztinformáció** képernyőn adja meg a **tesztnevét** a tesztfuttatáshoz.
4. Válassza ki a **platform** a `Windows Server` `Linux`virtuális gép, vagy. A platform választása hatással van a fennmaradó lehetőségekre.
5. Ha a virtuális gép használja ezt az adatbázis-szolgáltatást, jelölje be az **Azure SQL-adatbázis tesztelése** jelölőnégyzetet.

   ![Tanúsítványteszteszköz kezdő oldala](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítő eszköz csatlakoztatása virtuális gép lemezképéhez

  Az eszköz [powershelles](https://docs.microsoft.com/powershell/) Windows-alapú virtuális gépekhez csatlakozik, és [SSH.Net](https://www.ssh.com/ssh/protocol/)keresztül csatlakozik a Linux-virtuális gépekhez.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>A minősítő eszköz csatlakoztatása Linux virtuális géplemezképhez

1. Válassza az **SSH hitelesítési** módot: `Password Authentication` vagy `key File Authentication`.
2. Jelszóalapú hitelesítés használata esetén adja meg a **virtuális gép DNS-neve**, **felhasználóneve**és **jelszó értékét.**  Szükség esetén módosíthatja az alapértelmezett **SSH portszámot.**

     ![Linux virtuális géplemezjel jelszóhitelesítése](./media/publishvm_026.png)

3. Kulcsfájl-alapú hitelesítés használata esetén adja meg a **virtuális gép DNS-neve**, **felhasználónév**és **személyes kulcs** helyének értékeit.  Megadhatja a **jelszót,** vagy módosíthatja az alapértelmezett **SSH-portszámot.**

     ![Linux os virtuális géplemez fájlhitelesítése](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**A minősítő eszköz csatlakoztatása Windows-alapú virtuálisgép-lemezképhez**
1. Adja meg a teljesen minősített virtuális `MyVMName.Cloudapp.net`gép **DNS-nevét** (például ).
2. Adja meg a **Felhasználónév** és **a Jelszó**értékét.

   ![Windows virtuálisgép-lemezkép jelszóhitelesítése](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Hitelesítési vizsgálat futtatása

Miután megadta a virtuális gép lemezképének paraméterértékeit a minősítő eszközben, válassza a **Kapcsolat tesztelése** lehetőséget a virtuális géphez való érvényes kapcsolat biztosításához. A kapcsolat ellenőrzése után válassza a **Tovább** gombot a teszt elindításához.  Amikor a teszt befejeződött, egy táblázat jelenik meg a vizsgálati eredményekkel (Pass/Fail/Warning).  A következő példa egy Linux virtuális gép teszttesztjének teszteredményeit mutatja be. 

![A Linux virtuálisgép-lemezkép minősítési teszteredményei](./media/publishvm_029.png)

Ha a tesztek bármelyike sikertelen, a rendszerkép *nem* lesz hitelesítve. Ebben az esetben tekintse át a követelményeket és a hibaüzeneteket, hajtsa végre a jelzett módosításokat, és futtassa újra a tesztet. 

Az automatikus teszt után a kérdőív képernyőn további információkat kell **megadnia** a virtuális gép lemezképéről.  Két lapot tartalmaz, amelyeket ki kell töltenie.  Az **Általános értékelés** lap **Igaz/hamis** kérdéseket tartalmaz, míg a Kernel **testreszabása** több kijelölési és szabadkézi kérdést tartalmaz.  Töltse ki a kérdéseket mindkét lapon, majd válassza a **Tovább**gombot.

![Minősítő eszköz kérdőíve](./media/publishvm_030.png)

Az utolsó képernyő lehetővé teszi, hogy további információkat, például ssh hozzáférési információkat a Linux virtuális gép rendszerkép és a magyarázat a sikertelen értékelések, ha kivételeket keres. 

Végül kattintson a **Jelentés készítése gombra** a teszteredmények és a naplófájlok letöltéséhez a kivégzett tesztesetekhez, a kérdőívre adott válaszok mellett. Mentse az eredményeket ugyanabban a tárolóban, mint a virtuális merevlemez(ek).

![Tanúsítványvizsgálati eredmények mentése](./media/publishvm_031.png)


## <a name="next-steps"></a>További lépések

Ezután [létrehoz egy egységes erőforrás-azonosítók (URI) minden virtuális merevlemez,](./cpp-get-sas-uri.md) amely elküldi a piactérre. 
