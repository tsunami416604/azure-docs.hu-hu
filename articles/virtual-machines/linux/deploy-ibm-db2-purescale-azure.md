---
title: IBM DB2-höz pureScale Azure-beli üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe vállalati át az IBM DB2-höz környezet IBM DB2-höz pureScale az Azure-ban futó z/operációs rendszer mostanában használt példa architektúrát.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: fba6b5308b380b374611c09747302dbf8305dd9b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014973"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>IBM DB2-höz pureScale Azure-beli üzembe helyezése

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy [példa architektúra](ibm-db2-purescale-azure.md) , amely a nagyvállalati ügyfelek fiókazonosítóját legutóbb használt az IBM DB2-höz környezet z/OS IBM DB2-höz pureScale az Azure-ban futó át.

A lépések a migráláshoz használt, tekintse meg a telepítési parancsfájl a [DB2onAzure](https://aka.ms/db2onazure) tárházban a Githubon. Ezek a szkriptek egy tipikus, közepes méretű online tranzakció-feldolgozási (OLTP) számítási feladatok architektúrájának alapulnak.

## <a name="get-started"></a>Bevezetés

Ez az architektúra üzembe helyezéséhez töltse le és futtassa a deploy.sh parancsfájl található a [DB2onAzure](https://aka.ms/db2onazure) tárházban a Githubon.

A tárház egy lesz a Grafana irányítópultja beállításának parancsfájlokat is tartalmaz. Az Irányítópult segítségével Prometheus, a nyílt forráskódú figyelési és riasztási rendszer részét képező DB2 lekérdezése.

> [!NOTE]
> A deploy.sh parancsfájlt az ügyfél titkos SSH-kulcsokat hoz létre, és a HTTPS-kapcsolaton keresztül továbbítja azokat a központi telepítési sablont. A nagyobb biztonság érdekében javasoljuk a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) titkos kulcsok és jelszavak tárolására.

## <a name="how-the-deployment-script-works"></a>Az üzembe helyezési parancsfájl működése

A deploy.sh szkriptet hoz létre, és konfigurálja az architektúra az Azure-erőforrások. A parancsfájl kérni fogja az Azure-előfizetés és a cél-környezetben felhasznált virtuális gépek, és ezután a következő műveleteket hajtja végre:

-   Beállítja az erőforráscsoportot, virtuális hálózat és alhálózatok az Azure-ban a telepítéshez

-   Beállítja a környezet a hálózati biztonsági csoportok és az SSH

-   Beállítja a GlusterFS, mind a DB2 pureScale virtuális gépek hálózati Adaptereihez

-   A GlusterFS tárolás virtuális gépeket hoz létre

-   A jumpbox virtuális gépet hoz létre

-   A DB2 pureScale virtuális gépeket hoz létre

-   Létrehozza a tanúsító virtuális gépet a DB2 pureScale pingelésre.

-   Hozza létre a teszteléshez használni kívánt Windows virtuális gép azonban nem telepít semmit

Az üzembe helyezési parancsfájlok következő lépésként állítsa be az iSCSI virtuális tárolóhálózat (vSAN) megosztott tárolóként az Azure-ban. Ebben a példában szereplő iSCSI GlusterFS csatlakozik. Ez a megoldás is biztosít az iSCSI-tárolók telepítése egyetlen Windows csomópontot lehetőséget. iSCSI megosztott block storage felületet biztosít, amely lehetővé teszi a DB2 pureScale telepítési folyamat egy eszközillesztő használatára a megosztott tárhelyhez csatlakozhat TCP/IP felett. GlusterFS alapjait, lásd: a [architektúra: Kötetek típusú](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) Gluster Docs témakörében.

Az üzembe helyezési parancsfájlok futtassa az alábbi általános lépéseket:

1.  GlusterFS használatával állítsa be a megosztott tárolófürt, az Azure-ban. Ennek a lépésnek része a legalább két Linux-csomópontokat. További információ a telepítő: [beállítása a Microsoft Azure-ban a Red Hat Gluster Storage](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) a Red Hat Gluster dokumentációjában.

2.  Egy iSCSI cél Linux-kiszolgálókon közvetlen kapcsolat beállítása GlusterFS. További információ a telepítő: [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) a GlusterFS felügyeleti útmutató.

3.  Állítsa be az iSCSI-kezdeményező a Linux rendszerű virtuális gépeken. A kezdeményező hozzá fog férni a GlusterFS fürt iSCSI-tároló használatával. További információ a telepítő: [történő konfigurálását az iSCSI cél és a kezdeményező a Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) RootUsers dokumentációjában.

4.  A tárolási réteget az iSCSI-csatoló, GlusterFS telepítenie.

Miután a parancsfájlok az iSCSI-eszközt, az utolsó lépés az DB2 pureScale telepítése. A DB2 pureScale beállítása [IBM spektrum méretezési](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (korábbi nevén GPFS) összeállítani, és a GlusterFS fürtön telepítve. A fürtözött fájlrendszer lehetővé teszi, hogy a virtuális gépek, a DB2 pureScale motort futtató között adatokat megosztani DB2 pureScale. További információkért lásd: a [IBM spektrum méretezési](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentációja, az IBM-webhelyén.

## <a name="db2-purescale-response-file"></a>DB2-pureScale válaszfájl

A GitHub-adattár DB2server.rsp, a válasz (.rsp) fájl, amely lehetővé teszi, hogy hozzon létre egy automatizált szkript a DB2 pureScale telepítés magában foglalja. Az alábbi táblázat a telepítő a válaszfájlt használó DB2 pureScale beállítások listája. A környezet igényeinek megfelelően testre szabhatja a válaszfájlt.

> [!NOTE]
> Egy mintául szolgáló válaszfájl DB2server.rsp, szerepel a [DB2onAzure](https://aka.ms/db2onazure) tárházban a Githubon. Ha ezt a fájlt használja, szerkesztenie kell, mielőtt az adott környezetben együtt tudjon működni.

| Képernyő nevét               | Mező                                        | Érték                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Üdvözlőképernyő                   |                                              | Új telepítés                                                                                           |
| Termék kiválasztása          |                                              | DB2-verzió 11.1.3.3. A DB2 pureScale Server kiadásai                                              |
| Konfiguráció             | Címtár                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | A telepítés típusának kiválasztása                 | Tipikus                                                                                               |
|                           | Az IBM feltételeit elfogadom                     | Bejelölve                                                                                               |
| Példány tulajdonosa            | Meglévő felhasználói For példányát, felhasználónév        | DB2sdin1                                                                                              |
| Az elkülönített felhasználói               | Meglévő felhasználót, a felhasználó neve                     | DB2sdfe1                                                                                              |
| Fürt fájlrendszer       | A megosztott lemez partíció eszköz elérési útját            | /dev/DM-2                                                                                             |
|                           | Csatlakozási pont                                  | /DB2sd\_1804a                                                                                         |
|                           | Megosztott adatok lemezen                         | /dev/dm-1                                                                                             |
|                           | Csatlakoztatási pont (adatok)                           | /DB2fs/datafs1                                                                                        |
|                           | A megosztott lemez naplója                          | /dev/dm-0                                                                                             |
|                           | Csatlakoztatási pont (naplózás)                            | / DB2fs/logfs1                                                                                         |
|                           | DB2-Fürt szolgáltatások Tiebreaker. Eszköz elérési útját | /dev/dm-3                                                                                             |
| Gazdagép-listába                 | a D1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Előnyben részesített elsődleges cf-hez                         | cf1                                                                                                   |
|                           | Előnyben részesített másodlagos cf-hez                       | cf2                                                                                                   |
| Válaszfájl és összegzése | első lehetőség                                 | DB2-Server Edition kiadását telepíti az IBM DB2-höz pureScale szolgáltatással és a egy válaszfájlt a beállítások mentése |
|                           | Válasz fájl neve                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>A telepítéssel kapcsolatos megjegyzések

- /Dev-dm0, /dev-dm1, /dev-dm2 és /dev-dm3 értékeit módosíthatja a virtuális gépen, ha a telepítés történik újraindítás után (az automatizált szkript d0). Keresse meg a megfelelő értékeket, adja ki a következő parancsot a válaszfájlt a kiszolgálón, ahol a telepítő futtatja befejezése előtt:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- A telepítő szkriptek aliasok használhatók az iSCSI-lemezeket úgy, hogy a tényleges nevét egyszerűen tekintheti meg.

- A telepítési parancsfájl d0, való futtatásakor a **/dev/dm -\***  értékek d1, cf0 és cf1 eltérők lehetnek. A különbség az értékeket a DB2 pureScale beállítása nem befolyásolja.

## <a name="troubleshooting-and-known-issues"></a>Hibaelhárítás és ismert problémák

A GitHub-adattárat is tartalmaz, melyek fenntartják a szerzők Tudásbázis. Lehetséges, hogy a lehetséges problémák és próbálja meg megoldások listáját. Például ismert problémák fordulhat elő, ha:

-   Az átjáró IP-cím elérni kívánt.

-   Általános nyilvános licenc (GPL) használt fordítása.

-   A gazdagépek közötti biztonsági kézfogás sikertelen lesz.

-   A DB2-telepítő egy meglévő fájl rendszer észleli.

-   IBM spektrum méretezési manuálisan telepíti.

-   DB2-pureScale IBM spektrum méretezési csoport létrehozásakor már telepíti.

-   DB2-pureScale és IBM spektrum méretezési szeretne eltávolítani.

Ezen és egyéb ismert problémákat kapcsolatos további információkért tekintse meg a kb.md fájlt a [DB2onAzure](https://aka.ms/DB2onAzure) adattárat.

## <a name="next-steps"></a>További lépések

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Egy DB2-pureScale szolgáltatás telepítése a szükséges felhasználók létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - példány parancs létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2-pureScale fürtök megoldást](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance: IBM DB2-höz, az Azure-ban](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Az Azure virtuális adatközpont Lift és Shift útmutató](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
