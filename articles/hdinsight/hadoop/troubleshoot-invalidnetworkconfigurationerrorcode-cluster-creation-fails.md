---
title: InvalidNetworkConfigurationErrorCode az Azure HDInsight
description: Az Azure HDInsight InvalidNetworkConfigurationErrorCode-beli sikertelen fürtök létrehozásának különböző okai
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: a6b207086325018deb63383a0775af8dfe195ac4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091719"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>A fürt létrehozása nem sikerül a InvalidNetworkConfigurationErrorCode az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

Ha a "Virtual Network konfiguráció `InvalidNetworkConfigurationErrorCode` nem kompatibilis a HDInsight-követelménysel" leírásban látható hibakód jelenik meg, általában a fürt [virtuális hálózati konfigurációjával](../hdinsight-plan-virtual-network-deployment.md) kapcsolatos problémát jelez. A hiba további leírása alapján a probléma megoldásához kövesse az alábbi szakaszokat.

## <a name="hostname-resolution-failed"></a>"Az állomásnév feloldása sikertelen"

### <a name="issue"></a>Probléma

A hiba leírása tartalmazza az "állomásnév feloldása sikertelen" kifejezést.

### <a name="cause"></a>Ok

Ez a hiba az egyéni DNS-konfigurációval kapcsolatos problémára mutat. A virtuális hálózaton belüli DNS-kiszolgálók továbbítják a DNS-lekérdezéseket az Azure rekurzív feloldóinak az adott virtuális hálózaton belüli állomásnevek feloldásához (a részletekért lásd: [névfeloldás a virtuális](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) hálózatokban). Az Azure rekurzív feloldóit a virtuális IP-168.63.129.16 keresztül érheti el. Ez az IP-cím csak az Azure-beli virtuális gépekről érhető el. Így nem fog működni, ha helyszíni DNS-kiszolgálót használ, vagy a DNS-kiszolgáló egy Azure-beli virtuális gép, amely nem része a fürt vNet.

### <a name="resolution"></a>Megoldás:

1. SSH-t a fürt részét képező virtuális gépre, és futtassa a parancsot `hostname -f`. Ezzel visszaadja a gazdagép teljes tartománynevét ( `<host_fqdn>` az alábbi útmutatásnak megfelelően).

1. Ezután futtassa a parancsot `nslookup <host_fqdn>` ( `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`például:). Ha a parancs feloldja a nevet egy IP-címhez, az azt jelenti, hogy a DNS-kiszolgáló megfelelően működik. Ebben az esetben az HDInsight-mel támogatási esetet kell megvizsgálni, és a probléma megvizsgálható. A támogatási esetben adja meg a végrehajtott hibaelhárítási lépéseket. Ez segít a probléma gyorsabb megoldásában.

1. Ha a fenti parancs nem ad vissza IP-címet, futtassa `nslookup <host_fqdn> 168.63.129.16` a parancsot ( `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`például:). Ha ez a parancs képes az IP-cím feloldására, az azt jelenti, hogy a DNS-kiszolgáló nem továbbítja a lekérdezést az Azure DNS-re, vagy nem egy olyan virtuális gép, amely a fürttel azonos vNet részét képezi.

1. Ha nem rendelkezik olyan Azure-beli virtuális géppel, amely egyéni DNS-kiszolgálóként működhet a fürt vNet, előbb fel kell vennie ezt. Hozzon létre egy virtuális gépet a vNet, amely DNS-továbbítóként lesz konfigurálva.

1. Miután telepített egy virtuális gépet a vNet, konfigurálja a DNS-továbbítási szabályokat ezen a virtuális gépen. Továbbítsa az összes iDNS névfeloldási kérelmet a 168.63.129.16, a többi pedig a DNS-kiszolgálóra. [Íme](../hdinsight-plan-virtual-network-deployment.md) egy példa erre a beállításra egy egyéni DNS-kiszolgálón.

1. Adja hozzá a virtuális gép IP-címét első DNS-bejegyzésként a Virtual Network DNS-konfigurációhoz.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nem sikerült csatlakozni az Azure Storage-fiókhoz"

### <a name="issue"></a>Probléma

A hiba leírása a következőt tartalmazza: "nem sikerült csatlakozni az Azure Storage-fiókhoz" vagy "nem sikerült csatlakozni az Azure SQL-hez".

### <a name="cause"></a>Ok

Az Azure Storage és az SQL nem rendelkezik rögzített IP-címmel, ezért engedélyeznie kell a kimenő kapcsolatokat az összes IP-címhez a szolgáltatások elérésének engedélyezéséhez. A pontos megoldási lépések attól függnek, hogy beállította-e a hálózati biztonsági csoportot (NSG) vagy a felhasználó által definiált szabályokat (UDR). A konfigurációk részletes ismertetését lásd: a [hálózati forgalom szabályozása hálózati biztonsági csoportokkal és felhasználó által megadott útvonalakkal HDInsight](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) .

### <a name="resolution"></a>Megoldás:

* Ha a fürt [hálózati biztonsági csoportot (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)használ.

    Nyissa meg a Azure Portal, és keresse meg azt az alhálózathoz társított NSG, amelyben a fürtöt üzembe helyezi. A **kimenő biztonsági szabályok** szakaszban korlátozás nélkül engedélyezheti az internetre irányuló kimenő hozzáférést (vegye figyelembe, hogy a kisebb **prioritási** szám nagyobb prioritást jelent). Emellett az **alhálózatok** szakaszban ellenőrizze, hogy a NSG alkalmazva van-e a fürt alhálózatára.

* Ha a fürt [felhasználó által definiált útvonalakat (UDR)](../../virtual-network/virtual-networks-udr-overview.md)használ.

    Nyissa meg a Azure Portal, és azonosítsa a fürt üzembe helyezéséhez használt alhálózathoz társított útválasztási táblázatot. Miután megtalálta az alhálózat útválasztási táblázatát, ellenőrizze az **útvonalak** szakaszt.

    Ha vannak meghatározott útvonalak, ellenőrizze, hogy vannak-e útvonalak az IP-címekhez ahhoz a régióhoz, ahol a fürtöt telepítették, és az egyes útvonalak **NextHopType** az **Internet**. A fenti cikkben leírt minden szükséges IP-címhez meg kell adni egy útvonalat.

---

### <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
