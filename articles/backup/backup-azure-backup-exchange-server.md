---
title: Exchange-kiszolgáló biztonsági és biztonsági másolatot készítő biztonsági pontja a System Center DPM szolgáltatásán keresztül
description: Megtudhatja, hogy miként készíthet biztonsági másolatot az Exchange-kiszolgálóról az Azure Backup szolgáltatásba a System Center 2012 R2 DPM használatával
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 389713767409ff49c6fc83a4d6e8dc3c87272fc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614373"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel

Ez a cikk azt ismerteti, hogyan konfigurálható a System Center 2012 R2 Adatvédelmi kezelő (DPM) kiszolgáló biztonsági mentése a Microsoft Exchange kiszolgálóról az Azure Backup szolgáltatásba.  

## <a name="updates"></a>Frissítések

A DPM-kiszolgáló sikeres regisztrálásához az Azure Backup szolgáltatással telepítenie kell a System Center 2012 R2 DPM legújabb összegző frissítőcsomagját és az Azure backup-ügynök legújabb verzióját. A legújabb összegző frissítőcsomag beolvasása a [Microsoft Katalógusból](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> A jelen cikkben szereplő példákban az Azure biztonsági mentési ügynök 2.0.8719.0-s verziója van telepítve, és a 6.
>
>

## <a name="prerequisites"></a>Előfeltételek

Mielőtt [folytatna,](backup-azure-dpm-introduction.md#prerequisites-and-limitations) győződjön meg arról, hogy a Microsoft Azure Backup számítási feladatok védelmére való használatának minden előfeltétele teljesült. Ezek az előfeltételek a következők:

* Az Azure-webhelyen létrehozott egy biztonsági mentési tárolót.
* Az ügynök és a tároló hitelesítő adatai le lettek töltve a DPM-kiszolgálóra.
* Az ügynök telepítve van a DPM-kiszolgálón.
* A tároló hitelesítő adatait a DPM-kiszolgáló regisztrálásához használták.
* Ha az Exchange 2016-ot védi, frissítsen a DPM 2012 R2 UR2 vagy újabb verzióra

## <a name="dpm-protection-agent"></a>DPM védelmi ügynök

A DPM-védelmi ügynök Exchange-kiszolgálóra történő telepítéséhez hajtsa végre az alábbi lépéseket:

1. Ellenőrizze, hogy a tűzfalak megfelelően vannak-e konfigurálva. Lásd: [Tűzfalkivételek konfigurálása az ügynökhöz](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Telepítse az ügynököt az Exchange-kiszolgálóra a **DPM** felügyeleti > ügynökök > telepítése elemre kattintva a DPM felügyeleti felügyeleti konzolján. Részletes lépésekért [olvassa el A DPM védelmi ügynök telepítése](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) című témakört.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Védelmi csoport létrehozása az Exchange-kiszolgálóhoz

1. A DPM felügyeleti konzolon kattintson a **Védelem**gombra, majd az eszközmenüszalag **Új** gombjára kattintva nyissa meg az **Új védelmi csoport létrehozása varázslót.**
2. A varázsló **üdvözlőképernyőjén** kattintson a **Tovább**gombra.
3. A **Védelmi csoport típusának kiválasztása** képernyőn válassza a **Kiszolgálók** lehetőséget, és kattintson a **Tovább**gombra.
4. Jelölje ki a védeni kívánt Exchange-kiszolgálóadatbázist, és kattintson a **Tovább**gombra.

   > [!NOTE]
   > Ha az Exchange 2013-at védi, ellenőrizze az [Exchange 2013 előfeltételeit.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12))
   >
   >

    A következő példában az Exchange 2010 adatbázis van kiválasztva.

    ![Csoporttagok kijelölése](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Válassza ki az adatvédelmi módszert.

    Nevezze el a védelmi csoportot, majd adja meg mindkét alábbi lehetőséget:

   * Rövid távú védelmet akarok a Disk használatával.
   * Online védelmet akarok.
6. Kattintson a **Tovább** gombra.
7. Az **Eseutil futtatása** lehetőséget az adatintegritás ellenőrzéséhez válassza, ha ellenőrizni szeretné az Exchange Server-adatbázisok integritását.

    Miután ezt a beállítást választotta, a biztonsági mentés konzisztencia-ellenőrzése a DPM-kiszolgálón fut, hogy elkerülje az **eseutil** parancs Exchange kiszolgálón történő futtatásával generált I/O-forgalmat.

   > [!NOTE]
   > A beállítás használatához az Ese.dll és az Eseutil.exe fájlokat a DPM-kiszolgáló C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin könyvtárába kell másolni. Ellenkező esetben a következő hiba lép fel:  
   > ![eseutil hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Jelölje ki a **Biztonsági másolat**adatbázisát, majd kattintson a **Tovább**gombra.

   > [!NOTE]
   > Ha nem választja ki a "Teljes biztonsági mentés" lehetőséget az adatbázis legalább egy DAG-példányához, a naplók nem lesznek csonkolva.
   >
   >
10. Konfigurálja a célokat a **rövid távú biztonsági mentéshez,** majd kattintson a **Tovább**gombra.
11. Tekintse át a rendelkezésre álló lemezterületet, majd kattintson a **Tovább**gombra.
12. Válassza ki azt az időpontot, amikor a DPM-kiszolgáló létrehozza a kezdeti replikációt, majd kattintson a **Tovább**gombra.
13. Adja meg a konzisztencia-ellenőrzési beállításokat, majd kattintson a **Tovább**gombra.
14. Válassza ki azt az adatbázist, amelyről biztonsági másolatot szeretne tenni az Azure-ba, majd kattintson a **Tovább**gombra. Példa:

    ![Online védelmi adatok megadása](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Adja meg az **Azure Backup**ütemezését, majd kattintson a **Tovább**gombra. Példa:

    ![Online biztonsági mentés ütemezésének megadása](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Megjegyzés: Az online helyreállítási pontok expressz teljes helyreállítási pontokon alapulnak. Ezért az online helyreállítási pontot az expressz teljes helyreállítási ponthoz megadott idő után kell ütemezni.
    >
    >
16. Konfigurálja az **Azure Backup**adatmegőrzési szabályzatát, majd kattintson a **Tovább**gombra.
17. Válasszon egy online replikációs beállítást, majd kattintson a **Tovább**gombra.

    Ha nagy adatbázissal rendelkezik, a kezdeti biztonsági mentés létrehozása a hálózaton keresztül hosszú időt vehet igénybe. A probléma elkerülése érdekében létrehozhat egy offline biztonsági másolatot.  

    ![Online adatmegőrzési házirend megadása](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Erősítse meg a beállításokat, majd kattintson a **Csoport létrehozása gombra.**
19. Kattintson a **Bezárás** gombra.

## <a name="recover-the-exchange-database"></a>Az Exchange-adatbázis helyreállítása

1. Exchange-adatbázis helyreállításához kattintson a DPM felügyeleti konzol **helyreállítási** gombjára.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válasszon ki egy online helyreállítási pontot a *helyreállítási idő* legördülő listából.
4. A Helyreállítás varázsló elindításához kattintson a **Helyreállítás** **gombra.**

Az online helyreállítási pontok esetében öt helyreállítási típus létezik:

* **Helyreállítás az Exchange Server eredeti helyére:** Az adatok at az eredeti Exchange-kiszolgálóra hozza vissza a rendszer.
* **Helyreállítás egy másik Exchange Server adatbázisba:** Az adatok egy másik Exchange-kiszolgálón lévő másik adatbázisba kerülnek vissza.
* **Helyreállítás helyreállítási adatbázisba:** Az adatok visszakerülnek egy Exchange helyreállítási adatbázisba (RDB).
* **Másolás hálózati mappába:** Az adatok egy hálózati mappába kerülnek.
* **Másolás szalagra:** Ha a DPM-kiszolgálón szalagtár vagy önálló szalagos meghajtó van csatlakoztatva és konfigurálva, a helyreállítási pont egy szabad szalagra lesz másolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>További lépések

* [Azure biztonsági mentés – gyakori kérdések](backup-azure-backup-faq.md)
