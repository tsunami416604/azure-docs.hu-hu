---
title: A fürt létrehozása DomainNotFound hibával meghiúsul az Azure HDInsight
description: Hibaelhárítási lépések és lehetséges megoldások az Azure HDInsight-fürtökkel való interakció során felmerülő problémák esetén
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005945"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Forgatókönyv: a fürt létrehozása DomainNotFound hibával meghiúsul az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A HDI Secure (Enterprise Security Package) fürt létrehozása nem sikerül, `DomainNotFound` hibaüzenet jelenik meg.

## <a name="cause"></a>Ok

Helytelen DNS-beállítások.

## <a name="resolution"></a>Feloldás

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]