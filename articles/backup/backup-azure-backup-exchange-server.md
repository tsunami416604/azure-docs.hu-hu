---
title: "Készítsen biztonsági mentést egy Exchange-kiszolgálóhoz, az Azure Backup és a System Center 2012 R2 DPM |} Microsoft Docs"
description: "Útmutató: biztonsági mentése az Exchange-kiszolgáló Azure Backup szolgáltatás használatával a System Center 2012 R2 DPM"
services: backup
documentationcenter: 
author: MaanasSaran
manager: NKolli1
editor: 
ms.assetid: 13f32256-888e-416e-a78b-40c2a26a5939
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: masaran;jimpark;delhan;trinadhk;markgal
ms.openlocfilehash: 9dc0105034e0d354a6cbbd7ba70634bdff83c1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel
Ez a cikk ismerteti a System Center 2012 R2 Data Protection Manager (DPM) kiszolgáló biztonsági mentése egy Microsoft Exchange-kiszolgáló Azure Backup konfigurálása.  

## <a name="updates"></a>Frissítések
Sikerült regisztrálni a DPM-kiszolgáló Azure Backup szolgáltatásnál, telepítenie kell a legújabb kumulatív frissítéssel a System Center 2012 R2 DPM és az Azure Backup szolgáltatás ügynökének legújabb verzióját. A legújabb kumulatív frissítéssel az beszerzése a [Microsoft Catalog](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> A cikkben szereplő példák az Azure Backup szolgáltatás ügynökének 2.0.8719.0 verziója van telepítve, és 6. kumulatív frissítés a System Center 2012 R2 DPM telepítve van.
>
>

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy minden a [Előfeltételek](backup-azure-dpm-introduction.md#prerequisites) a Microsoft Azure Backup szolgáltatás használatával történő védelméhez munkaterhelések teljesült. Az Előfeltételek a következők:

* Az Azure webhelyen egy biztonsági mentési tároló létrehozása.
* Ügynök és a tároló hitelesítő adatait a DPM-kiszolgáló lettek töltve.
* Az ügynök telepítve van a DPM-kiszolgálón.
* A tárolói hitelesítő adatokat a DPM-kiszolgáló regisztrálása használták.
* Exchange 2016 véd, frissítse a DPM 2012 R2 UR9 vagy újabb

## <a name="dpm-protection-agent"></a>A DPM védelmi ügynök
Az Exchange kiszolgálón a DPM védelmi ügynök telepítéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak konfigurációja megfelelő. Lásd: [tűzfalkivétel konfigurálása az ügynök számára](https://technet.microsoft.com/library/Hh758204.aspx).
2. Az Exchange-kiszolgálóhoz gombra kattintva a ügynök telepíthető **felügyeleti > ügynökök > telepítése** a DPM felügyeleti konzolján. Lásd: [a DPM védelmi ügynök telepítése](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) a részletes lépéseket.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Az Exchange-kiszolgáló védelmi csoport létrehozása
1. A DPM felügyeleti konzolján kattintson **védelmi**, és kattintson a **új** megnyitásához az eszközsávon a **új védelmi csoport létrehozása** varázsló.
2. Az a **üdvözlő** képernyőjén kattintson a varázsló **következő**.
3. Az a **védelmi csoport típusának kiválasztása** képernyőn, jelölje be **kiszolgálók** kattintson **következő**.
4. Válassza ki az Exchange server-adatbázis védelmét, és kattintson a kívánt **következő**.

   > [!NOTE]
   > Ha az Exchange 2013 védelmét, ellenőrizze a [Exchange 2013 előfeltételei](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    A következő példában az Exchange 2010 adatbázis van kiválasztva.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Az adatvédelmi módszer kiválasztása.

    A védelmi csoport neve, majd válassza ki a következők mindegyikét:

   * Rövid távú lemezes védelmet szeretnék.
   * Online védelmet szeretnék.
6. Kattintson a **Tovább** gombra.
7. Válassza ki a **Eseutil futtatása az adatok sértetlenségének ellenőrzéséhez** lehetőséget, ha azt szeretné, hogy az Exchange Server-adatbázisok sértetlenségének ellenőrzéséhez.

    Miután kiválasztotta ezt a beállítást, biztonsági mentés konzisztencia-ellenőrzést futni fog a DPM-kiszolgálón az i/o-forgalmat futtatásával elkerülése érdekében a **eseutil** parancsot az Exchange-kiszolgálón.

   > [!NOTE]
   > Használja ezt a beállítást, át kell másolnia az Ese.dll és az Eseutil.exe fájloknak a C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin könyvtárba, a DPM-kiszolgálón. Ellenkező esetben aktiválódik, a következő hibával:  
   > ![az Eseutil hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Válassza ki az adatbázist a **másolásos biztonsági mentésre**, és kattintson a **következő**.

   > [!NOTE]
   > Ha nincs bejelölve a "Teljes biztonsági másolat" adatbázis másolatának legalább egy DAG, naplók nem lesznek csonkolva.
   >
   >
10. Konfigurálja a célokat **rövid távú biztonsági mentés**, és kattintson a **következő**.
11. Tekintse át a rendelkezésre álló lemezterület, majd a **következő**.
12. Válassza ki a időpont, amikor a DPM-kiszolgáló fog létrehozni a kezdeti replikálás, és kattintson a **következő**.
13. A konzisztencia-ellenőrzési beállítások kiválasztása, és kattintson **következő**.
14. Adja meg az adatbázis biztonsági mentése az Azure-ba, és kattintson a kívánt **következő**. Példa:

    ![Online védelem adatainak megadása](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Adja meg a ütemezését **Azure biztonsági mentés**, és kattintson a **tovább**. Példa:

    ![Adja meg az online biztonsági mentés ütemezése](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Jegyezze fel az Online helyreállítási pontok alapuló Expressz teljes helyreállítási pontokat. Az online helyreállítási pontot, ezért úgy kell ütemeznie után az idő megadott az expressz teljes helyreállítási pont.
    >
    >
16. Konfigurálja az adatmegőrzési **Azure biztonsági mentés**, és kattintson a **következő**.
17. Válasszon egy online replikációs lehetőséget, és kattintson a **következő**.

    Ha nagy adatbázis, a kezdeti biztonsági másolatot a hálózaton keresztül a létrehozandó hosszú ideig eltarthat. A probléma elkerülése érdekében, létrehozhat egy offline biztonsági másolat.  

    ![Online megőrzési szabály megadása](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Hagyja jóvá a beállításokat, és kattintson a **csoport létrehozása**.
19. Kattintson a **Bezárás** gombra.

## <a name="recover-the-exchange-database"></a>Az Exchange-adatbázis helyreállítása
1. Exchange-adatbázis helyreállítása, kattintson a **helyreállítási** a DPM felügyeleti konzolon.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázis.
3. Válassza ki az online helyreállítási pontot a *helyreállításkor* legördülő listából.
4. Kattintson a **helyreállítása** elindítani a **helyreállítási varázsló**.

Az online helyreállítási pontok, öt helyreállítási típusa van:

* **Helyreállítás az eredeti Exchange-kiszolgálón:** fogja visszaállítani az adatokat az eredeti Exchange-kiszolgálóhoz.
* **Helyreállítás az Exchange-kiszolgáló egy másik adatbázisba:** fogja visszaállítani az adatokat egy másik egy másik Exchange server-adatbázisba.
* **Helyreállítás helyreállítási adatbázisba:** lesz az adatok helyreállítása egy Exchange helyreállítási adatbázis (Rekordadatbázis).
* **Másolás hálózati mappába:** fogja visszaállítani az adatokat egy hálózati mappába.
* **Másolás szalagra:** egy szalagtárat vagy egy önálló szalagos meghajtót csatlakoztatott és a DPM-kiszolgálón konfigurálva van, ha a helyreállítási pont egy szabad szalagra kerülnek-e.

    ![Válassza ki az online replikációs](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Következő lépések
* [Az Azure biztonsági mentési – gyakori kérdések](backup-azure-backup-faq.md)
