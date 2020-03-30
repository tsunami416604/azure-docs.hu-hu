---
title: Azure VMware-megoldás a CloudSimple-től – a magánfelhő használata katasztrófa-helyként helyszíni számítási feladatokhoz
description: A CloudSimple Private Cloud beállítása a helyszíni VMware-számítási feladatok vész-helyreállítási helyeként
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083132"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>A CloudSimple Private Cloud beállítása vész-helyreállítási helyként a helyszíni VMware-számítási feladatokhoz

A CloudSimple private cloud beállítható helyreállítási helyként a helyszíni alkalmazások számára, hogy katasztrófa esetén biztosítsa az üzletmenet folytonosságát. A helyreállítási megoldás a Zerto virtuális replikáción alapul, mint replikációs és vezénylési platformon. A kritikus infrastruktúra és az alkalmazás virtuális gépek folyamatosan replikálhatók a helyszíni vCenterből a privát felhőbe. Használhatja a privát felhő feladatátvételi tesztelés, és az alkalmazás rendelkezésre állásának biztosítása katasztrófa esetén. Hasonló megközelítést lehet követni, hogy állítsa be a magánfelhő elsődleges helyként, amely védi a helyreállítási hely egy másik helyen.

> [!NOTE]
> A vész-helyreállítási környezet méretezésére vonatkozó irányelveket a Zerto-dokumentum [méretezési szempontjai a Zerto virtuális replikációhoz](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) című dokumentumban olvassa el.

A CloudSimple megoldás:

* Szükségtelenné teszi, hogy egy adatközpontkifejezetten vész-helyreállítási (DR) beállítása.
* Lehetővé teszi, hogy kihasználja az Azure-helyeket, ahol a CloudSimple telepítve van a globális földrajzi rugalmasság érdekében.
* Lehetőséget ad a központi telepítési költségek és a teljes tulajdonlási költség csökkentésére a DR számára.

A megoldás megköveteli, hogy:

* Telepítse, konfigurálja és kezelje a Zerto-t a privát felhőben.
* Adja meg saját licenceit a Zerto számára, ha a magánfelhő a védett webhely. A CloudSimple webhelyen futó Zerto párosítható a helyszíni webhelyével a licenceléshez.

Az alábbi ábra a Zerto-megoldás architektúráját mutatja be.

![Architektúra](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás telepítése

A következő szakaszok ismertetik, hogyan telepíthet vész-kiszolgáló t a magánfelhőben a Zerto virtuális replikációhasználatával.

1. [Előfeltételek](#prerequisites)
2. [Opcionális konfiguráció a CloudSimple magánfelhőn](#optional-configuration-on-your-private-cloud)
3. [ZVM és VRA beállítása a CloudSimple privát felhőben](#set-up-zvm-and-vra-on-your-private-cloud)
4. [A Zerto virtuális védelmi csoport beállítása](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Előfeltételek

A Zerto virtuális replikáció engedélyezéséhez a helyszíni környezetből a magánfelhőbe, hajtsa végre az alábbi előfeltételeket.

1. [Állítson be egy helyek közötti VPN-kapcsolatot a helyszíni hálózat és a CloudSimple magánfelhő között.](set-up-vpn.md)
2. [Állítsa be a DNS-keresési adatokat úgy, hogy a magánfelhő-kezelési összetevők továbbításra kerülnek a magánfelhő DNS-kiszolgálóira](on-premises-dns-setup.md).  A DNS-alapú keresési továbbítás engedélyezéséhez hozzon létre egy továbbítási zónabejegyzést a helyszíni DNS-kiszolgálón a CloudSimple DNS-kiszolgálókszámára. `*.cloudsimple.io`
3. Állítsa be a DNS-keresési úgy, hogy a helyszíni vCenter-összetevők továbbítása a helyszíni DNS-kiszolgálók.  A DNS-kiszolgálóknak elérhetőnek kell lenniük a CloudSimple magánfelhőből a helyek közötti VPN-en keresztül. Segítségért nyújtson be [támogatási kérelmet,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)amely a következő információkat tartalmazza.  

    * Helyszíni DNS-tartománynév
    * Helyszíni DNS-kiszolgáló IP-címei

4. Telepítsen egy Windows-kiszolgálót a magánfelhőbe. A szerver telepítéséhez használt Zerto Virtual Manager.
5. [A CloudSimple jogosultságai továbbmélyítése.](escalate-private-cloud-privileges.md)
6. Hozzon létre egy új felhasználót a Private Cloud vCenter a felügyeleti szerepkört használni, mint a szolgáltatás fiók Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Opcionális konfiguráció a magánfelhőn

1. Hozzon létre egy vagy több erőforráskészletet a private cloud vCenter-en, hogy a helyszíni környezetből származó virtuális gépek célerőforrás-készleteiként használhassa.
2. Hozzon létre egy vagy több mappát a Private Cloud vCenter-ben, hogy a helyszíni környezetből a virtuális gépek célmappáiként használhassa.
3. Hozzon létre VLAN-okat a feladatátvételi hálózathoz, és állítson be tűzfalszabályokat. Nyisson meg egy [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a segítségért.
4. Elosztott portcsoportok létrehozása feladatátvételi hálózathoz és teszthálózathoz a virtuális gépek feladatátvételének teszteléséhez.
5. Telepítse [a DHCP- és DNS-kiszolgálókat,](dns-dhcp-setup.md) vagy használjon Active Directory tartományvezérlőt a magánfelhő-környezetben.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>A ZVM és a VRA beállítása a privát felhőben

1. Telepítse a Zerto Virtual Manager (ZVM) alkalmazást a Privát felhőben lévő Windows-kiszolgálóra.
2. Jelentkezzen be a ZVM-be az előző lépésekben létrehozott szolgáltatásfiókkal.
3. A Zerto Virtual Manager licencelésének beállítása.
4. Telepítse a Zerto virtuális replikációs készüléket (VRA) a privát felhő ESXi-állomásain.
5. Párosítsa a privát felhőzat ZVM-jét a helyszíni ZVM-mel.

### <a name="set-up-zerto-virtual-protection-group"></a>A Zerto virtuális védelmi csoport beállítása

1. Hozzon létre egy új virtuális védelmi csoportot (VPG), és adja meg a VPG prioritását.
2. Válassza ki azokat a virtuális gépeket, amelyek az üzletmenet folytonossága érdekében védelmet igényelnek, és szükség esetén szabja testre a rendszerindítási sorrendet.
3. Válassza ki a helyreállítási helyet a magánfelhőként és az alapértelmezett helyreállítási kiszolgálót a magánfelhő-fürtként vagy a létrehozott erőforráscsoportként. Válassza ki a **vsanDatastore** a helyreállítási adattár a magánfelhőben.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Testreszabhatja az egyes virtuális gépek gazdagép-beállítását a Virtuálisgép-beállítások beállítás alatt.

4. Igény szerint testreszabhatja a tárolási beállításokat.
5. Adja meg a feladatátvevő hálózathoz és a feladatátvételi teszthálózathoz használandó helyreállítási hálózatokat a korábban létrehozott elosztott portcsoportokként, és szükség szerint szabja testre a helyreállítási parancsfájlokat.
6. Szükség esetén testreszabhatja az egyes virtuális gépek hálózati beállításait, és létrehozhatja a VPG-t.
7. A feladatátvétel tesztelése a replikáció befejezése után.

## <a name="reference"></a>Referencia

[Zerto dokumentáció](https://www.zerto.com/myzerto/technical-documentation/)
