---
title: Adatok helyreállítása az Azure Backup Serverről
description: Állítsa helyre az adatokat, tettem védetté a Recovery Services-tárolót minden olyan Azure Backup Serverről a tárban regisztrált.
services: backup
author: nkolli1
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: adigan
ms.openlocfilehash: 66d78be296d0786626325df36aa133d277131b3b
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267096"
---
# <a name="recover-data-from-azure-backup-server"></a>Adatok helyreállítása az Azure Backup Serverről
Az Azure Backup Server használatával készített biztonsági a Recovery Services-tárolót az adat-helyreállítást. Ennek a folyamat tehát az Azure Backup Server felügyeleti konzol integrálva van, és a helyreállítási munkafolyamat más az Azure Backup-összetevők hasonló.

> [!NOTE]
> Ez a cikk az alkalmazható [System Center Data Protection Manager 2012 R2 vagy újabb verziójával UR7](https://support.microsoft.com/en-us/kb/3065246)együtt, a [legújabb Azure Backup szolgáltatás ügynökének](https://aka.ms/azurebackup_agent).
>
>

Adatok helyreállítása az Azure Backup Server:

1. A a **helyreállítási** lap az Azure Backup Server felügyeleti konzol, kattintson a **"Külső DPM hozzáadása"** (jelenleg a képernyő bal felső).   
    ![Külső DPM hozzáadása](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Új letöltési **hitelesítő adatokat tároló** a tárolóból társított a **az Azure Backup Server** , ha az adatok helyreállítása folyik, válassza ki az Azure Backup Server az Azure Backup-kiszolgálók listájából regisztrálva a Recovery Services-tárolót, és adja meg a **titkosítási jelszó** társított a kiszolgáló, amelynek az adatok helyreállítása folyik.

    ![Külső DPM hitelesítő adatait](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Az Azure Backup csak kiszolgálók regisztrációs ugyanahhoz a tárolóhoz társított helyreállíthatja egymás adataihoz.
   >
   >

    Miután sikeresen hozzáadta a külső az Azure Backup Server, az adatokat a külső kiszolgáló és a helyi Azure Backup Server megkeresheti a **helyreállítási** fülre.
3. Keresse meg a rendelkezésre álló védi a külső az Azure Backup Server éles kiszolgálók listájában, és válassza ki a megfelelő adatforrást.

    ![Keresse meg a külső DPM-kiszolgáló](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Válassza ki **a hónapban és évben** származó a **helyreállítási pontok** legördülő menüben válassza ki a szükséges **helyreállítási dátum** számára, amikor a helyreállítási pont lett létrehozva, és válassza ki a **Helyreállítási idő**.

    Fájlok és mappák listája jelenik meg az alsó ablaktáblában, amely tallózott, és bármely helyére helyreállítani.

    ![Külső DPM-kiszolgáló helyreállítási pontok](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kattintson a jobb gombbal a megfelelő elemet, és kattintson a **helyreállítása**.

    ![Külső DPM-helyreállítási operátor](./media/backup-azure-alternate-dpm-server/recover.png)
6. Tekintse át a **kijelölés helyreállítása**. Ellenőrizze az adatokat, és folyamatban van a helyreállítása biztonsági másolat idő, valamint a forrást, amelyről a biztonsági másolat készült. Ha a kijelölt helytelen, kattintson a **Mégse** helyreállítási fülre kattintva válassza ki a megfelelő helyreállítási pontra való visszatéréshez. Ha a kijelölt helyes, kattintson a **tovább**.

    ![Külső DPM-helyreállítási operátor összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Válassza ki **visszaállítás másik helyre**. **Tallózás** a megfelelő helyre a helyreállításhoz.

    ![Külső DPM helyreállítás másik helyre](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Válassza ki a kapcsolódó lehetőséget **másolatot**, **kihagyása**, vagy **felülírás**.

   * **Másolat létrehozása** -Névütközés esetén a fájl másolatának hoz létre.
   * **Kihagyás** – névütközés, ha nem állítja helyre a fájlt, így az eredeti fájlt.
   * **Írja felül** – ha van egy ütközést, felülírja a meglévő fájl másolatát.

     Válassza ki a megfelelő lehetőséget annak **biztonság visszaállítása**. Ahol az adatok helyreállítása folyik a célszámítógép biztonsági beállításainak vagy a helyreállítási pont létrehozása idején volt a termékre vonatkozó biztonsági beállításokat alkalmazhat.

     Azonosítsa-e egy **értesítési** érkezik, a helyreállítás sikeres befejeződése után.

     ![Külső DPM-helyreállítási értesítések](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. A **összefoglalás** képernyő, amennyiben a kiválasztott beállítások listája. Miután rákattint **"Helyreállítása"**, a állítja helyre a rendszer a megfelelő helyszíni helyre.

    ![Külső DPM-helyreállítási beállítások összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A helyreállítási feladat lesz figyelhető a **figyelés** az Azure Backup Server lapján.
   >
   >

    ![Helyreállítási figyelése](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Kattinthat **külső DPM törlése** a a **helyreállítási** fülre, távolítsa el a nézetet a külső DPM-kiszolgáló DPM-kiszolgáló.

    ![Külső DPM törlése](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Hibaüzenetek hibaelhárítása
| Nem. | Hibaüzenet | Hibaelhárítási lépések |
|:---:|:--- |:--- |
| 1. |Ez a kiszolgáló nincs regisztrálva a tároló hitelesítő adataiban megadott tárolóban. |**OK:** Ez a hiba akkor jelenik meg, ha a kiválasztott tároló hitelesítőadat-fájlja nem tartozik a Recovery Services-tároló, amelyen a helyreállítás kísérlet történik az Azure Backup Server társított. <br> **Megoldás:** A Recovery Services-tároló, amely az Azure Backup-kiszolgáló regisztrálva van, töltse le a tároló hitelesítőadat-fájlja. |
| 2. |Vagy nem érhető el a helyreállítható adatok vagy a kiválasztott kiszolgáló nem DPM-kiszolgáló. |**OK:** Vannak más az Azure Backup kiszolgálók regisztrálva a Recovery Services-tárolót, vagy a kiszolgálók van még nem töltött fel a metaadatokat a kiválasztott kiszolgáló nem áll az Azure Backup Server (más néven a Windows Server vagy Windows-ügyfél). <br> **Megoldás:** Ha más Azure biztonságimásolat-kiszolgálók regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van-e. <br>Ha más Azure biztonságimásolat-kiszolgálók regisztrálva a Recovery Services-tároló, várja meg a helyreállítási folyamat elindításához a telepítést követően naponta. A feladat feltölti a metaadatokat az összes védett biztonsági mentés a felhőbe. Az adatok a helyreállításhoz elérhető lesz. |
| 3. |Másik DPM-kiszolgáló regisztrálva van ebben a tárban. |**OK:** Nem találhatók más az Azure Backup kiszolgálók, amelyek a tárolóban, amelyről a helyreállítási kísérlet történt.<br>**Megoldás:** Ha más Azure biztonságimásolat-kiszolgálók regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van-e.<br>Ha más Azure biztonságimásolat-kiszolgálók regisztrálva a Recovery Services-tároló, várja meg a helyreállítási folyamat elindításához a telepítést követően naponta. A feladat összes védett, a felhőalapú biztonsági mentés metaadatainak tölt fel. Az adatok a helyreállításhoz elérhető lesz. |
| 4. |A megadott titkosítási jelszó nem felel meg a következő kiszolgálóhoz társított jelszóval: **<server name>** |**OK:** A folyamatban, az Azure Backup Server adatokból, amelyik a helyreállítandó adatok titkosításához használt titkosítási jelszó nem felel meg a megadott titkosítási jelszó. Az ügynök nem tudja visszafejteni az adatokat. Ezért a helyreállítás sikertelen lesz.<br>**Megoldás:** Adja meg az Azure Backup Server, amelynek az adatok helyreállítása folyik társított pontos ugyanolyan titkosítási jelszava. |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Miért nem lehet külső DPM-kiszolgáló felvétele UR7 és a legújabb Azure Backup-ügynök telepítése után?

A DPM-kiszolgálók a felhőben (használatával a kumulatív frissítések kumulatív frissítés 7-nél régebbi) védett adatforrások esetében kellene, indítsa el a UR7 és a legújabb Azure Backup-ügynök telepítése után legalább egy napot várnia kell **külső DPM hozzáadása server**. A DPM védelmi csoportok a metaadatok feltöltése az Azure-bA az egynapos időszakban van szükség. Védelmi csoport metaadatai feltöltődtek először egy éjszakai feladattal keresztül.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Mi a Microsoft Azure Recovery Services agent szükséges minimális verziója?

A Microsoft Azure Recovery Services Agent ügynököt, vagy az Azure Backup-ügynök, ez a funkció engedélyezéséhez szükséges minimális verziója 2.0.8719.0.  Az ügynök verziója megtekintése: Nyissa meg a Vezérlőpultot **>** összes Vezérlőpult elemek **>** programok és szolgáltatások **>** A Microsoft Azure Recovery Services Agent. Ha a verziószáma kisebb, mint 2.0.8719.0, töltse le és telepítse a [legújabb Azure Backup szolgáltatás ügynökének](https://go.microsoft.com/fwLink/?LinkID=288905).

![Külső DPM törlése](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Következő lépések:
• [Az azure Backup – gyakori kérdések](backup-azure-backup-faq.md)
