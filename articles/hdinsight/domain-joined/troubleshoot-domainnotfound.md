---
title: A fürt létrehozása sikertelen az Azure HDInsight Tartomány nem található hibájával
description: Az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák lépéseinek és megoldásának elhárítása
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776234"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Eset: A fürt létrehozása sikertelen az Azure HDInsight DomainNotFound hibával

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A HDI Secure (Enterprise Security `DomainNotFound` Package) fürt létrehozása hibaüzenettel meghiúsul.

## <a name="cause"></a>Ok

Helytelen DNS-beállítások.

## <a name="resolution"></a>Megoldás:

A tartományhoz csatlakozó fürtök telepítésekor a HDI belső felhasználónevet és jelszót hoz létre az AAD DS-ben (minden egyes fürthöz), és csatlakozik a tartomány összes fürtcsomópontjához. A tartományhoz való csatlakozás samba eszközökkel történik. Győződjön meg arról, hogy a következő előfeltételek teljesülnek:

* A tartománynév nek dns-en keresztül kell feloldania.
* A tartományvezérlők IP-címét annak a virtuális hálózatnak a DNS-beállításaiban kell beállítani, amelyen a fürt telepítve van.
* Ha a virtuális hálózat az AAD DS virtuális hálózatával van társviszonyban, akkor azt manuálisan kell elvégezni.
* Ha DNS-továbbítókat használ, a tartománynévnek megfelelően kell feloldania a virtuális hálózaton belül.
* A biztonsági házirendek (NSG-k) nem akadályozhatják a tartományhoz való csatlakozást.

### <a name="additional-debugging-steps"></a>További hibakeresési lépések

* Windows virtuális gép telepítése ugyanabban az alhálózatban, a tartomány felhasználónévvel és jelszóval csatlakozik a számítógéphez (ez a vezérlőpult felhasználói felületén keresztül is elvégezhető), vagy

* Ubuntu virtuális gép üzembe helyezése ugyanabban az alhálózatban és tartománycsatlakozás a géphez
  * SSH a gépbe
  * sudo su
  * Futtassa a parancsfájlt felhasználónévvel és jelszóval
  * A parancsfájl pingel, létrehozza a szükséges konfigurációs fájlokat, majd tartományt. Ha ez sikerül, a DNS-beállítások jó.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
