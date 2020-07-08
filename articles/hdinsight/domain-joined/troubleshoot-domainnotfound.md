---
title: A fürt létrehozása DomainNotFound hibával meghiúsul az Azure HDInsight
description: Hibaelhárítási lépések és lehetséges megoldások az Azure HDInsight-fürtökkel való interakció során felmerülő problémák esetén
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76776234"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Forgatókönyv: a fürt létrehozása DomainNotFound hibával meghiúsul az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A HDI Secure (Enterprise Security Package) fürt létrehozása nem sikerül, `DomainNotFound` hibaüzenet jelenik meg.

## <a name="cause"></a>Ok

Helytelen DNS-beállítások.

## <a name="resolution"></a>Megoldás:

A tartományhoz csatlakoztatott fürtök telepítésekor a HDI létrehoz egy belső felhasználónevet és egy jelszót a HRE DS-ben (minden fürthöz), és az összes fürtcsomópont ehhez a tartományhoz csatlakozik. A tartományhoz való csatlakozás a Samba-eszközök használatával valósítható meg. Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* A tartománynévnek a DNS használatával kell feloldania.
* A tartományvezérlők IP-címét be kell állítani azon virtuális hálózat DNS-beállításaiban, ahol a fürtöt üzembe helyezi.
* Ha a virtuális hálózat a HRE DS virtuális hálózatával van társítva, akkor azt manuálisan kell elvégezni.
* Ha DNS-továbbítókat használ, a tartománynevet megfelelően fel kell oldani a virtuális hálózaton belül.
* A biztonsági házirendek (NSG) nem blokkolják a tartományhoz való csatlakozást.

### <a name="additional-debugging-steps"></a>További hibakeresési lépések

* Egy Windows rendszerű virtuális gép üzembe helyezése ugyanabban az alhálózatban, tartomány csatlakoztatása a számítógéphez felhasználónévvel és jelszóval (ezt a Vezérlőpult felhasználói felületén keresztül teheti meg), vagy

* Ubuntu-alapú virtuális gép üzembe helyezése ugyanabban az alhálózatban és tartomány csatlakoztatása a géphez
  * SSH a gépre
  * sudo Su
  * A szkript futtatása felhasználónévvel és jelszóval
  * A szkript pingelni fog, létrehozza a szükséges konfigurációs fájlokat, majd tartományt. Ha ez sikeres, a DNS-beállítások jók.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
