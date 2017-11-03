---
title: "Lassú biztonsági mentési fájlok és mappák, az Azure Backup hibaelhárítása |} Microsoft Docs"
description: "Az Azure Backup teljesítményproblémák okait diagnosztizálása hibaelhárítási útmutatást nyújt"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: genli
ms.openlocfilehash: f1aa4117b389bb127eb7235f69f587dcb715ac25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
Ez a cikk nyújt hibaelhárítási útmutatót okának fájlok és mappák biztonsági mentési teljesítménycsökkenést használatakor Azure Backup segítségével. Fájlok biztonsági mentése az Azure Backup szolgáltatás ügynöke használatakor a biztonsági mentési folyamat hosszabb ideig eltarthat. Ez a késés oka lehet az alábbiak közül:

* [Nincsenek szűk keresztmetszetek a számítógépen, amelyek biztonsági mentése van folyamatban.](#cause1)
* [Egy másik folyamat vagy a víruskereső szoftver ütközik az Azure biztonsági mentési folyamat során.](#cause2)
* [A Backup szolgáltatás ügynökének futása egy Azure virtuális gépen (VM).](#cause3)  
* [Biztonsági mentést készít a fájlok sok (több millió).](#cause4)

Mielőtt elkezdené a problémák elhárításához, azt javasoljuk, hogy töltse le és telepítse a [legújabb Azure Backup szolgáltatás ügynökének](http://aka.ms/azurebackup_agent). Gyakori frissítések különböző kapcsolatos problémák megoldása, a szolgáltatások hozzáadása és a teljesítmény javítása a biztonsági mentés Agent biztosítjuk.

Emellett ajánlott áttekinteni a [Azure Backup szolgáltatás – gyakori kérdések](backup-azure-backup-faq.md) győződjön meg arról, hogy nem minden a közös konfigurációs problémák tapasztalhatók a.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>OK: Szűk keresztmetszetek a számítógépen
A szűk keresztmetszetek a számítógépen, amely biztonsági mentése van folyamatban késést okozhat. Például a számítógép képes olvasni vagy írni lemezre vagy küldhet adatokat a hálózaton keresztül a rendelkezésre álló sávszélesség szűk keresztmetszetek okozhat.

A Windows beépített eszközt nevezett biztosít [Teljesítményfigyelő](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) a szűk keresztmetszetek észleléséhez.

Íme, néhány teljesítményszámlálók és a tartományt a szűk keresztmetszetek optimális biztonsági másolatok diagnosztizálásához hasznos lehet.

| A számláló | status |
| --- | --- |
| Logikai lemez (fizikai lemez) – üresjárati % |• 100 %-os üresjárati 50 %-os üresjárati = kifogástalan</br>• 49 % 20 %-át üresjárati üresjárati = figyelmeztetés vagy a figyelő</br>• 19 % 0 % üresjárati üresjárati kritikus vagy kívüli Spec = |
| Logikai lemez (fizikai lemez) – % Átl. Lemez mp Olvasás vagy írás |• 0,015 MS 0,001 ms = kifogástalan</br>• 0,025 MS 0,015 ms = figyelmeztetés vagy a figyelő</br>• 0.026 ms vagy kritikus vagy kívüli Spec már = |
| Logikai lemez (fizikai lemez) – Lemezvárólista jelenlegi hossza (az összes példányra) |80 kérelem 6 percnél hosszabb ideig |
| Memória – nem lapozható memória |• Felhasznált készlet 60 %-nál kisebb = kifogástalan<br>• 61 % és 80 %-készlet felhasznált = figyelmeztetés vagy a figyelő</br>• Nagyobb, mint a 80 %-készlet felhasznált kritikus vagy kívüli Spec = |
| Memória – lapozható készlet mérete bájtban |• Felhasznált készlet 60 %-nál kisebb = kifogástalan</br>• 61 % és 80 %-készlet felhasznált = figyelmeztetés vagy a figyelő</br>• Nagyobb, mint a 80 %-készlet felhasznált kritikus vagy kívüli Spec = |
| Memória – rendelkezésre álló hely MB-ban |• 50 %-a rendelkezésre álló szabad memória vagy további = kifogástalan</br>• 25 %-a rendelkezésre álló szabad memória = figyelője</br>• 10 %-a rendelkezésre álló szabad memória = figyelmeztetés</br>• Kevesebb mint 100 MB vagy 5 %-a rendelkezésre álló szabad memória kritikus vagy kívüli Spec = |
| Processzor –\%processzor kihasználtsága () |• 60 %-nál kisebb felhasznált = kifogástalan</br>• 61 – 90 %-felhasznált = figyelő vagy figyelmeztetés</br>• 91-100 % felhasznált kritikus = |

> [!NOTE]
> Ha azt állapítja meg, hogy az infrastruktúra-e a probléma oka, azt javasoljuk, hogy a lemezek, rendszeresen a jobb teljesítmény töredezettségmentesítése.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>OK: Egy másik folyamat vagy az Azure Backup zavarja, a víruskereső szoftver
Ahol más folyamatok, a Windows rendszerben az Azure Backup szolgáltatás ügynökének folyamatánál teljesítményét negatívan befolyásolt több példányt is láttuk. Például ha használja az Azure Backup szolgáltatás ügynöke egy másik program adatok biztonsági mentéséhez, vagy ha víruskereső fut, és zárolta a fájlok biztonsági mentését, az a többszörös zárolja a fájlok okozhat versengés. Ebben a helyzetben a biztonsági mentés sikertelen lehet, vagy a feladat a vártnál hosszabb ideig is tarthat.

A legjobb javasoljuk ebben a forgatókönyvben, megtekintéséhez, hogy megváltozik-e a biztonsági mentési időt, hogy az Azure Backup szolgáltatás ügynöke más biztonsági mentési program kikapcsolása. Általában annak biztosítása, hogy több biztonsági mentési feladatok nem futnak egyszerre is használhatók, nehogy azok egymással.

A víruskereső programok azt javasoljuk, hogy a következő fájlok és helyek kizárása:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe folyamatként
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappák
* Ideiglenes helyre (Ha nem használja a szokásos helyre)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>OK: Egy Azure virtuális gépen futó Backup szolgáltatás ügynökének
Ha a virtuális gép futtatja a biztonsági mentési ügynök, teljesítmény lesz lassabb, mint ha is futtat, a fizikai gépen. Ez egy várható üzenet, IOPS korlátozásai miatt.  A teljesítmény azonban a adatok meghajtókat, amelyek biztonsági mentése folyamatban prémium szintű Azure Storage váltásával is optimalizálhatja. A probléma megoldásán dolgozunk, és a javítás egy későbbi kiadásban elérhető lesz.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>OK: Biztonsági másolatának fájljait sok (több millió)
Nagy mennyiségű adat áthelyezését-nál kisebb mennyiségű adat áthelyezését hosszabb ideig tart. Bizonyos esetekben nem csupán az adatokat, hanem a fájlok vagy mappák száma méretétől biztonságimásolat-készítési időpont vezethető vissza. Ez akkor különösen igaz olyan esetben, ha több millió kis fájlt (néhány kilobájtostól néhány bájtok) biztonsági mentése van folyamatban.

Ez akkor fordul elő, mert az adatok biztonsági mentése és áthelyezni, ha az Azure-ba, amíg Azure egyidejűleg katalogizálni a fájlokat. Bizonyos ritka esetekben a katalógus művelet hosszabb ideig is tarthat.

A következő mutatók segítségére lehetnek a szűk keresztmetszetek ismertetése, és ennek megfelelően működni a következő lépéseket:

* **Felhasználói felület folyamatban láthatók az adatátvitel**. A továbbra is adatátvitel. A hálózati sávszélesség vagy adatok mérete okozza késést.
* **Felhasználói felület nem látható folyamatban van az adatok átvitele**. Nyissa meg a naplók helye: C:\Microsoft Azure Recovery Services Agent\Temp, és ellenőrizze a naplókat a FileProvider::EndData bejegyzést. Ez a bejegyzés azt jelzi, hogy, hogy az adatok átvitele befejeződött, és a katalógus művelet történik. A biztonsági mentési feladat nem szakítható meg. Ehelyett Várjon, amíg már kissé a katalógus művelet befejezéséhez. Ha a probléma továbbra is fennáll, forduljon a [az Azure támogatási](https://portal.azure.com/#create/Microsoft.Support).
