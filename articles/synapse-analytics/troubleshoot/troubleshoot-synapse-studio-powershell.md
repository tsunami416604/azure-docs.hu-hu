---
title: Az Azure Synapse Studio (előzetes verzió) kapcsolattal kapcsolatos PowerShell – problémamegoldás
description: Az Azure Synapse Studio-kapcsolat hibáinak elhárítása a PowerShell használatával
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431474"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Az Azure Synapse Studio (előzetes verzió) csatlakozási problémáinak diagnosztizálása a PowerShell-parancsfájllal

Az Azure Synapse Studio (előzetes verzió) a webes API-végpontok megfelelő működésétől függ. Ez az útmutató segít azonosítani a kapcsolódási problémák okait, ha:
- konfigurálása a helyi hálózat (például a hálózat mögött egy vállalati tűzfal) az Azure Synapse Studio eléréséhez.
- kapcsolódási problémák az Azure Synapse Studio használatával.

## <a name="prerequisite"></a>Előfeltétel

* PowerShell 5.0-s vagy újabb verziója Windows rendszeren, vagy
* PowerShell Core 6.0-s vagy újabb verzió Windows vagy Linux rendszeren.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Kattintson a jobb gombbal az alábbi linkre, és kattintson a "Cél mentése másként" parancsra:

- [Teszt-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Másik lehetőségként közvetlenül is megnyithatja a hivatkozást, és mentheti a megnyitott parancsfájlt. Ne mentse a fenti hivatkozás címét, mert a jövőben változhat.

A fájlkezelőben kattintson a jobb gombbal a letöltött parancsfájlra, és kattintson a "Futtatás a PowerShell-el" parancsra.

![Letöltött parancsfájl futtatása a PowerShell segítségével](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Amikor a rendszer kéri, adja meg az Azure Synapse munkaterület nevét, amely jelenleg problémát tapasztal, vagy hogy szeretné tesztelni a kapcsolatot, és nyomja meg az entergombot.

![Munkaterület nevének megadása](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

A diagnosztikai munkamenet elindul. Várd meg, amíg befejeződik.

![Várja meg, amíg a diagnózis befejeződik](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Végül egy diagnosztikai összefoglaló jelenik meg. Ha a számítógép nem tud csatlakozni egy vagy több végponthoz, az "Összegzés" szakaszban megjelenik néhány javaslat.

![Diagnosztikai összefoglaló áttekintése](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Ezenkívül a rendszer a hibaelhárító parancsfájllal azonos mappában hozza létre a munkamenet diagnosztikai naplófájlját. A helyét az "Általános tippek"`D:\TestAzureSynapse_2020....log`részben ( jelenik meg. ). Szükség esetén elküldheti ezt a fájlt a technikai támogatásnak.

Ha Ön hálózati rendszergazda, és az Azure Synapse Studio tűzfal-konfigurációjának finomhangolása, az "Összegzés" szakasz felett látható technikai részletek segíthetnek.

* Az "Átadott" jelöléssel ellátott összes tesztelem (kérés) azt jelenti, hogy megfeleltek a kapcsolati teszteken, függetlenül a HTTP állapotkódtól.
 A sikertelen kérelmek esetén az ok sárga színnel jelenik meg, például `NamedResolutionFailure` vagy `ConnectFailure`. Ezek az okok segíthetnek annak kiderítésében, hogy vannak-e helytelen konfigurációk a hálózati környezettel kapcsolatban.


## <a name="next-steps"></a>További lépések
Ha az előző lépések nem segítenek a probléma megoldásában, akkor [hozzon létre egy támogatási jegyet.](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)
