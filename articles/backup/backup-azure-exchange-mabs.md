---
title: Exchange-kiszolgáló biztonsági mentése az Azure Backup Server rel
description: Megtudhatja, hogy miként készíthet biztonsági másolatot egy Exchange-kiszolgálóról az Azure Backup Server használatával az Azure Backup Server szolgáltatással
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421339"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Exchange-kiszolgáló biztonsági mentése az Azure-ba az Azure Backup Server rel

Ez a cikk azt ismerteti, hogyan konfigurálható a Microsoft Azure Backup Server (MABS) egy Microsoft Exchange kiszolgáló Azure-ba történő biztonsági mentése.  

## <a name="prerequisites"></a>Előfeltételek

Mielőtt folytatna, győződjön meg arról, hogy az Azure Backup Server [telepítve van és készen áll.](backup-azure-microsoft-azure-backup.md)

## <a name="mabs-protection-agent"></a>MABS védelmi ügynök

A MABS védelmi ügynök Exchange-kiszolgálóra történő telepítéséhez hajtsa végre az alábbi lépéseket:

1. Ellenőrizze, hogy a tűzfalak megfelelően vannak-e konfigurálva. Lásd: [Tűzfalkivételek konfigurálása az ügynökhöz](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Telepítse az ügynököt az Exchange-kiszolgálóra a **MABS** felügyeleti > telepítése > telepítése gombra kattintva. Részletes lépésekért [olvassa el A MABS védelmi ügynök telepítése](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) című témakört.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Védelmi csoport létrehozása az Exchange-kiszolgálóhoz

1. A MABS felügyeleti konzolon kattintson a **Védelem**gombra, majd az eszközmenüszalag **Új** gombjára az **Új védelmi csoport létrehozása** varázsló megnyitásához.
2. A varázsló **üdvözlőképernyőjén** kattintson a **Tovább**gombra.
3. A **Védelmi csoport típusának kiválasztása** képernyőn válassza a **Kiszolgálók** lehetőséget, és kattintson a **Tovább**gombra.
4. Jelölje ki a védeni kívánt Exchange-kiszolgálóadatbázist, és kattintson a **Tovább**gombra.

   > [!NOTE]
   > Ha az Exchange 2013-at védi, ellenőrizze az [Exchange 2013 előfeltételeit.](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016)
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

    Miután kiválasztotta ezt a beállítást, a biztonsági mentés konzisztencia-ellenőrzése a MABS rendszeren fut, hogy elkerülje az **eseutil** parancs Exchange kiszolgálón történő futtatásával generált I/O-forgalmat.

   > [!NOTE]
   > A beállítás használatához az Ese.dll és az Eseutil.exe fájlokat át kell másolnia a MABS kiszolgáló N.\Program Files\Microsoft Azure Backup\DPM\DPM\bin könyvtárába. Ellenkező esetben a következő hiba lép fel:  
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
12. Válassza ki azt az időpontot, amikor a MABS-kiszolgáló létrehozza a kezdeti replikációt, majd kattintson a **Tovább**gombra.
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

1. Exchange-adatbázis helyreállításához kattintson a **Helyreállítás** gombra a MABS felügyeleti konzolon.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válasszon ki egy online helyreállítási pontot a *helyreállítási idő* legördülő listából.
4. A Helyreállítás varázsló elindításához kattintson a **Helyreállítás** **gombra.**

Az online helyreállítási pontok esetében öt helyreállítási típus létezik:

* **Helyreállítás az Exchange Server eredeti helyére:** Az adatok at az eredeti Exchange-kiszolgálóra hozza vissza a rendszer.
* **Helyreállítás egy másik Exchange Server adatbázisba:** Az adatok egy másik Exchange-kiszolgálón lévő másik adatbázisba kerülnek vissza.
* **Helyreállítás helyreállítási adatbázisba:** Az adatok visszakerülnek egy Exchange helyreállítási adatbázisba (RDB).
* **Másolás hálózati mappába:** Az adatok egy hálózati mappába kerülnek.
* **Másolás szalagra:** Ha a MABS-en szalagtár vagy önálló szalagos meghajtó van csatlakoztatva és konfigurálva, a helyreállítási pont egy szabad szalagra lesz másolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>További lépések

* [Azure biztonsági mentés – gyakori kérdések](backup-azure-backup-faq.md)
