---
title: Egy Azure Backup Server tárolt adatok helyreállításához
description: A korábban védett a Recovery Services-tároló bármely Azure biztonsági mentés kiszolgálóról, hogy a tároló regisztrált adatok helyreállítását.
services: backup
author: nkolli1
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: adigan
ms.openlocfilehash: 8559532f873e8073e736f881374fec1c080d08c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604403"
---
# <a name="recover-data-from-azure-backup-server"></a>Adatok helyreállítása az Azure Backup Serverről
Azure Backup Server használatával állítsa helyre az adatokat készített biztonsági mentést a Recovery Services-tároló. Ennek a folyamat úgy integrálva van az Azure Backup Server kezelőkonzolján, és a helyreállítási munkafolyamat más Azure Backup szolgáltatás-összetevők hasonló.

> [!NOTE]
> Ez a cikk esetén alkalmazható [System Center Data Protection Manager 2012 R2 vagy újabb verziójával UR7] (https://support.microsoft.com/en-us/kb/3065246), együtt a [legújabb Azure Backup szolgáltatás ügynökének](http://aka.ms/azurebackup_agent).
>
>

Adatok helyreállítása egy Azure Backup Server:

1. Az a **helyreállítási** fülre az Azure Backup Server kezelőkonzolján kattintson **"Külső DPM hozzáadása"** (, a képernyő bal felső).   
    ![Külső DPM hozzáadása](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Új letöltési **hitelesítő adatokat tároló** társított tárolóból az **Azure Backup Server** az adatok helyreállítása folyamatban, ahol az Azure biztonsági mentés kiszolgáló Azure Backup kiszolgálók közül regisztrálva a a Recovery Services-tároló kiválasztása, és adja meg a **titkosítási jelszó** a kiszolgáló, amelynek adatait helyreállítás alatt álló társított.

    ![Külső DPM hitelesítő adatait](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Csak Azure biztonsági mentés kiszolgálóit a azonos regisztrációja tárolóval társított állíthatja helyre egymás adatait.
   >
   >

    A külső Azure Backup Server sikeres hozzáadása után megkeresheti a külső kiszolgáló és a helyi Azure biztonsági mentés kiszolgáló származó adatok a **helyreállítási** fülre.
3. Keresse meg a rendelkezésre álló az Azure biztonsági mentés külső kiszolgáló az üzemi kiszolgálók listájában, és válassza ki a megfelelő adatforrást.

    ![Keresse meg a külső DPM-kiszolgáló](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Válassza ki **a hónapban és évben** a a **helyreállítási pontok** legördülő listán, válassza ki a szükséges **helyreállítási dátum** a helyreállítási pont létrehozásának, és válassza ki a **helyreállításkor**.

    Az alsó ablaktáblában, megtekintése és bárhova helyreállított fájlok és mappák listája jelenik meg.

    ![Külső DPM-kiszolgáló helyreállítási pontok](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kattintson jobb gombbal a megfelelő elemre, és kattintson a **helyreállítása**.

    ![Külső DPM helyreállítás](./media/backup-azure-alternate-dpm-server/recover.png)
6. Tekintse át a **kijelölés helyreállítása**. Ellenőrizze az adatokat és a helyreállítás alatt álló biztonsági másolat idő, valamint a biztonsági másolat létrehozásához használt forrás. Ha a kijelölt adatok helytelenek, kattintson a **Mégse** lehetőségre, és navigáljon a helyreállítási fülre kattintva válassza ki a megfelelő helyreállítási pont. Ha az érték a helyes-e, kattintson a **következő**.

    ![Külső DPM helyreállítási összefoglaló](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Válassza ki **másodlagos helyre**. **Tallózás** a megfelelő helyre a helyreállításhoz.

    ![Külső DPM helyreállítás másik helyre](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Kapcsolódó lehetőséget **készítsen másolatot**, **kihagyása**, vagy **felülírása**.

   * **Készítsen másolatot** -Névütközés esetén a fájl másolatát hozza létre.
   * **Kihagyás** - Ha névütközés, nem tudja elhárítani a fájlt, így az eredeti fájlt.
   * **Írja felül** - Ha névütközés, felülírja a fájlt meglévő példányát.

     Válassza ki a megfelelő lehetőséget annak **biztonság visszaállítása**. A biztonsági beállításainak a célszámítógépen, amelyen az adatok helyreállítása folyamatban vagy a helyreállítási pont létrehozása idején volt termékre vonatkozó biztonsági beállításokat alkalmazhat.

     Azonosítsa e egy **értesítési** zajlik, a helyreállítás sikeres befejeződése után.

     ![Külső DPM helyreállítási értesítések](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. A **összegzés** képernyő, amennyiben a kiválasztott beállítások listája. Miután rákattintott **"Helyreállítása"**, az adatok helyreállítása a megfelelő helyszíni helyre.

    ![Külső DPM-helyreállítási beállítások összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A helyreállítási feladat lesz figyelhető a **figyelés** az Azure Backup Server lapján.
   >
   >

    ![Helyreállítási figyelő](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Kattinthat **egyértelmű a külső DPM** a a **helyreállítási** a nézet a külső DPM-kiszolgáló eltávolítása a DPM-kiszolgáló lapon.

    ![Külső DPM törlése](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Hibaüzenetek hibaelhárítása
| Nem. | Hibaüzenet | Hibaelhárítási lépések |
|:---:|:--- |:--- |
| 1. |Ez a kiszolgáló nincs regisztrálva a tárolónál, amelyet a tároló hitelesítő adatait. |**OK:** Ez a hiba akkor jelenik meg, amikor a kijelölt tároló hitelesítő adatait tartalmazó fájlt nem tartozik a Recovery Services-tároló, amelyen a helyreállítási kísérlet Azure biztonsági mentés kiszolgálóhoz társított. <br> **Megoldás:** letöltési a tárolói hitelesítő adatok fájlját az a Recovery Services-tároló az Azure Backup Server regisztrálni. |
| 2. |A helyreállítható adatok nem érhető el, vagy a kijelölt kiszolgálón található DPM-kiszolgáló. |**OK:** van regisztrálva a Recovery Services-tároló, hogy más Azure biztonsági mentés kiszolgálók vagy a kiszolgálók még nem feltöltött a metaadatok, vagy a kiválasztott kiszolgáló nem egy Azure Backup Server (más néven a Windows Server vagy a Windows ügyfél). <br> **Megoldás:** Ha egyéb Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy telepítve van-e a legújabb Azure Backup szolgáltatás ügynöke. <br>Ha más Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, várja meg a helyreállítási folyamat elindításához a telepítés után naponta. Az ütemezett feladat fel kell töltenie az összes védett biztonsági mentés felhőbe metaadatait. Az adatok elérhetők helyreállítás. |
| 3. |Egyetlen más DPM-kiszolgáló regisztrálva van ebben a tárolóban. |**OK:** nincsenek kiszolgálók, amelyek más Azure biztonsági mentése a tárolóba, ahol a helyreállítási kísérletek regisztrált.<br>**Megoldás:** Ha egyéb Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy telepítve van-e a legújabb Azure Backup szolgáltatás ügynöke.<br>Ha más Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, várja meg a helyreállítási folyamat elindításához a telepítés után naponta. Az ütemezett feladat minden védett felhőbe biztonsági mentés metaadatainak feltöltését. Az adatok elérhetők helyreállítás. |
| 4. |A megadott titkosítási jelszó nem egyezik a következő kiszolgálóhoz tartozó jelszóval: **<server name>** |**OK:** folyamatban van, az Azure Backup Server adatokból, amelyik a helyreállítandó adatok titkosítására használt titkosítási jelszó nem egyezik meg a megadott titkosítási jelszó. Az ügynök nem tudja visszafejteni az adatokat. Ezért a helyreállítás sikertelen lesz.<br>**Megoldás:** adja meg a helyreállítás alatt álló amelyek esetén az Azure biztonsági mentés kiszolgálóhoz tartozó pontos azonos titkosítási jelszó. |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Miért nem külső DPM-kiszolgáló hozzáadása UR7 és a legújabb Azure Backup szolgáltatás ügynökének telepítése után?

A DPM-kiszolgálók (az Update Rollup 7-nél korábbi felhasználásával kumulatív frissítések) a felhő védett adatforrások, meg kell várnia az UR7 és a legújabb Azure Backup szolgáltatás ügynöke, indítsa el a telepítése után legalább egy napot **vegye fel a külső DPM-kiszolgáló**. A egy napos időszakon belül van szükség a DPM védelmi csoportok a metaadatok feltöltése az Azure-bA. Védelmi csoport metaadatai feltöltődtek először egy éjszakai feladattal keresztül.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Újdonságok a Microsoft Azure Recovery Services Agent ügynököt szükséges minimális verziója?

A Microsoft Azure Recovery Services Agent ügynököt, vagy az Azure Backup szolgáltatás ügynöke, a szolgáltatás engedélyezéséhez szükséges minimális verziója 2.0.8719.0.  Az ügynök verziójának megtekintése: Nyissa meg a Vezérlőpultot **>** összes elemek **>** programok és szolgáltatások **>** Microsoft Azure Recovery Services Agent. Ha a verziószáma kisebb, mint 2.0.8719.0, töltse le és telepítse a [legújabb Azure Backup szolgáltatás ügynökének](https://go.microsoft.com/fwLink/?LinkID=288905).

![Külső DPM törlése](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Következő lépések:
• [Azure biztonsági mentési – gyakori kérdések](backup-azure-backup-faq.md)
