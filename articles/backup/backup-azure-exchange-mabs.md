---
title: Az Azure Backup az Azure Backup Server biztonsági mentése az Exchange server
description: 'Útmutató: biztonsági mentés Exchange-kiszolgáló Azure Backup az Azure Backup Server használatával'
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 40541596b4da9e0590d497785afd7d6d7f4cbcb4
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495394"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Egy Exchange-kiszolgáló biztonsági mentése az Azure-bA az Azure Backup Server
Ez a cikk ismerteti, hogyan konfigurálhatja a Microsoft Azure Backup Server (MABS) a Microsoft Exchange-kiszolgáló biztonsági mentése az Azure-bA.  

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy az Azure Backup Server [telepítve és előkészített](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>MABS védelmi ügynök
Az Exchange-kiszolgálón a MABS védelmi ügynök telepítéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak helyesen van-e konfigurálva. Lásd: [tűzfalkivétel konfigurálása az ügynök számára](https://technet.microsoft.com/library/Hh758204.aspx).
2. Telepítse az ügynököt az Exchange-kiszolgálóhoz gombra kattintva **felügyeleti > ügynökök > telepítése** a MABS felügyeleti konzolon. Lásd: [a MABS védelmi ügynök telepítése](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) a részletes lépéseket.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Az Exchange-kiszolgálón a védelmi csoport létrehozása
1. A MABS felügyeleti konzolon kattintson a **védelmi**, és kattintson a **új** megnyitásához az eszközök menüszalagján a **új védelmi csoport létrehozása** varázsló.
2. Az a **üdvözlő** a varázslóban kattintson képernyő **tovább**.
3. Az a **védelmi csoport típusának kiválasztása** képernyőn válassza ki **kiszolgálók** kattintson **tovább**.
4. Válassza ki az Exchange server-adatbázis védelmét, és kattintson a kívánt **tovább**.

   > [!NOTE]
   > Ha az Exchange 2013 védelmét, ellenőrizze a [az Exchange 2013 előfeltételei](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    A következő példa az Exchange 2010 adatbázishoz ki van jelölve.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Az adatvédelmi módszer kiválasztása.

    Nevezze el a védelmi csoport, és válassza ki a következő beállításokat is:

   * Rövid távú lemezes védelmet szeretnék.
   * Online védelmet szeretnék.
6. Kattintson a **tovább**.
7. Válassza ki a **Eseutil futtatása az adatok sértetlenségének ellenőrzéséhez** lehetőséget, ha azt szeretné, hogy az Exchange Server-adatbázisok sértetlenségének ellenőrzéséhez.

    Miután ezt a beállítást, biztonsági mentés konzisztencia-ellenőrzést fognak futni a MABS az i/o-forgalmat futtatásával elkerülése érdekében a **eseutil** parancsot az Exchange-kiszolgálón.

   > [!NOTE]
   > Használja ezt a beállítást, át kell másolnia az Eseutil.exe és az Ese.dll fájlokat a C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin könyvtárba a MAB-kiszolgálón. Ellenkező esetben a következő hiba akkor történik:  
   > ![az Eseutil hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **tovább**.
9. Válassza ki az adatbázist a **másolásos biztonsági mentésre**, és kattintson a **tovább**.

   > [!NOTE]
   > Ha nem jelöli be a "Teljes biztonsági másolat" legalább egy DAG másolatot készít egy adatbázisról, naplók nem lesznek csonkolva.
   >
   >
10. A céljainak konfigurálása **rövid távú biztonsági mentés**, és kattintson a **tovább**.
11. Tekintse át a rendelkezésre álló lemezterület, és kattintson a **tovább**.
12. Válassza ki, amelyen a MAB kiszolgálót fog létrehozni a kezdeti replikáció, és kattintson az idő **tovább**.
13. A konzisztencia-ellenőrzési beállítások kiválasztása, és kattintson a **tovább**.
14. Adja meg az adatbázis biztonsági mentése az Azure-ba, és kattintson a kívánt **tovább**. Példa:

    ![Online védelem adatainak megadása](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Adja meg az ütemezést a **Azure Backup**, és kattintson a **tovább**. Példa:

    ![Online biztonsági mentés ütemezésének megadása](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Vegye figyelembe az Online helyreállítási pontok alapján expressz teljes helyreállítási pontokat. Az online helyreállítási pontot, ezért úgy kell ütemeznie után az expressz teljes időpontját megadott helyreállítási pont.
    >
    >
16. Konfigurálása esetén a megtartási házirendben **Azure Backup**, és kattintson a **tovább**.
17. Válasszon egy online replikáció lehetőséget, és kattintson a **tovább**.

    Ha nagy méretű adatbázisok, azt a kezdeti biztonsági mentés, a hálózat felett létrehozandó kell hosszú ideig eltarthat. A probléma elkerülése érdekében, létrehozhat egy offline biztonsági mentés.  

    ![Online megőrzési szabály megadása](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Hagyja jóvá a beállításokat, és kattintson a **csoport létrehozása**.
19. Kattintson a **Bezárás** gombra.

## <a name="recover-the-exchange-database"></a>Az Exchange-adatbázis helyreállítása
1. Az Exchange-adatbázis visszaállításához kattintson **helyreállítási** a MABS felügyeleti konzolon.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válassza ki az online helyreállítási pontot a *helyreállítási idő* legördülő listából.
4. Kattintson a **helyreállítása** elindításához a **helyreállítási varázsló**.

Online helyreállítási pontok a öt helyreállítási típusa van:

* **Helyreállítás az eredeti Exchange-kiszolgáló:** Az adatok az eredeti Exchange-kiszolgáló fogja visszaállítani.
* **Helyreállítás egy másik adatbázisba egy Exchange-kiszolgálón:** Az adatok egy másik Exchange server egy másik adatbázisba lesznek helyreállítva.
* **Helyreállítás helyreállítási adatbázisba:** Az adatokat az Exchange helyreállítási adatbázis (RDB) megtérülnek.
* **Másolás hálózati mappába:** Az adatok egy hálózati mappába lesznek helyreállítva.
* **Másolás szalagra:** Ha egy szalagtárat vagy egy önálló szalagos meghajtót csatlakoztatott és a MABS konfigurálva van, a helyreállítási pont egy szabad szalagra kerül másolhatók.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>További lépések
* [Az Azure Backup – gyakori kérdések](backup-azure-backup-faq.md)
