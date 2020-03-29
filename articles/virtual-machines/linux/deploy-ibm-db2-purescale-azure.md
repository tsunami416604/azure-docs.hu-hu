---
title: Az IBM DB2 pureScale telepítése az Azure-ban
description: Ismerje meg, hogyan helyezhet üzembe egy példaarchitektúrát, amelyet a közelmúltban használt egy vállalat átáttelepítésére a z/OS rendszeren futó IBM DB2 pureScale-ről az Azure-beli IBM DB2 pureScale-re.
author: njray
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 98e912894a4d93a057a2f6a2153d0690deaed250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968892"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Az IBM DB2 pureScale telepítése az Azure-ban

Ez a cikk bemutatja, hogyan helyezhet üzembe egy [példaarchitektúrát,](ibm-db2-purescale-azure.md) amelyet egy vállalati ügyfél a közelmúltban használt a z/OS rendszeren futó IBM DB2 környezetből az Azure-beli IBM DB2 pureScale-re való áttelepítéshez.

Az áttelepítéshez használt lépések végrehajtásához tekintse meg a telepítési parancsfájlok a [DB2onAzure](https://aka.ms/db2onazure) tárházban a GitHubon. Ezek a parancsfájlok egy tipikus, közepes méretű online tranzakciófeldolgozási (OLTP) számítási feladat architektúráján alapulnak.

## <a name="get-started"></a>Bevezetés

Az architektúra üzembe helyezéséhez töltse le és futtassa [a2onAzure-tárházban](https://aka.ms/db2onazure) a GitHubon található deploy.sh parancsfájlt.

A tárház is rendelkezik a Grafana irányítópult beállítására szolgáló parancsfájlokkal. Az irányítópult segítségével lekérdezheti a Prometheus-t, a DB2-ben található nyílt forráskódú figyelési és riasztási rendszert.

> [!NOTE]
> Az ügyfél deploy.sh parancsfájlja létrehozza a privát SSH-kulcsokat, és továbbítja azokat a központi telepítési sablonhttps-en keresztül. A nagyobb biztonság érdekében azt javasoljuk, hogy az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) használatával tárolja a titkos kulcsokat, kulcsokat és jelszavakat.

## <a name="how-the-deployment-script-works"></a>A központi telepítési parancsfájl működése

A deploy.sh parancsfájl létrehozza és konfigurálja az Azure-erőforrásokat ehhez az architektúrához. A parancsfájl kéri, hogy az Azure-előfizetés és a célkörnyezetben használt virtuális gépek, majd végrehajtja a következő műveleteket:

-   Beállítja az erőforráscsoportot, a virtuális hálózatot és az alhálózatokat az Azure-ban a telepítéshez.

-   Beállítja a hálózati biztonsági csoportokat és az SSH-t a környezethez.

-   Több hálózati adaptert állít be a megosztott tárolón és a DB2 pureScale virtuális gépeken is.

-   Létrehozza a megosztott tároló virtuális gépek. Ha közvetlen tárolóhelyeket vagy más tárolási megoldást használ, olvassa el [a Közvetlen tárolóhelyek – áttekintés című témakört.](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)

-   Létrehozza a jumpbox virtuális gépet.

-   Létrehozza a DB2 pureScale virtuális gépek.

-   Létrehozza a tanúsító virtuális gépet, amelyet a DB2 pureScale pingel. Hagyja ki a központi telepítés ezen részét, ha a Db2 pureScale verziója nem igényel tanúsító

-   Létrehoz egy Windows virtuális gépet, amelyet tesztelésre használhat, de nem telepít rá semmit.

Ezután a központi telepítési parancsfájlok egy iSCSI virtuális tárolóhálózatot (vSAN) állítanak be az Azure-beli megosztott tároláshoz. Ebben a példában az iSCSI csatlakozik a megosztott tárolófürthöz. Az eredeti ügyfélmegoldásban a GlusterFS-t használták. Az IBM azonban már nem támogatja ezt a megközelítést. Az IBM támogatásának fenntartásához támogatott iSCSI-kompatibilis fájlrendszert kell használnia. A Microsoft a Közvetlen tárolóhelyeket (S2D) ajánlja fel.

Ez a megoldás azt is lehetővé teszi, hogy az iSCSI-tárolókat egyetlen Windows-csomópontként telepítse. Az iSCSI tcp/IP-kapcsolaton keresztül megosztott blokktárolási felületet biztosít, amely lehetővé teszi, hogy a DB2 pureScale beállítási eljárás eszközillesztőt használjon a megosztott tárolóhoz való csatlakozáshoz.

A központi telepítési parancsfájlok az alábbi általános lépéseket futtatják:

1.  Hozzon létre egy megosztott tárolófürtöt az Azure-ban. Ez a lépés legalább két Linux-csomópontot foglal magában.

2.  ISCSI Direct adapter beállítása a megosztott tárolófürt cél Linux-kiszolgálóin.

3.  Állítsa be az iSCSI-kezdeményezőt a Linux virtuális gépeken. A kezdeményező iSCSI-tároló használatával éri el a megosztott tárolófürtöt. A telepítéssel kapcsolatos részletekről a RootUsers dokumentációban található [ISCSI-tároló és -kezdeményező linuxos konfigurálása](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) című témakörben olvashat.

4.  Telepítse az iSCSI-felület megosztott tárolórétegét.

Miután a parancsfájlok létrehozják az iSCSI-eszközt, az utolsó lépés a DB2 pureScale telepítése. A DB2 pureScale beállítás részeként az [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (korábbi nevén GPFS) fordítása és telepítése a GlusterFS fürtre történik. Ez a fürtözött fájlrendszer lehetővé teszi, hogy a DB2 pureScale adatokat osszon meg a DB2 pureScale motort futtató virtuális gépek között. További információt az [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentációjában talál az IBM webhelyén.

## <a name="db2-purescale-response-file"></a>DB2 pureScale válaszfájl

A GitHub-tárház tartalmazza a DB2server.rsp fájl, a válasz (.rsp) fájlt, amely lehetővé teszi, hogy automatikus parancsfájlt hozzon létre a DB2 pureScale telepítéshez. Az alábbi táblázat felsorolja a DB2 pureScale beállításokat, amelyeket a válaszfájl a telepítéshez használ. A válaszfájlt igény szerint testreszabhatja a környezetéhez.

> [!NOTE]
> A db2server.rsp mintaválaszfájl a GitHub [DB2onAzure-tárházában](https://aka.ms/db2onazure) található. Ha ezt a fájlt használja, akkor azt kell szerkesztenie, mielőtt a környezetében működne.

| Képernyő neve               | Mező                                        | Érték                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Üdvözlőképernyő                   |                                              | Új telepítés                                                                                           |
| Válasszon egy terméket          |                                              | DB2 verzió 11.1.3.3. Server Editions DB2 pureScale                                              |
| Konfiguráció             | Címtár                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | A telepítés típusának kiválasztása                 | Tipikus                                                                                               |
|                           | Elfogadom az IBM feltételeit                     | Bejelölve                                                                                               |
| Példány tulajdonosa            | Meglévő felhasználó példányhoz, felhasználónév        | DB2sdin1                                                                                              |
| Bekerített felhasználó               | Meglévő felhasználó, felhasználónév                     | DB2sdfe1                                                                                              |
| Fürtfájlrendszer       | Megosztott lemezpartíció-eszköz elérési útja            | /dev/dm-2                                                                                             |
|                           | Rögzítési pont                                  | /DB2sd\_1804a                                                                                         |
|                           | Megosztott lemez az adatokhoz                         | /dev/dm-1                                                                                             |
|                           | Csatlakoztatási pont (adatok)                           | /DB2fs/datafs1                                                                                        |
|                           | Megosztott lemez a naplóhoz                          | /dev/dm-0                                                                                             |
|                           | Csatlakoztatási pont (napló)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Cluster Services Tiebreaker. Eszköz elérési útja | /dev/dm-3                                                                                             |
| Állomáslista                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Preferált elsődleges CF                         | cf1                                                                                                   |
|                           | Preferált másodlagos CF                       | cf2                                                                                                   |
| Válaszfájl és összegzés | első lehetőség                                 | Telepítse a DB2 Server Edition rendszert az IBM DB2 pureScale szolgáltatással, és mentse a beállításokat egy válaszfájlba |
|                           | Válaszfájl neve                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Megjegyzések a telepítéssel kapcsolatban

- A /dev-dm0, /dev-dm1, /dev-dm2 és /dev-dm3 értékei a telepítést leendő virtuális gépen történő újraindítás után változhatnak (d0 az automatikus parancsfájlban). A megfelelő értékek megkereséséhez a következő parancsot adhatja ki, mielőtt befejezné a válaszfájlt azon a kiszolgálón, amelyen a telepítő futni fog:

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

- A telepítőparancsfájlok aliasokat használnak az iSCSI-lemezekhez, hogy a tényleges nevek könnyen megtalálhatók legyenek.

- Ha a telepítőszkript d0-n fut, a **/dev/dm-\* ** értékek d1, cf0 és cf1 esetén eltérőek lehetnek. Az értékek közötti különbség nincs hatással a DB2 pureScale beállításra.

## <a name="troubleshooting-and-known-issues"></a>Hibaelhárítás és ismert problémák

A GitHub-tártár tartalmaz egy tudásbázist, amelyet a szerzők fenntartanak. Felsorolja a lehetséges problémákat, és a kipróbálható megoldásokat. Például ismert problémák fordulhatnak elő, ha:

-   Az átjáró IP-címét próbálja elérni.

-   Ön összeállítása Általános Nyilvános Licenc (GPL).

-   Az állomások közötti biztonsági kézfogás sikertelen.

-   A DB2 telepítő egy meglévő fájlrendszert észlel.

-   Manuálisan telepíti az IBM Spectrum Scale-t.

-   A DB2 pureScale-et akkor telepíti, amikor az IBM Spectrum Scale már létrejött.

-   Eltávolítja a DB2 pureScale és az IBM Spectrum Scale-t.

Ezekről és más ismert problémákról további információt a [DB2onAzure-tárházban](https://aka.ms/DB2onAzure) található kb.md fájlban talál.

## <a name="next-steps"></a>További lépések

-   [A DB2 pureScale funkció telepítéséhez szükséges felhasználók létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – Példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale fürtök adatmegoldása](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Az Azure virtuális adatközpontok –– fuvar- és műszakútmutató](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
