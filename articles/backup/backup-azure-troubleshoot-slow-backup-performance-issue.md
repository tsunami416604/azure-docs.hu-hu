---
title: Fájlok és mappák lassú biztonsági mentése – problémamegoldás
description: Hibaelhárítási útmutatást nyújt az Azure Backup teljesítményproblémáinak okainak diagnosztizálásához
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 6c650ee735ffcdd50f4361a867fa592f4965ab68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408691"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása

Ez a cikk hibaelhárítási útmutatást nyújt a fájlok és mappák lassú biztonsági mentési teljesítményének diagnosztizálásához az Azure Backup használata során. Ha az Azure Backup ügynök fájlok biztonsági mentéséhez használja, a biztonsági mentési folyamat a vártnál tovább tarthat. Ezt a késleltetést az alábbi okokkal okozhatja:

* [A biztonsági másolatot tartalmazó számítógépen teljesítményszűk keresztmetszetek vannak.](#cause1)
* [Egy másik folyamat vagy víruskereső szoftver zavarja az Azure Backup folyamat.](#cause2)
* [A biztonsági mentési ügynök fut egy Azure virtuális gépen (VM).](#cause3)  
* [Nagy számú (millió) fájlról készít biztonsági másolatot.](#cause4)

A hibaelhárítás megkezdése előtt javasoljuk, hogy töltse le és telepítse a [legújabb Azure Backup-ügynököt.](https://aka.ms/azurebackup_agent) Gyakori frissítéseket készítünk a biztonsági mentési ügynökhöz a különböző problémák megoldásához, a funkciók hozzáadásához és a teljesítmény javításához.

Azt is javasoljuk, hogy tekintse át az [Azure Backup szolgáltatás gyIK](backup-azure-backup-faq.md) győződjön meg arról, hogy nem tapasztalja a gyakori konfigurációs problémák.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Ok: A biztonsági mentési feladat nem optimalizált módban fut

* A MARS-ügynök a teljes kötet beolvasásával futtathatja a biztonsági mentési feladatot **optimalizált módban** az USN (frissítési sorszám) módosítása napló vagy **nem optimalizált módban.**
* Az nem optimalizált mód lassú, mivel az ügynöknek a kötet minden egyes fájlját be kell vizsgálnia, és össze kell hasonlítania a metaadatokkal a módosított fájlok meghatározásához.
* Ennek ellenőrzéséhez nyissa meg a **Feladat részletei lehetőséget** a MARS-ügynök konzoljáról, és ellenőrizze az állapotot, és ellenőrizze, hogy az adatok **átvitele (nem optimalizált, több időt vehet igénybe)** az alábbiak szerint:

    ![Futtatás nem optimalizált módban](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* A következő feltételek miatt a biztonsági mentési feladat nem optimalizált módban futhat:
  * Az első biztonsági mentés (más néven kezdeti replikáció) mindig nem optimalizált módban fog futni
  * Ha az előző biztonsági mentési feladat sikertelen, akkor a következő ütemezett biztonsági mentési feladat nem optimalizáltként fog futni.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Ok: A számítógép teljesítményszűk keresztmetszetei

A biztonsági másolatot tartalmazó számítógép szűk keresztmetszetei késéseket okozhatnak. Például a számítógép lemezre olvasási vagy írási képessége, vagy a hálózaton keresztüli adatküldéshez rendelkezésre álló sávszélesség szűk keresztmetszeteket okozhat.

A Windows egy beépített eszközt, a [Performance Monitort](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) biztosítja a szűk keresztmetszetek észlelésére.

Íme néhány teljesítményszámláló és tartomány, amelyek hasznosak lehetnek az optimális biztonsági mentések szűk keresztmetszeteinek diagnosztizálásában.

| Számláló | status |
| --- | --- |
| Logikai lemez(fizikai lemez)--%tétlen |* 100% tétlen 50% tétlen = Egészséges</br>* 49% tétlen 20% tétlen = Figyelmeztetés vagy monitor</br>* 19% tétlen 0% tétlen = kritikus vagy out of Spec |
| Logikai lemez(fizikai lemez)--%Átlagos lemezmásodpercek olvasása vagy írása |* 0,001 ms és 0,015 ms = Kifogástalan</br>* 0,015 ms és 0,025 ms = Figyelmeztetés vagy monitor</br>* 0,026 ms vagy hosszabb = kritikus vagy out of Spec |
| Logikai lemez(fizikai lemez)--Aktuális lemezvárólista hossza (az összes példányhoz) |80 kérelem több mint 6 percig |
| Memória – nem lapozható bájtok készletei |* Kevesebb, mint 60%-a medence fogyasztott = Egészséges<br>* 61%, hogy 80%-a medence fogyasztott = Figyelmeztetés vagy Monitor</br>* Nagyobb, mint 80% medence fogyasztott = kritikus vagy out of Spec |
| Memória – lapozható bájtok |* Kevesebb, mint 60%-a medence fogyasztott = Egészséges</br>* 61%, hogy 80%-a medence fogyasztott = Figyelmeztetés vagy Monitor</br>* Nagyobb, mint 80% medence fogyasztott = kritikus vagy out of Spec |
| Memória – Elérhető megabájt |* 50%-a szabad memória áll rendelkezésre, vagy több = Egészséges</br>* 25%-a szabad memória áll rendelkezésre = Monitor</br>* 10%-a szabad memória áll rendelkezésre = Figyelmeztetés</br>* Kevesebb, mint 100 MB vagy 5%-a szabad memória áll rendelkezésre = kritikus vagy out of Spec |
| Processzor --\%Processzor idő (minden példány) |* Kevesebb, mint 60% fogyasztott = Egészséges</br>* 61% -hoz 90% fogyasztott = Monitor vagy óvatosság</br>* 91% -hoz 100% elfogyasztott = Kritikus |

> [!NOTE]
> Ha úgy dönt, hogy az infrastruktúra a bűnös, azt javasoljuk, hogy a lemezek töredezettségmentesítése rendszeresen a jobb teljesítmény érdekében.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Ok: Az Azure Backup működését zavaró másik folyamat vagy víruskereső szoftver

Számos olyan esetet láttunk, amikor a Windows rendszer más folyamatai negatívan befolyásolták az Azure Backup ügynök folyamatának teljesítményét. Ha például az Azure Backup-ügynököt és egy másik programot is használja az adatok biztonsági mentéséhez, vagy ha víruskereső szoftver fut, és zárolja a fájlokat, amelyekről biztonsági másolatot kell készíteni, a fájlok többszörös zárolása versengést okozhat. Ebben az esetben a biztonsági mentés sikertelen lehet, vagy a feladat a vártnál tovább tarthat.

Ebben a forgatókönyvben a legjobb javaslat az, hogy kapcsolja ki a másik biztonsági mentési programot, hogy lássa, hogy az Azure Backup ügynök biztonsági mentési ideje megváltozik-e. Általában győződjön meg arról, hogy több biztonsági mentési feladatok nem futnak egyszerre elegendő ahhoz, hogy megakadályozzák, hogy befolyásolják egymást.

Víruskereső programok esetén azt javasoljuk, hogy zárja ki a következő fájlokat és helyeket:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe mint folyamat
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappák
* A munkajogúság helye (ha nem a normál helyet használja)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Ok: Azure-beli virtuális gépen futó biztonságimásolat-ügynök

Ha a biztonsági mentési ügynök egy virtuális gépen, a teljesítmény lassabb lesz, mint amikor futtatja a fizikai gépen. Ez az IOPS-korlátozások miatt várható.  Azonban optimalizálhatja a teljesítményt az Azure Premium Storage-ra biztonsági másolatot tartalmazó adatmeghajtók váltásával. Dolgozunk a probléma megoldásán, és a javítás egy későbbi kiadásban lesz elérhető.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Ok: Nagy számú (millió) fájl biztonsági mentése

Nagy mennyiségű adat áthelyezése hosszabb időt vesz igénybe, mint egy kisebb adatmennyiség áthelyezése. Bizonyos esetekben a biztonsági mentési idő nem csak az adatok méretéhez kapcsolódik, hanem a fájlok vagy mappák számához is. Ez különösen akkor igaz, ha több millió kis fájlok (néhány byte néhány kilobájt) is biztonsági másolatot.

Ez a jelenség azért fordul elő, mert miközben biztonsági másolatot készít az adatokról, és áthelyezi őket az Azure-ba, az Azure egyidejűleg katalogizálja a fájlokat. Egyes ritka esetekben a katalógus-művelet a vártnál tovább tarthat.

A következő mutatók segíthetnek megérteni a szűk keresztmetszetet, és ennek megfelelően dolgozni a következő lépéseken:

* **A felhasználói felület az adatátvitel előrehaladását mutatja.** Az adatok átvitele még folyamatban van. A hálózati sávszélesség vagy az adatok mérete késleltetheti a hálózati sávszélességet.
* **A felhasználói felület nem mutatja az adatátvitel előrehaladását.** Nyissa meg a C:\Program Files\Microsoft Azure Recovery Services Agent\Temp mappában található naplókat, majd keresse meg a FileProvider::EndData bejegyzést a naplókban. Ez a bejegyzés azt jelzi, hogy az adatátvitel befejeződött, és a katalógus művelet történik. Ne szakítsa meg a tartalék feladatokat. Ehelyett várjon még egy kicsit, amíg a katalógusművelet befejeződik. Ha a probléma továbbra is fennáll, forduljon az [Azure ügyfélszolgálatához.](https://portal.azure.com/#create/Microsoft.Support)

## <a name="next-steps"></a>További lépések

* [Gyakori kérdések a fájlok és mappák biztonsági mentésével kapcsolatban](backup-azure-file-folder-backup-faq.md)