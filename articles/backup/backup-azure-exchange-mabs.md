---
title: Exchange-kiszolgáló biztonsági mentése a Azure Backupra Azure Backup Server
description: Megtudhatja, hogyan készíthet biztonsági mentést egy Exchange-kiszolgálóról Azure Backup használatával Azure Backup Server
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 0eacd295e279a3b3bcfbdad2c67cac7160f8dcb3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466395"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Exchange-kiszolgáló biztonsági mentése az Azure-ba Azure Backup Server
Ez a cikk azt ismerteti, hogyan konfigurálható a Microsoft Azure Backup-kiszolgáló (MABS) a Microsoft Exchange Serverről az Azure-ba történő biztonsági mentéshez.  

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy a Azure Backup Server [telepítve és előkészített](backup-azure-microsoft-azure-backup.md)állapotban van.

## <a name="mabs-protection-agent"></a>MABS védelmi ügynök
Ha a MABS védelmi ügynököt az Exchange-kiszolgálóra szeretné telepíteni, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak megfelelően vannak konfigurálva. Lásd: [tűzfal-kivételek konfigurálása az ügynökhöz](https://technet.microsoft.com/library/Hh758204.aspx).
2. Telepítse az ügynököt az Exchange-kiszolgálóra, ehhez kattintson a **felügyeleti > ügynökök elemre > install** in MABS felügyeleti konzol. A részletes lépésekért lásd [a MABS védelmi ügynök telepítése](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) című témakört.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Védelmi csoport létrehozása az Exchange-kiszolgálóhoz
1. A MABS felügyeleti konzol kattintson a **védelem**elemre, majd kattintson az **új** elemre az eszköz menüszalagján az **új védelmi csoport létrehozása** varázsló megnyitásához.
2. A varázsló **üdvözlő** képernyőjén kattintson a **tovább**gombra.
3. A **védelmi csoport típusának kiválasztása** képernyőn válassza a **kiszolgálók** elemet, majd kattintson a **tovább**gombra.
4. Válassza ki a védelemmel ellátni kívánt Exchange Server-adatbázist, és kattintson a **tovább**gombra.

   > [!NOTE]
   > Ha az Exchange 2013-et védi, ellenőrizze az [exchange 2013](https://technet.microsoft.com/library/dn751029.aspx)előfeltételeit.
   >
   >

    A következő példában az Exchange 2010-adatbázis van kiválasztva.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Válassza ki az adatvédelmi módszert.

    Nevezze el a védelmi csoportot, majd válassza az alábbi lehetőségek egyikét:

   * Rövid távú védelmet szeretnék a lemezzel.
   * Online védelmet szeretnék.
6. Kattintson a **Tovább** gombra.
7. Jelölje be az **eseutil futtatása az adatok sértetlenségének ellenőrzése** lehetőséget, ha szeretné megtekinteni az Exchange Server-adatbázisok integritását.

    Ha ezt a beállítást választja, a biztonsági mentés konzisztencia-ellenőrzése a MABS-on fut, hogy elkerülje az **eseutil** parancsnak az Exchange Serveren való futtatásával generált I/O-forgalmat.

   > [!NOTE]
   > A beállítás használatához át kell másolnia az ESE. dll és az Eseutil. exe fájlokat a Mohácsi-kiszolgáló C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin könyvtárába. Ellenkező esetben a rendszer a következő hibát váltja ki:  
   > ![Eseutil-hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Válassza ki a **másolási biztonsági mentés**adatbázisát, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Ha nem a "teljes biztonsági mentés" lehetőséget választja egy adatbázis legalább egy DAG-példányához, a rendszer nem csonkolja a naplókat.
   >
   >
10. Konfigurálja a **rövid távú biztonsági mentés**céljait, és kattintson a **tovább**gombra.
11. Tekintse át a rendelkezésre álló lemezterületet, majd kattintson a **tovább**gombra.
12. Válassza ki azt az időpontot, amikor a Mohácsi-kiszolgáló létrehozza a kezdeti replikálást, majd kattintson a **tovább**gombra.
13. Válassza ki a konzisztencia-ellenőrzési beállításokat, majd kattintson a **tovább**gombra.
14. Válassza ki azt az adatbázist, amelyről biztonsági másolatot szeretne készíteni az Azure-ba, majd kattintson a **tovább**gombra. Példa:

    ![Online védelmi adatértékek meghatározása](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Adja meg a **Azure Backup**ütemtervét, majd kattintson a **tovább**gombra. Példa:

    ![Online biztonsági mentési ütemterv megadása](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Megjegyzés: az online helyreállítási pontok expressz teljes helyreállítási pontokon alapulnak. Ezért az online helyreállítási pontot az expressz teljes helyreállítási ponthoz megadott idő után kell ütemeznie.
    >
    >
16. Konfigurálja **Azure Backup**adatmegőrzési szabályát, majd kattintson a **tovább**gombra.
17. Válasszon online replikálási lehetőséget, majd kattintson a **tovább**gombra.

    Ha nagyméretű adatbázissal rendelkezik, hosszú időt is igénybe vehet, hogy a rendszer létrehozza a kezdeti biztonsági mentést a hálózaton keresztül. A probléma elkerüléséhez létrehozhat egy offline biztonsági mentést.  

    ![Online adatmegőrzési szabály meghatározása](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Erősítse meg a beállításokat, majd kattintson a **csoport létrehozása**elemre.
19. Kattintson a **Bezárás** gombra.

## <a name="recover-the-exchange-database"></a>Az Exchange-adatbázis helyreállítása
1. Exchange-adatbázis helyreállításához kattintson a **helyreállítás** elemre a MABS felügyeleti konzol.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válasszon ki egy online helyreállítási pontot a *helyreállítási idő* legördülő listából.
4. Kattintson **a** helyreállítás gombra a **helyreállítási varázsló**elindításához.

Az online helyreállítási pontok esetében öt helyreállítási típus létezik:

* **Helyreállítás az eredeti Exchange-kiszolgáló helyére:** A rendszer az eredeti Exchange-kiszolgálóra állítja vissza az adatgyűjtést.
* **Helyreállítás egy másik adatbázisba egy Exchange-kiszolgálón:** Az adatgyűjtést egy másik Exchange-kiszolgálón lévő másik adatbázisba fogja helyreállítani.
* **Helyreállítás helyreállítási adatbázisba:** Az adatgyűjtés egy Exchange helyreállítási adatbázisba (RDB) lesz helyreállítva.
* **Másolás hálózati mappába:** Az Adathelyreállítás egy hálózati mappába történik.
* **Másolás szalagra:** Ha szalagos kódtár vagy önálló szalagos meghajtó van csatlakoztatva és konfigurálva a MABS-on, a helyreállítási pont egy szabad szalagra lesz másolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>További lépések
* [Azure Backup GYIK](backup-azure-backup-faq.md)
