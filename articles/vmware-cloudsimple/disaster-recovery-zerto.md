---
title: Azure VMware Solutions (AVS) – az AVS Private Cloud-t használja a helyszíni számítási feladatokhoz
description: Leírja, hogyan állítható be az AVS Private Cloud vész-helyreállítási webhelyként a helyszíni VMware-alapú számítási feladatokhoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5ee43af97e79f1e835787d61bd79cfb256ef445
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083132"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Az AVS Private Cloud beállítása vész-helyreállítási webhelyként a helyszíni VMware-alapú számítási feladatokhoz

Az AVS Private Cloud a helyszíni alkalmazások helyreállítási helyeként állítható be, hogy vészhelyzet esetén is biztosítson üzletmenet-folytonosságot. A helyreállítási megoldás a replikációs és a Zerto virtuális Replikáción alapul. A kritikus infrastruktúrát és az alkalmazás virtuális gépeket folyamatosan replikálhatja a helyszíni vCenter az AVS Private Cloud szolgáltatásba. A feladatátvételi teszthez használhatja a saját AVS-Felhőjét, és biztosíthatja az alkalmazás rendelkezésre állását a katasztrófa során. Hasonló módszer követhető az AVS Private Cloud olyan elsődleges helyként való beállítása, amelyet egy másik helyen található helyreállítási hely véd.

> [!NOTE]
> Tekintse át a Zerto-dokumentum [méretezési szempontjait a Zerto virtuális replikálásához](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) a vész-helyreállítási környezet méretezésére vonatkozó irányelvek alapján.

Az AVS-megoldás:

* Nem kell külön adatközpontot beállítani a vész-helyreállításhoz (DR).
* Lehetővé teszi, hogy kihasználja azokat az Azure-telephelyeket, amelyeken az AVS a globális földrajzi rugalmasság érdekében telepítve van.
* Lehetőséget biztosít a központi telepítési költségek és a DR teljes tulajdonlási költségeinek csökkentésére.

A megoldáshoz a következőket kell tennie:

* Zerto telepítése, konfigurálása és kezelése az AVS Private Cloud-ban.
* Adja meg saját licenceit a Zerto, ha az AVS Private Cloud a védett hely. Az AVS-helyen futó Zerto-ket a helyszíni hellyel is párosíthatja a licenceléshez.

Az alábbi ábra a Zerto-megoldás architektúráját mutatja be.

![Architektúra](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás üzembe helyezése

A következő szakaszok azt ismertetik, hogyan helyezhet üzembe egy DR-megoldást a Zerto virtuális replikálás használatával az AVS Private Cloud-ban.

1. [Előfeltételek](#prerequisites)
2. [Opcionális konfiguráció a saját AVS-felhőben](#optional-configuration-on-your-avs-private-cloud)
3. [A ZVM és a VRA beállítása az AVS Private Cloud-on](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [Zerto virtuális védelmi csoport beállítása](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek végrehajtásával engedélyezheti a Zerto virtuális replikálását a helyszíni környezetből az AVS Private-felhőbe.

1. [Hozzon létre egy helyek közötti VPN-kapcsolatot a helyszíni hálózat és az AVS Private Cloud között](set-up-vpn.md).
2. [Állítsa be a DNS-lekérdezést úgy, hogy az AVS Private Cloud Management-összetevők továbbítva legyenek az AVS Private Cloud DNS-kiszolgálókra](on-premises-dns-setup.md). A DNS-címkeresés továbbításának engedélyezéséhez hozzon létre egy továbbítási zóna bejegyzést a helyi DNS-kiszolgálón `*.cloudsimple.io` az AVS DNS-kiszolgálókra.
3. DNS-címkeresés beállítása, hogy a helyszíni vCenter-összetevők továbbítva legyenek a helyszíni DNS-kiszolgálókra. A DNS-kiszolgálóknak elérhetőknek kell lenniük az AVS saját felhőben a helyek közötti VPN-kapcsolaton keresztül. Ha segítségre van, nyújtson be egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), amely a következő információkat biztosítja. 

    * Helyszíni DNS-tartománynév
    * Helyszíni DNS-kiszolgáló IP-címei

4. Telepítsen egy Windows-kiszolgálót az AVS Private Cloud-ra. A kiszolgáló a Zerto Virtual Manager telepítésére szolgál.
5. [Az AVS-jogosultságok kiterjesztve](escalate-private-cloud-privileges.md).
6. Hozzon létre egy új felhasználót az AVS Private Cloud vCenter, és a rendszergazdai szerepkört használja a Zerto Virtual Manager szolgáltatáshoz.

### <a name="optional-configuration-on-your-avs-private-cloud"></a>Opcionális konfiguráció a saját AVS-felhőben

1. Hozzon létre egy vagy több erőforráskészletet az AVS Private Cloud vCenter, hogy célként megadott erőforrás-készletekként használja a helyszíni környezetből származó virtuális gépekhez.
2. Hozzon létre egy vagy több olyan mappát az AVS Private Cloud vCenter, amelyet a helyszíni környezetből származó virtuális gépek számára célként szolgáló mappákként kíván használni.
3. Hozzon létre VLAN-okat a feladatátvételi hálózathoz, és állítsa be a tűzfalszabályok szabályait. Nyisson meg egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a segítséghez.
4. Elosztott porttartomány létrehozása a feladatátvételi hálózathoz és a tesztelési hálózathoz a virtuális gépek feladatátvételének teszteléséhez.
5. Telepítse a [DHCP-és DNS-kiszolgálókat](dns-dhcp-setup.md) , vagy használjon egy Active Directory tartományvezérlőt az AVS saját felhőalapú környezetében.

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>A ZVM és a VRA beállítása az AVS Private Cloud-on

1. Telepítse a Zerto Virtual Managert (ZVM) a Windows Serverre az AVS Private Cloud-ban.
2. Jelentkezzen be a ZVM-be az előző lépésekben létrehozott szolgáltatásfiók használatával.
3. A Zerto Virtual Manager licencelésének beállítása.
4. Telepítse a Zerto virtuális replikációs berendezést (VRA) az AVS Private Cloud ESXi-gazdagépekre.
5. Párosítsa az AVS Private Cloud ZVM a helyszíni ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto virtuális védelmi csoport beállítása

1. Hozzon létre egy új virtuális védelmi csoportot (VPG), és határozza meg a VPG prioritását.
2. Válassza ki azokat a virtuális gépeket, amelyeknek védelmet kíván biztosítani az üzletmenet folytonossága érdekében, és szükség esetén szabja testre a rendszerindítási sorrendet
3. Válassza ki a helyreállítási helyet az AVS Private Cloud és az alapértelmezett helyreállítási kiszolgálóként az AVS Private Cloud-fürtként vagy az Ön által létrehozott erőforráscsoportot. Válassza a **vsanDatastore** lehetőséget a helyreállítási adattárhoz az AVS Private Cloud-on.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Az egyes virtuális gépek gazdagépének beállítását a virtuálisgép-beállítások lehetőség alatt szabhatja testre.

4. Szükség szerint szabja testre a tárolási beállításokat.
5. Határozza meg a feladatátvételi hálózathoz és a feladatátvételi teszthez használt helyreállítási hálózatot a korábban létrehozott elosztott portok csoportjaként, és szükség szerint szabja testre a helyreállítási parancsfájlokat.
6. Szükség esetén szabja testre az egyes virtuális gépek hálózati beállításait, és hozza létre a VPG.
7. Feladatátvételi teszt a replikálás befejeződése után.

## <a name="reference"></a>Referencia

[A Zerto dokumentációja](https://www.zerto.com/myzerto/technical-documentation/)
