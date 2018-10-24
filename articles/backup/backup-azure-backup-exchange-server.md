---
title: Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel
description: Ismerje meg az Azure Backup használata a System Center 2012 R2 DPM biztonsági mentése az Exchange server
services: backup
author: adigan
manager: NKolli1
ms.service: backup
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: adigan
ms.openlocfilehash: 8fbe3e716971f4d82e6d24fb866334ffd626bf32
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945977"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel
Ez a cikk ismerteti a System Center 2012 R2 Data Protection Manager (DPM) kiszolgáló a Microsoft Exchange-kiszolgáló biztonsági mentése az Azure Backup konfigurálása.  

## <a name="updates"></a>Frissítések
Sikerült regisztrálni a DPM-kiszolgáló az Azure Backup szolgáltatással, telepítenie kell a legújabb kumulatív frissítést a System Center 2012 R2 DPM és az Azure Backup szolgáltatás ügynökének legújabb verzióját. A legújabb kumulatív frissítést a lekérése a [Microsoft Catalog](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Ebben a cikkben szereplő példák az Azure Backup ügynök 2.0.8719.0 verziója van telepítve, és 6. kumulatív frissítés a System Center 2012 R2 DPM telepítve van.
>
>

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy minden a [Előfeltételek](backup-azure-dpm-introduction.md#prerequisites-and-limitations) védelme érdekében a Microsoft Azure Backup segítségével a számítási feladatok teljesül-e. Ezek az Előfeltételek a következők:

* Az Azure site backup-tároló létrehozása.
* Ügynök és a tároló hitelesítő adatok le vannak töltve a DPM-kiszolgálóhoz.
* Az ügynök telepítve van a DPM-kiszolgálón.
* A DPM-kiszolgáló regisztrálása használták a tároló hitelesítő adatait.
* Ha az Exchange 2016 védelmét, frissítse a DPM 2012 R2 UR9 vagy újabb verzióra

## <a name="dpm-protection-agent"></a>A DPM védelmi ügynök
Az Exchange-kiszolgálón a DPM védelmi ügynök telepítéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak helyesen van-e konfigurálva. Lásd: [tűzfalkivétel konfigurálása az ügynök számára](https://technet.microsoft.com/library/Hh758204.aspx).
2. Telepítse az ügynököt az Exchange server kattintva **felügyeleti > ügynökök > telepítése** a DPM felügyeleti konzolon. Lásd: [a DPM védelmi ügynök telepítése](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) a részletes lépéseket.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Az Exchange-kiszolgálón a védelmi csoport létrehozása
1. A DPM felügyeleti konzolon kattintson a **védelmi**, és kattintson a **új** megnyitásához az eszközök menüszalagján a **új védelmi csoport létrehozása** varázsló.
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
6. Kattintson a **Tovább** gombra.
7. Válassza ki a **Eseutil futtatása az adatok sértetlenségének ellenőrzéséhez** lehetőséget, ha azt szeretné, hogy az Exchange Server-adatbázisok sértetlenségének ellenőrzéséhez.

    Miután kiválasztotta ezt a beállítást, biztonsági mentés konzisztencia-ellenőrzést futni fog a DPM-kiszolgálón az i/o-forgalmat futtatásával elkerülése érdekében a **eseutil** parancsot az Exchange-kiszolgálón.

   > [!NOTE]
   > Használja ezt a beállítást, át kell másolnia az Eseutil.exe és az Ese.dll fájlokat a DPM-kiszolgálón a C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin könyvtárba. Ellenkező esetben a következő hiba akkor történik:  
   > ![az Eseutil hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Válassza ki az adatbázist a **másolásos biztonsági mentésre**, és kattintson a **tovább**.

   > [!NOTE]
   > Ha nem jelöli be a "Teljes biztonsági másolat" legalább egy DAG másolatot készít egy adatbázisról, naplók nem lesznek csonkolva.
   >
   >
10. A céljainak konfigurálása **rövid távú biztonsági mentés**, és kattintson a **tovább**.
11. Tekintse át a rendelkezésre álló lemezterület, és kattintson a **tovább**.
12. Válassza ki az időpontot, amikor a DPM-kiszolgáló fog létrehozni a kezdeti replikáció, és kattintson **tovább**.
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
1. Az Exchange-adatbázis visszaállításához kattintson **helyreállítási** a DPM felügyeleti konzolon.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válassza ki az online helyreállítási pontot a *helyreállítási idő* legördülő listából.
4. Kattintson a **helyreállítása** elindításához a **helyreállítási varázsló**.

Online helyreállítási pontok a öt helyreállítási típusa van:

* **Helyreállítás az eredeti Exchange-kiszolgáló:** fogja visszaállítani az adatokat az eredeti Exchange-kiszolgálón.
* **Helyreállítás az Exchange-kiszolgáló egy másik adatbázisba:** fogja visszaállítani az adatokat egy másik Exchange server egy másik adatbázisba.
* **Helyreállítás helyreállítási adatbázisba:** lesz az adatok állíthatók az Exchange helyreállítási adatbázis (RDB).
* **Másolás hálózati mappába:** az adatokat egy hálózati mappába lesznek helyreállítva.
* **Másolás szalagra:** egy szalagtárat vagy egy önálló szalagos meghajtót csatlakoztatott és a DPM-kiszolgálón konfigurálva van, ha a helyreállítási pont egy szabad szalag lesznek másolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>További lépések
* [Az Azure Backup – gyakori kérdések](backup-azure-backup-faq.md)
